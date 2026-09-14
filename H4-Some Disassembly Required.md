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
> Tässä kohtaa olin jo siirtänyt uuden 4packd tiedoston H4 kansioon: `mv 4packd /home/blendi/H4`

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

4. Voimme aloittaa analysointi etsimällä `main`:



## c) If backwards (`passtr`)



## d) Nora CrackMe



## e) Nora CrackMe (01)



## e) Nora CrackMe (01e)



## f) Nora CrackMe (02)
