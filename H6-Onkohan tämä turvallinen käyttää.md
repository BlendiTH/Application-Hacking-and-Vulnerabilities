<img width="543" height="169" alt="VirtualBoxVM_2AdGC17tS5" src="https://github.com/user-attachments/assets/10afd6df-84ae-4a4a-9a84-01a371417473" />## H6 Onkohan tämä turvallinen käyttää? | Blendi Thaqi 29/09/2026

## Ympäristö

**OS**: Kali GNU/Linux Rolling

**Browser**: Mozilla Firefox 140.11.0esr (64-bit)

**Hardware**: Virtualbox memory used 8 GB

**Processor**: AMD Ryzen 7 7800X3D | 4 cores used

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

**Disk**: 40 GB

**Network**: NAT

## TP-Link Tapo C200 Firmware analyysi

1. Aloitetaan ihan ensin työkalujen tarkistamisella, löytyvätkö tarpeelliset työkalut Kalista, jos ei, ladataan ne:
> Tarkistin myös `àwscli`:n sillä lataamme sillä tarvittavan firmwaren myöhemmin

```
sudo apt update
aws --version
binwalk -h
git --version
```

<img width="600" height="178" alt="VirtualBoxVM_oQhC51wBcm" src="https://github.com/user-attachments/assets/cc9c03e1-8ec8-4f24-b4ab-136116cb3b75" />

<img width="429" height="206" alt="VirtualBoxVM_11gYHHDXq0" src="https://github.com/user-attachments/assets/5eb2f046-ffb4-43e6-b21f-05a7072db0de" />

> Täällä näkyykin että `awscli` puuttuu!

<img width="321" height="62" alt="VirtualBoxVM_eLq8Ma3HwG" src="https://github.com/user-attachments/assets/7e62073e-1fda-4708-9c38-64426ef6826d" />

Jatketaan lataamalla `awscli` koska se puuttui:

```
aws s3 cp s3://download.tplinkcloud.com/firmware/Tapo_C200v3_en_1.4.2_Build_250313_Rel.40499n_up_boot-signed_1747894968535.bin Tapo_C200v4_en_1.4.2.bin --no-sign-request
```

<img width="492" height="157" alt="VirtualBoxVM_6x1bJDUEFa" src="https://github.com/user-attachments/assets/4b409299-2b02-4794-8b11-80069663d788" />

2. Jatketaan firmwaren lataamiseen, ladataan samalla `tp-link-decrypt` työkalu:

```bash
aws s3 cp s3://download.tplinkcloud.com/firmware/Tapo_C200v3_en_1.4.2_Build_250313_Rel.40499n_up_boot-signed_1747894968535.bin Tapo_C200v4_en_1.4.2.bin --no-sign-request
ls -lh ~/Tapo_C200v4_en_1.4.2.bin    # Tarkistin tässä tuliko ladattua tiedosto
git clone https://github.com/robbins/tp-link-decrypt.git
mv Tapo_C200v4_en_1.4.2.bin tp-link-decrypt    # Siirretään firmware oikeaan hakemistoon
cd tp-link-decrypt
```

<img width="600" height="341" alt="VirtualBoxVM_n6NqRbgHSh" src="https://github.com/user-attachments/assets/9b96227b-fd31-411a-8df4-fc28ac6ec28e" />

