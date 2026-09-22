## H5 Binääri tässä, missä koodit? | Blendi Thaqi 21/09/2026

## Ympäristö

**OS**: Kali GNU/Linux Rolling

**Browser**: Mozilla Firefox 140.11.0esr (64-bit)

**Hardware**: Virtualbox memory used 8 GB

**Processor**: AMD Ryzen 7 7800X3D | 4 cores used

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

**Disk**: 40 GB

**Network**: NAT

## Valmistelu Kalissa

1. Aloitetaan ensin lataamalla `Lab` ZIP tiedostot Moodlesta, puretaan tiedostot ja siirretään ne uuteen hakemistoon, josta voimme helpommin navigoida tehtävien välillä. 

Siirrytään työpöydälle ja puretaan labs.zip tiedosto (jossa on lab0-lab4.zip tiedostot):

```bash
cd Desktop
unzip labs.zip
```

<img width="251" height="197" alt="VirtualBoxVM_xRq8mso1bk" src="https://github.com/user-attachments/assets/b6212fa6-1ccd-4a01-b8d9-3044fca68072" />

Luodaan uusi hakemisto ja siirretään tiedostot sinne:

```bash
mkdir H5
mv lab0.zip lab1.zip lab2.zip lab3.zip lab4.zip H5/
```

<img width="455" height="104" alt="VirtualBoxVM_VRmtgZzwgn" src="https://github.com/user-attachments/assets/e4362f2b-9f37-48c9-8435-27d69a20ad57" />

Siirrytään uuteen hakemistoon ja puretaan loput tiedostot:

```bash
cd H5
unzip -o 'lab*.zip'
```

<img width="281" height="291" alt="VirtualBoxVM_IL1uY69qzR" src="https://github.com/user-attachments/assets/2bfab84e-ee99-4805-9103-505d5008a130" />

2. Jatketaan asentamalla `cgdb` joka on GDB:n käyttöliittymä, jossa ohjelman lähdekoodi ja GDB:n komentorivi näkyvät samassa näkymässä.
> Käytän itse tätä mielummin sillä sitä on helpompi käyttää, sekä näyttää selkeämmältä.

```bash
sudo apt update
sudo apt install cgdb
```

<img width="600" height="196" alt="VirtualBoxVM_zNpJ7gRBfz" src="https://github.com/user-attachments/assets/560821e9-e9f9-4bfe-a81f-3f590c3bb8b8" />

<img width="377" height="223" alt="VirtualBoxVM_N4RZI5Dpez" src="https://github.com/user-attachments/assets/ead837b3-cb5f-41eb-9a00-019862b72127" />

Tarkistetaan vielä saimmeko `cgdb`:n ladattua oikein, ja kuten näkyykin saimme sen ladattua oikein:

```bash
cgdb --version
```

<img width="600" height="124" alt="VirtualBoxVM_3KdOHhLmgs" src="https://github.com/user-attachments/assets/994ad50c-e6c1-49d2-9c3d-6d2f06a671b5" />

## Lab 0: Debuggerin käyttö

Ennen kuin aloitamme debuggerin kanssa, kokeillaan tehtävän ajamista ja tutkitaan mitä oikein tapahtuu:

```bash
./buggy_program
```

<img width="313" height="140" alt="VirtualBoxVM_6yCYx9A4AV" src="https://github.com/user-attachments/assets/34de0f78-6818-455e-bb7c-f8d09d37268f" />

Kun ohjelma on ajettu, voimme nyt huomata että se tulostaa yhden alkion liikaa. Taulukossa pitäisi näkyä alkiot `0-4`, mutta ohjelma tulostaa kuitenkin vielä `Element 5: 0`

4. Jatketaan käynnistämällä debugger (`cgdb`) ja tutkitaan ohjelmaa tarkemmin:

```bash
cgdb ./buggy_program
```

<img width="313" height="140" alt="VirtualBoxVM_6yCYx9A4AV" src="https://github.com/user-attachments/assets/ec0ace96-03a1-445c-9022-c4da4bd21ce0" />

> Aluksi työpöydällä pitäisi näkyä ns. "Welcome screen" jonka jälkeen `Enter` napin painamisen jälkeen, käyttöliittymmään tulee näkyviin ylhäälle lähdekoodi ja alhaalle GDB:n komentorivi.

