# ZADATAK 2 - DATA LOGGER SA TELNET VEZOM

## 📋 Opis zadatka

Glavni program upravlja izvršavanjem **paletizacijskog programa** (Zadatak 1) i **data logger programa** koji šalje podatke o statusu robota preko TELNET veze.

### Funkcionalnost:
1. **Glavni program** (`hartl_main`) poziva:
   - Paletizacijski program iz Zadatka 1
   - Data logger program koji šalje podatke preko TELNET-a

2. **Data logger** (`hartl_datalogger`) šalje:
   - Datum i vrijeme
   - Poziciju TCP-a (X, Y, Z, W, P, R)
   - Aktivni UFRAME broj
   - Pozicije svih 6 zglobova (J1-J6)
   - Vremensku oznaku (FAST_CLOCK)

## 📁 Datoteke

- **`hartl_kresimir_02_main.kl`** - Glavni program
- **`hartl_kresimir_02_logger.kl`** - Data logger program
- **`README_zadatak_2.md`** - Ovaj dokument s uputama

**Napomena:** Također je potreban i program iz Zadatka 1!

## 🎯 Kontrola pomoću flagova

### FLG[7] - Kontrola glavne petlje
- **ON** = Program radi
- **OFF** = Program se zaustavlja

**Postavljanje:**
```
Menu → Data → Flag
FLG[7] = OFF
```

### FLG[1] - Detekcija pokreta (automatski)
- **ON** = Robot se giba
- **OFF** = Robot je u mirovanju

**Napomena:** Ovaj flag se postavlja automatski u TP programu

### FLG[2] - Kontinuirano slanje (ručno)
- **ON** = Kontinuirano slanje podataka svakih 10ms
- **OFF** = Slanje samo pri završetku pokreta

**Postavljanje:**
```
Menu → Data → Flag
FLG[2] = ON (za kontinuirano slanje)
FLG[2] = OFF (za slanje pri završetku gibanja)
```

### FLG[10] i FLG[20] - Interni flagovi
- Koriste se za kontrolu data loggera
- **Ne mijenjaj ručno**

## 🚀 Korištenje u Roboguide

### 1. PRIPREMA

Prije pokretanja Zadatka 2, **obavezno** učitaj programe iz Zadatka 1:
- `hartl_kresimir_01.kl` (KAREL)
- `MOVE_ZADATAK_01.ls` (TP)

Također postavi **PR[1]** HOME poziciju (vidi README Zadatka 1).

### 2. UČITAVANJE PROGRAMA ZADATKA 2

#### A) Učitaj KAREL programe:
**Glavni program:**
1. **Tools → Karel Tool**
2. **File → Open** → `hartl_kresimir_02_main.kl`
3. **Build → Build**
4. **Build → Download to Robot**
5. Program se učitava kao **HARTL_MAIN.PC**

**Data logger:**
1. **Tools → Karel Tool**
2. **File → Open** → `hartl_kresimir_02_logger.kl`
3. **Build → Build**
4. **Build → Download to Robot**
5. Program se učitava kao **HARTL_DATALOGGER.PC**

### 3. POSTAVLJANJE TELNET VEZE

#### Opcija A: Simulator Console (najjednostavnije)
Podaci se automatski prikazuju u Roboguide konzoli.

#### Opcija B: Stvarna TELNET veza

1. **Pronađi IP adresu virtualnog kontrolera:**
   - Virtual Robot Controller → Menu → Setup → Host Comm
   - Zapiši IP adresu (npr. `192.168.1.10`)

2. **Otvori TELNET klijent na PC-u:**
   ```cmd
   telnet 192.168.1.10
   ```

3. **Alternativa - PuTTY:**
   - Otvori PuTTY
   - Connection type: **Telnet**
   - Host: `192.168.1.10`
   - Port: `23`
   - Klikni **Open**

### 4. POKRETANJE PROGRAMA

#### Korak 1: Postavi flagove
**Na Teach Pendantu:**
1. **Menu → Data → Flag**
2. Postavi **FLG[7] = ON** (omogući izvršavanje)
3. Postavi **FLG[2]** prema potrebi:
   - **ON** = Kontinuirano slanje
   - **OFF** = Slanje samo pri završetku pokreta

#### Korak 2: Pokreni glavni program
1. **Menu → SELECT**
2. **Program Type: KAREL**
3. Odaberi **HARTL_MAIN**
4. **[SHIFT] + [FWD]** za kontinuirano izvršavanje

#### Korak 3: Prati podatke preko TELNET-a

U TELNET konzoli vidjet ćeš podatke:
```
=====================================
   DATA LOGGER TELNET - HARTL
=====================================

--- NOVA ITERACIJA ---
DATUM/VRIJEME: 12/02/26 14:35:22
SEKUNDE: 44 s
FAST_CLOCK: 1234567890 ms
AKTIVAN UFRAME: 8

TCP POZICIJA [mm / deg]:
  X:   150.00 mm
  Y:   200.00 mm
  Z:   200.00 mm
  W:     0.00 deg
  P:     0.00 deg
  R:   180.00 deg

ZGLOBNE POZICIJE [deg]:
  J1:    15.234 deg
  J2:   -22.156 deg
  J3:    45.789 deg
  J4:     0.000 deg
  J5:   -90.000 deg
  J6:     0.000 deg

--- NOVA ITERACIJA ---
...
```

