+++
title = 'How to make the Game Boy speak'
date = 2024-09-04T21:06:47+02:00
draft = true
+++

A while ago, as I was reading about chess engines, I suddenly started wondering how a chess engine might perform on my favorite retrocomputing platform: the Game Boy. I quickly found a ROM for a game called "The Chessmaster", and booted it up in an emulator. On the title screen I was greeted not only by the face of an old man present on all games of the series, but to my surprise I also heard a voice say the line "Welcome to Chessmaster".

<style>
    .audio-button {
        position: relative;
    }

    .audio-button:active {
        transform: scale(0.995);
    }

    .audio-button:hover .play-icon:not(.hidden) {
        opacity:0.99;
    }

    .play-icon.hidden {
        opacity: 0;
        /*visibility: hidden;*/
    }

    .play-icon {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 20vh;
        color: white;
        opacity: 0.75;
        transition: opacity 0.2s;
        user-select: none;
    }
</style>

<figure class="align-center">
    <div class="audio-button" onclick="playAudio(this)">
        <img loading="lazy" src="chessmaster.png#center" alt="Chessmaster intro screen">
        <audio id="audio-file" src="intro.wav"></audio>
        <div class="play-icon">▶</div>
    </div>
    <figcaption><p>Is this the eponymous Chessmaster? Press to play</p></figcaption>
</figure>

<script>
    function playAudio(e) {
        const audio = e.getElementsByTagName('audio')[0];
        const playIcon = e.getElementsByClassName('play-icon')[0];

        if (audio.paused) {
            audio.play();
            playIcon.classList.add('hidden');

            audio.onended = () => {
                audio.currentTime = 0;
                playIcon.classList.remove('hidden');
            };
        }
    }
</script>

I call it a surprise, because the Game Boy's audio capabilities, despite allowing skillful composers to make the wonderful chiptunes that accompanied many hours of gaming fun in my youth, aren't particularly stellar. The Game Boy sports four distinct "channels", two pulse-width modulated square wave generators, a rudimentary pulse-code modulated (PCM) wave channel and a pseudorandom noise channel. Although the wave channel would appear to be the likely culprit, it cannot read from arbitrary memory regions, you need to prefill a 16 byte (32 samples) buffer in the CPU first, which is inaccessible during playback. Moreover, writing out the channels in separate files from the emulator showed that only channels 1 and 2 were producing an identical sound, with the other two muted. Inspecting the waveform confirms a PCM wave is indeed being emitted, at about 7.5 kHz and with a bitdepth of 3. We'll get to the peculiar appearence of the demodulated signal, with the tops of the "staircase" so strongly slanted, later.

{{< figure src="waveform.png" caption="Waveform of the sound clip" >}}

## Annotated disassembly

