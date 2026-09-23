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

Aluksi työpöydällä pitäisi näkyä ns. "Welcome screen" jonka jälkeen `Enter` napin painamisen jälkeen, käyttöliittymmään tulee näkyviin ylhäälle lähdekoodi ja alhaalle GDB:n komentorivi.

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
> Tässä kohtaa jäin hieman jumiin joten kysyin Claude Sonnet 5 (medium):lta apua miten pystyisin jatkamaan, ja se muistutti siitä että pystyn mennä frameja taaksepäin

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

Jatketaan seuraavasta labistä: `passtr`.

1. Aloitetaan siirtymällä oikeaan hakemistoon ja avaamalla binääri `cgdb`:llä :

```bash
cd Desktop/H5/lab2/passtr
cgdb ./passtr2o
```

<img width="362" height="93" alt="VirtualBoxVM_x0mlDGxzZR" src="https://github.com/user-attachments/assets/444b0990-3324-41df-b914-1441f855704a" />

<img width="600" height="454" alt="VirtualBoxVM_yReSq8o2Ps" src="https://github.com/user-attachments/assets/c7e5de9c-6b00-4add-b83a-63d00ddd9e49" />

2. Kuten näkyykin, koska tässä tehtävässä ei ole lähdekoodia käytettävissä, aloitin tutkimalla mitä funktioita binäärissä on samalla `info` komennolla mitä käytin Lab1:ssä :

```bash
info functions
```

Tuloksissa tuli esille aika monta eri asiaa, kuitenkin itseäni kiinnosti eniten nämä seuraavat highlightatut funktiot:

```
main
EaseEAs
check_password
mAsdf3a
```

<img width="343" height="286" alt="VirtualBoxVM_rObgk5QCgR" src="https://github.com/user-attachments/assets/ec48b943-3a27-4fd5-a119-de66938ff1c4" />

3. Jatketaan tutkimalla jokaista funktiota vuorotellen, aloitin tässä `main` funktiolla:

```
disassemble main
```

<img width="589" height="309" alt="VirtualBoxVM_OVXLOZY1xu" src="https://github.com/user-attachments/assets/2d0c7a2d-9502-45e9-9fd3-6115dba34d27" />

<img width="463" height="132" alt="VirtualBoxVM_KTzaeFz5cF" src="https://github.com/user-attachments/assets/2a83bfec-4a50-4711-be92-d730e17aec65" />

4. Tästä voimme nähdä että ohjelma käyttää myöhemmin ainakin `mAsdf3a` ja `EaseEAs` funktiota. Kuitenkin tässä vaiheessa on aika vaikeaa sanoa että mitä ne tekee, sillä en tiedä asiasta tarkemmin, joten mennään tutkimaan niitä seuraavaksi:
> `call` tarkoittaa siis "kutsuu x funktiota"!

```
disassemble EaseEAs
```

<img width="532" height="95" alt="VirtualBoxVM_phA8Oq7hq3" src="https://github.com/user-attachments/assets/be86143c-735d-4974-9ffc-c09cb6bdb84d" />

