## H4 Some Disassembly Required | Blendi Thaqi 14/09/2026

## Ympäristö

**OS**: Kali GNU/Linux Rolling

**Browser**: Mozilla Firefox 140.11.0esr (64-bit)

**Hardware**: Virtualbox memory used 8 GB

**Processor**: AMD Ryzen 7 7800X3D | 4 cores used

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

**Disk**: 40 GB

**Network**: NAT

## Video ja sen tiivistelmä

**Hammond 2022: [Ghidra for Reverse Engineering (PicoCTF 2022 #42 'bbbloat')](https://www.youtube.com/watch?v=oTD_ki86c9I)**
> Videossa ratkaistiin PicoCTF 2022:n obfuskoitu "bbbloat" haaste. Käyttämällä sekä Ghidraa että muita ratkaisuja.
- Video aloitti tutkimalla binääriä eri komentorivityökaluilla kuten: `strace`, `ltrace`, `strings` ja `objdump`
  - **Komennot lyhyesti:**
    - **strace:** Näyttää ohjelman tekemiä järjestelmäkutsuja.
    - **ltrace:** Näyttää ohjelman tekemiä kirjastokutsuja.
    - **strings:** Etsii binääristä luettavat tekstipätkät.
    - **objdump:** Purkaa binäärin luettavaksi assembly-koodiksi.
- Video sitten jatkoi käyttämään Ghidraa analyysin haastavuuden takia. Käyttäen Xrefs- ja Decompiler näkymiä niiden avulla Hammond sai assemblystä käännettyä luettavampaa C koodia.
- Ratkaisu löydettiin paikantamalla heksadesimaaliarvo ja muuntamalla se Pythonilla desimaaliksi oikean syötteen löytämiseksi.
- Videon pääopetuksena oli yksinkertainen: aloita perus komentorivityökaluista, siirry sitten Ghidraan ja muista aina varmistaa löydökset suorittamalla ohjelma.

## a) Ghidran asennus
Ennen kuin voimme jatkaa Ghidran asennuksen kanssa, kannattaa lukea Ghidran Getting Started -ohje, sillä se listaa kaikki tuetut käyttöjärjestelmät sekä Java vaatimukset:
> Lataa täältä: [Ghidra Releases](https://github.com/NationalSecurityAgency/ghidra/releases/),
> Latausohje: [Installing Ghidra](https://github.com/NationalSecurityAgency/ghidra/blob/master/GhidraDocs/GettingStarted.md#installing-ghidra),
> Yleinen ohje: [Getting started](https://github.com/NationalSecurityAgency/ghidra/blob/master/GhidraDocs/GettingStarted.md)

<img width="600" height="383" alt="1" src="https://github.com/user-attachments/assets/b82ec844-336b-4cc9-92e3-693e1055f4d0" />

1. Aloitetaan asentamalla Java

```bash
sudo apt update
sudo apt install openjdk-25-jdk
```

<img width="364" height="91" alt="2" src="https://github.com/user-attachments/assets/7843e2f8-b039-4cb1-9c2d-d9a683f38b92" />
<br>
<img width="341" height="126" alt="3" src="https://github.com/user-attachments/assets/b871b6d5-947a-49bf-aa30-28cc6f032c15" />

Tarkistetaan vielä asennus:

```bash
java --version
```

<img width="432" height="93" alt="4" src="https://github.com/user-attachments/assets/c1dc9031-0abb-49bd-955c-1a89df8f5d26" />

2. Jatketaan lataamalla Ghidran uusin julkaisu heidän GitHub sivustolta.

```bash
wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_12.1.3_build/ghidra_12.1.3_PUBLIC_20260817.zip
```

<img width="600" height="114" alt="5" src="https://github.com/user-attachments/assets/be2452e4-0a5e-4351-afcc-d144d9f932de" />

3. Voimme sitten jatkaa purkamalla ZIP ja siirtymällä Ghidran hakemistoon:

```bash
unzip ghidra_12.1.3_PUBLIC_20260817.zip 
cd ghidra_12.1.3_PUBLIC
```

<img width="440" height="141" alt="6" src="https://github.com/user-attachments/assets/94c348de-669e-428b-861d-2cb6caa1c74e" />

4. Voimme nyt jatkaa käynnistämällä Ghidra

```bash
./ghidraRun
```

<img width="600" height="306" alt="7" src="https://github.com/user-attachments/assets/a7b77c7f-1da1-485b-9e58-3e7a7669dc97" />

5. Jatketaan luomalla projekti Ghidrassa:

`File -> New Project -> Non-Shared Project -> H4`

<img width="290" height="154" alt="aa" src="https://github.com/user-attachments/assets/8f8e3638-44ba-4027-a10b-c73ff3c52f19" />
<img width="208" height="82" alt="8" src="https://github.com/user-attachments/assets/adb9e176-479f-41e4-b18b-096cdb9c3dcc" />
<br>
<img width="600" height="452" alt="9" src="https://github.com/user-attachments/assets/65e4396c-c025-425c-a90d-7674f644f035" />

## b) rever-C (`packd`)

Aloitetaan ensin lataamalla ja purkamalla `ezbin-challenges.zip`, jonka jälkeen voimme jatkaa terminaalissa `challenges/packd` hakemistoon.
> Lataa tiedosto täältä: [**Tero Karvinen: h4 Some Disassembly Required**](https://terokarvinen.com/application-hacking/#homework-tasks)  
> Voit myös ladata tiedoston suoraan tästä: [**ezbin-challenges.zip**](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip)

```bash
cd Desktop
unzip ezbin-challenges.zip
cd challenges/packd
```

1. Jatketaan samalla tavalla kuin edellisessä H3 tehtävässä, jossa aloitime kopioimalla alkuperäisen tiedoston ja sitten purimme UPX-pakatun `packd` binäärin. UPX:n manpagen ohjeiden mukaan:
> "All UPX supported file formats can be unpacked using the -d switch, eg. upx -d yourfile.exe will uncompress the file you've just compressed."

```bash
cp packd 4packd
upx -d 4packd
```

2. Seuraavaksi avataan binääri Ghidrassa
> Tässä kohtaa olin jo kopioinut uuden 4packd tiedoston H4 kansioon: `cp 4packd /home/blendi/H4`

`File -> Import File -> H4 -> 4packd`

<img width="255" height="458" alt="10" src="https://github.com/user-attachments/assets/2ca99b71-caa1-4bbb-9159-3a75c41e8988" />
<img width="406" height="567" alt="VirtualBoxVM_1mjA707fKt" src="https://github.com/user-attachments/assets/72664da5-9c03-43ed-b890-0247c5c7d9da" />
<br>
<img width="520" height="293" alt="VirtualBoxVM_riIcE1xrKB" src="https://github.com/user-attachments/assets/a6050bac-c45b-474b-b765-1fdb367303d7" />

Ghidra tunnistaa binääri tyypin automaattisesti, joten hyväksytään ehdotetut asetukset ja painetaan **OK.** Tämän jälkeen projekti ikkunassa näkyy `4packd`, avataan se kaksoisklikkaamalla.

3. Käynnistetään analysointi:

Kun ohjelma avautuu ensimmäistä kertaa, Ghidra kysyy: **"Analyze now?"**, valitsemme tässä kohtaa **Yes** sen jälkeen Auto Analysis ikkunassa voimme jättää oletusvalinnat päälle ja painaa **Analyze**

Tälläinen näkymä pitäisi tulla kun kaikki valinnat on tehty:

<img width="600" height="460" alt="VirtualBoxVM_b307SNIiIU" src="https://github.com/user-attachments/assets/113ffbbe-5e38-4670-b9dc-8d7621ec4798" />

4. Voimme aloittaa analysoinnin etsimällä `main` funktion:

Analyysin jälkeen avautuu "CodeBrowser" ikkuna, josta sitten siirrytään "Symbol Tree" kohtaan jotta voimme tutkia `main` funktiota, sitten kaksoisklikataan sitä:
`Symbol Tree -> Functions -> main`

<img width="166" height="320" alt="VirtualBoxVM_MjRrmE0TJZ" src="https://github.com/user-attachments/assets/6eb9266d-07de-4ec0-b023-3d8841149d31" />

5. Kun `main` funktio on valittu, tarkistetaan Listing ja Decompiler:

Keskellä pitäisi näkyä **Listing** ja **Decompiler** oikealla puolella. Decompilerissa näkyy aluksi Ghidran antamia outoja nimiä, kuten `local_28`. Ennen kuin aloitamme yhtään mitään, meidän täytyy tutkia ja selvittää mitä funktiot ja muuttujat tekevät, jotta voimme jatkaa nimeämällä ne selkeämmiksi.

<img width="600" height="300" alt="VirtualBoxVM_wBuskIFwBi" src="https://github.com/user-attachments/assets/1f89166a-1f05-4c6d-90ed-44bfcf33b3e9" />

6. Jatketaan selvittämällä funktioiden toiminta
> **[strcmp:](https://cplusplus.com/reference/cstring/strcmp/)** Compares the C string str1 to the C string str2

Decompilerista näkyy, että `main` kysyy käyttäjältä salasanaa **(1)**, ja sitten tallentaa sen `local_28` muuttujaan **(2)**. Tämän jälkeen salasanaa verrataan `strcmp` funktiolla merkkijonoon `"piilos-AnAnAs"` **(3)**, jos vertailu palauttaa meille arvon `0`, ohjelma tulostaa _"Yes! That's the password. FLAG{Tero...}"_ ja saamme lipun, muuten saamme virheilmoituksen _"Sorry, no bonus."_ **(4)**.

<img width="404" height="334" alt="VirtualBoxVM_lyueOKUB0L" src="https://github.com/user-attachments/assets/8697fc21-4f29-46b1-ad5a-99e31c0cffdc" />

7. Nyt kun olemme selvittäneet mitä nämä muuttujat tekevät, voidaan jatkaa niiden uudelleen nimeämisellä. 

`Paina muuttujan päältä hiiren oikealla klikkauksella -> Rename Variable`

<img width="315" height="43" alt="VirtualBoxVM_qwCS2aZxVd" src="https://github.com/user-attachments/assets/9fc60b56-56c0-4fe2-a8b7-75d7d8d6b30b" />
<br>
<img width="442" height="147" alt="VirtualBoxVM_7QxDFOFrwF" src="https://github.com/user-attachments/assets/853d2977-374c-468e-a8b7-45072fc95b46" />

Näin saimme decompiler koodista paljon helpommin ymmärrettävän, sekä saimme tietää myös salasanan:
> **Salasana**: piilos-AnAnAs

<img width="397" height="333" alt="VirtualBoxVM_A0KN50rjGR" src="https://github.com/user-attachments/assets/52d134fa-4084-4eaf-aa61-42dc269fa27b" />

8. Kokeillaan ratkaista tehtävä uudella tiedollamme, aloitetaan siirtymällä terminaalissa oikeaan hakemistoon ja ajetaan ohjelma:

```bash
cd Desktop/challenges/packd
./4packd
```

<img width="574" height="152" alt="VirtualBoxVM_S8pHaTahel" src="https://github.com/user-attachments/assets/f7aa773e-621f-4456-a768-2373fff8b44a" />

> Kuten näkyykin, saimme sen tehtyä oikein!

## c) If backwards (`passtr`)

1. Aloitetaan ensin kopioimalla `passtr` tiedosto, uudelleen nimeämällä sen ja viemällä se H4 kansioon jotta voimme analysoida se Ghidrassa:
> Tarkistin tässä myös oliko `passtr` pakattu UPX:n avulla: `file passtr` & `strings passtr | grep -i "UPX"` (Keltaisella)

```bash
cp passtr 4passtr
cp 4passtr /home/blendi/H4/
```

<img width="458" height="286" alt="VirtualBoxVM_ETXQt89zlP" src="https://github.com/user-attachments/assets/57b09faa-ef70-44f4-ad24-c28dc13da70b" />

2. Jatketaan sitten tuomalla `passtr` Ghidraan samalla tavalla kuin kohdassa b:

`File -> Import File -> H4 -> passtr`

<img width="197" height="109" alt="VirtualBoxVM_iSezfCsE1p" src="https://github.com/user-attachments/assets/831d1f88-b9e1-4d39-b6c9-57e4e59961b8" />

3. Avataan tämän jälkeen taas `main` funktio ja etsitään **Decompiler** näkymästä kohta, jossa ohjelma vertailee käyttäjän syötettä (`strcmp`) oikeaan salasanaan. Sitten siirrytään Listing näkymään, jossa nähdään kuinka vertailun tulos `TEST`, `JNZ` ratkaisee tulostetaanko lippu vai "Sorry, no bonus".
> `JNZ` = Jump If Not Zero  
> Selitykset tarkemmin: [JNZ](https://stackoverflow.com/questions/14841169/jnz-cmp-assembly-instructions), [MOV](https://coddy.tech/learn/assembly/fundamentals/what_is_mov)

<img width="884" height="362" alt="VirtualBoxVM_WEKjFSqPy2" src="https://github.com/user-attachments/assets/7ec5134b-f6e1-475a-a083-163c3dcf9f15" />

4. Kuten huomataankin kuvassa näkyvä `JNZ` hyppää "Sorry, no bonus" haaraan silloin kun `strcmp` vertailun tuloksena ei ole `0`. Tämä sai minut miettimään onkohan tällä olemassa vastakohtaa, ja löysin Googlen kautta Conditional Jumps Instructions materiaalin, josa `JZ` toimisi täydellisesti, päinvastaisena ehtona. Tämän myötä tajusin vaihtamalla `JNZ` -> `JZ` saamme aina oikean salasanan hylättyä ja kaikki muut väärät salasanat hyväksyttyä!
> `JZ` = Jump If Zero

5. Muutetaan `JNZ` -> `JZ` klikkaamalla `JNZ` käskyä hiiren oikealla klikkauksella ja valitaan `Patch Instruction`, sitten kirjoitetaan tilalle `JZ`:

<img width="309" height="254" alt="VirtualBoxVM_9hFCNvc7Uh" src="https://github.com/user-attachments/assets/8da62822-f05d-4ebe-85da-b6b7fb32f341" />
<br>
<img width="235" height="60" alt="VirtualBoxVM_zuFucfhDDk" src="https://github.com/user-attachments/assets/316f6798-364d-45f4-92f0-7da107531fc5" />

Painetaan Enter ja Ghidra muuttaa tuon assembly käskyn `JZ`:ksi.

6. Jatketaan viemällä muokattu binääri kun muutos on tehty:

`File -> Export Program`

<img width="253" height="266" alt="VirtualBoxVM_zDjk0aOaW1" src="https://github.com/user-attachments/assets/27fef66d-a709-460f-bea8-dcfbbfee463d" />

Sitten tallennetaan eri nimellä jotta tiedämme mitä kokeilla, itse annoin nimeksi `4passtr_k`

<img width="350" height="234" alt="VirtualBoxVM_KZcLz59rpM" src="https://github.com/user-attachments/assets/e7c5c0b7-87ba-4d4b-be3b-e3792b75d6b1" />

> **HUOM.** jos tässä kohtaa ei valitse "Original File" ohjelmaa ei voi ajaa!

7. Nyt tärkeimpään kohtaan, testataan muutos! Aloitetaan ajamalla ensin väärällä salasanalla ja sitten oikealla:

<img width="562" height="240" alt="VirtualBoxVM_89PAt1rm4l" src="https://github.com/user-attachments/assets/4abda69d-6f83-4439-84fa-022c74cabc88" />

> Se toimii, wohoo! :D






## d) Nora CrackMe



## e) Nora CrackMe (01)



## e) Nora CrackMe (01e)



## f) Nora CrackMe (02)