### 5. ZAUSTAVLJANJE PROGRAMA

**Metoda 1: Preko flaga**
1. **Menu → Data → Flag**
2. Postavi **FLG[7] = OFF**
3. Program će se zaustaviti nakon sljedeće iteracije

**Metoda 2: ABORT**
- Pritisni **ABORT** na teach pendantu
- Program se odmah zaustavlja

## 🔧 Prilagodba parametara

### Promjena frekvencije slanja

U `hartl_kresimir_02_main.kl`:
```karel
update_frequency_ms = 10  -- Promijeni na željenu vrijednost [ms]
```

**Napomena:** 
- Manja vrijednost = češće slanje podataka
- Preporučeno: 10-1000 ms

### Dodavanje dodatnih podataka

U `hartl_kresimir_02_logger.kl` dodaj nove WRITE naredbe:

```karel
-- Primjer: Slanje broja pozicijskog registra
WRITE telnet_console('TRENUTNI PR: R[2] = ', $NUMREG[2], CR)

-- Primjer: Slanje stanja I/O
WRITE telnet_console('DIN[1]: ', $DIN[1], CR)
```

## 📊 Primjer tijeka izvršavanja

### Scenario 1: Kontinuirano slanje (FLG[2] = ON)

```
1. Program startovan (FLG[7]=ON, FLG[2]=ON)
2. Šalje se podatak preko TELNET-a (svakih 10ms)
3. Korisnik unosi parametre u paletizaciji
4. Šalje se podatak preko TELNET-a
5. Robot se giba na paletno mjesto
6. Šalje se podatak preko TELNET-a
7. Gibanje završeno
8. Vraćanje na korak 2 (nova iteracija)
```

### Scenario 2: Slanje pri završetku (FLG[2] = OFF)

```
1. Program startovan (FLG[7]=ON, FLG[2]=OFF)
2. Korisnik unosi parametre
3. Robot se giba (FLG[1]=ON)
4. Gibanje završeno (FLG[1]=OFF)
5. SADA se šalje podatak preko TELNET-a
6. Vraćanje na korak 2 (nova iteracija)
```

## 🎯 Način rada flagova

### Logika u glavnom programu:
```karel
IF (FLG[1] = OFF) AND (FLG[2] = ON) THEN
    -- Slanje podataka
    RUN_TASK('hartl_datalogger', ...)
ENDIF
```

**Objašnjenje:**
- `FLG[1]=OFF` → Robot nije u pokretu
- `FLG[2]=ON` → Kontinuirano slanje omogućeno
- **Rezultat:** Šalji podatke

### Postavljanje FLG[1] u TP programu

U `MOVE_ZADATAK_01.ls` dodaj:
```
5:  FLG[1]=(ON) ;    -- Početak gibanja
6:  J PR[...] ... ;   -- Gibanje robota
7:  FLG[1]=(OFF) ;   -- Kraj gibanja
```

## 🐛 Mogući problemi

### Problem: "Program hartl_paletization not found"
**Rješenje:** 
- Učitaj programe iz Zadatka 1
- Proveri imena programa u KAREL kodu

### Problem: TELNET ne pokazuje podatke
**Rješenje:**
- Provjeri je li TELNET veza otvorena
- Provjeri IP adresu kontrolera
- Koristi Roboguide konzolu kao alternativu

### Problem: Previše podataka (preplavljeno)
**Rješenje:**
- Smanji `update_frequency_ms` u glavnom programu
- Postavi `FLG[2]=OFF` za slanje samo pri završetku

### Problem: Data logger ne reagira
**Rješenje:**
- Provjeri je li `FLG[10]=ON` i `FLG[20]=ON` prije pokretanja
- Resetiraj flagove ručno ako je potrebno

## 📝 Napomene

- **Multi-tasking:** Program koristi `RUN_TASK()` za paralelno izvršavanje
- **Prioriteti:** Data logger ima prioritet 2, paletizacija prioritet 1
- **E-učenje:** Paletizacija ima omogućeno e-učenje (može se pokrenuti iz TP-a)
- **Safety:** Program koristi `%NOPAUSE` za neprekinuto izvršavanje
- **TELNET veza:** Automatski se otvara i zatvara u data logger programu

## 🔗 Veza sa Zadatkom 1

Ovaj program **OVISI** o Zadatku 1:
- Poziva `hartl_paletization` KAREL program
- Koristi iste registre (R[1], R[2], R[3])
- Koristi iste pozicijske registre (PR[1], PR[20-35])

**Obavezno učitaj Zadatak 1 prije pokretanja Zadatka 2!**

---

**Autor:** Krešimir Hartl  
**Datum:** 2026-02-12  
**Zadatak:** Roboguide-FANUC Zadatak 2 - Data Logger sa TELNET
