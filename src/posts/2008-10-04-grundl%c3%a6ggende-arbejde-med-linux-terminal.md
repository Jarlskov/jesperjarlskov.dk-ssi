---
title: 'Grundlæggende arbejde med Linux'' terminal'
date: 2008-10-04
---

Selvom Ubuntu er et Linuxbaseret styresystem, kan man, i modsætning til hvad mange tror, sagtens klare sig, uden nogensinde at møde "Den sorte skærm med den lille blinkende pil", i folkemunde kendt som terminalen. Men man har stadig mulighed for at styre systemet fra sin terminal, og jeg mener at der er rigtig mange gode grunde til at lære terminalen at kende, i det mindste overfladisk.
Terminalen er et rigtigt kraftigt værktøj, det kan gøre computerbrugerens hverdag meget lettere ved at automatisere en masse af det arbejde man ellers skulle lave manuelt. Som de skriver på <a href="http://www.linuxcommand.org/">linuxcommand.org</a>:
<blockquote>
Graphical user interfaces (GUIs) are helpful for many tasks, but they are not good for all tasks. I have long felt that most computers today do not use electricity. They instead seem to be powered by the "pumping" motion of the mouse! Computers were supposed to free us from manual labor, but how many times have you performed some task you felt sure the computer should be able to do? You ended up doing the work by tediously working the mouse. Pointing and clicking, pointing and clicking.
</blockquote>

Denne første artikel vil omhandle grundlæggende brug af terminalen, hvad det er for en, hvordan den virker og hvordan den ser ud. Derudover vil den komme ind på nogle grundlæggende ting omkring hvordan man navigerer rundt i filer og mapper.
Linjer i artiklen skrevet som
<code>dette</code>
er kommandoer, og kan indtastes direkte i terminalen som det står.

Vi starter med nogle grundlæggende ting omkring terminalen og hvordan den bruges.
[caption id="attachment_59" align="alignnone" width="300" caption="screenshot af konsole vindue"][/caption]
Her ses et screenshot af KDE's terminalprogram Konsole, din terminal kan se lidt anderledes ud end dette alt efter hvilket program du bruger, men de grundlæggende ting er præcis det samme.
Selve terminaldelen er teksten på den sorte baggrund. Der står nogle oplysninger om hvem og hvad.
<blockquote>
jarlen@kk:~$
</blockquote>
først "jarlen", dette fortæller "hvem". Det er brugernavnet for den bruger der er logget på maskinen. @ udtales "at", og benyttes på samme måde som i e-mailadresser, til at forklare hvilken maskine der er tale om. Det er altså brugeren jarlen på maskinen "kk", på samme måde som jesper@jarlskov.dk er brugeren jesper på adressen jarlskov.dk.
Det næste "~" forklarer hvilken mappe på maskinen terminalen pt. er i. ~ er en forkortelse for brugerens private mappe på computeren. Man kan altså altid komme tilbage til sin private mappe med kommandoen 
<code>cd ~</code>
cd vil blive gennemgået om lidt.
Hvis man af en eller anden grund har brug for den fulde sti til den mappe man er i, bruges kommandoen:
<code>pwd</code>
I mit tilfælde får jeg ouputtet:
<blockquote>
jarlen@kk:~$ pwd
/home/jarlen
jarlen@kk:~$
</blockquote>
da /home/jarlen er den fulde sti til min private mappe.
Men nu til nogen af de grundlæggende kommandoer.
<h2>man</h2>
man er en forkortelse for manual, og giver en beskrivelse af de forskellige kommandoer. Det er en kommando der er meget god som opslagsværk hvis man skal finde ud af hvordan en kommando bruges, men det er nok ikke en almindelige mennesker vil bruge så fantastisk meget, da disse manualer er utroligt kedelige. :-)
Kommandoen bruges med man, efterfulgt af den kommando man skal bruge manualen for, f.eks. for ls:
<code>man ls</code>
For nogle programmer findes der også infofiler, de svarer til man-filerne, men er for det meste lidt kortere og mere overskuelige.

