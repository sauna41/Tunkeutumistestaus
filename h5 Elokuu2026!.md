_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h5 Elokuu2026! Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

## h5 Elokuu2026!
####_Nyt levitään! Opit murtamaan salasanoja._

### x) Lue/katso ja tiivistä
  [Karvinen 2022: Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)

  - Salasanoja ei tallennetta sellaisenaan syötettyinä merkkijonoina vaan _hasheina_. Hashaus toimii vain yhteen suuntaan, joten niitä ei voi muuntaa takaisin salasanaksi.
  - Hasheja voidaan kuitenkin verrata sanakirjassa oleviin sanoihin työkalujen avulla. Yksi niistä on HashCat.
  - HashCat rajaamaan käytetyn hashaus-tyypin todennäköisiin vaihtoehtoihin ```hashid -m``` -komennolla
  - Itse kräkkääminen tapahtuu ``hashcat -m <int n> '<hash>' <sanakirja> -o solved`` -komennolla. Tämä vertaa syötettyä hashia valitun sanakirjan sisältöön ja tallentaa salasanan uuteen tiedostoon jos sellainen löytyy.
  - HashCattia voi vauhdittaa ajamalla sitä host-koneella hyödyntäen näytönohjaimen tarjoamaa vauhtia.    
  
  [Karvinen 2023: Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)

  - asd


  Vapaaehtoinen: € Santos et al 2017: Security Penetration Testing - The Art of Hacking Series LiveLessons: Lesson 6: Hacking User Credentials (8 videos, about 30 min)

________________________________________________________________________________________________________________________________________________________________________________________

a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.


________________________________________________________________________________________________________________________________________________________________________________________


c) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

________________________________________________________________________________________________________________________________________________________________________________________

e) Tiedosto. Tee itse tai etsi verkosta jokin salakirjoitettu tiedosto, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi).

________________________________________________________________________________________________________________________________________________________________________________________

f) Tiiviste. Tee itse tai etsi verkosta salasanan tiiviste, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi. Voit esim. tehdä käyttäjän Linuxiin ja murtaa sen salasanan.)

________________________________________________________________________________________________________________________________________________________________________________________

g) Sanakirja. Oman sanakirjan teko parantaa onnistumismahdollisuuksia. Demonstroi, kuinka teet oman sanakirjan hashcat:n tai john:iin.

________________________________________________________________________________________________________________________________________________________________________________________

h) Hash rules. Näytä esimerkki HashCatin sääntöjen käytöstä (rules).

________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.


Karvinen, T. Cracking Passwords with HashCat. 2022. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/. Luettu 17.9.2026.

Karvinen, T. Crack File Password with John. 2023. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/. Luettu 17.9.2026.
