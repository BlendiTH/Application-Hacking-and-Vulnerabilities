## H2 Break and Unbreak | Blendi Thaqi 02/09/2026

## Ympäristö

**OS**: Kali GNU/Linux Rolling

**Browser**: Mozilla Firefox 140.11.0esr (64-bit)

**Hardware**: Virtualbox memory used 8 GB

**Processor**: AMD Ryzen 7 7800X3D | 4 cores used

**GPU**: NVIDIA GeForce RTX 5070 Ti 16GB

**Disk**: 40 GB

**Network**: NAT

## Artikkelit ja niiden tiivistelmät

**OWASP Top 10: [A01 Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)** 
- Yleisin verkkohaavoittuvuus on puutteellinen pääsynhallinta, jonka myötä luvattoman tiedon katselu ja muokkaaminen mahdollistuu. Jotta se pystytään torjumaan, sovellusten pitäisi oletuksena estää pääsy kaikkeen ei-julkiseen tietoon.

**Karvinen 2023: [Find Hidden Web Directories](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)** 
- ffuf on nopea työkalu, jolla pystyt etsimään palvelimelta piilotettuja hakemistoja kokeilemalla sanalistan avulla automaattisesti eri vaihtoehtoja.

**PortSwigger: [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control)**
- Puutteet sivuston käyttöoikeuksissa (väärin rajattu), peruskäyttäjä pystyy pääsemään käsiksi esim. ylläpitäjän toimintoihin tai jopa muiden käyttäjien tietoihin. 

