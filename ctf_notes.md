# Muistiinpanot lipunryöstöön

### ffuf

- peruskäyttö: ffuf -w (wordlist.txt) -u https://URL/FUZZ

flagit:

- -mc = match codes, vain halutut statuskoodit
- -fc 404 = filter statuskoodit pois
- filtterit: -fc, fw
- rekursio: https://URL/fuzz -recursion
- Host fuzzing: https://TARGET/ -H "Host: FUZZ.example.com"



### Idor

Broken access control
- hakemistopolun muuttaminen ../
- absoluuttinen polku /etc/passwd tai muu hakusana



### SQL-injektiot

- ' merkkijonon lopettaminen, -- kommentin aloittaminen
