# **Semestrálne zadanie: Komunikácia s využitím UDP protokolu** ([EN](en.md))

## **Cieľ zadania**

Navrhnúť a implementovať P2P(PEER-TO-PEER) aplikáciu využívajúcu vlastný protokol postavený nad UDP (User Datagram Protocol) v transportnej vrstve sieťového modelu TCP/IP. Aplikácia bude umožňovať komunikáciu dvoch účastníkov v lokálnej Ethernet sieti, vrátane výmeny textu a prenosu ľubovoľných súborov medzi počítačmi (uzlami). Oba uzly budú fungovať súčasne ako prijímač aj odosielateľ.

Zadanie pozostáva z dvoch častí: teoretickej a praktickej. V teoretickej časti navrhnete vlastný protokol nad UDP. V praktickej časti následne implementujete a predvediete jeho funkčnosť pri komunikácii medzi dvoma uzlami, pričom zdôrazníte úpravy vykonané počas implementácie v porovnaní s pôvodným návrhom.

## **Teoretická časť - návrh protokolu**

Požiadavky na návrh protokolu sú nasledovné:

- nadviazanie spojenia medzi dvoma stranami a dohodnutie si parametrov spojenia (ako napr. TCP handshake),
- umožniť poslať dáta po fragmentoch s veľkosťou zadanou zo vstupu od používateľa,
- overenie integrity poslanej správy na strane príjemcu,
- vedieť znova poslať stratenú alebo poškodenú správu,
- zabezpečenie vzájomnej kontroly medzi uzlami, či je účastník na druhej strane spojenia stále aktívny,
- umožniť úmyselne vytvoriť chybu v niektorej z prenášaných správ s cieľom simulovať poškodené dáta,
- ostatné veci, ktoré považujete za dôležité.

Výstupom tejto časti je dokumentácia, v ktorej predstavíte návrh svojho protokolu a plán implementácie jednotlivých mechanizmov. Následne túto dokumentáciu prediskutujete so svojím cvičiacim počas kontrolného bodu, kde získate pripomienky na zapracovanie do praktickej časti. Dokumentácia musí obsahovať nasledujúce časti:

- štruktúru hlavičiek vášho protokolu,
- opis metódy na kontrolu integrity prenesenej správy (napr. ak si zvolíte CRC16, tak ho opíšete ako sa vypočíta, rovnako postupovať pri iných metódach),
- opis metódy na zabezpečenie spoľahlivého prenosu dát (ARQ),
- opis metódy na udržanie spojenia (Keep-Alive),
- diagramy opisujúce predpokladané správanie vášho uzla/uzlov, použite UML diagramy ako napr. sekvenčný, aktivity a stavový (používajte vhodné nástroje, ako napr. [miro](https://miro.com) alebo [draw.io](https://draw.io/), nie skicár).

Pri hodnotení sa zohľadňuje prehľadnosť a zrozumiteľnosť odovzdanej dokumentácie ako aj kvalita navrhovaného riešenia. Dokumentácia musí obsahovať aj formálne časti ako úvodnú stranu, obsah, číslovanie strán atď. Konkrétnejšie informácie o jednotlivých požiadavkách na program nájdete v praktickej časti.

## **Praktická časť - implementácia p2p komunikátora + doimplementácie**

V tejto časti implementujte P2P komunikátor podľa vášho návrhu, pričom zohľadnite pripomienky z kontrolného bodu. Vaše hodnotenie bude založené na splnení nasledujúcich úloh:

#### 1. Vytvorenie a overenie základnej funkčnosti spojenia

Vytvoriť P2P spojenie medzi dvoma uzlami, dohodnutie si parametrov spojenia a overiť funkčnosť spojenia odoslaním textu v oboch smeroch komunikácie. Používateľské rozhranie na každom uzle musí umožňovať nastaviť nasledovné parametre:

- port, na ktorom uzol počúva,
- IP adresu a port na cieľový uzol, na ktorý sa bude posielať text/súbor.

Prezentovať úspešný prenos prostredníctvom Wireshark(WS) tak, aby ste vedeli identifikovať správy patriace vášmu protokolu v správnom poradí a vedeli ukázať jednotlivé polia vášho protokolu.

#### 2. Prenos textu/súboru - fragmentácia

V počítačových sieťach fragmentácia označuje proces rozdelenia veľkých dátových blokov na menšie fragmenty pred ich odoslaním cez sieť. Tento proces je často nevyhnutný na zabezpečenie optimálneho prenosu dát, či už kvôli vyťaženosti siete alebo kvôli vlastnostiam daného prenosového média.

Vašou úlohou je implementovať proces fragmentácie s nasledujúcimi vlastnosťami. Používateľské rozhranie uzla musí umožniť dynamické nastavenie veľkosti fragmentu počas behu programu (medzi jednotlivými prenosmi). Pri prenose textu/súboru môžu nastať dva scenáre, ktoré je potrebné implementovať a úspešne demonštrovať pre získanie bodov:

- _prenos bez fragmentácie_ - veľkosť textu/súboru je menšia alebo rovná nastavenej maximálnej veľkosti fragmentu, vtedy sa text/súbor prenesie ako jeden celok bez zbytočného paddingu.
- _prenos s fragmentáciou_ - veľkosť textu/súboru je väčšia ako nastavená maximálna veľkosť fragmentu, odosielajúci uzol rozdelí súbor na menšie fragmenty, ktoré pošle samostatne.

Nastavenie veľkosti fragmentu musí byť obmedzené tak, aby používateľ nemohol zvoliť veľkosť, ktorá by spôsobila opätovnú fragmentáciu fragmentov na linkovej vrstve. Režijné správy, ktoré slúžia na riadenie komunikácie, sa prenášajú vždy bez fragmentácie. Na uzle je požadované, aby si používateľ mohol vybrať súbor na odoslanie z ktoréhokoľvek priečinka v súborovom systéme zariadenia.

V prípade prenosu s fragmentáciou je potrebné na jednotlivých uzloch vypísať nasledujúce informácie:

- _Odosielajúci uzol_ - zobrazí názov posielaného súboru (pri textovej správe sa tento údaj ignoruje), veľkosť textu/súboru, počet odoslaných fragmentov a veľkosť fragmentu. Ak má posledný fragment inú veľkosť než ostatné fragmenty, je potrebné tiež vypísať aj veľkosť posledného fragmentu.
- _Prijímajúci uzol_ - zobrazí správu o prijatí každého fragmentu s poradovým číslom a informáciou o tom, či bol fragment prenesený bez chýb. Po prijatí celého textu/súboru uzol zobrazí informáciu o úspešnom prijatí, čas trvania prenosu, veľkosť textu/súboru a absolútnu cestu, kam bol súbor uložený.

#### 3. Prenos textu/súboru - poškodenie a strata dát

V sieťovej komunikácii občas dochádza k strate alebo poškodeniu dát počas prenosu. Z tohto dôvodu musí váš P2P komunikátor zaviesť mechanizmus na kontrolu chýb a zabezpečiť opätovné vyžiadanie stratených alebo poškodených fragmentov dát. Komunikátor musí podporovať pozitívne (ACK) aj negatívne (NACK) potvrdenia správ.

Vašou úlohou je vybrať a implementovať jednu metódu pre spoľahlivý prenos dát, tzv. Automatic Repeat Request (ARQ). Jednotlivé metódy sú hodnotené podľa ich zložitosti. Na výber máte tieto ARQ metódy:

- Stop & wait (S&W)
- Go Back-N (GBN)
- Selective Repeat (SR)
- Vylepšená Go Back-N/Selective Repeat (dynamický sliding window, optimalizácia počtu ACK správ, ... )

Základnou požiadavkou na splnenie tejto úlohy je úspešne preniesť text/súbor aj pri simulácii chýb prenosu, pričom musí byť korektne implementovaná detekcia chýb a ARQ metóda. Podrobnejšie informácie o jednotlivých metódach nájdete v časti [Literatúra](#literatúra).

#### 4. Udržanie spojenia (Keep-Alive)

Technika používaná v sieťových protokoloch na udržanie aktívneho spojenia medzi dvoma uzlami počas ich nečinnosti. Jej cieľom je minimalizovať počet nových nadväzovaných spojení, čím sa znižuje latencia spojená s vytváraním každého spojenia. Zároveň umožňuje monitorovať stav spojenia a overovať, či sú obe strany aktívne, čo zlepšuje spoľahlivosť spojenia a umožňuje rýchlejšie reagovať v prípade výpadku jedného z účastníkov spojenia.

Implementujte v rámci komunikátora vlastný mechanizmus Keep-Alive s nasledujúcimi vlastnosťami:

- použite vlastné signalizačné správy,
- monitorovanie spojenia začnite hneď po jeho nadviazaní,
- posielajte heartbeat správy každých 5 sekúnd, ak medzi zariadeniami neprebieha žiadna aktivita,
- ak jedna zo strán spojenia nedostane odpoveď na 3 po sebe odoslané heartbeat správy, považuje spojenie za ukončené,
- uzol, ktorý vyhodnotí spojenie ako ukončené/prerušené, zobrazí informáciu o ukončení spojenia v používateľskom rozhraní,
- komunikátor by mal byť schopný reagovať, ak počas prenosu textu/súboru dôjde k náhlemu výpadku spojenia (napr. odpojenie sieťového kábla), vtedy overí stav druhej strany poslaním heartbeat správ a na základe výsledku vykoná jednu z nasledujúcich akcií:
  - ak na všetky odoslané heartbeat správy nedostane od druhej strany odpoveď, prehlási spojenie za ukončené a vypíše, že prenos textu/súboru zlyhal,
  - ak sa spojenie podarí obnoviť, teda aspoň na poslednú odoslanú heartbeat správu príde odpoveď od druhej strany, pokúsi sa pokračovať v prenose textu/súboru, vrátane opätovného odoslania fragmentov, ktoré neboli úspešne doručené počas výpadku spojenia.

#### 5. Lua skript vo Wiresharku

Vytvorte Lua skript, ktorý bude analyzovať váš protokol nad UDP a zobrazí jeho obsah vo WS. Skript rozpozná váš vlastný protokol na základe portu, dekóduje jednotlivé polia protokolu a zobrazí ich hodnoty priamo vo výstupe analýzy WS. Farebne odlíšte správy, ktoré prenášajú užitočné dáta, od režijných správ. Príklad nájdete na tomto [odkaze](https://wiki.wireshark.org/Lua/Examples).

#### 6. Finálna dokumentácia

Študent dokumentáciu z návrhu protokolu doplní o nasledujúce kapitoly:

- zmeny vykonané počas implementácie oproti návrhu riešenia,
- doplní diagramy, ak mú chýbali v kontrolnom bode,
- uvedie aspoň 1 komplexnú ukážku testovacieho scenára so screenami z WS(otvorenie spojenia, posielanie bez chyby prenosu, posielanie s chybou prenosu a následnou opravou, zatvorenie spojenia),
- zhodnotenie a použité zdroje.

Hodnotí sa prehľadnosť a zrozumiteľnosť odovzdanej dokumentácie ako aj kvalita pracovania celového riešenia.

#### 7. Kvalita spracovania

Hodnotí sa celková kvalita implementácie komunikátora, čo môže zahŕňať nasledujúce aspekty:

- prívetivosť používateľského rozhrania v command line(CLI)(práca s ním, prehľadnosť výpisov atď.), áno, aj v CLI je možné vytvoriť prehľadné a užívateľsky príjemné rozhranie programu,
- rýchlosť prenosu,
- stabilita komunikátora (frekvencia pádu komunikátora pri prezentácii),
- schopnosť adaptácie komunikátora na rôzne používateľské požiadavky bez reštartu.

## Akceptačné podmienky

Podmienky, ktoré je potrebné splniť pri finálnom odovzdaní zadania, aby vaše zadanie bolo akceptované a mohlo byť hodnotené. Nesplnenie aj jednej z týchto podmienok má za následok hodnotenie 0 bodmi. V prípade P2P sú uzly rovnocenné, a preto podmienky musia spĺňať oba uzly.

1.  Program musí byť implementovaný v jednom z nasledujúcich programovacích jazykov: C, C++, C#, Python a Rust(len, ak cvičiaci odsúhlasí).
2.  Používateľ musí vedieť nastaviť na uzle port na počúvanie.
3.  Používateľ musí vedieť nastaviť cieľovú IP adresu a port.
4.  Používateľ musí mať možnosť zvoliť si max. veľkosť fragmentu na uzle a meniť ju dynamicky počas behu programu pred poslaním textu/súboru (neplatí pre režijné správy).
5.  Uzol musí vedieť zobraziť nasledujúce informácie o prijatom/odoslanom texte/súbore:

    - názov a absolútnu cestu k súboru na danom uzle,
    - veľkosť a počet fragmentov vrátane celkovej veľkosti (posledný fragment môže mať odlišnú veľkosť ako predchádzajúce fragmenty).

6.  Simulovať chybu prenosu odoslaním minimálne 1 chybného fragmentu pri prenose textu a súboru (do dátovej časti fragmentu alebo do checksum je cielene vnesená chyba, to znamená, že prijímajúca strana deteguje chybu pri prenose).
7.  Prijímajúca strana musí byť schopná oznámiť odosielateľovi správne aj nesprávne doručenie fragmentov. Pri nesprávnom doručení fragmentu vyžiada znovu poslať poškodené dáta.
8.  Možnosť odoslať 2MB súbor do 60 sekúnd a uložiť ho na prijímacom uzle ako rovnaký súbor.
9.  Možnosť nastaviť na uzle miesto, kam sa súbor uloží po prijatí.

Pomocou nasledujúcej tabuľky si môžete overiť, či pred odovzdaním zadania spĺňate všetky akceptačné požiadavky. Zadanie odovzdáte iba v prípade, ak máte 'YES' 9/9.

<table style="text-align: center;">
    <thead>
    <tr>
        <th>Podmienka</th>
        <th>Stav (YES/NO)</th>
    </tr>
    </thead>
    <tbody>
        <tr>
            <td style="text-align: center;">1</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">2</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">3</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">4</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">5</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">6</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">7</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">8</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">9</td>
            <td></td>
        </tr>
    </tbody>
</table>

## Fázy odovzdania a harmonogram

### Kontrolný bod (KB)

Odovzdáva sa jeden .ZIP súbor s názvom <ais_login>.zip napr. xpacket.zip, ktorý obsahuje tieto súbory:

- Dokumentáciu s návrhom vášho vlastného protokolu vo formáte **pdf**.
- Aplikácia s implementáciou úlohy '[Vytvorenie a overenie základnej funkčnosti spojenia ](#1-vytvorenie-a-overenie-základnej-funkčnosti-spojenia)'

#### Termín odovzdania do AIS: 21.10.2024 23:59

### Finálne odovzdanie (FO)

Odovzdáva sa jeden .ZIP súbor s názvom <ais_login>.zip napr. xpacket.zip, ktorý obsahuje tieto súbory:

- Finálna dokumentácia
- Finálna aplikácia bez skrytých priečinkov (začínajú znakom "." napr. .vscode, .config, .idea ...)

#### Termín odovzdania do AIS: 25.11.2024 23:59

### Doplnenie funkčnosti (doimplementácia) počas cvičenia (DI)

Študent implementuje pridelenú úlohu do odovzdaného komunikátora na cvičení a predvedenie jej funkčnosť bez zlyhania alebo chybových hlášok.

#### Termín konania: na cvičeniach od 26.11.2024 do 28.11.2024

## Hodnotiaca tabuľka

<table>
<thead>
  <tr>
    <th>Fáza<br>odovzdania</th>
    <th>Číslo<br>úlohy</th>
    <th>Názov úlohy</th>
    <th style="text-align: center;">Max<br>bodov</th>
    <th style="text-align: center;">Min<br>bodov</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>KB</td>
    <td></td>
    <td><b>Návrh programu a komunikačného protokolu</b>
    <td style="text-align: center;">3</td>
    <td style="text-align: center;" rowspan="2">3</td>
  </td>
  </tr>
  <tr>
    <td>KB</td>
    <td>1</td>
    <td><b>Vytvorenie a overenie základnej funkčnosti spojenia</b>
    <td style="text-align: center;">3</td>
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>2</td>
    <td><b>Prenos textu/súboru - fragmentácia</b>
    <td style="text-align: center;">4</td>
    <td style="text-align: center;" rowspan="6">9</td>
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>3</td>
    <td><b>Prenos textu/súboru - poškodenie a strata dát</b>
    <td>6 - Vylepšená GBN/SR<br>
    5 - SR<br>
    3 - GBN<br>
    1 - S&W</td>
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>4</td>
    <td><b>Udržanie spojenia (Keep-Alive)</b>
    <td style="text-align: center;">3</td>
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>5</td>
    <td><b>Lua skript vo Wiresharku</b>
    <td style="text-align: center;">2</td> 
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>6</td>
    <td><b>Finálna dokumentácia</b>
    <td style="text-align: center;">2</td> 
  </td>
  </tr>
  <tr>
    <td>FO</td>
    <td>7</td>
    <td><b>Kvalita spracovania</b>
    <td style="text-align: center;">1</td> 
  </td>
  </tr>
  <tr>
    <td>DI</td>
    <td></td>
    <td><b>Doplnená funkčnosť (doimplementácia) priamo na cvičení.</b>
    <td style="text-align: center;">1</td>
    <td style="text-align: center;">0,5</td>
  </td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td><b>Spolu</b>
    <td style="text-align: center;">25</td>
    <td></td>
  </td>
  </tr>
</tbody>
</table>

## Literatúra

1. [Stop & Wait, Sliding Window Protocol](https://www.youtube.com/watch?v=LnbvhoxHn8M&ab_channel=NesoAcademy)
2. [Selective repeat](https://www.youtube.com/watch?v=WfIhQ3o2xow&t=5s&ab_channel=NesoAcademy)
3. [Go Back-N](https://www.youtube.com/watch?v=QD3oCelHJ20&ab_channel=NesoAcademy)
