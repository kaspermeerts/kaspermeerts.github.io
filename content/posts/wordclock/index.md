+++
title = 'Word Clock'
date = 2024-11-13T16:15:19+01:00
draft = true
+++

When I first saw a clock spelling out the time in words somewhere online, I thought it looked like a neat project, not too complicated but still giving a very aesthetically pleasing result. After I gained some experience with a laser cutter, I finally felt up to the task.

## The letters

For the language I quickly decided on my native Dutch, English is already way too overused and borderline fethisized in our society anyway. And it saves me from the temptation of stealing the frontplate from somebody else's project.

After that I had to decide the size of the grid. I wanted it to be square, and it was a hard requirement for me that the clock be actually functional, with which I mean that it had to give the exact time up to the minute. Rounding the time to the nearest five minutes, even with some workaround to otherwise signal the remainder, was unacceptable. Furthermore I wanted the words to be contiguous, i.e. I didn't want to reuse any common prefix or suffix, broken over two lines. Finally, the way it tells the time had to feel natural. I didn't really have a nice algorithmic plan to fill in the letters, so I just started fitting them as best as I could, overlapping parts of words where I could (e.g. TWEE with EEN, or EIGHT with EIGHTEEN). I ended up reusing part of the minutes indications for the hours, though I did swap them around a little bit to avoid a distracting repetion being too obvious.

While I think you can do it in English, I just barely did not get it to fit in a 12 by 12 grid, which meant that with the 25 extra spaces a 13 by 13 grid provides there were ample empty spaces. I filled them in with random letters, and the end result looks a bit like the following:

<style>
    .grid {
        margin: auto;
        display: grid;
        grid-template-columns: repeat(13, 1fr);
        width: 480px;
        height: 480px;
        background-color: rgb(20,20,20);
        padding: 10px;
        line-height: normal;
    }
    .letter {
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 28px;
        color: grey;
        //font-weight: bold;
        font-family: Monospace;
    }
    .letter.on {
        font-weight: bold;
        color: yellow;
    }
</style>
<div class="grid" id="grid"></div>

<script>
    const ROWS = 13;
    const COLS = ROWS;

    function lookupWord(word, startRow = 0) {
        for (let i = startRow; i < ROWS; i++) {
            for (let j = 0; j < COLS - word.length + 1; j++) {
                if (letters.slice(i*COLS + j, i*COLS + j + word.length) == word) {
                    //console.log("Found " + word + " at " + i + ", " + j);
                    return [i*COLS + j, word.length];
                }
            }
        }
        console.error("No match for " + word);
        return [];
    }

    const letters = ""
