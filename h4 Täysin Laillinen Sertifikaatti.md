_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)_

_Päivämäärä: 11.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h4 Täysin Laillinen Sertifikaatti. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


x) Lue/katso ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva kustakin artikkelista - ei pitkiä esseitä. Kannattaa lisätä myös jokin oma ajatus, idea, huomio tai kysymys.)

OWASP 2021: OWASP Top 10:2021
  A01:2021 – Broken Access Control (IDOR ja path traversal ovat osa tätä)
PortSwigget Academy:
  Insecure direct object references (IDOR)
  Path traversal
  Cross-site scripting

________________________________________________________________________________________________________________________________________________________________________________________


### a) Totally Legit Sertificate. Asenna OWASP ZAP, generoi CA-sertifikaatti ja asenna se selaimeesi. Laita ZAP proxyksi selaimeesi. Laita ZAP sieppaamaan myös kuvat, niitä tarvitaan tämän kerran kotitehtävissä. Osoita, että hakupyynnöt ilmestyvät ZAP:n käyttöliittymään. (Voi vaatia Firefox about:config network.proxy.allow_hijacking_localhost. Foxyproxy laittoi tämän aiemmin päälle itse. Kalin Firefox ESR oli viimeksi ongelmia Foxyproxyn kanssa - vaihtoehtona on asettaa Proxy käsin Settings, hakusana "proxy")

________________________________________________________________________________________________________________________________________________________________________________________


### b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn. (Läksyssä ohjataan varmaankin PortSwigger Labs ja localhost.)

________________________________________________________________________________________________________________________________________________________________________________________


#### PortSwigger Labs: ratkaise tehtävät

Cross Site Scripting (XSS)
  c) Reflected XSS into HTML context with nothing encoded
  d) Stored XSS into HTML context with nothing encoded
  e) Selitä esimerkin avulla, mitä hyökkääjä hyötyy XSS-hyökkäyksestä. Alert("Hei Tero!") ei vielä tarjoa kummoista pääsyä. (Tässä alakohdassa ei tarvitse tehdä testejä tietokoneella, pelkkä lyhyt ja selkeä selitys riittää.)
Path traversal
  f) File path traversal, simple case. Laita tarvittaessa Zapissa kuvien sieppaus päälle.
  g) File path traversal, traversal sequences blocked with absolute path bypass
  h) File path traversal, traversal sequences stripped non-recursively
Insecure Direct Object Reference (IDOR)
  i) Insecure direct object references

  ________________________________________________________________________________________________________________________________________________________________________________________

#### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-eternalhomework. Luettu 11.9.2026.

OWASP 2021: OWASP Top 10:2021. Luettavissa: https://owasp.org/Top10/A01_2021-Broken_Access_Control/. Luettu 11.9.2026.

Insecure direct object references (IDOR). Portswigger Academy. https://portswigger.net/web-security/access-control/idor. Luettu 11.9.2026.

Path traversal. Portswigger Academy. Luettavissa: https://portswigger.net/web-security/file-path-traversal. Luettu 11.9.2026.

Cross-site scripting. Portswigger Academy. Luettavissa: https://portswigger.net/web-security/cross-site-scripting. Luettu 11.9.2026.

Cross-site scripting Labs. Portswigger Labs. Luettavissa: https://portswigger.net/web-security/all-labs#cross-site-scripting:~:text=Cross%2Dsite%20scripting,-LAB. Luettu 11.9.2026.

Path Traversal Labs. Portswigger Labs. Luettavissa: https://portswigger.net/web-security/all-labs#path-traversal:~:text=Path%20traversal,-LAB. Luettu 11.9.2026.
