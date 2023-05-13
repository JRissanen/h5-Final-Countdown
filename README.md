# h5-Final-Countdown
This is a repository for Penetration testing course 2023 Tunkeutumistestaus ict4tn027-3009 course exercise h5

---
Olen tehnyt kaikki harjoitukset omalla tietokoneellani. </br>

Koneen infot: </br>
Edition: Windows 10 Pro </br>
Version: 22H2 </br>
OS build: 19045.2728 </br>
Processor: Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz   3.19 GHz </br>
Installed RAM: 16,0 GB </br>
System type: 64-bit operating system, x64-based processor </br>

Virtuaalikoneet ovat Oracle VM VirtualBoxissa, jonka versio on: Version 6.1.40. </br>

Virtuaalikoneiden infot: </br>

Metasploitable: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 1024 MB </br>
Video Memory: 16 MB </br>
Processors: 1 CPU

Kali: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 2024 MB </br>
Video Memory: 128 MB </br>
Processors: 2 CPU

Varmistan aina ennen tehtävien aloittamista, että molemmat virtuaalikoneet ovat yhteyksissä toisiinsa pingaamalla kali-koneelta metasploitable2-konetta: `ping 192.168.60.3`. </br>
Varmistan myös aina, ettei kumpikaan virtuaalikone saa verkkoyhteyttä pingaamalla google.com sekä googlen ensisijaista dns-serveriä 8.8.8.8: `ping google.com` ja `ping 8.8.8.8`.

Jos haluat enemmän tietoa minun tunkeutumistestausympäristöstä, niin voit lukea artikkelini: </br>
https://github.com/JRissanen/h1-OmaLabra

---

## x) Lue ja tiivistä (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

### Karvinen 2022: [Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)

* Järjestelmät eivät tallenna salasanoja, vaan tiivisteitä (hash).
  * Esim. `f2477a144dff4f216ab81f2ac3e3207d`.
* Tiivistettyä salasanaa ei saa enää käännettyä takaisin "normaaliksi" salasanaksi.

* Salasanojen murtaminen onnistuu "Hashcat" ja "Hashid" -ohjelmilla.
* Asenna lisäksi jokin iso sanakirja täynnä salasanoja.
  * "Rockyou" -niminen sanakirja on yksi suosituimmista. (Yli 14 miljoonaa salasanaa/sanaa)

* Tiivistetyyppi pitää olla tiedossa, jotta Hashcat voi murtaa `-m` parametrin numeron.
  * Komennolla: `$ hashid -m 6b1628b016dff46e6fa35684be6acc96` pystyy analysoimaan kyseistä tiivistettä.
  * Tuloste:
  * ```
    Analyzing '6b1628b016dff46e6fa35684be6acc96'
    [+] MD2 
    [+] MD5 [Hashcat Mode: 0]
    [+] MD4 [Hashcat Mode: 900]
    ...
    ```
  * Usein oikea tiivistetyppi on hashid:n antamien kolmen ensimmäisen vaihtoehdon joukossa.

* MD5 on yleisempi kuin MD2 ja MD4.
* Tulosteen perusteella tiivisteen voisi murtaa komennolla: 
* `$ hashcat -m 0 '6b1628b016dff46e6fa35684be6acc96' rockyou.txt -o solved`.
  * `-m 0` tulee aiemman hashid:n tulostuksesta MD5 kohdasta: `[+] MD5 [Hashcat Mode: 0]`.
  * `-o solved` tallentaa hashcatin ratkaisun tyhjään tiedostoon nimeltä "solved". </br>
(Karvinen, 2022).

---

### Karvinen 2023: [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)

* John The Ripper on sanakirjahyökkäystä hyödyntävä ohjelma, jonka avulla voi murtaa salasanoja.
  * Toiminnan kannalta oleellisia työkaluja: </br>
    <img width="310" alt="Screenshot_1" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/f94e09e2-11c4-47bb-80f5-2a42f02fbb88"> (Karvinen, 2023).

* John The Ripper, Jumbo version lataaminen onnnistuu komennolla:
  * `$ git clone --depth=1 https://github.com/openwall/john.git`.
   * `--depth=1` -parametri säästää latausaikaa kopioimalla vain viimeisimmät versiot tiedostoista.
* Konfigurointi ja kasaaminen (compile) onnistuu komennoilla:
  * ```
    $ cd john/src/	
    $ ./configure
    ```
  * `$ make -s clean && make -sj4`