+"HETGISKTWAALF"
+"TWEENEGENTIEN"
+"ZEVENTIENDRIE"
+"VIJFLVEERTIEN"
+"ACHTTIENXVIER"
+"ELFMQNZESTIEN"
+"DERTIENUYACHT"
+"TWINTIGMKWART"
+"VOOROVERPHALF"
+"VIERNEGENACHT"
+"TWEENDRIETIEN"
+"TWAALFVIJFELF"
+"ZEVENZESBIUUR";

    const cHET = lookupWord("HET");
    const cIS = lookupWord("IS");
    const cKWART = lookupWord("KWART");
    const cMINUTES = ["", "EEN", "TWEE", "DRIE", "VIER", "VIJF", "ZES", "ZEVEN", "ACHT", "NEGEN", "TIEN", "ELF", "TWAALF", "DERTIEN", "VEERTIEN", "KWART", "ZESTIEN", "ZEVENTIEN", "ACHTTIEN", "NEGENTIEN", "TWINTIG"].map((word) => lookupWord(word));
    const cHALF = lookupWord("HALF");
    const cVOOR = lookupWord("VOOR");
    const cOVER = lookupWord("OVER");
    const cHOURS = ["TWAALF", "EEN", "TWEE", "DRIE", "VIER", "VIJF", "ZES", "ZEVEN", "ACHT", "NEGEN", "TIEN", "ELF", "TWAALF"].map((word) => lookupWord(word,Math.floor(cVOOR[0]/COLS)));
    const cUUR = lookupWord("UUR", ROWS - 1);

    var grid = document.getElementById('grid');
    //grid.style.gridTemplateColumns = 'repeat(' + ROWS + ', 1fr)';

    const gridArray = [];
    for (let row = 0; row < ROWS; row++) {
        const rowArray = [];
        for (let col = 0; col < COLS; col++) {
            const letter = document.createElement('div');
            letter.className = 'letter';
            letter.textContent = letters[row * COLS + col] ; //+ '' + (row*12+col);
            grid.appendChild(letter);
            const coord = document.createElement('div');
            coord.className = 'coord';
            coord.textContent = row*COLS+col;
            //grid.appendChild(coord);
            gridArray.push(letter);
        }
    }

    function getLetter(row, col) {
        if (row >= 0 && row < ROWS && col >= 0 && col < COLS) {
            return gridArray[row*ROWS + col];
        }
        return null;
    }

    function colorWord(word) {
        for (let i = 0; i < word[1]; i++) {
            gridArray[word[0] + i].classList.add("on");
        }
    }

    function colorTime(time) {
        const hours = time.getHours() % 12;
        const minutes = time.getMinutes();

        colorWord(cHET);
        colorWord(cIS);

        if (minutes == 0) {
            colorWord(cHOURS[hours]);
            colorWord(cUUR);
            return;
        } else if (minutes < 20) {
            colorWord(cMINUTES[minutes]);
            colorWord(cOVER);
            colorWord(cHOURS[hours]);
        } else if (minutes < 30) {
            colorWord(cMINUTES[30 - minutes]);
            colorWord(cVOOR);
            colorWord(cHALF);
            colorWord(cHOURS[(hours + 1)%12]);
        } else if (minutes == 30) {
            colorWord(cHALF);
            colorWord(cHOURS[(hours + 1)%12]);
        } else if (minutes < 40) {
            colorWord(cMINUTES[minutes - 30]);
            colorWord(cOVER);
            colorWord(cHALF);
            colorWord(cHOURS[(hours + 1)%12]);
        } else if (minutes < 60) {;
            colorWord(cMINUTES[60 - minutes]);
            colorWord(cVOOR);
            //colorWord(cHALF);
            colorWord(cHOURS[(hours + 1)%12]);
        }
    }

    function updateColors() {
        const now = new Date();
        const seconds = now.getSeconds();
        const minutes = now.getMinutes();
        const hours = now.getHours() % 12;

        gridArray.forEach(letter => letter.classList.remove("on"));

        let randomTime = new Date(Math.random()*86400*1000-3600000);
        colorTime(randomTime);
    }

    setInterval(updateColors, 5000);
    updateColors();
</script>

You'll notice every letter of the alphabet appears at least oonce (or at least, that was the plan! More on that later). While originally I had vague plans to use this to simulate some type of ouija board or ticker tape, in the end they just served to fill the voids.

## Hardware

I didn't want get to experimental so I planned to use a simple addressable LED strip for the lights behind the letters. There's not that much choice in the spacing, and between the gigantic 30 LEDs per m and tiny 144 LEDs per m, 60 LEDs per m seemed reasonable. I bought 5 meters of WS2813 individually addressable LEDs, which in hindsight almost turned out to be a serious mistake, an Arduino, power supply, breadboard, etc... as well as a new soldering iron, because the one I had seems to have vanished from the face of the earth.

<figure class='align-center'>
<video src="firstarduinoleds.mp4" controls style="display:block;margin:auto;width:75%"></video>
<figcaption><p>The first thing everyone does with an Arduino is drive some LEDs</p></figcaption>
</figure>

For the support for the LEDs I thought I had a luminous idea in using an aluminum plate. My reasoning was twofold: for one it could serve as a heatsink, as overheating is apparently one of the main causes of early LED degradataion, and for two I had the ambition to use it as a ground plate, which I hoped would save me a ton of time soldering. As I found out, to the detriment of a brand new soldering tip, it's in fact very hard to solder anything to aluminum. Moreover, the glue on the bottom of the strip serves as the only tiny isolating layer between the aluminum and the PCB pads, which meant I had to constantly check if I hadn't accidentally made a short-circuit.