<img width="600" height="457" alt="VirtualBoxVM_n7rQRTgId6" src="https://github.com/user-attachments/assets/fb18c0d2-6cf8-46c3-8723-ad60fec5c864" />

<img width="600" height="457" alt="VirtualBoxVM_mRdUph58ue" src="https://github.com/user-attachments/assets/f8e069ac-17f7-41d1-b2ff-f239953df021" />

5. Jatketaan etsimällä virheen aiheuttavan kohdan debuggerin avulla, normaalissa ajossa ohjelma käy taulukkoa läpi aivan liian pitkälle joten voimme käyttää `break` komentoa siihen funktioon (`buggy_function`) jossa tämä tämä tekee virheen. Käynnistetään tämän jälkeen ohjelma ja katsotaan mitä saamme siitä:
> `break` komento lisää "breakpointin" joka pysäyttää ohjelman halutussa kohdassa

```bash
break buggy_function
run
```

<img width="600" height="454" alt="VirtualBoxVM_ZOeL7Ka04M" src="https://github.com/user-attachments/assets/84d49a99-72a6-41a4-b3a9-c10b5fdf4916" />

Okei, jatketaan vielä tulostamalla taulukon koko:

```bash
print size
```

<img width="143" height="41" alt="VirtualBoxVM_7efV0ykbdj" src="https://github.com/user-attachments/assets/9b51794e-6c12-4307-8578-5d351da50bdb" />

Tästä näkyy että taulukossa on viisi alkiota (koska taulukon ensimmäinen indeksi on 0 ja viimeinen taas on 4)

Yritetään vielä selvittää missä kohtaa ohjelma menee liian pitkälle, kokeillaan uutta komentoa `next` tarkistetaan myös samalla `i` muuttuja:
> `next` komento suorittaa seuraavan ohjelmarivin ja pysyy myös samassa nykyisessä funktiossa

```bash
next
print i
```

<img width="449" height="70" alt="VirtualBoxVM_pN0DUtJgwX" src="https://github.com/user-attachments/assets/6590de94-49bf-4ad2-956b-4decf85f3050" />

Jatkoin tässä samalla tavalla, kunnes sain tuon `i` muuttujan saavuttamaan arvon `5` sillä siitä pystymme huomaamaan että ohjelma menee yhden alkion liian pitkälle, vaikka taulukon viimeinen oikea indeksi on oikeasti **4**:
> Kaksi kertaa `next` jotta se pystyy mennä takaisin `for ()` kohtaan!

```bash
next
next
print i
```

<img width="458" height="137" alt="VirtualBoxVM_1qQzrve4WM" src="https://github.com/user-attachments/assets/ad25c248-621b-4a89-97a3-a8d0875932e9" />

Voimme tästä päätellä että ongelmana on silmukan ehto, siellä näkyy `<=` joka hyväksyy myös tuon `i = 5`, joten korjataan se vaihtamalla se tähän `<`:

- Poistutaan ensin cgdb:stä: `q` tai `quit`
- Avataan lähdekoodi: `nano buggy_program.c`


<img width="600" height="224" alt="VirtualBoxVM_ONmpZag19x" src="https://github.com/user-attachments/assets/f5cbaa89-475a-41ef-802d-7d6f405497cc" />

- Tehdään muutokset ja tallennetaan ne: **Ctrl + O** + **Ctrl + X**

<img width="600" height="58" alt="VirtualBoxVM_o0w8OdPpV7" src="https://github.com/user-attachments/assets/6090ca0b-95fe-4fbd-ae2b-3c1dfc15708b" />

- Käännetään ohjelma uudelleen ja testataan: `make buggy`, `./buggy_program`

<img width="452" height="187" alt="VirtualBoxVM_kwTFXSs3Rk" src="https://github.com/user-attachments/assets/0e06ae2a-92ab-4d14-85b0-bbe3ea5d6396" />

Kuten näkyykin, saimme kaiken toimimaan kuten pitääkin!

---
**Vanha versio:**
```C
for (int i = 0; i <= size; i++)
```
**Uusi versio:** vaihdettu `<=` muotoon `<` tällöin ohjelma ei enää käsittele ylimääräistä alkiota! :D
```C
for (int i = 0; i < size; i++)
```
---

## Lab 1: Kaatumisen analysointi
Siirrytään nyt seuraavaan lab hakemistoon ja aloitetaan analysointi: `cd Desktop/H5/lab1`