Täältä löytyi jotain tuttua, XOR! Tajusin tässä kohtaa suoraan että `EaseEAs` funktio käy sille annetun merkkijonon läpi ja se XORaatan.
> Tarkemmin XOR:rauksesta pystyt lukemaan H3 tehtäväni raportista: [H3-No Strings Attached](https://github.com/BlendiTH/Application-Hacking-and-Vulnerabilities/blob/main/H3-No%20Strings%20Attached.md#b-koodin-obfuskointi)

5. Siirrytään nyt `check_password` funktion tutkimiseen:

```bash
disassemble check_password
```

<img width="429" height="68" alt="VirtualBoxVM_qLiOit5sZn" src="https://github.com/user-attachments/assets/d989f332-3565-40d1-a468-c3cb5c7bc4c6" />

Tuloksena näkyy käytännössä se että funktio vain palauttaa arvon `0` eikä sisällä minkäänlaista varsinaista salasanan tarkistusta.
> Tarkemmin tuosta `xor eax,eax`: [Why xor eax, eax?](https://xania.org/202512/01-xor-eax-eax)

```assembly
xor eax,eax
ret
```

6. Tutkitaan vielä lopuksi tuota `mAsdf3a` funktiota:

```
disassemble mAsdf3a
```

<img width="497" height="118" alt="VirtualBoxVM_DhYOkRWL4R" src="https://github.com/user-attachments/assets/f29056ff-3c64-4894-8aba-e53f81a731e6" />

Täältä tuli esille nämä tärkeimpinä komentoina:

```assembly
test al,0x1
sub edx,0x7
add edx,0x3
```

`test al,0x1` tarkistaa tässä, onko käsiteltävän merkin indeksi parillinen vai pariton, jonka jälkeen ohjelman laskuri alkaa arvosta 0 ja kasvaa jokaisella kierroksella. Tämän perusteella se jatkaa vähentämällä parittomista indekseista 7 (`sub edx,0x7`) ja parillisiin indekseihin se lisää 3 (`add edx,0x3`). Kaivamalla esiin vanha merkkijono jonka löysimme `anLTj4u8` ja laskemalla ASCII-arvojen mukaan voimme selvittää oikean salasanan:

```
a + 3 = d
n - 7 = g
L + 3 = O
T - 7 = M
j + 3 = m
4 - 7 = -
i + 3 = x
8 - 7 = 1
```

Eli oikeaksi salasanaksi saamme: `dgOMm-x1`, testataan sitä vielä ohjelmassa ja varmistetaan että se toimii:

<img width="550" height="93" alt="VirtualBoxVM_RAlbkDrjkX" src="https://github.com/user-attachments/assets/c5d35061-6bc1-4c25-9d11-2a5bcf41cb92" />

> Mahtavaa saimme sen oikein, saimme myös lipun tulostettua: `FLAG{Lari-rsvRDx04WMBZpuwg4qfYwzdcvVa0oym}`

## Lab3: Nora CrackMe

Tässä tehtävässä tarkoituksena on selvittää ohjelman oikea salasana debuggerilla, joten aloitetaan suoraan lab:in kanssa siirtymällä oikeaan hakemistoon ja avaamalla binääri `cgdb`:llä:
> Tässä tehtävässä voit katsoa kuinka koko ASCII arvo homma toimii vanhasta tehtävästäni, sillä se on jo siellä selitetty joten täällä siihen ei ole tarvetta: [H4-Some Disassembly Required](https://github.com/BlendiTH/Application-Hacking-and-Vulnerabilities/blob/main/H4-Some%20Disassembly%20Required.md#f-nora-crackme-02)

```bash
cd ..        # 2x kertaa!
cd lab3
cd crackmes
```

<img width="381" height="243" alt="VirtualBoxVM_UtZbUCB1iL" src="https://github.com/user-attachments/assets/76d9209b-eb05-4231-af0b-a699298d8daf" />

1. Koska kyseessä on taas binääri, eikä ohjelman toimintaa pysty näkemään suoraan lähdekoodista jatketaan taas tutkimalla ohjelman funktiot:

```
info functions
```

<img width="336" height="247" alt="VirtualBoxVM_AfxYwzjIuw" src="https://github.com/user-attachments/assets/bc5c091b-87dc-4d27-a916-38859a12a8bd" />

2. Tuloksissa näkyy tuttu `main` funktio, käydään tutkimassa sitä:

```disassemble main```

x
Okei, disassemblyssä näkyy muun muassa seuraavat kohdat jotka näytti kiinnostavilta:

```assembly
mov  $0x70,%eax
sub $0x1,%eax
cmp %edx,%edx
jne ...
```

`mov` siis asettaa `eax` rekisteriin arvon `0x70` joka vastaa ASCII taulukossa merkkiä `p` (ASCII taulukon käytöstä päättelin viime tehtävän perusteella, tämä oli odotettua). Seuraavaksi `sub` vähentää siitä yhdenv joten se `p` muuttuu `o`:ksi. Kaiken tämän jälkeen siirrymme vielä `cmp`:hen joka vertaa tätä arvoa käyttäjän antamaan merkkiin ja `jne` (**jump if not equal**) siirtyy väärän salasanan kohtaan, vain jos ne eivät täsmää.

<img width="470" height="205" alt="VirtualBoxVM_tga1CmyJR6" src="https://github.com/user-attachments/assets/edfe41a1-3d1c-47a1-9135-8162370e4cd0" />

3. No, selvitetään sitten tämän tiedon perusteella mistä tämä verrattava merkkijono tulee. Tässä kohtaa tarvitsin tekoälyltä (Claude Sonnet 5 (Medium))taas apua kun olin hieman jumissa ja en ymmärtänyt täysin 100% mitä piti seuraavaksi tehdä. Se sitten kertoi Disassemblyssä näkyvästä `lea 0xed0(%rip),%rdi` kohdasta, jonka rekisteriin (`rdi`) ladataan muistiosoite, jota ohjelma sitten käyttää merkkijonon käsittelyyn. 

Jatkoin sitten Googlaamalla miten oikein pystyn saamaan tuon osoitteen sisältämän teksin ja päädyin [GDB Memory](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Memory.html) sivustoon ja sieltä löytyi `x` komento jolla pystyn tutkimaan muistissa olevaa dataa, kokeilin kuitenkin ensin vain pelkkää komentoa mutta se antoi `No registers` virheen:

```
x/s $rdi 
```
> Tarkemmin: `x` = tutkii muistia, `/s` = näyttää sisällön merkkijonona, ja `$rdi` = käyttää `rdi` rekisterin sisältämää muistiosoitetta

<img width="146" height="39" alt="VirtualBoxVM_aq1o4zQwz4" src="https://github.com/user-attachments/assets/06a4597e-723c-4154-bea6-e0b110f23e0f" />

Tämä virhe tuli varmasti koska ohjelma oli ehtinyt loppua, joten käynnistin ohjelman uudelleen mutta tällä kertaa breakpointilla `main` funktioon ja etenin siitä sitten `nexti` komennolla jota jatkoin, kunnes vastaan tuli tämä kohta:
> `nexti` ei `next` koska käytössä on vain assembly, joten `nexti` menee aina seuraavaan assembly käskyyn eli instructioniin 

```assembly
lea 0xed0(%rip),%rdi
```

<img width="600" height="644" alt="VirtualBoxVM_h6NLYNVFMU" src="https://github.com/user-attachments/assets/78619bc7-ed56-40d5-92df-d122da2fae76" />

<img width="600" height="370" alt="VirtualBoxVM_src3fLCYX5" src="https://github.com/user-attachments/assets/ea3dfbf3-6e36-4b74-b2bd-b8adfd3c72e3" />

Tässä kohtaa `rdi` rekisteriin oli ladattuna halutun merkkijonon muistiosoite jonka jälkeen kokeilin uudelleen samaa komentoa, josta sain tulokseksi `password1`, eli ohjelman käyttämä verrattava merkkijono on tuo!

<img width="233" height="39" alt="VirtualBoxVM_8wAgBycyf1" src="https://github.com/user-attachments/assets/15b0b57f-c9ec-40fb-a2f1-0f8832984afa" />

4. Seuraavaksi selvitetään oikea salasana, nyt kun tiedämme että verrattava merkkijono on `password1` voimme vihdoin käyttää mitä löysimme aikaisemmin:

```assembly
sub $0x1,%eax
cmp %edx,%edx
```

Joka siis lyhyesti tarkoitta sitä että jokaisesta verrattavan merkkijonon merkistä vähennetään yksi ennenkuin sitä verrataan käyttäjän syötteeseen ja tutkitaan onko se oikein. Muistetaan käyttää ASCII-arvoja:

Salasana on password1 joten aloitetaan p:stä: p - 1 = o eli ensimmäisen merkin täytyy olla se "o" jatketaan tämä sama homma koko password1 merkkijonolle, joten salasanaksi saadaan: ```o`rrvnqc0```

5. Testataan vielä lopuksi salasana:

```bash
./crackme02.64 'o`rrvnqc0'
```

<img width="386" height="72" alt="VirtualBoxVM_W7mkvNptY3" src="https://github.com/user-attachments/assets/7a3078e0-fcfb-424a-812c-3b10b17d95fc" />

Se oli oikein! Saimme sen tehtyä! :D

## Lähteet
- https://hackingcpp.com/cpp/tools/gdb_intro
- https://sourceware.org/gdb/current/onlinedocs/gdb.html/Backtrace.html
- https://www.sourceware.org/gdb/current/onlinedocs/gdb.html/Frame-Info.html
- [H3-No Strings Attached](https://github.com/BlendiTH/Application-Hacking-and-Vulnerabilities/blob/main/H3-No%20Strings%20Attached.md#b-koodin-obfuskointi)
- https://xania.org/202512/01-xor-eax-eax
