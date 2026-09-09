## H3 No Strings Attached | Blendi Thaqi 08/09/2026

## Ympäristö

**OS**: Kali GNU/Linux Rolling

**Browser**: Mozilla Firefox 140.11.0esr (64-bit)

**Hardware**: Virtualbox memory used 8 GB

**Processor**: AMD Ryzen 7 7800X3D | 4 cores used

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

**Disk**: 40 GB

**Network**: NAT

## a) Strings analyysi (`passtr`)

Aloitetaan ensin lataamalla ja purkamalla `ezbin-challenges.zip`, jonka jälkeen voimme jatkaa terminaalissa `challenges/passtr` hakemistoon.
> Lataa tiedosto täältä: [**Tero Karvinen: h3 No Strings Attached**](https://terokarvinen.com/application-hacking/#homework-tasks)  
> Voit myös ladata tiedoston suoraan tästä: [**ezbin-challenges.zip**](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip)

```bash
cd Desktop
unzip ezbin-challenges.zip
cd challenges/passtr
```

<img width="334" height="286" alt="Screenshot 2026-09-08 232346" src="https://github.com/user-attachments/assets/cc99d5aa-4219-4e0f-9730-f6e59c997b4d" />

1. Aloitetetaan kokeilemalla `strings` komentoa:

<img width="389" height="173" alt="Screenshot 2026-09-08 232405" src="https://github.com/user-attachments/assets/b59e2d4c-c208-4e8c-b264-568e85e93a7c" />

2. Kuten näkyy, `strings` komento tuo aivan liikaa tuloksia, joten voimme jatkaa rajaamalla niitä `grep` komennolla sekä lisätä siihen `-i` muuttuja jotta kirjainkokoa ei huomioida.

<img width="561" height="61" alt="Screenshot 2026-09-08 232425" src="https://github.com/user-attachments/assets/140e0d99-6b65-4b5d-8bc0-971a980826bb" />

> Se toimi! Saimme flagin: `FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}` mutta missä on salasana?

3. Tässä kohtaa aloin miettimään kuinka pystyisin myös saamaan salasanan, joten kävin googlettamassa miten tämä olisi mahdollista `grep` komennolla, ja löysin [GeeksforGeeks: Grep command in Unix/Linux](https://www.geeksforgeeks.org/linux-unix/grep-command-in-unixlinux/) sivulta `-C` muuttujan joka näyttää ympärissä olevat rivit: 

<img width="570" height="127" alt="Screenshot 2026-09-08 235553" src="https://github.com/user-attachments/assets/ba01fc3f-861f-4958-8c50-01cf9cc32dd6" />

> Saimme myös salasanan: `sala-hakkeri-321`

4. Ajetaan vielä tiedosto josta voimme katsoa että onko salasana oikein, josta tulee vastaukseksi että kyllä, sieltä myös tulostuu flagi!

<img width="565" height="96" alt="Screenshot 2026-09-09 005032" src="https://github.com/user-attachments/assets/bf084f54-e607-412b-a195-afbf07b5fe6e" />

## b) Koodin obfuskointi

Tässä kohdassa käytin noin 2 tuntia yrittäessäni ymmärtää, miten obfuskointi oikein toimii ja mietin, miten sen sitten pystyisi toteuttamaan käytännössä. Päädyin XOR:iin tekoälyn (ChatGPT 5.6 Luna) ja [Sam's Classin](https://samsclass.info/124/proj14/VP310.htm) esimerkin avulla. Miten XOR sitten toimii on että jokainen merkki "XORataan" samalla avaimella, jolloin siitä saadaan muunneltu arvo. Ohjelman aikana sama avain käyetään uudelleen, jolloin saamme alkuperäisen salasanan takaisin.
> Tässä myös työkalu jolla pystyt netissä XORaamaan mitä tahansa: https://legacy.cryptool.org/en/cto/xor

1. Aloitin ihan aluksi käyttämällä Pythonia XOR-arvojen laskemiseen Sam's Classin esimerkin avulla jossa merkkijonon jokainen merkki käydään läpi ja XORataan samalla avaimella:

```bash
nano xor.py
```
```py
password = "sala-hakkeri-321"
key = 0x07

for char in password:
    print(hex(ord(char) ^ key))
```

<img width="278" height="108" alt="Screenshot 2026-09-09 023354" src="https://github.com/user-attachments/assets/bd6677f1-fd13-4b2d-b525-0db1a564a687" />

2. Saimme tulokseksi tämän:

```
0x74 0x66 0x6B 0x66
0x2A 0x6F 0x66 0x6C
0x6C 0x62 0x75 0x6E
0x2A 0x34 0x35 0x36
```

<img width="386" height="301" alt="Screenshot 2026-09-09 023416" src="https://github.com/user-attachments/assets/070e4bb7-e327-4eb7-a5b2-1df50307e7dc" />

3. Lisätään se uuteen `2passtr.c` tiedostoon, jolla kokeilemme koko homman:

<img width="425" height="238" alt="Screenshot 2026-09-09 024257" src="https://github.com/user-attachments/assets/25f77401-e91f-42fa-be63-8478e4519a0d" />

5. Nyt voidaan jatkaa testaamisen parissa, aloitin muuntamalla `gcc`:n avulla ohjelman, ja sitten kokeilin suoraan `strings` komennolla löytyisikö mitään, ja sieltä ei löytynyt mitään!

<img width="396" height="92" alt="Screenshot 2026-09-09 024636" src="https://github.com/user-attachments/assets/aa8dc315-8d9c-4003-af1d-3fc4ce2c1f47" />

6. Kokeillaan vielä toimiiko ohjelma normaalisti, ja kuten sieltä näkyykin salasanan syöttäminen toimii normaalisti, saimme salasanan obfuskoitua!

<img width="388" height="112" alt="Screenshot 2026-09-09 024656" src="https://github.com/user-attachments/assets/02cfdc90-8f0b-4e58-8bb2-1b45cc3e35a0" />

## c) Reverse engineering (`packd`)

1. Aloitetaan kuten a) kohdassa, tutkimalla stringsejä, sieltä ei kuitenkaan löytynyt mitään ja itsellä tuli vain nämä ns. "tärkeimpänä" esille:
```
$Info: This file is packed with the UPX executable packer http://upx.sf.net $
$Id: UPX 4.21 Copyright (C) 1996-2023 the UPX Team. All Rights Reserved. $

W. FLAG{Tero-0e3bed0a89d88
51da933c64fefad
```
Nämä eivät kuitenkaan hirveästi auta, kuitenkin sieltä paljastui selkeä UPX tunniste, kävin lukemassa aiheesta tarkemmin: [Debian man page](https://manpages.debian.org/trixie/upx-ucl/upx.1.en.html), [UPX](https://upx.github.io/), 

<img width="600" height="183" alt="Screenshot 2026-09-09 025725" src="https://github.com/user-attachments/assets/d9b5f4aa-548a-44c0-b739-247807350328" />

2. Jatketaan kokeilemalla UPX:n purkamista, man pagesta tuli hyvä vinkki jota sitten sovelsin tähän tehtävään:
> "All UPX supported file formats can be unpacked using the -d switch, eg. upx -d yourfile.exe will uncompress the file you've just compressed."

Tein ensin kuitenkin kopion tiedostosta jonka jälkeen purin sen seuraavalla komennolla:

```bash
cp packd 2packd
upx -d packd 2packd
```
<img width="600" height="276" alt="Screenshot 2026-09-09 030439" src="https://github.com/user-attachments/assets/72050397-c556-461d-ae85-57fe7ee9382b" />

3. Jatketaan kokeilemalla strings komentoa uudelleen, sieltä näkyykin lisää tietoa nyt, salasana ja flag:

<img width="565" height="84" alt="Screenshot 2026-09-09 030638" src="https://github.com/user-attachments/assets/8706454f-20b7-4332-9ba7-744f3369cde6" />

> Flag: FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}  
> Salasana: piilos-AnAnAs

4. Testataan vielä onko tämä oikea salasana, ja kuten näkyykin se toimii, saimme tehtävän tehtyä oikein! :D :

<img width="570" height="95" alt="Screenshot 2026-09-09 030734" src="https://github.com/user-attachments/assets/284fb7aa-2119-4a92-89b7-3bd52d940cc9" />

## Lähteet
- https://manpages.debian.org/trixie/upx-ucl/upx.1.en.html
- https://samsclass.info/124/proj14/VP310.htm
- https://legacy.cryptool.org/en/cto/xor
- https://upx.github.io/
- https://askubuntu.com/questions/29589/chmod-ux-versus-chmod-x
- https://www.geeksforgeeks.org/linux-unix/grep-command-in-unixlinux/
- https://terokarvinen.com/application-hacking/#homework-tasks