1. Aloitetaan ensin ajamalla ohjelma:

`./gdb_example1`

Ohjelmaa normaalisti ajattaessa sain virheilmoituksen että ohjelma kaatui `Segmentation fault` virheeseen:

```bash
Khoor/#zruog1
zsh: segmentation fault  ./gdb_example1
```

<img width="356" height="78" alt="VirtualBoxVM_GeNFQZTa19" src="https://github.com/user-attachments/assets/8f722db2-ba58-4f08-abd1-82cdf4ac2aab" />

Siirrytään suoraan tutkimaan ohjelmaa debuggerilla, sillä en tässä vaiheessa osaa sanoa mistä kaatuminen oikein johtuu. Avataan tiedosto `cgdb`:llä ja ajetaan tiedosto suoraan:

```bash
cgdb ./gdb_example1
run
```

<img width="600" height="138" alt="VirtualBoxVM_p5G69fvu0y" src="https://github.com/user-attachments/assets/3fbbfa09-310e-41ad-a17d-df359e552f4b" />

Okei, tässä kun ohjelma kaatui yritin löytää tietoa miten pystyn jatkamaan tästä, ja löysin että `backtrace` komennolla pystyn löytämään mitä funktioita ohjelma kutsui ennen kaatumista. Kokeillaan sitä: [GDB - Backtraces](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Backtrace.html)

<img width="600" height="55" alt="VirtualBoxVM_DUPxTevPvF" src="https://github.com/user-attachments/assets/86597261-1145-4209-bedd-4171c1b49d10" />

Tässä näkyykin että `main` kutsui `print_scrambled` funktiota ja kaatuminen tapahtui juuri siellä.

2. Tarkistetaan vielä tarkemmin tuota kaatumiskohtaa:
Valitaan ensin se kohta, jossa ohjelma kaatui jonka voimme tehdä komennola `frame 0`, sillä ohjelma kaatui siellä. Jatkamme sitten etsimällä siitä lisätietoa `info frame` komennolla ja sitten tarkastellaan vielä kyseisen funktion paikalliset muuttujat `info locals` komennolla:

```bash
frame 0
info frame
info locals
```

<img width="600" height="191" alt="VirtualBoxVM_06Q3an5cGJ" src="https://github.com/user-attachments/assets/28b17787-9b56-4531-8932-841e14936c04" />

Tulosteessa näkyi tämmöinen kohta joka alkoi kiinnostamaan: `args: message=0x0` eli tuolla `message` muuttujalla on arvona `0x0`, ohjelma myös yrittää käyttää tuota samaa muuttujaa:
> Näkyy siitä "frame 0" kohdasta tarkemmin :)

```bash
printf("%c", (*message)+i);
```

Tarkoittaakohan tämä sitten sitä että toi `message` aiheuttaa kaatumisen? 
> Tässä kohtaa jäin hieman jumiin joten kysyin Claude Sonnet 4.6:lta apua miten pystyisin jatkamaan, ja se muistutti siitä että pystyn mennä frameja taaksepäin

3. Selvitetään mistä `message` tulee kokeilemalla siirtyä yhden framen taakse `main` funktioon:

```bash
frame 1
info locals
```

<img width="446" height="122" alt="VirtualBoxVM_Q8BeuMEPaO" src="https://github.com/user-attachments/assets/ead66dc3-ef88-4c3b-8fa3-eabc5856cca8" />

Okei! Täältä löytyi jotain, `bad_message` on asetettu `NULL`:ksi (`char *bad_message = NULL;`) jota sitten käytetään myöhemmin (`print_scrambled(bad_message);`) jonka takia ohjelma kaatuu! Ohjelma antaa `print_scrambled` funktiolle `NULL` osoittimen ja yrittää käyttää sitä, mutta tottakai se ei toimi.
> `0x0` tarkoittaa samaa kuin `NULL`!

Eli se menee jotenkin näin:

```
bad_message = NULL --> print_scrambled(bad_message) --> message = 0x0 --> (message) --> Segmentation fault
```

## Lab2: GNU Debugger







































































## Lähteet
- https://hackingcpp.com/cpp/tools/gdb_intro
- https://sourceware.org/gdb/current/onlinedocs/gdb.html/Backtrace.html
- https://www.sourceware.org/gdb/current/onlinedocs/gdb.html/Frame-Info.html
- 
