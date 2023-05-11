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
* Tiivistettyä salasanaa ei saa enää käännettyä takaisin "normaaliksi" salasanaksi

* Salasanojen murtaminen onnistuu "Hashcat" ja "Hashid" -ohjelmilla.
* Asenna lisäksi jokin iso sanakirja täynnä salasanoja.
  * "Rockyou" -niminen sanakirja on yksi suosituimmista. (Yli 14 miljoonaa salasanaa/sanaa)


































## Lähteet
https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h5-final-countdown </br>