At this point I decided to just dive in the assembly code, and I used [BGB](https://bgb.bircd.org/)'s excellent debugger to figure out what was going on. I noticed that simply breaking into the debugger during playback put me right around the right spot, as interrupts were disabled (presumably to avoid jitter), but to be sure I didn't miss anything, I just stepped through all the code starting from the entry point, as I knew the first clip played very early in the game, at the title screen. The only thing to notice before looking at the relevant subroutine, is that the game stores `$1C` (28 in decimal) at memory location `$C7FB`.

We jump into the subroutine at address `$2726`, which I've labelled `PlayPCM`. This first bit of the code uses the `A` register as an index into an array starting at `$27BC`, where each element consists of two 16 bit entries, which are then loaded in the `HL` and `DE` registers.

```rgbds
PlayPCM:
sla  a
sla  a          ; Multiply A by 4
add  a, $BC
ld   c, a
ld   a, $27
adc  a, $00
ld   b, a       ; And add to $27BC
ld   a, [bc]
ld   l, a
inc  bc
ld   a, [bc]
ld   h, a       ; Load the first word into HL
inc  bc
ld   a, [bc]
ld   e, a
inc  bc
ld   a, [bc]
ld   d, a       ; And the second into DE
```

Next, interrupts are disabled, the APU is initialized and channel 2 is turned on, at a ridiculous frequency of 131072 Hz.

```rgbds
di   
ld   a,$77
ldh  [NR50],a   ; Master volume to maximum
ld   a,$FF
ldh  [NR51],a   ; Pan all channels center
ld   a,$FF
ldh  [NR52],a   ; Turn the APU on. Only the MSB is read, making the previous redundant assignment of $FF to A even more pointless
ld   a,$00
ldh  [NR21],a   ; Set duty cycle of channel 2 to 7 high, 1 low
ld   a,$08
ldh  [NR22],a   ; Turn the DAC on, but do not actually unmute the channel
ld   a,$FF
ldh  [NR23],a   ; Period divider to 2^20 Hz
ld   a,$87
ldh  [NR24],a   ; Trigger the channel to turn it on
```

The following code block loads three bytes from the memory region pointed to by `HL` into the `A` register and calls the `PlaySample` routine, which we'll come to later. Taken together the three bytes contain eight 3-bit samples. To make the bit twiddling a bit more clear, I will label the samples A through H, hence the three bytes are layed out as `AAABBBCC CDDDEEEF FFGGGHHH`. In the comments after every line I've put the result of the instruction, where the relevant sample is capitalized and the others are not. As we refer to the [CPU opcode reference](https://rgbds.gbdev.io/docs/v0.8.0/gbz80.7), one thing that is important to keep in mind is that the rotate and shift instructions involve the carry flag, which explains why some manipulations look they're immediately overwritten. At the end the `DE` register, which counts the number of bytes in the clip, is decremented by three.

```rgbds
.loop:
ld   a, [hl]    ; AAAbbbcc
srl  a          ; 0AAAbbbc
call PlaySample ; play AAA
ld   a, [hl]    ; aaaBBBcc
sla  a
sla  a          ; aBBBcc00
call PlaySample ; play BBB
ldi  a, [hl]    ; aaabbbCC
ld   b, [hl]    ; Cdddeeef
sla  b          ; dddeeef0
rl   a          ; aabbbCCC
swap a          ; bCCCaabb
call PlaySample ; play CCC
ld   a, [hl]    ; cDDDeeef
call PlaySample ; play DDD
ld   a, [hl]    ; cdddEEEf
srl  a          ; 0cdddEEE
swap a          ; dEEE0ccc
call PlaySample ; play EEE
ldi  a, [hl]    ; cdddeeeF
srl  a          ; 0cdddeee
ld   a, [hl]    ; FFggghhh
rr   a          ; FFFggghh
srl  a          ; 0FFFgggh
call PlaySample ; play FFF
ld   a, [hl]    ; ffGGGhhh
sla  a          ; fGGGhhh0
call PlaySample ; play GGG
ldi  a, [hl]    ; ffgggHHH
swap a          ; gHHHffgg
call PlaySample ; play HHH
dec  e
jr   z, .E1
dec  e
jr   z, .E2
dec  e
jr   z, .E3
jp   .loop

.E1:
dec  e
.E2:
dec  e
.E3:
dec  d
ld   a, d
cp   a, $FF
jp   nz, .loop
ei
ret
```

Finally, the `PlaySample` routine masks out the 3 bit sample in the upper nibble of `A`, duplicates the nibbles, and at last writes it `NR50`, setting both the left and right master volume. After loading the sample it busy-waits for as many iterations as the value at `$C7FB` mentioned earlier indicates.

```rgbds
PlaySample:
and  a, $70
ld   b, a
swap a
or   b
ldh  [NR50], a      ; Master volume control
ld   a, [$C7FB]
.wait:
dec  a
jr   nz, .wait
ret  
```

## How does it work?

So we find the code simply toggles the volume extremely rapidly, essentially "bit-banging" the PCM. But how does this work, given that the channels are not actually outputting any sound? We have to refer to the ever useful [Pan Docs](https://gbdev.io/pandocs/Audio.html) on the Game Boy's APU to figure out the mechanism. First of all, because the analog output of the DAC is -1 to +1 (in arbitrary units), a volume of 0 doesn't correspond to zero voltage, but instead to +1 (for some reason the voltage range is inverted). Usually this doesn't matter because a high-pass filter will remove any DC bias, but if you're quick enough... Then, the master volume is applied to the analog signal, before it goes through said high-pass filter. This explains the funny slanted look of the signal, as BGB, to its author's credit, emulates this behavior quite accurately.

To estimate the sample rate, we're going to have to count the cycles, consulting the [optables](https://gbdev.io/gb-opcodes/optables/) whenever necessary (contrary to those tables I will give the values here in M-cycles, not T-states). Starting from `PlaySample` we find that it takes 20 cycles, plus another 4 for every value of `$C7FB` above 1. Taking the intro sound as reference, where `$C7FB` is 28, that makes for a total of 128 cycles. One iteration of `PlayPCM.loop` usually takes 107 cycles, unless it's time to decrement `D`, in which case it takes 108, 110 or 112 cycles, depending on whether `E` is respectively 1, 2 or 3 (remember that every iteration of the loop loads three bytes in total). So to be really pedantic, on average it takes another 9 256ths of a cycle. The Game Boy's CPU operates at a clock rate of 4.194304 MHz, which seems like an awfully specific number until you realize it's just the 22nd power of 2. A single M-cycle takes 4 clock ticks, hence the cycle budget is 2^20 per second. Putting all of this together, we get a sample rate of `8 samples per loop / ((107 + 9/256 + 8 * (20 + 27*4) cycles per loop) / 2^20 cycles per second) = 7416.75... samples per second`, which thankfully checks out perfectly with the recording from the emulator.

Three bits of volume control is all the Game Boy's APU offers, so there is no easy way to increase the bit-depth, but by reducing the waiting time between samples it should in principle be possible to increase the sample rate to about 30 kHz, and that's not even taking into account the optimizations one could make to the code. With some dithering perhaps the effective bit depth could be made a lot higher as well, potentially greatly increasing the fidelity of the audio. Presumably this wasn't done in the game because of size issues (the cartridge is already jam-packed) or because the developers thought it was good enough (and I certainly don't disagree).

But if only channel 2 is initialized, why does channel 1 emit an identical tune? Well, it turns out channel 1 was in fact initialized the moment the console was booted up, as the Nintendo logo scrolls down and you hear the classic two-tone pling. The boot ROM never actually turns off the channel used for those notes, it uses the volume envelope feature to make them fade out, and the Game Boy doesn't automatically turn off the DAC corresponding to a channel when its envelope hits zero, for that to happen the volume needs to be manually set to zero (which [according to the Pan Docs](https://gbdev.io/pandocs/Audio_details.html#mixer) is apparently counterindicated, as turning off DACs tends to produce audible pops). This oversight explains a bug (if you can even call it that), where PCM sounds permanently play at half the volume if the menu is entered at any point during the game. For some reason that is beyond me, for the menu beeps the developers didn't opt to make use of the length or volume envelope feature of the first PWM channel, instead choosing to busy-loop for a little more than 50 000 cycles, which is the full duration of the beep, a little less than 50 milliseconds (the subroutine at `$26BA` for the curious). After this complete waste of time, the volume of the channel is manually set to zero, which turns off the DAC as well.

Now that we know how it works, it's not a very hard task anymore to manually extract the soundbytes from the ROM with a little bit of bit-twiddling in Python. I even found out the Python standard library includes [a module for reading and writing WAV files](https://docs.python.org/3/library/wave.html) (batteries included indeed!). Although at first sight I thought the table at `$27BC` seemed to go on for quite a while, only the first 11 entries seem to refer to sounds(?), which I confirmed with some judicious usage of watchpoints (the next entries, although they're in the same format, refer to tilemaps). These are ....

## Conclusion

Halfway through figuring this out, I suddenly realized that there was another game that could "speak", ironically one of the very first games I ever played on the Game Boy: Pokémon Yellow. I remembered one of my favorite YouTube channels, Retro Game Mechanics Explained, had already made [a video](https://youtu.be/fooSxCuWvZ4) on this topic. I heartily recommend anyone to give it a watch, as well as the other videos on their channel, but luckily for me I still had reason to make this post, as the mechanism used in Pokémon Yellow is quite different, not relying on digital-to-analog conversion quirks, but keeping the wave channel on and quickly changing its output level instead.

There's something very enjoyable about reverse engineering that tickles my brain in the right way. You gather clues and perform experiments, and slowly things start to click and what used to be a bunch of unlabeled assembly code suddenly has meaning and a clear purpose. Moreover, you get to peek in the minds of developers from multiple decades ago, and figure out what they were thinking (or when they clearly weren't thinking at all). Furthermore, many games were shipped with unused content or code, which makes it a sort of digital archaeology. Even from my short dive into "The Chessmaster"'s code there are still quite a few loose ends left now, like the unused jumptable routine at the very start of the ROM, or the jumps in the joypad interrupt routine that are unreachable because it starts with a `RETI` instruction, but I think I'll call it a day now. I hope you found it as interesting as I did.