* ZIP-tiedostojen murtaminen John The Ripperillä on kaksiosainen prosessi.
  * Ensin pitää purkaa ZIP-tiedosto tiivisteeksi.
   * Onnistuu komennolla: `$ $HOME/john/run/zip2john esimerkki.zip >esimerkki.zip.hash`.
  * Sitten vain Johnilla sanakirjahyökkäys tiivisteeseen.
   * Onnistuu komennolla: `$ $HOME/john/run/john esimerkki.zip.hash`.
  * Tuloste on pitkä, mutta oleellinen tieto (salasana) on aika alhaalla tulosteessa.
   * Näyttää esimerkiksi: `butterfly        (esimrkki.zip/secretFiles/SECRET.md)`. </br>
(Karvinen, 2023).

---

### Karvinen 2023: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

* Fuff on joohoi:n (Joona Hoikkala) kehittämä "web fuzzer" -työkalu, joka mahdollistaa salaisten hakemistojen löytämisen.

* Fuff vaatii sanakirjan toimiakseen.
  * Esim Daniel Miesslerin sanakirjan voi ladata:
   * `$ wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt`.

* Fuff on todella nopea.
* Kaikki parametrit voi nähdä: `$ ./fuff`.

* Fuff toimii esimerkiksi komennolla:
  * `$ ./ffuf -w common.txt -u http://<esimerkki.com>/FUZZ`.
   * `-w` määrittää sanalistan/sanakirjan.
   * `-u` määrittää url-osoitteen. </br>
(Karvinen, 2023).

---

## y) The SUPER ultimate Hakk3r Che33tsheet 0.0.1. Tee tiivistelmä omista ja kavereiden parhaista tunketumistekniikoista. Ole täsmällinen, liitä komennot mukaan. Tämän kohdan vastaus lienee pidempi kuin aiempien x-tehtävien. Viittaa lähteisiin. Tässä alakohdassa ei tarvitse ajaa komentoja tietokoneella.