<h2>cd</h2>
cd står for change directory, og bruges til at navigere terminalen til en anden mappe.
<code>cd Desktop</code>
tager mig f.eks. fra min brugermappe til mappen Desktop. Navigering i mapper foregår på samme måde som i den almindelige filbrowser, og jeg kan altså kun bruge <code>cd Desktop</code> når jeg er i min homemappe, da det er her Desktop-mappen er placeret. Man kan også gå direkte til undermapper, f.eks.
<code>cd Desktop/mappe1</code> skifter til mappe1 i Desktop-mappen. Det skal bemærkes at navigering i Linux er case-sensitivt. Der er altså forskel på store og små bogstaver. "Desktop" og "desktop" er altså to forskellige mapper. En rigtig lækker feature her er at de fleste terminaler understøtter tab-afslutning. Det betyder at hvis du skriver en del af mappenavnet og trykker på tab, vil terminalen selv færdiggøre navnet. Så <code>cd De [tab]</code> bliver altså automatisk til <code>cd Desktop</code> hvis du ikke har andre mappenavne der starter med "De". Hvis der er mere end én mulighed kan man trykke tab to gange, så vil terminalen vise alle muligheder.
Der findes to specialtilfælde her. "." og "..", disse to "mapper" findes i alle mapper på systemet. "." betyder "current directory", altså nuværende mappe. Det er ikke noget man normalt vil få brug for, da <code>cd .</code> bare bringer dig til den mappe du allerede er i, der sker altså ikke nogen mærkbar ændring. ".." derimod betyder "parent directory", altså "forældre mappe". Dette henviser til den mappe der ligger niveauet højere i hierarkiet end hvor terminalen pt. befinder sig.
Så når:
<code>cd Desktop</code>
har taget terminalen fra din homemappe ind i Desktop-mappen, vil:
<code>cd ..</code>
tage dig et niveau op, altså fra Desktop-mappen tilbage til din home-mappe.

<h2>ls</h2>
ls står for list. Kommandoen giver en liste over indholdet af den mappe terminalen er i. Hvis du bruger en terminal med farver slået til vil filer og mapper blive vist i hver deres farve. Kommandoen som den står vil dog normalt ikke vise alt indholdet. Dette skyldes bl.a. at Linux behandler alle filer og mapper startende med "." som skjulte, og de vil derfor ikke blive vist som standard.
Her får du brug for de såkaldte "flag". Det er ekstra indstillinger du kan give programmerne, for at få dem til at fungere anderledes. Disse flag bruges med "-" efterfulgt af en bogstav. Det er her man-siderne bliver gode at have, da du her kan finde en liste over alle flag for en given kommando, samt en beskrivelse af hvordan de bruges.
Hvis du kigger i <code>man ls</code> kan du bl.a. se at ls kan benyttes med flaget -a, der vil vise disse skjulte filer og mapper.
<code>ls -a</code> vil altså vise alle filer og mapper der findes i den mappe terminalen er i, inkl. de skjulte. -l (lille L) flaget giver en listevisning, med flere detaljer om hver fil og mappe.
Flag kan kombineres, så:
<code>ls -a -l</code> vil derfor give en listevisning indeholdende bl.a. rettigheder og filstørrelse på alle filer og mapper i mappen, inkl. skjulte.

<h2>locate</h2>
locate er et søgeprogram der bruges til at finde filer på maskinen. Programmet indeholder en database over filer på computeren og arbejder derfor utroligt hurtigt. Dog er det nødvendigt selv at sørge for at denne database bliver vedligeholdt. Dette er ikke så slemt som det lyder, da det bare kræver at du engang imellem kører kommandoen:
<code>updatedb</code>
det synes jeg personligt er en meget lille pris at betale for så hurtig og effektiv søgning :-).
Køres kommandoen som den står vil den dog give fejlen:
<code>updatedb: fatal error: You are not authorized to create a default slocate database!</code> da der kræves administratorrettigheder. Dette klares vha. kommandoen <code>sudo</code>.

