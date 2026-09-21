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

Luodaan uusi hakemisto ja siirretään tiedostot sinne:

```bash
mkdir H5
mv lab0.zip lab1.zip lab2.zip lab3.zip lab4.zip H5/
```

Siirrytään uuteen hakemistoon ja puretaan loput tiedostot:

```bash
cd H5
unzip -o 'lab*.zip'
```

2. Jatketaan asentamalla `cgdb` joka on GDB:n käyttöliittymä, jossa ohjelman lähdekoodi ja GDB:n komentorivi näkyvät samassa näkymässä.
> Käytän itse tätä mielummin sillä sitä on helpompi käyttää, sekä näyttää selkeämmältä.

```bash
sudo apt update
sudo apt install cgdb
```

Tarkistetaan vielä saimmeko `cgdb`:n ladattua oikein:

```bash
cgdb --version
```

## 







































































## Lähteet
- https://hackingcpp.com/cpp/tools/gdb_intro
- 