**Karvinen 2006:[ Report Writing](https://terokarvinen.com/2006/raportin-kirjoittaminen-4/)** 
- Raportit pitää kirjoittaa niin selkeästi, vaihe vaiheeltaan, että kuka tahansa pystyy toistamaan raportin sisällön itse, ja ymmärtämään täysin mitä on tehty.

## Break into ``010-staff-only``

Aloitetaan lataamalla ja purkamalla `teros-challenges.zip` tiedosto, minkä jälkeen siirrytään terminaalissa `challenges/010-staff-only` hakemistoon.

> Lataa tiedosto täältä: [Karvinen 2024: Hack'n Fix](https://terokarvinen.com/hack-n-fix/)

```bash
cd Desktop
unzip teros-challenges.zip
cd challenges/010-staff-only
```

<img width="459" height="174" alt="Screenshot 2026-09-02 002529" src="https://github.com/user-attachments/assets/f36f3bd0-07ad-4d9a-981e-f5734b80c69e" />

<img width="465" height="97" alt="Screenshot 2026-09-02 002544" src="https://github.com/user-attachments/assets/afafe9b0-7c60-41fa-90a8-8739c9919348" />

1. Seuraavaksi, käynnistetään kohdesovellus ja avataan sen etusivu selaimessa *(Itsellä käytössä: Mozilla Firefox 140.11.0esr)*.

```bash
python3 staff-only.py
```

<img width="633" height="172" alt="Screenshot 2026-09-02 003041" src="https://github.com/user-attachments/assets/6fd5eef8-ce0d-4cb1-8f86-f81b259ec6aa" />

<img width="1096" height="349" alt="Screenshot 2026-09-02 004043" src="https://github.com/user-attachments/assets/abeb3f1d-41be-492a-8000-204d9767065b" />

2. Tavoitteena oli löytää haavoittuvuus pelkästään tämän web käyttöliittymän kautta ilman että tarkistelemme lähdekoodia. Aloitin syöttämällä PIN-kenttään aiemmin opitun SQL-injektion: `' OR 1=1 --`. Selain kuitenkin esti tämän, koska kenttä oli määritelty HTML:ssä numerokentäksi ja client side validointi ei päästä tekstimerkkejä läpi.  

<img width="685" height="265" alt="Screenshot 2026-09-02 005155" src="https://github.com/user-attachments/assets/e4aedf92-717a-4708-afeb-25b27ca1664a" />

3. Koska voimme nyt päätellä että sivusto käyttää client side validointia, voimme todeta että sen voi kiertää helposti koska se toimii vain käyttäjän selaimessa. Aloitin avaamalla selaimesta kehittäjätyökalut **(Inspect Element / F12)**. Etsin sitten kentän HTML koodin, ja vaihdoin siellä olevan atribuutin `input type="number"` arvoksi: `input type="text"` 

<img width="413" height="69" alt="Screenshot 2026-09-02 005706" src="https://github.com/user-attachments/assets/9207ffbb-b401-4df2-99f5-8a8e36bac754" />

4. Tämän jälkeen kokeilin uudelleen syöttämällä saman SQL-injektion, ja kenttä hyväksyi sen, saimme yhden salasanan, ei kuitenkaan sitä mitä halusimme. Palvelin kuitenkin suoritti syötteen ilman validoimatta sitä, ja sovellus palautti salasanan ilman oikeaa PIN koodia joten olemme oikealla tiellä. 

<img width="596" height="151" alt="Screenshot 2026-09-02 005953" src="https://github.com/user-attachments/assets/65aa0c9a-382f-4606-8ae9-b10bc73d2fb4" />

5. Huomasin, että sovellus palauttaa aina vain yhden rivin kerrallaan, joten kokeilin sitten kohdistaa haun tarkasti yhteen riviin lauseella: `' OR 1=1 LIMIT 2,1 --`
   **Selitys:** LIMIT 2,1 tarkoittaa että ohita 2 riviä ja näytä vain yksi, testasin tätä numeroa kunnes sain oikean. [Lähde mistä sain tiedon](https://www.sqlitetutorial.net/sqlite-limit/)

6. Saimme sitten oikean admin salasanan:

<img width="1016" height="308" alt="Screenshot 2026-09-02 022036" src="https://github.com/user-attachments/assets/2a5970f4-b35e-4e0d-8b25-376d909be5e4" />

## b) Fix the `010-staff-only` vulnerability

Nyt kun pääsimme murtautumaan sisään a) kohdassa, voimme jatkaa korjaamalla haavoittuvuus lähdekoodista.

1. Avataan tiedosto `staff-only.py` ja etsitään ensin mitä ohjelmointikirjastoa tässä tiedostossa on käytetty. Voimme todeta heti **riviltä 8** että tässä on käytetty SQLAlchemy:ä

<img width="737" height="60" alt="Screenshot 2026-09-02 023604" src="https://github.com/user-attachments/assets/6d26021b-93cb-41ae-a9e7-e41c8cd3e35f" />

2. Jatketaan etsimällä kohtaa, joka rakentaa SQL kyselyn, joka löytyy riviltä 22:

<img width="553" height="118" alt="Pasted image 20260902030034" src="https://github.com/user-attachments/assets/1f85c1ce-90d8-46b9-ae45-5925b220f73c" />

Haavoittuvuus syntyy tässä siten, että käyttäjän syöte (eli tuo pin) liitetään suoraan SQL kyselyyn kun se pistetään lomakkeeseen. Näin myös jos käyttäjän syötteessä on SQL syntaksia se voi muuttaa koko kyselyn rakennetta.

Tämän pystymme korjaamaan käyttämällä [SQLAlchemyn bind parameteria](https://docs.sqlalchemy.org/en/20/core/sqlelement.html#sqlalchemy.sql.expression.text): 
> Tässä vaihdoimme rivin 22 sekä rivin 25:

```
sql = text("SELECT password FROM pins WHERE pin= :pin")
res=db.session.execute(sql, {"pin": pin})
```

<img width="558" height="115" alt="Pasted image 20260902030327" src="https://github.com/user-attachments/assets/4490e4dc-f8f9-47d5-9e95-8bb6ac219d41" />

Tarkemmin, tuo `:pin` on kuin tyhjä paikka kyselyssä, joka sitten täytetään vasta erikseen suorituksen yhteydessä, tietokanta täten tietää aina, että tuo on täytettyä arvoa ja pelkkää dataa eikä itse osana komentoa!

3. Nyt voimme jatkaa testaamaan toimiko meidän korjaus koodiin. Kun yritin uudelleen sivustolla käyttää samoja SQL-injektioita sain vastaukseksi sivustoon että "Your password is **(not found)**" eli korjaus toimi!

<img width="611" height="154" alt="Pasted image 20260902031137" src="https://github.com/user-attachments/assets/d35a41bc-ddec-48e8-9098-8de2c742fad1" />

## c) Solve dirfuzt-1

Tässä tavoitteenamme on löytää kaksi piiloitettua URL-osoitetta `dirfuzt-1` tiedostosta: admin sivu sekä versionhallintaan liittyvä sivu

1. Aloitetaan lataamalla sekä käynnistämällä tiedosto
> **Latausohje:** Karvinen 2023: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

```bash
wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-0
chmod u+x dirfuzt-0
./dirfuzt-0
```

<img width="1096" height="464" alt="Pasted image 20260902032058" src="https://github.com/user-attachments/assets/89f54fb6-bab7-45d1-85ed-2cb2d7a0ed9c" />

2. Sitten asennetaan ffuf artikkelin ohjeiden mukaisesti, tein latauksen *ilman paketinhallintaohjelmaa*:

```bash
wget https://github.com/ffuf/ffuf/releases/download/v2.0.0/ffuf_2.0.0_linux_amd64.tar.gz
tar -xf ffuf_2.0.0_linux_amd64.tar.gz
./ffuf
```

<img width="628" height="84" alt="Pasted image 20260902032242" src="https://github.com/user-attachments/assets/e42ea273-e556-4eb9-b481-4cd838822170" />

3. Ladataan seuraavaksi sanalista joka mahdollistaa sen että saamme ffuf:in kokeilemaane erilaisia mahdollisia verkkopolkuja yksi kerrallaan, jotka ovat tässä sanalistassa
> Muita sanalistoja: [Daniel Miessler SecLists](https://github.com/danielmiessler/seclists)

```bash
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt
```

<img width="630" height="263" alt="Pasted image 20260902032938" src="https://github.com/user-attachments/assets/a22066f0-3cde-4add-8b87-1a08cd3ef79a" />

4. Voimme nyt viimein aloittaa 'fuzzaus', aloitetan määrittelemällä ``./ffuf`` komentoon ``-w `` valitsimella komennossa käytettyä sanalistaa.

```
./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ
```

<img width="652" height="364" alt="Pasted image 20260902033515" src="https://github.com/user-attachments/assets/584d1b40-4d5c-4098-8e9f-cbecf609d552" />

5. Ensimmäinen ajo antoi hirveän määrän tuloksia, ja me ei tarvita niitä kaikkia, tässä harjoitustiedostossa "ei olemassa" olevat polut palauttavat melkeinpä aina samankokoisen vastauksen, joissa niiden koko on 132 tavua, joten valitsin `-fs 132` poistaa ne tuloksista, kokeillaan sitä.

```bash
./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 132
```

<img width="655" height="451" alt="Pasted image 20260902033832" src="https://github.com/user-attachments/assets/fdd4ba56-fae5-4943-84dc-e27fb2a8e6e5" />

Kuten näkyykin, ffuf löysi meille admin verkkopolun!
## d) Break into `020-your-eyes-only`

Jatketaan samalla tyylillä kuin a) kohdassa, aloitin siirtymällä haasteen hakemistoon ja luomalla Python virtuaaliympäristön, josta jatkoin lataamaan sovelluksen vaatiman Django-version (4.2). Siirryin sitten tästä `manage.py` tiedoston sisältävään hakemistoon `logtin` ja käynnistin palvelimen:

<img width="667" height="182" alt="Pasted image 20260902035906" src="https://github.com/user-attachments/assets/f23f335b-e557-4d97-a42a-dd2666fdd493" />

1. Aloitin sivustolla tekemällä oman tavallisen käyttäjän, tavoitteella: päästä hallintakonsoliin. Sivua ei normaalisti ole linkitetty, joten kokeillaa fuzzauksella löytää piiloitettu URL-osoite.

<img width="1202" height="442" alt="Pasted image 20260902040243" src="https://github.com/user-attachments/assets/519d414d-4691-4e8a-99cd-6ed34f2beb4a" />

2. ffuf komennolla saimme esille admin-consolen, löysimme piiloitetun url-osoitteen!
```bash
ffuf -w common.txt -u http://127.0.0.1:8000/FUZZ
```

<img width="665" height="69" alt="Pasted image 20260902040400" src="https://github.com/user-attachments/assets/9b8c08ad-db6d-480d-8c06-67f75fd4d9b6" />

3. Kun käymme sivustolla, voimme nähdä että löysimme oikean sivuston ja pääsimme oikeaan paikkaan, saimme sen tehtyä!

<img width="669" height="315" alt="Pasted image 20260902040530" src="https://github.com/user-attachments/assets/5f4e35a5-399e-4b4e-bb5e-cbb633f686dc" />

Tiivistettynä, käyttäjäoikeudet olivat määritelty väärin, joten ainoa asia mitä tässä piti tehdä oli, kirjautua sisään, kokeilla ffuf:in kautta jos sieltä löytyy admin-console linkki (tai vastaava) ja siten pistää se osoitteen perään. :)
## Lähteet

- https://terokarvinen.com/hack-n-fix/
- https://owasp.org/Top10/A01_2021-Broken_Access_Control/
- https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/
- https://portswigger.net/web-security/access-control
- https://terokarvinen.com/2006/raportin-kirjoittaminen-4/
- https://github.com/danielmiessler/seclists
- https://docs.sqlalchemy.org/en/20/core/sqlelement.html#sqlalchemy.sql.expression.text
- https://www.sqlitetutorial.net/sqlite-limit/
