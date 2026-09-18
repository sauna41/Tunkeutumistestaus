<img width="170" height="266" alt="image" src="https://github.com/user-attachments/assets/328d15b2-5c5b-4a64-bb24-c45c623289f1" />_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h5 Elokuu2026! Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

## h5 Elokuu2026!
#### _Nyt levitään! Opit murtamaan salasanoja._
<br>
<br>

### x) Lue/katso ja tiivistä
  [Karvinen 2022: Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)

  - Salasanoja ei tallennetta sellaisenaan syötettyinä merkkijonoina vaan _hasheina_. Hashaus toimii vain yhteen suuntaan, joten niitä ei voi muuntaa takaisin salasanaksi.
  - Hasheja voidaan kuitenkin verrata sanakirjassa oleviin sanoihin työkalujen avulla. Yksi niistä on HashCat.
  - HashCat rajaamaan käytetyn hashaus-tyypin todennäköisiin vaihtoehtoihin ```hashid -m``` -komennolla
  - Itse kräkkääminen tapahtuu ``hashcat -m <int n> '<hash>' <sanakirja> -o solved`` -komennolla. Tämä vertaa syötettyä hashia valitun sanakirjan sisältöön ja tallentaa salasanan uuteen tiedostoon jos sellainen löytyy.
  - HashCatin vahvuus on sen nopeus. Kräkkäystä voi vauhdittaa ajamalla sitä host-koneella hyödyntäen näytönohjaimen tarjoamaa vauhtia 
  
  [Karvinen 2023: Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)

  - Toinen murtamistyökalu on John the Ripper. Sen Jumbo-version voi ladata GitHubista ja kääntää lähdekoodista komennoin ``configure`` ja ``make``.
  - Artikkelissa murretaan ZIP-tiedosto, joka on suojattu salasanalla.
    - Salasanatiedot muutetaan ensin hash-muotoon _zip2john_ -työkalulla.
    - Verrataan eri salasanojen hash-arvoja kohde hashiin.
  - John tukee ZIPin lisäksi myös esimerkiksi 7z, PDF, Office, Bitlocker -formaatteja.

<br>
<br>

| HashCat | John the Ripper |
|---|--|
| hashien murtaminen | hashien sekä tiedostojen salasanojen murtaminen |
| todella nopea GPU:lla | hitaampi kuin HashCat |
| vahvuus: nopea hash-cracking | vahvuus: monipuolisempi password auting |
| hashcat -m 1400 hash.txt wordlist.txt | john --wordlist=wordlist.txt hash.txt |

<br>
<br>

_Vapaaehtoinen: € Santos et al 2017: Security Penetration Testing - The Art of Hacking Series LiveLessons: Lesson 6: Hacking User Credentials (8 videos, about 30 min)_



________________________________________________________________________________________________________________________________________________________________________________________

### a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.

Oletetaan, että salasana on "SALASANA123". Merkkijonon hash256 saadaan luotua komennolla ``echo -n 'salasana' | sha256sum``. 

 - ``-n`` vipu poistaa uuden rivin merkkijonon lopusta.
 - **TÄNNE MIKSI NÄIN TEHDÄÄN**


<img width="683" height="77" alt="sha256_sum" src="https://github.com/user-attachments/assets/c5c59ed0-b493-408b-b34a-4837be9946c7" />
<br>
<br>

Nyt meillä oli hash, mutta koska hashia voi muuttaa takaisin salasanaksi sitä täytyi alkaa vertaamaan muihin hasheihin. Manuaalilla tämä olisi työllistävää ja hidasta, joten avuksi otettiin HashCat -työkalu. 

<br>

**Asennus:** 

    sudo apt-get update
    sudo apt-get -y install hashcat wget

Eri salakirjoitusmuotoja on pitkä lista mutta HashCat kykenee päättelemään todennäköisempiä tyyppejä. Se ei kuitenkaan valitse käyttäjälle automaattisesti yhtä oikeaa, joten on hyödyllistä tuntea yleisesti käyetyt salaustavat. 

Jotta HashCat pystyy vertailemaan sille syötettyä hashia, se tarvitsee myös listan sanoista, joihin hashia verrataan. Loin tehtävää varten uuden "testikirjasto.txt" tiedoston. Sen sisältä löytyi merkkijonoja, joista vain yhden _"SALASANA123"_ tulisi olla oikea salasana.

<img width="170" height="266" alt="TESTIKIRJASTO" src="https://github.com/user-attachments/assets/9733622f-d15f-48da-b312-2c8ca68997f6" />

<br>
<br>

**Käyttö:**

    hashid -m <HASH>   // HashCat pyrkii tunnistamaan käytetyn hash-salaustyyypin
    hashcat -m <valittu tyyppi (SHA256 (1400), MD5 (0))> <hash> <sanakirjasto> < -o solved (kirjoittaa osuman erilliseen tiedostoon>


Ajoin siis ``hashcat -m 1400 '366c5c22e389a0e6a562d6ded5e21cdc166ffd571c58b7455f189145e95feae6' testikirjasto.txt -o solved`` -komennon, jolloin HashCat asettu komennon hashin vertailuun. Se alkoi hashaamaan sanakirjassa olevia sanoja ja jos osuma löytyisi, se tallentaisi sen uuteen _solved_ -tiedostoon työhakemistossa. Nyt sanakirja oli äärimmäisen lyhyt eikä vertailuja ei tarvinnut suorittaa montaa, joten suoritus oli todella verkkaisa. 


<img width="820" height="627" alt="HASHCAT_LOPPUTULOS" src="https://github.com/user-attachments/assets/7203d7d7-4aa3-4a00-b338-7918509229a5" />


Jos ``-o solved`` -vipua ei käytetä, kräkätty salasana (_apina_) näkyy suoraan HashCatin tulosteessa eikä sitä tallenneta muualle.

<img width="810" height="205" alt="ILMAN TALLENNUSTA" src="https://github.com/user-attachments/assets/82b4f211-d340-4800-a329-86706d8e3318" />

________________________________________________________________________________________________________________________________________________________________________________________


### c) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

________________________________________________________________________________________________________________________________________________________________________________________

### e) Tiedosto. Tee itse tai etsi verkosta jokin salakirjoitettu tiedosto, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi).

________________________________________________________________________________________________________________________________________________________________________________________

### f) Tiiviste. Tee itse tai etsi verkosta salasanan tiiviste, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi. Voit esim. tehdä käyttäjän Linuxiin ja murtaa sen salasanan.)

________________________________________________________________________________________________________________________________________________________________________________________

### g) Sanakirja. Oman sanakirjan teko parantaa onnistumismahdollisuuksia. Demonstroi, kuinka teet oman sanakirjan hashcat:n tai john:iin.

________________________________________________________________________________________________________________________________________________________________________________________

### h) Hash rules. Näytä esimerkki HashCatin sääntöjen käytöstä (rules).

________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.


Karvinen, T. Cracking Passwords with HashCat. 2022. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/. Luettu 17.9.2026.

Karvinen, T. Crack File Password with John. 2023. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/. Luettu 17.9.2026.