* Turvallinen ympäristö
  * Tunketumista pitää aina harrastaa turvallisessa ympäristössä, siihen opin tekemään oman ympäristön [Tero Karvisen](https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h1-omalabra) tunkeutumistestaus kurssin ensimmäisellä oppitunnilla.
  * Oman ympäristöni näkee [täältä](https://github.com/JRissanen/h1-OmaLabra).

* Metasploitable, [Tero Karvinen](https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/)
  * Täynnä hyviä exploitteja ja loistava lähtökohta tunkeutumistestaukseen.
  * Käynnistyy komennolla: `msfdb run`.
  * Uuden työtilan saa tehtyä komennolla: `workspace --add <haluamasi nimi>.`
  * Komennolla: `search` löytää exploitteja.
  * Komennolla: `use` voi ajaa exploitteja.
  * Komennolla `options` näkee exploitin vaatimukset.

* Nmap, [Tero Karvinen](https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/) sekä [StationX](https://www.stationx.net/nmap-cheat-sheet/)
  * Yleisin porttiskannaus työkalu.
  * Toimii komennolla: `sudo nmap <kohde ip-osoite>`.
  * Hyödyllisiä parametreja:
   * `-A` "aggressiivinen" skannaus (käyttöjärjestelmä, versio, skripiti skannaus ja traceroute).
   * `-sV` yrittää tunnistaa porttia käyttävän palvelun version.
   * `-sC` skannaa oletus scripteillä (oletus NSE=Nmap Scipting Engine documentation). 
   * `-p` tietyn portin skannaus.
   * `-o <tiedoston nimi>` tallentaa skannauksen tuloksen haluttuun tiedostoon, aktiiviseen hakemistoon.

---

## a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.

Seurasin tätä tehtävää tehdessäni Tero Karvisen artikkelia: [Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/).

Aloitin päivittämällä paketit ja lataamalla Hashcatin sekä Hashid:n. </br>
```
$ sudo apt-get update
$ sudo apt-get -y install hashid hashcat
```

Sitten loin uuden hakemsiton tehtävää varten ja siirryin sinne. </br>
```
$ mkdir hash_hash
$ cd hash_hash
```

Sitten latasin `wget` työkalun avulla Daniel Miesslerin ["Rockyou"](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases) -nimisen sanakirjan. </br>
`$ wget https://github.com/danielmiessler/SecLists/raw/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz`. </br>
Purin kompressoidun tiedoston komennolla: `$ tar xf rockyou.txt.tar.gz`. </br>
Ja lopuksi poistin purkamisen jälkeen turhaksi jääneen tiedoston komennolla: `$ rm rockyou.txt.tar.gz`.

Seuraavaksi menin [OnlineHashtoolsin](https://onlinehashtools.com/generate-random-md5-hash) sivulle, jossa pystyi generoimaan satunnaisia MD5 tiivisteitä. </br>

<img width="821" alt="Screenshot_1" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/e3bdaa4e-0846-4d3a-9a96-a7efb85ce40b">

Kopioin sivulta ylimmän tiivisteen ja ajattelin testata Hashcatin ja Hashid:n toimivuutta sillä. </br>
Annoin siis seuraavaksi komennon `$ hashid -m f838443d16a64e0ab95433cafa287bd9`.

<img width="806" alt="Screenshot_2" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/984f5700-6de5-4c2b-835c-8faddd1b0dd4">

Tiedän jo valmiiksi että tiiviste on MD5, koska etsin tarkoituksella MD5 tiivisteen. </br>
Seuraavaksi ajoin komennon: `$ hashcat -m 0 'f838443d16a64e0ab95433cafa287bd9' rockyou.txt -o ratkaisu`. </br>
Tällä komennolla oletin saavani tiivisteen purettua ja sitä vastaavan sanan selville, sekä tallennettua sen pelkkänä tekstinä tiedostoon nimeltä "ratkaisu".
Hashcat ei kuitenkaan pystynyt purkamaan kyseistä tiivistettä, joten ehkä se ei ole lataamassani rockyou.txt -tiedostossa?

<img width="819" alt="Screenshot_3" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/85ebe6a0-e369-412d-934f-29c576858adf">

Jos tiivisteen purku olisi onnistunut, "Status" kohdassa olisi lukenut "Cracked", eikä "Exhausted". </br>
Lisäksi hakemistossa olisi tiedosto nimeltä "ratkaisu", jossa olisi tiivistettä vastaava sana.

Kokeilin [MD5 Hash Generatorin](https://www.md5hashgenerator.com) avulla luoda jostain sanasta tiivisteen, jonka oletin löytyvän rockyou.txt sanalistasta.

<img width="821" alt="Screenshot_5" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/e183e8b4-dd16-4606-9068-6b3a8bec7130">

Sitten ajoin saman komennon, mutta tällä kertaa eri tiivisteellä: </br>
`$ hashcat -m 0 '6fd5ab146e72e4446bbb6aa069d6c81c' rockyou.txt -o ratkaisu`.

<img width="1136" alt="Screenshot_6" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/8f951a29-8e47-456d-b2e0-dc9ee1f69351">

Nyt tuloste vastasi oletustani, joten voin todeta, että hashcat ja hashid toimivat.

---

## b) Salainen, mutta ei multa. Ratkaise dirfuzt-1 artikkelista Karvinen 2023: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

Seurasin tässä tehtävässä Tero Karvisen artikkelia: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/).

Aloitin luomalla uuden hakemsiton tehtävää varten ja siirryin sinne. </br>
`$ mkdir dirfuzt_1`.
`$ cd dirfuzt_1`.

Sitten latasin `wget` työkalun avulla "dirfuzt-1" -tiedoston Teron sivuilta. </br>
`$ wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1`.

Muutin oikeuksia komennolla: `chmod u+x dirfuzt-1`. </br>
Sitten käynnistin verkkosivun: `./dirfuzt-1`. </br>

<img width="1420" alt="Screenshot_1" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/41d568f8-a2c5-4c1a-ba67-de6670afeda1">

Sitten latasin ffufin: </br>
`$ wget https://github.com/ffuf/ffuf/releases/download/v2.0.0/ffuf_2.0.0_linux_amd64.tar.gz`. </br>
Purin tiedoston: </br> 
`$ tar -xf ffuf_2.0.0_linux_amd64.tar.gz`.

Seuraavaksi latasin Daniel Miesslerin [hakemistolsitan](https://github.com/danielmiessler/SecLists): </br>
`wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt`.

Teron artikkelia seuratessa oli selvää, että jos käyttäisin yleisintä hakemistojen etsimis tapaa ffufilla: </br>
`$ ./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ`, niin tuloste olisi todella pitkä ja sisältäisi todella paljon turhaa infoa. </br>
Sen sijaan käytin artikkelissakin neuvottua `-fs` parametria, jonka avulla pystyy suodattamaan tulosetta HTTP vastauksen koon mukaan. </br>
Suurin osa turhista HTTP vastauksista olivat 154 tavua kooltaan, joten suodatin sen mukaan: </br>
`./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 154`.

<img width="938" alt="Screenshot_3" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/1a7d2bc5-5cbb-4bd4-b082-88326efedfbd">

Näin löytyi ainakin "wp-admin" sekä muutama eri ".git" sivu. </br>
Kokeilin "wp-admin" käyttäjää sekä ensimmäistä ".git" sivua ja ne olivat oikeat. </br>
Molemmilta sivuilta löytyi Teron jättämä lippu ja siitä tiiviste.

<img width="1447" alt="Screenshot_4" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/2ad33eb6-0292-4c9f-ad64-d8c0ce18f290">

Kopioin liput talteen ja mursin ne Hashcatin avulla. </br>
`hashcat -m 0 3364c855a2ac87341fc7bcbda955b580 rockyou.txt -o wp-admin`. </br>
`hashcat -m 0 3cc87212bcd411686a3b9e547d47fc51 rockyou.txt -o git`. </br>
Vastauksiksi sain: wp-admin = peruna ja .git = raindrop.

<img width="1208" alt="Screenshot_5" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/35d4e3a9-001a-4c4f-8c03-398f33292151">

---

### c) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

Seurasin tässä tehtävässä Tero Karvisen artikkelia: [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/).

Aloitin tekemällä tehtävälle oman hakemiston, siirtymällä sinne ja lataamalla vaaditut työkalut/ohjelmat: </br>
`$ mkdir john_the_ripper`. </br>
`$ cd john_the_ripper`. </br>
`$ sudo apt-get -y install micro bash-completion git build-essential libssl-dev zlib1g zlib1g-dev zlib-gst libbz2-1.0 libbz2-dev atool zip wget`.

Seuraavaksi latasin Jumbo version John The Ripperistä, koska se tukee useita tiedostotyyppejä: </br>
`$ git clone --depth=1 https://github.com/openwall/john.git`.
 * `--depth=1` -parametri säästää latausaikaa kopioimalla vain viimeisimmät versiot tiedostoista.

Seuraavaksi siirryin "john/src" hakemistoon, jonka juuri latasin: `$ cd john/src/	`. </br>
Ja ajoin konfiguraation: `$ ./configure`.

<img width="813" alt="Screenshot_1" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/f5da8129-2735-42f1-a326-7b6c105ee069">

Seuraavaksi konfiguraation jälkeen john ehdottaa kasaamista komennolla: </br>
`$ make -s clean && make -sj4`, joten tein sen.

Kasaamisprosessi kesti hetken, mutta onnistui lopulta. </br>
Sitten käynnistin ohjelman komennolla: `$ ./run/john`.

<img width="1057" alt="Screenshot_2" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/38815085-ad7b-4d4d-a703-f7483bd66b04">

Seuraavaksi tein "/john" hakemistoon Zip-tiedoston nimeltä "break_this" ja salasin sen salasanalla: "Christina".

<img width="1072" alt="Screenshot_4" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/633dcb69-b211-4a28-9be0-67ff66aba53e">

Sitten oli enää jäljellä kyseisen tiedoston purkaminen. </br>
Ensin tiedostosta piti tehdä tiiviste komennolla: `$ ./run/zip2john break_this.zip >break_this.zip.hash`. </br>
Ja lopuksi tiivisteen murtaminen komennolla: `$ ./run/john break_this.zip.hash`.

<img width="1039" alt="Screenshot_5" src="https://github.com/JRissanen/h5-Final-Countdown/assets/116954333/abc792bb-6180-476d-863e-812c5ba30b7e">

---

## d) Jurpon sivut. Ohhoh, sieppasit juuri Jurpon Windowsista NTLM-tiivisteen 83f1cf89225005caeb4e52c9ea9b00e0 . Liitteenä Jurpon kotisivulta leikattu ja liimattu teksti. Tee oma hyökkäyssanakirja ja murra tiiviste myöhempää liikkumista (lateral movement) varten.





























## Lähteet
https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h5-final-countdown </br>
https://terokarvinen.com/2022/cracking-passwords-with-hashcat/ </br>
https://terokarvinen.com/2023/crack-file-password-with-john/ </br>
https://www.stationx.net/nmap-cheat-sheet/ </br>
https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases </br>
https://onlinehashtools.com/generate-random-md5-hash </br>
https://www.md5hashgenerator.com </br>
https://github.com/danielmiessler/SecLists </br>