<h2>sudo</h2>
sudo er en forkortelse for Super User DO, og bruges til at give administratorrettigheder til det program du ønsker at køre. Hvis vi tager eksemplet fra før med updatedb giver man administratorrettigheder til updatedb-programmet vha. sudo således:
<code>sudo updatedb</code>. Terminalen vil herefter spørge efter din administratorkode. Sudo kan kun bruges af medlemmer af "admin"-gruppen på maskinen, den bruger der oprettes under installationen er automatisk med i denne gruppe, så hvis du kun har en bruger på maskinen bruges samme kode som når du logger på. Dette vil nok være tilfældet de fleste steder.
<b>HUSK!</b> når du skriver dit password i terminalen vil der ikke komme noget output. Du kan altså hverken se hvad eller hvor meget du har skrevet som password indtil videre, dette kan give nogle problemer engang imellem hvis man ikke lige er opmærksom på det.

<h2>which</h2>
Lidt i samme stil som locate findes programmet which. Det er også et søgeprogram, men i stedet for at finde filer som locate, bruges det til at finde ud af hvor dine programmer bliver installeret.
Jeg har f.eks. installeret programmet firefox som internetbrowser, så hvis jeg bruger kommandoen:
<code>which firefox</code>
får jeg outputtet:
<blockquote>/usr/bin/firefox</blockquote>
Vær opmærksom på at som sædvanlig i linux er inputtet case-sensitivt.
<code>which Firefox</code> vil altså ikke give noget resultat.

<h2>mv</h2>
Nu da du har lært at finde lidt rundt i dit filsystem og finde de filer og mapper du skal bruge, kunne det også være relevant rent faktisk at kunne gøre noget ved disse, så jeg vil nu gennemgå et par grundlæggende muligheder. Den første er mv. mv er en forkortelse for move, og bruges altså til at flytte filer og mapper rundt.
<code>mv fil mappe</code>
tager filen ved navn "fil" og ligger den i mappen "mappe", stille og roligt. :-) Husk at du også her kan bruge tab-completion som nævnt ovenfor ved cd-kommandoen. Både til når du skriver filnavne og mappenavne. Hvis den mappe der står i andet argument ikke eksisterer vil mv behandle det som et filnavn du gerne vil gemme filen som. Det betyder at der ikke findes ingen kommando til at skifte navn på filer i linux, man bruger simpelthen mv kommandoen.
<code>mv gammeltNavn nytNavn</code>
ændrer navnet på filen "gammeltNavn" til "nytNavn" med mindre der findes en mappe ved navn "nytNavn" i den mappe terminalen er i.

<h2>cp</h2>
cp er en forkortelse for copy, og bruges til at kopiere filer og mapper. Grundlæggende bruges den på samme måde som mv.
<code>cp fil mappe</code>
kopierer filen ved navn "fil" ind i mappen ved navn "mappe". Hvis du gerne vil kopiere mapper får du dog brug for flag, som jeg omtalte tidligere. Rigtig mange programmer kan bruge flaget "-r", hvilket vil kalde programmet rekursivt, den samme kommando vil altså blive udført for alle filer og mapper i den mappe du arbejder med.
<code>cp -r mappe1 mappe2</code>
vil kopiere mappe1 over i mappe2, flaget -r sørger så for at alt indholdet af mappe1 bliver taget med, så du ikke ender med en tom mappe2.

<h2>rm</h2>
rm er en forkortelse for remove, og det er sådan set hvad den gør. Den bruges til at slette filer og mapper. Den fungerer lidt på samme måde som mv og cp.
<code>rm fil</code> sletter filen ved navn "fil".
På samme måde som cp arbejder rm som standard kun med den fil eller mappe som du specificerer direkte. Hvis du skal slette en mappe med indhold skal du altså igen bruge -r flaget.
<code>rm -r mappe</code> sletter altså mappen med navnet "mappe" og alt indhold.

<h2>mkdir</h2>
mkdir er en forkortelse for "make directory". Den bruges til at oprette nye mapper.
<code>mkdir nyMappe</code>
opretter en mappe med navnet "nyMappe".

<h2>Afsluttende ord</h2>
Det var en gennemgang af meget grundlæggende brug af terminalen i Linux. Jeg vil i senere indlæg komme ind på nogle lidt mere avancerede funktioner, og altså nogle steder hvor terminalen rigtig vil kunne gøre dit arbejde lettere og hurtigere. :-)