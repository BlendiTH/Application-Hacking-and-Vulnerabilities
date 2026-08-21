## H0 Compile and Analyze | Blendi Thaqi 21/08/2026

## Ympäristö

**OS**: Windows 11

**Browser**: Helium 0.15.5.1

**Compiler**: g++ 

**Analysis tools**: GNU Binutils (objdump)

**Processor**: AMD Ryzen 7 7800X3D

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

## Prerequisites

1. Ennen kuin voimme jatkaa, meidän täytyy asentaa C/C++ laajennus Visual Studio Codeen:

<img width="943" height="251" alt="image" src="https://github.com/user-attachments/assets/4d181eb7-0236-47dd-95fa-7d86609d0c3b" />

2. Sitten jatketaan asentamalla kääntäjä:

Latasin [MSYS2](https://www.msys2.org/):n kautta Mingw-w64:n joka sitten latasi meille gcc, g++ ja gdb:

```shell
pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain
```

## a) Compile a simple program. Analyze the binary.

1. Aloitamme simppelisti tekemällä uuden kansion, siirtymällä sinne ja avaamalla Visual Studio Coden:

```bash
mkdir hellou
cd hellou
code .  # Avaa Visual Studio Coden luodusta kansiosta
```

<img width="817" height="405" alt="image" src="https://github.com/user-attachments/assets/0cebe6ac-00a7-4dce-accd-be3674be1f14" />

2. Kun saamme VSCode:n avauttua, voimme jatkaa luomalla uuden tiedoston nimellä `hellou.cpp`:

<img width="1532" height="1008" alt="image" src="https://github.com/user-attachments/assets/3c0378ad-533d-49fb-9116-9e393cebb2ae" />

<img width="942" height="711" alt="image" src="https://github.com/user-attachments/assets/73ffa3cd-7a44-4981-893a-7d7991275753" />

3. Seuraavaksi, voimme jatkaa kirjoittamalla koodin tiedostoon, tätä tehtävää varten tein yksinkertaisen C++ ohjelman joka tulostaa **"Hellou everyone :)"**:

```cpp
#include <iostream>

int main()
{
    std::cout << "Hellou everyone :)" << std::endl;
}
```

<img width="721" height="231" alt="image" src="https://github.com/user-attachments/assets/e89ed440-c79b-44eb-baf1-ed1ced12e139" />

4. Sitten, käännetään (compile) tiedosto g++ avulla terminaalista ja kokeillaan toimiiko tiedosto:

<img width="917" height="39" alt="image" src="https://github.com/user-attachments/assets/4802a314-f248-42fe-a873-c1ddcd77ac86" />

> "Mitä tuo -o tarkoittaa?" -o tarkoittaa output file, eli mihin tiedostoon käännämme koodimme.

Kuten näkyykin, saimme tiedoston tehtyä ja binääri on valmis!

<img width="865" height="301" alt="image" src="https://github.com/user-attachments/assets/2ee2e4fc-1d0f-40a0-a40e-ac94fc4ec83d" />

Voimme testauksen jälkeen nähdä että tiedosto toimii kuten pitääkin.

<img width="698" height="60" alt="image" src="https://github.com/user-attachments/assets/a89b63b6-7061-4122-bcfd-0a968c5cbe52" />

5. Seuraavaksi, jatketaan binäärin analysointia GNU Binutils paketin avulla (jonka saimme kun latasimme MinGW-w64:n MSYS2:n kautta):

Aloitetaan `objdump` komennolla, joka näyttää tietoja objektitiedostoista, käyttämällä `-f` valitsinta joka näyttä meille tiedoston arkkitehtuurin, sen formaatin, entry pointin ja muita tiedoston headeriin liittyviä tietoja:

<img width="843" height="183" alt="image" src="https://github.com/user-attachments/assets/3125e175-1f03-4675-a20e-d57c3bb4498c" />

Voimme sitten jatkaa vaihtamalla valitsinta, `-h` valitsimeen joka näyttää meille osion sisältämiä ominaisuuksia.

<img width="1102" height="545" alt="image" src="https://github.com/user-attachments/assets/6c6fe490-c3e0-46b3-be93-46b4c33ddf38" />

Tämän jälkeen vaihdetaan `-d` valitsimeen jonka avulla saamme konekoodin assembly-muodossa, ja näin olemme valmiita.

<img width="1096" height="595" alt="image" src="https://github.com/user-attachments/assets/1b7df964-7429-470c-bca9-4f3bd1c561c4" />

## Yhteenveto

- Luotiin yksinkertainen C++ ohjelma, joka käännettiin g++:lla binääritiedostoksi ja sitten analysoitiin GNU Binutils työkaluilla.

## Lähteet
- https://code.visualstudio.com/docs/languages/cpp
- https://www.aussieai.com/book/ch37-examining-object-files
- https://www.geeksforgeeks.org/linux-unix/objdump-command-in-linux-with-examples/
- https://medium.com/leclevietnam/build-c-application-with-gnu-gcc-by-command-line-gnu-make-and-cmake-a9d28a6f9764
- https://cs107e.github.io/guides/binutils/
- https://man7.org/linux/man-pages/man1/objdump.1.html
- https://www.baeldung.com/linux/find-string-binary-file
