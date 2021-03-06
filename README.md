# **Little M.**
<div style="text-align:center">
    <img src="presentation/little_m_logo_no_bg.png"/>

Bakalársky projekt študenta na FEI STU Bratislava.<br> Jedná sa o návrh malého autonómneho robotického vozdila s orientáciou v interieri.
</div>

---

## **Princíp**
Základom projektu je myšlienka že takmer všade okolo nás sa už nachdáza wifi sieť. Pomocou určitých techník vieme zmerať silu wifi signálu na danom mieste.
Zisťovanie v akej miestnosti sa vozdilo nachádza je založené na zbere dát o rôznych silách signálov, rôznych wifi sietí(v projekte troch) a priradovania týmto wifi sietam aj "id" miestnosti. Následne sú dáta spracované a je nimi natrénovaný model s SVM kalsifikátorom teda ide o tzv. učenie s učiteľom. Pomocou openSource knižnice je následne tento model prevedený do jazyka C aby bol použiteľný vo vozidle ktoré vie pomocou tohoto modelu a sily signálu zistiť v akej miestnosti sa nachdáza.

Projekt je zložený z troch častí: 
- ### zariadenie na zber dát

- ### modelová časť
- ### vozidlo
---
### **Zariadenie na zber dát**
Toto zariadenie má za úlohu zmerať sily signálov na nami zvolených miestach.\
Preferovaný počet je v každej miestnosti vykonať 15 meraní.\
Počer miestností je ľubovoľný.\
Skladá sa z USB Adaptéra pre ESP-01, ESP8266 ESP-01 a tlačidla ktoré je používané na sprístupnenie programovancieho módu.\
V kóde je potrebné zvoliť počet miestností, počet wifi sietí, ich mená, a koľko meraní má byť v každej miestnosti vykonaných.\
Kód je možné loggovať pomocou putty alebo iného softwaru.\
Pri nahrávaní kódu uvedieme modul do programovacieho módu tak že pri vkladaní do USB portu budeme držať tlačidlo.\
Pri programovaní musíme dbať na správne nastavenie výšky boaudov.\
**súčiastky**:
- Adaptér pre ESP-01
- ESP8266 ESP-01
- tlačidlo

**schéma**: 
- [schemas/zber_dat_png.png](https://github.com/TvarozekRastislav/Little-M./blob/main/schemas/zber_dat_png.png)

**kód**:
- [code_base/data_collection.ino](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/data_collection/data_collection.ino)

**fotka**:
- [hardware_pics/signal_collector.ino](https://github.com/TvarozekRastislav/Little-M./blob/main/hardware_pics/signal_collector.png)

---
### **Modelová časť**

Modelová časť má za úlohu z loggovaného výstupu z predchádzajúcej časti vygenrovať "clean" csv súbor a pomocou tohoto súboru natrénovať model ktorý využíva SVM klasifikátor a následne tento model pomocou open source knižnice micromlgen prekonvertovať na súbor v jazyku C ktorý je možné využiť v arduine.\
Modelovú časť možno vykonať dvoma spôsobmi a to spustením dvoch jupyter notebookov alebo nastavením configu a lokálneho spustenia scriptu ktorý sa nachádza nižšie alebo pushnutím nových vstupných dát.txt do main branche sa spustá GitHub action  ktorá model vygeneruje a pushne na GitHub.
Pri jupyternotebooku môžeme vidieť aj extra grafy týkajúce sa dát.

**schéma**:
- [schemas/software_schema.png](https://github.com/TvarozekRastislav/Little-M./blob/main/schemas/software_schema.PNG)

**kód**

***manuálne***:
- [code_base/data_clear_jupyter.ipynb](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/data_clear_jupyter.ipynb)
    - v rovnakom priečinku sa musí nachádzať vstupný súbor s dátami s formátom z predchádzajúcej časti
    - vystupom je csv súbor 
- [code_base/data_train_model_jupyter.ipynb](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/data_train_model_jupyter.ipynb)
    - v rovnakom priečinku sa musí nachádzať vstupný súbor csv súbor s formátovanými dátami z predhádzajúceho bodu
    - výstupom je model.h súbor ktorý obsahuje natrénovaný model

***pomocou configu a scriptu***:
- [code_base/create_model_automatic/config.json](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/create_model_automatic/config.json)
    - config je potrebné zmeniť na požadované parametre
- [code_base/create_model_automatic/generate_model_from_data_file.py](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/create_model_automatic/generate_model_from_data_file.py)
    - na spustenie scriptu sú potrebné moduly ktoré sa nachádzajú v - [.github/workflows/requirements.txt](.github/workflows/requirements.txt)
---
### **Vozidlo**
Vozidlo využíva Arduino Uno a Arduino sensor shield kvoli veľkému množstvu GND a VCC pinov. Vozidlo je poháňané 4 motormi ktoré sú ovladané H bridge modulov, ktorý umožňuje nastavať rýchlosť vozidla ako aj smer jazdy.\
 Arduino je napájané dvomi 4.5V batériami.
 Obsahuje ultrazvukový senzor, bluetooth prijímač, ESP-01, display ktorý zobrazuje aktuálnu miestnosť v ktorej sa vozidlo nachádza, 3 senzori na sledovanie čiary na zemi.\
 Prvý kód je potrebné nahrať do Arduina druhý na ESP-01, potom je už jednoducho možné vozidlo ovládať cez telefón s bluetooth pripojením, kde sa dá nastavoať mód jazdy, a to bude sledovanie čiary alebo diaľkové ovládanie.

 **schéma**:
- [schemas/car_connected_pdf.png](https://github.com/TvarozekRastislav/Little-M./blob/main/schemas/car_connected_pdf.pdf)

**kód**
- [code_base/car_code/main.ino](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/car_code/main/main.ino)
    - v rovnakom priečinku sa musí nachádzať aj model.h
    - tento kód je ptrebné nahrať na arduino
- [code_base/sending_via_rxtx](https://github.com/TvarozekRastislav/Little-M./blob/main/code_base/auto_wifi_signal_sender/sending_via_rxtx/sending_via_rxtx.ino)
    - tento kód je potrebné nahrať na ESp-01

**fotka**:
- [hardware_pics/little_m_back.png](https://github.com/TvarozekRastislav/Little-M./blob/main/hardware_pics/little_m_back.png)
