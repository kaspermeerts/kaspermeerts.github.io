+++
title = 'On the Russian verb "ответить"'
date = 2024-10-12T23:58:13+02:00
draft = true
summary = "When did ответить appear in the Russian language, and what came before it?"
+++

As I struggled my way through the 1842 novel Мертвые Души, something peculiar jumped out at me

> Здесь учитель обратил всё внимание на Фемистоклюса и, казалось, хотел ему вскочить в глаза, но наконец совершенно успокоился и кивнул головою, когда Фемистоклюс **сказал**: "Париж".  
> "А у нас какой лучший город?" **спросил** опять Манилов.  
> Учитель опять настроил внимание.  
> "Петербург", **отвечал** Фемистоклюс.  
> "А еще какой?"  
> "Москва", **отвечал** Фемистоклюс.  
> "Умница, душенька!" **сказал** на это Чичиков.  

The dialogue tags are usually in the perfective aspect, as is to be expected, apart from one: отвечать. When this verb occurs in between dialogue, it's exclusively the imperfective отвечать, and never its perfective counterpart ответить. In fact, the verb отвечать shows up in the book 100 times, whereas ответить occurs only 3 times in total. It's not just a quirk of Gogol either, if we turn to another book, Капитанская Дочка, which was released 6 years earlier, the score this time is 106 to 1. However, moving forward in time the picture changes a bit, by 1878 in Tolstoy's Анна Каренина, both are freely used, with отвечать occuring 346 times compared to 80 times for ответить. Moreover, it now also occurs near direct speech, and, strangely, occasionally both are used in short succession

> – Это помещение для доктора и аптеки, – **отвечал** Вронский, увидав подходившего к нему в коротком пальто архитектора, и, извинившись перед дамами, пошел ему навстречу.  
> Обойдя творило, из которого рабочие набирали известку, он остановился с архитектором и что-то горячо стал говорить.  
> – Фронтон все выходит ниже, – **ответил** он Анне, которая спросила, в чем дело.

