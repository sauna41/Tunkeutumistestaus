_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h5 Fuzzy. Opettajana toimi Tero Karvinen.

________________________________________________________________________________________________________________________________________________________________________________________

x) Lue/katso/kuuntele. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva kustakin artikkelista. Kannattaa lisätä myös jokin oma ajatus, idea, huomio tai kysymys. [Päivitys 2026-09-25 w39 Fri: laitoin Hoikkalan kalvot tähän mukaan, kun sain luvan.]

  Hoikkala 2026: Fuzzing with Fuff, kalvot joohoin esityksestä kurssilta.
  Vapaaehtoista lisälukemistoa
      Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf
      Hoikkala 2026: ffuf README.md
      Hoikkala "joohoi" 2020: Still Fuzzing Faster (U fool). In HelSec Virtual meetup #1. (Noin tunnin mittainen)


________________________________________________________________________________________________________________________________________________________________________________________


Vaultline https://ffuf.io.fi/play

a) Tallenna itsellesi kopio säännöistä. Kirjoita omin sanoin,
    Scope. Mikä on kohde?
    Rules of engagement. Mitä sille saa tehdä, eli mitä tai millaisia menetelmiä saa käyttää?
    Mihin oikeutesi tehdä tietoturvatestausta tähän kohteeseen perustuu?
    Riskit ja mitigointi. Tuo palvelin on Internetissä. Tunnista lyhyesti riskit ja niiden mitigointi ennen käytännön harjoittelua.
________________________________________________________________________________________________________________________________________________________________________________________


b) Asenna ffuf versio, joka tukee aivan uutta preflight-ominaisuutta.
c1) Content discovery (Vaultline https://ffuf.io.fi/play tehtävät on numeroitu näin, käytetään tässä samoja.).

 ________________________________________________________________________________________________________________________________________________________________________________________
     
c2) The interesting non-200

________________________________________________________________________________________________________________________________________________________________________________________

c3) Recursion
c4) Virtual hosts
c9) The login you cannot replay (Has preflight! Has CSRF token!)
Vapaaehtoisena muut Vaultlinen tehtävät


________________________________________________________________________________________________________________________________________________________________________________________

#### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.