Time to start marking the positions of the LEDs on the plate and soldering them together. After a small crisis, because it turns out there are absolutely zero stores (even among those geared towards electronics) in a wide range around me selling something as simple as plain old **wire** (which meant I had to order it online), I managed to align the first few strips, which luckily went without a hitch.

{{< figure align="center" src="soldermess.jpg" caption="Not too proud of this" >}}

Soon after, I noticed that I had shot myself in the foot a bit by getting the fancy WS2813 chips instead of the more common WS2812b. I turns out the former has an extra backup line, which means that a single LED failing doesn't turn off all the LEDs following it. However, soldering together 3 lines was already very fiddly, and I rued having to put in a fourth one. I risked simply leaving the line unconnected, which luckily seemed to work.

<figure class='align-center'>
<video src="firststrips.mp4" controls style="display:block;margin:auto;width:75%"></video>
<figcaption><p>Two down, eleven to go</p></figcaption>
</figure>

The solderwork was quite relaxing, apart from the short-circuits that occasionally showed up, luckily always before testing. I ruined more than a few LEDs (luckily I had 131 of them to spare) but in the end managed to get a fully 2D addressable matrix on there.

<figure class='align-center'>
<video src="matrix.mp4" controls style="display:block;margin:auto;width:75%"></video>
<figcaption><p>Only a few hours of soldering work</p></figcaption>
</figure>

Next up were the baffles to block the light from bleeding over to neighboring letters. As I still had access to a lasercutter, I wrote a small Python script to simultaneously plot the baffles as well as position the letters inside of the little squares. This was meant to check the alignment, but I immediately discovered a serious problem with the Q. Of the capital Latin letters, no letter has an ascender, but Q as the lone exception does have a descender, meaning the little bottom hook goes below the baseline. This meant I had to choose between either giving every single other letter a small margin on all sides (to keep them centered on the LED), or ditch the Q. In the end I chose the latter option and replaced it with an H, because I prefered to keep the letters closely spaced yet easily readible.

For the frame of the entire contraption, I settled on an Ikea SANNAHED frame. As I was anticipation the square to measure 13/60 meters, or about 22cm, it turned out to be a perfect fit. With a piece of tracing paper as the diffuser, it was time to think about the frontplate with the actual words on it.

At first I cut the letters out of a thin plate of wood, but I really didn't like the aesthetic of it. Even the thinnest wood gives a feeling of depth to the letters, making them particularly hard to read at an angle. Moreover, there is no nice way to keep the parts of the letter not joined to the outside, e.g. the semicircles in the B's and D's or the circle at the center of the O. One solution is to use a stencil font, in which all letters are simply connected, but this again offended my aesthetic sensibilities.

In the end I tried using a black piece of thick paper in the laser cutter, and glued it directly onto the tracing paper. I made tons of copies of the loose bits of the nonconnected letters to try and salvage at least a few of them, and very tediously glued them back in the right places. Luckily the SANNAHED frame included an plastic front protection to slightly flatten everything.

## Software

Although I initially aimed to run the project on an Arduino, aided by a DS3231 real-time clock, I discovered the Arduino is very limited memory-wise and synchronizing by NTP would be much more practical than relying on an RTC that would eventually drift anyway. Luckily I found out ESP32 devkits are quite cheap, and transferred my project to this platform.

Sadly, the ESP32's API is incredibly underdocumented and useful examples are lacking. Though I planned to program the microcontroller quite robustly and defensively, as is my custom, the internet is so reliable nowadays that by the time I got the prototype working I kind of lost the motivation. The clock has been running uninterruptedly for a few months now with nary a hiccup. Instead of keeping it white or monochrome, the colors slowly cycle through all kinds of patterns.

I ended up just connecting the devkit to the LED array and leaving it at that. In a future project, I'll definitely plan on making my own PCB.

## End result

It looks better in real life than on a picture, because the contrast of the active letters to the background is so high the colors wash out. All in all I'm quite happy with the result.

{{<figure src="endresult.jpg" caption="What it ended up looking like" align="center" >}}