Seuraavaksi, mennään [README](https://github.com/robbins/tp-link-decrypt):n ohjeiden mukaan ja ladataan riippuvudet jotka voidaan asentaa `preinstall.sh` komennolla, jonka jälkeen meidän täytyy suorittaa `extract_keys.sh` sekä `make` jotta voimme saada ajettavan ohjelman tehtyä:

```bash
./preinstall.sh
./extract_keys.sh
make
```

<img width="600" height="204" alt="VirtualBoxVM_QMFixH9ySS" src="https://github.com/user-attachments/assets/3868d1ac-b931-4d0a-9425-e1f5bd3e3c32" />

>  Suorittaessani `preinstall.sh` skriptiä, kaikki muut riippuvuudet asentuivat onnistuneesti, mutta sain terminaaliin kuitenkin virheilmoituksen että `mips-linux-gnu-m` puuttui. En kuitenkaan lähtenyt selvitämän tätä enemoää sillä skriptin ajaminen oli README:n mukaan valinnaista.

<img width="600" height="195" alt="VirtualBoxVM_kivHB9Jl1Z" src="https://github.com/user-attachments/assets/e3255c7f-750e-4ceb-9ca1-e38f9d66359f" />

<img width="600" height="131" alt="VirtualBoxVM_vUlUgce2oz" src="https://github.com/user-attachments/assets/00bb834a-682d-43d9-aff9-6dd2b7c6336c" />

Okei, kuten näkyykin `make` komento epäonnistui, koska OpenSSL puuttui. Asennetaan sille tarvittava paketti ja yritetään uudelleen!

```bash
sudo apt install libssl-dev
```

<img width="600" height="187" alt="VirtualBoxVM_G050QJ9yJp" src="https://github.com/user-attachments/assets/e16de0ea-8543-4f27-9949-6bf4201ddd6a" />

Kokeillaan `make` komentoa uudelleen:

<img width="600" height="248" alt="VirtualBoxVM_3TAWGNtBsU" src="https://github.com/user-attachments/assets/752e9899-7b7f-4a1e-9f3c-618e894932e5" />

3. Nyt kun kaikki on ladattuna voimme jatkaa ajamalla `tp-link-decrypt` ohjelman laiteohjelmistotiedostolle:

```bash
../tp-link-decrypt/bin/tp-link-decrypt Tapo_C200v4_en_1.4.2.bin
```

<img width="555" height="303" alt="VirtualBoxVM_FB0MaLJ2Jy" src="https://github.com/user-attachments/assets/c1fa4376-7912-4892-8932-2796568c2a63" />

> Lyhyesti, työkalu purkaa firmwaretiedoston ja luo siitä uuden dekryptatun version alkuperäisestä firmwaresta (`.dec`)

4. Jatketaan tutkimalla sekä alkuperäistä että uutta purettua firmwarea:
> `binwalk` tutkii mitä firmwarekuvan sisällä oikeasti on, ja löytyykö sieltä mitään tarpeellista kuten eri tiedostojärjestelmiä (kernel, SquashFS, jne.)

```bash
file Tapo_C200v4_en_1.4.2.bin
file Tapo_C200v4_en_1.4.2.bin.dec
```

<img width="479" height="125" alt="VirtualBoxVM_nm1Pmq83qa" src="https://github.com/user-attachments/assets/a08bda91-00c7-41bb-8a5e-e04646ab09b0" />

Jatketaan `binwalk` komennolla:

```bash
binwalk Tapo_C200v4_en_1.4.2.bin
binwalk Tapo_C200v4_en_1.4.2.bin.dec
```

<img width="425" height="112" alt="VirtualBoxVM_bzoiIyeh9Y" src="https://github.com/user-attachments/assets/5036efb6-0998-4d63-84e4-cbe11a73f526" />

> `Tapo_C200v4_en_1.4.2.bin` tiedostosta ei tullut mitään..

<img width="600" height="81" alt="image" src="https://github.com/user-attachments/assets/1c88a319-4081-46cd-99f0-30530dbe150c" />

> `Tapo_C200v4_en_1.4.2.bin.dec` löytyi jotain! Binwalk löysi puretusta firmwaresta `SquashFS` tiedostojärjestelmän!

Selvitetään vielä tarkemmin mitä tästä puretusta firmwaresta löytyy Binwalkin `-e` muuttujan avulla:
> Komento ei muutu hirveästi, tämän muutujan avulla saamme kuitenkin puretun firmwaren purettua vielä enemmän ja kaikki Binwalkin löytämät rakenteet saavat omat tiedostonsa, joita voimme tutkia vielä tarkemmin!

```bash
binwalk -e Tapo_C200v4_en_1.4.2.bin.dec
```

<img width="600" height="92" alt="VirtualBoxVM_X7xMOpAWZw" src="https://github.com/user-attachments/assets/9d9fdce2-63c6-456a-b13a-b1347b91a7b1" />

5. Komennon suorittamisen jälkeen voimme nähdä että Binwalk löysi taas SquashFS tiedojärjestelmän kohdasta `0x3E0200` ja loi siitä puretun `squashfs-root` hakemiston, käydään tutkimassa sitä ja firmwaressa olleita tiedostoja sekä kansioita tarkemmin:
> `-l` näyttää tarkemmat tiedot, `-a` näyttää myös piilotetut tiedostot, `-h` näyttää tiedostokoot hieman helpommin.

```bash
ls -lah _Tapo_C200v4_en_1.4.2.bin.dec.extracted/squashfs-root
```

<img width="543" height="169" alt="VirtualBoxVM_2AdGC17tS5" src="https://github.com/user-attachments/assets/fcdb4ac9-c8f2-4fe0-b72c-9b9d8012b053" />

Katsotaan löytyykö minkälaisia ohjelmia firmwarsta, erityisesti haluaisin kuitenkin löytää suoritettavia tiedostoja, jotta pystymme tutkimaan niitä myöhemmin (esim. `strings` komennolla ja/tai Ghidralla) Tarkoituksena löytää mahdollisia kohteita tarkempaa tietoturva analyysiä varten:

```bash
find _Tapo_C200v4_en_1.4.2.bin.dec.extracted/squashfs-root -type f -executable
```

<img width="600" height="112" alt="VirtualBoxVM_6igTOntbMt" src="https://github.com/user-attachments/assets/244be0e8-127f-4089-823b-7927d14118a0" />

Komento löysi kyllä aika monia suoritettavaksi merkittyjä tiedostoja. Tärkeimpinä kuitenkin `bin/main`, `bin/gdbserver`, ja `bin/impdgb`, jotka voisivat olla kiinnostavia kohteita. Erityisesti `main` sillä se voi ja varmaankin liittyykin laitteen pääohhjelmistoon, `gdbserver` ja `impdbg` taas viitaavat debuggaamiseen liittyviin työkaluihin, en voi kuitenkaan vielä varmistää näiden käyttötarkoitusta.

Jatketaan seuraavaksi tarkistamalla, minkä tyyppisiä löydetyt tiedostot ovat aj mille arkkitehtuurille ne ovat käännettyjä. Näin pystymme varmistamaan, pystyykö mitään tiedostoja suorittaa, komento myös näyttää meille tiedoston tyypin ja mahdollisesti sen arkkitehtuurin:

```bash
file _Tapo_C200v4_en_1.4.2.bin.dec.extracted/squashfs-root/bin/main
```

<img width="600" height="82" alt="VirtualBoxVM_dCE91QMCEe" src="https://github.com/user-attachments/assets/e1810044-ce14-4604-9220-cd9e9c943830" />

Komento vahvisti, että `main` on 32 bittinen MIPS ohjelma, josta on poistettu symbolitietoja, no jatketaan kuitenkin tutkimalla sen sisältä löytyviä merkkijonoja:

```bash
strings _Tapo_C200v4_en_1.4.2.bin.dec.extracted/squashfs-root/bin/main
```
> Tässä kohtaa komento alkoi tulostamaan aivan liikaa niin ajattelin jatkaa tätä Ghidralla myöhemmin, joten tallensin sen tulostuksen tiedostoon.

```bash
strings _Tapo_C200v4_en_1.4.2.bin.dec.extracted/squashfs-root/bin/main > main_strings.txt
```

<img width="600" height="342" alt="VirtualBoxVM_si8Eu9kKvY" src="https://github.com/user-attachments/assets/94819067-6894-41f2-924f-0c2057107e30" />

6. Mennään kuitenkin seuraavaan asiaan, root-salasanan tutkimiseen. Katsotaan löytyykö salasanaa ghidrasta