Instead of just scrolling through random books, we can make this search a little more systematic by turning to the [Russian National Corpus](https://ruscorpora.ru/) (НКРЯ), and plotting the frequency of both lemmas by year.

{{< figure src="ruscorpora.svg" caption="Отвечать (red) compared to ответить (blue)" >}}

Surprisingly, the word ответить basically did not exist until the 19th century! I also suspect the earliest counts are a little inflated. If we [sort the occurrences of ответить by creation date](https://ruscorpora.ru/s/en147), we see that the earliest attestations are mostly forms of отведенный, which are derived from the verb отвести instead. Moreover, I started checking out some of the actual attestations of ответить.

## Attestations

The oldest one, dated to 1765, appears in a book with a mouthful of a title "Семена Порошина записки, служащие к истории Его Императорского Высочества Благоверного Государя Цесаревича и Великого Князя Павла Петровича". Although I [could verify this one on National Electronic Library](https://viewer.rusneb.ru/ru/000199_000009_003545204?page=158), the book in which these notes were published itself dates from 1881. That's more than a century later, and I doubt whether this counts as a true attestation, who knows what kind of editorialization was done in the meantime, not to mention the possibility of a simple typo.

The next comes from a collection of Russian fairytales, "Пересмешник, или Славенские сказки", published in 1766. Luckily НЭБ also [has it online](https://viewer.rusneb.ru/ru/000199_000009_006500551?page=54) (though perhaps it's not a coincidence that the texts in the НКРЯ are usually also digitized somewhere). To my surprise, it turned out the original text actually read "отвечал" instead of "ответил"!

{{< figure src="otvetit1.png" align="center" >}}

Following up with the next attestation in a 1773 translation of a French book by Gabriel Bonnot de Mably, Размышления о греческой истории или о причинах благоденствия и несчастия Греков, the transcript reads "хотя ответить злодею". Annoyingly, this book is on the НЭБ Книжные памятники, which uses a different reader where I can't link to individual pages (I do not know the difference between the normal НЭБ and this subsite), but if we look at [page 249 of this book](https://kp.rusneb.ru/item/reader/razmyshleniya-o-grecheskoy-istorii-ili-o-prichinah-blagodenstviya-i-neschastiya-grekov-sochinenie-g-abbata-de-mabli-perevedeno-s-francuzskago-izhdiveniem-obshchestva-starayushchegosya-o-napechatanii-knig-prodaetsya-v-lugovoy-milionnoy-ulice-u-knigoprodavca-kv-millera), we find instead the word отмстить, so presumably there was a transcription error.

{{< figure src="otvetit2.png" align="center" >}}

Coming up as we get to the year 1786 is "Сказка о Игнатье-царевиче и о Суворе-невидимке мужичке", which we can find in the book "Лекарство от задумчивости и безсонницы, или Настоящия руския сказки", where the transcript reads "Пожалуй, поезжай, — ответил Сувор, — только не кушай винограду" yet again on [page 130](https://kp.rusneb.ru/item/reader/lekarstvo-ot-zadumchivosti-i-bezsonnicy-ili-nastoyashchiya-ruskiya-skazki) we find отвечать.

{{< figure src="otvetit3.png" align="center" >}}

Moving on to the 36th letter in "Почта Духов", 1789, the transcript again is given as "Это меня восхищает, — ответила она", but again we find [that it's supposed to be отвечала](https://viewer.rusneb.ru/ru/000199_000009_007894728?page=154).

{{< figure src="otvetit4.png" align="center" >}}

As I'm having fun with this, the second to last one: in 1790, Karamzin put his pen to the paper in Paris and although the НКРЯ tells us he wrote "Прошу ответить на прилагаемые вопросы, чем меня обяжете" in his "Письма рускаго путешественника", we can [see he did in fact use отвечать here](https://viewer.rusneb.ru/ru/000199_000009_005115112?page=332).

{{< figure src="otvetit5.png" align="center" >}}

And to finish off, in 1794 the book "Влюблённый дух, или Приключение дона Альвара" was published, and at this point I don't need to tell you anymore that although the corpus reports to us that it contains a single ответил, in the sentence "На что он ответил, что он последует закону естественному", this also [turns out to be the other one](https://viewer.rusneb.ru/ru/000199_000009_003340598?page=13).

{{< figure src="otvetit6.png" align="center" >}}

The first true attestation I can find dates to 1792, and it's in a play by the name of "Бобыль" (an old word for a landless peasant). The not entirely unamusing play can be found on [Wikisource](https://ru.wikisource.org/wiki/Бобыль_(Плавильщиков)), and a single appearance is made by ответить, which is finally corroborated by [the source material](https://viewer.rusneb.ru/ru/000199_000009_003337823?page=42). What's very interesting about this, is that the speech of the character saying it, as well as that of the others, is rife with vernacular, colloquialisms, and eye dialect. Other words used in the surrounding dialogue, like спрошать, ведашь ты, экий, хошь, слышь, ась, стакаться, etc... are nowadays indicated in the dictionary as dialectical, colloquial, or even labeled with the dreaded просторечное, so it stands to reason that ответить itself might have had such a connotation, which would explain its absence in other texts of its time.

{{< figure src="otvetit0.png" align="center" caption="One of the very first attestations of ответить">}}

## Dictionaries

So what do the dictionaries say? Sadly, Russian was codified somewhat late, and compared to its current literary weight it has quite a short philological tradition, so there aren't a great many sources. The 1789 Словарь Академии Российской as expected lists [отвечать](https://viewer.rusneb.ru/ru/000199_000009_004095555?page=587) and not ответить, but it doesn't tell us what aspect any verb is nor their aspect pairs. Though barely half a century later, in 1847, ответить was sufficiently acceptable for [Словарь церковнославянского и русского языка](https://viewer.rusneb.ru/ru/000199_000009_003825499?page=103) to include it without labels, referring to it as the perfective version of отвечать.

The only noteworthy mention I could find was in [Словарь Ушакова](https://dic.academic.ru/dic.nsf/ushakov/908566), where it is explicitly tagged as both imperfective and perfective, confirming its biaspectual nature.

## Etymology

What strikes me as particularly odd about this though, is that ответить and отвечать look like an archetypical aspect pair. You have a prefixed perfective verb, ответить, and from that you can derive a secondary imperfective отвечать with the -ать suffix and the expected consonant mutation, and that's certainly the correct synchronic analysis. So where does отвечать come from? Wiktionary leads us to the reconstructed [\*otъvěťati](https://en.wiktionary.org/wiki/Reconstruction:Proto-Slavic/otъvěťati) in Proto-Slavic, which it says comes from From \*otъvětiti + \*-ati, corresponding to the current synchronic analysis. This is confirmed by the 39th volume of the [Etymological Dictionary of Slavic Languages](https://etymolog.ruslang.ru/doc/essja39.pdf), though the few cognates отвечать seems to have do also appear to be either perfective or at most biaspectual. The problem I have with these etymologies is that they don't explain why ответить shows up so late, though the existence of a few descendants in other Slavic languages of course serves as evidence that \*otъvětiti might have existed (though it's possible the ideas presented here also apply to those languages, I do not have the linguistic knowledge to investigate this).

But there's also the unprefixed [\*věťati](https://en.wiktionary.org/wiki/Reconstruction:Proto-Slavic/věťati) (which just means "to speak"), ostensibly derived from an unattested \*větiti, which left behind some descendants in modern languages. There's supposedly even the expected reflex вечать in Russian, but I can't find a single attestation of it other than a line in Dal's dictionary mentioning it as occuring in the Pskov regional dialect. This is very helpful, because apart from \*otъvěťati, \*věťati also has two other prefixed derivations. There's the unremarkable \*privěťati, but more interestingly, [\*oběťati](https://en.wiktionary.org/wiki/Reconstruction:Proto-Slavic/oběťati), of which the descendants nowadays in other languages are *perfective*. Expect, of course, in Russian, where the loaned обещать is one of the rare biaspectual verbs outside of the -овать class. The innovation пообещать only arose around the 1840's, if the [НКРЯ](https://ruscorpora.ru/s/dPLBw) is to be believed.

## Conclusion

As my knowledge of Slavic linguistics in general and the development of the aspectual system in Russian in particular is sadly not very advanced, the conclusion I'm drawing here should not be taken to be too authoritative.

However, my hypothesis is that \*otъvěťati was either formed as a prefixed version of \*věťati, or reanalyzed as such later, which meant отвечать was a perfective or possibly biaspectual verb. In Russian it certainly gained a biaspectual meaning in the past tense at some point. Its expected perfective counterpart, \*otъvětiti, did not leave any descendants in Russian, as evidenced by the complete lack of attestation up to the 18th century, at which point ответить was innovated by simply putting an infinitive ending on the noun ответ. This was possibly much to the chagrin of language purists at the time given the context of one early attestation, and it does take a while for ответить to show up in literature. At this point a transitory period began, during which writers experimented more and more with this new word, until nearly a century later the couple отвечать and ответить finally became a normal aspectual pair.
