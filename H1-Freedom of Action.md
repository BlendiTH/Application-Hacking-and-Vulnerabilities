## H1 Freedom of Action, Control, and Risk Mitigation | Blendi Thaqi 24/08/2026

## a) ISMS Scope, Home Network and Study Lab

ISMS laajuuteen kuuluu oma kotiverkko ja kurssin opiskelua varten käytettävä ympäristö. Scopeen kuuluu kotiverkon perusinfrastruktuuri, kuten kotireititin sekä siihen kuuluvat Wi-fi/palomuuri toiminnot, jotka sitten toimivat rajapintana internetiin. Kurssitehtäviin käytettäviin laitteisiin kuuluu henk.koht. pöytäkone jossa on käyttöjärjestelmänä Windows 11 sekä kannettava jossa on myös Windows 11 käyttöjärjestelmänä. Molemmissa laitteissa ajetaan VirtualBoxin avulla Kali Linux -virtuaalikonetta muita koulutehtäviä varten. Lisäksi, puhelin jota käytän MFA:han ja salasanojen hallintaan.
Muita scopeen kuuluvaa tietoa ovat esim. kurssimateriaalit, eri projektirepot, muistiinpanot tehtävistä sekä eri tunnukset ja avaimet jotka on säilytettynä näillä eri laitteilla.

Kuitenkin, scopen ulkopuolelle jäävät muut kotitalouden laitteet, kuten esim. tabletti, älytelevisio ja pelikonsoli, sillä nämä eivät liity mitenkään kurssityöhön eivätkä sisällä minkäänlaista kurssidataa, jonka syystä ne ovat scope rajan ulkopuolella. Tähän myös kuuluu internet palveluntarjoajan oma infrastruktuuri sillä en omista tai hallinnoi sitö, kaikki vastuu sen turvallisuudesta on ISP:llä.

Erilaisia keskeisiä rajapintoja ovat hyvänä esimerkkinä pilvipalvelut, kuten: GitHub, johon raportoin eri projektit, sekä koulutyöt, Google Drive, jossa pidän henk.koht. dataa ja OneDrive jossa pystyn turvallisesti säilyttämään koulutöitä. Täten nämä muodostavat rajan minun oman ympäristön ja kolmannen osapuolen palveluiden välillä, sillä verkon ns. raja kulkee minun reitittimen ja palomuurin kohdalla, joka sitten pystyy erottamaan sisäisen kotiverkkoni muusta internetistä.

### Verkko- ja rajapintakaavio
Alla pystyt visuaalisesti ymmärtämään kotiverkon ja opiskelulabran rakenteen, sekä scopeen kuuluvat ja siihen kuulumattomat osat:

<img width="3117" height="2262" alt="H1 kaavio" src="https://github.com/user-attachments/assets/8a386929-6f3d-49e8-9e1e-93f44604b37d" />

### Evidence Addendum
Yllä kuvatusta scopesta, seuraavat asiat toimisivat todisteina: Kuvakaapaus reitittimen asetussivulta (Eri palomuurisäännöt, Wi-Fi salaus, jne.), VM-lista jossa näkyy eri koneet ja niiden käyttötarkoitukset, eri repo-linkit, laiteinventaario, sekä kuvakaappaus MFA asetuksista.

## b) Interested parties taulukko

| Interested Party         | Need or Requirement                                                                          | ISO 27001 Reference    | Evidence                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------- | ---------------------- | ----------------------------------------------------------- |
| Minä                     | Kurssitehtävien ja datan säilyminen jotta ne eivät katoa                                     | Operation              | Eri backupit, git-historia repoissa                         |
| Kotitalouden eri jäsenet | Ettei labraympäristö (VM) vaaranna muiden yksityisyyttä tai häiritse heidän verkon käyttöä   | Context                | Erillinen Wi-Fi/VLAN                                        |
| ISP                      | Liittymän käyttäminen sopimuksen ja käyttöehtojen mukaisesti                                 | Compliance / Context   | Voimassa oleva liittymäsopimus, sekä rekisteröidyt laitteet |
| Pilvipalveluntarjoajat   | Tilien käyttö turvallisesti, käyttöehtoja noudattaen                                         | Support                | MFA päällä, tilin turva-asetusten pitäminen päivitettyinä   |
| Oppilaitos               | Verkon käyttäminen vain kursseissa, eikä mihinkään ulkopuoliseen tai haitalliseen toimintaan | Leadership / Operation | Eristetty VM-labraympäristö ja tehtävien dokumentaatio      |
