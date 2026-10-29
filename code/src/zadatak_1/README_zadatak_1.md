# ZADATAK 1 - PALETIZACIJA

## 📋 Opis zadatka

Program generira koordinate za 16 paletnih mjesta i sprema ih u pozicijske registre PR[20] do PR[35]. Korisnik unosi:
- **Identifikator paletnog mjesta** (1-16)
- **Korisnički koordinatni sustav** (UFRAME 8 ili 9)
- **Brzinu izvođenja gibanja** (10-100%)

Nakon unosa, program pokreće TP program koji pozicionira robota na odabrano paletno mjesto.

## 📁 Datoteke

- **`hartl_kresimir_01.kl`** - KAREL program (glavna logika)
- **`MOVE_ZADATAK_01.ls`** - TP program (izvršavanje gibanja)
- **`README_zadatak_1.md`** - Ovaj dokument s uputama

## 🎯 Funkcionalne značajke

### Generiranje paletnih mjesta
- **16 pozicija** raspoređenih u 4x4 mrežu
- **Razmak:** 100mm u X i Y smjeru
- **Visina:** Z = 200mm
- **Orijentacija:** W=0°, P=0°, R=180°
- **Spremanje:** U registre PR[20] - PR[35]

### Korisnički unos
Za svaku iteraciju korisnik može unijeti:
1. **ID paletnog mjesta** (1-16)
   - `0` = zadrži prethodnu vrijednost
   - `999` = prekini program
   
2. **UFRAME** (8 ili 9)
   - Omogućava rad u različitim koordinatnim sustavima
   - `0` = zadrži prethodni
   - `999` = prekini program

3. **Brzina** (10-100%)
   - Brzina izvođenja Joint gibanja
   - `0` = zadrži prethodnu
   - `999` = prekini program

### Validacija unosa
- Program provjerava ispravnost svih unesenih vrijednosti
- Ispisuje jasne poruke o greškama
- Dozvoljava ponovni unos kod pogrešnih vrijednosti

## 🚀 Korištenje u Roboguide

### 1. Učitavanje programa

#### A) KAREL program
1. Otvori **Tools → Karel Tool**
2. **File → Open** → Odaberi `hartl_kresimir_01.kl`
3. **Build → Build** za kompajliranje
4. **Build → Download to Robot**
5. Program se učitava kao **HARTL_PALETIZATION.PC**

#### B) TP program
1. Otvori **TP Program List**
2. **Edit → Load Program from File**
3. Odaberi `MOVE_ZADATAK_01.ls`

### 2. Postavljanje HOME pozicije

Program koristi **PR[1]** kao HOME poziciju. Postavi je ručno:

1. Pomakni robota u željenu HOME poziciju
2. **Menu → Data → Position Reg**
3. Odaberi **PR[1]**
4. Pritisni **[SHIFT] + [RECORD]** - sprema trenutnu poziciju

**Preporučena HOME pozicija:**
```
J1 = 0°
J2 = 0°
J3 = 0°
J4 = 0°
J5 = -90°
J6 = 0°
```

### 3. Postavljanje UFRAME sustava

Program koristi UFRAME[8] i UFRAME[9]. Postavi ih prema potrebi:

1. **Menu → Setup → Frames**
2. Odaberi **UFRAME[8]** (ili [9])
3. Definiraj ili kopiraj postojeći okvir

**Za testiranje:** Možeš koristiti osnovni UFRAME[0] (World koordinate)

### 4. Pokretanje programa

#### TEST mod:
1. Drži **[SHIFT]** i pritisni **[FWD]** (uključi TEST mod)
2. **Menu → SELECT**
3. **Program Type: KAREL**
4. Odaberi **HARTL_PALETIZATION**
5. **[SHIFT] + [FWD]** za pokretanje

#### Unos parametara:
```
Generiranje koordinata...
Spremljeno 16 paletnih mjesta u PR[20]-PR[35]

--- NOVA ITERACIJA ---
Unesite ID paletnog mjesta (1-16):
> 5

Unesite UFRAME (8 ili 9):
> 8

Unesite brzinu [10-100]:
> 75

Parametri postavljeni:
  - Paletno mjesto: 5
  - PR registar: PR[24]
  - UFRAME: 8
  - Brzina: 75 %

Pokretanje TP programa...
```

Robot će se pomaknuti na paletno mjesto #5.

### 5. Provjera pozicija

Možeš pregledati generirane pozicije:
1. **Menu → Data → Position Reg**
2. Pregledaj **PR[20]** do **PR[35]**

**Raspored paletnih mjesta:**
```
[13] [14] [15] [16]   Y=300
[ 9] [10] [11] [12]   Y=200
[ 5] [ 6] [ 7] [ 8]   Y=100
[ 1] [ 2] [ 3] [ 4]   Y=0
 X=0 X=100 X=200 X=300
```

## 🔧 Registri

### Position Registers (PR)
- **PR[1]** - HOME pozicija (postavi ručno)
- **PR[20-35]** - Paletna mjesta #1-16 (generira se automatski)

### Number Registers (R)
- **R[1]** - Brzina izvođenja [%]
- **R[2]** - ID pozicijskog registra (20-35)
- **R[3]** - Broj UFRAME-a (8 ili 9)

## ⚙️ Prilagodba parametara

### Promjena layout palete

U KAREL programu, promijeni konstante:

```karel
CONST
    OFFSET_X = 100    -- Razmak u X smjeru [mm]
    OFFSET_Y = 100    -- Razmak u Y smjeru [mm]
    START_Z = 200     -- Visina [mm]
```

### Promjena broja mjesta

```karel
CONST
    PALETTE_ROWS = 4  -- 4 reda
    PALETTE_COLS = 4  -- 4 stupca
```

**Napomena:** Za više od 16 pozicija, prilagodi i FOR petlju u programu.

### Promjena orijentacije

```karel
position.W = 0
position.P = 0
position.R = 180     -- Promijeni orijentaciju alata
```

## 🐛 Mogući problemi

### Problem: "Program MOVE_ZADATAK_01 not found"
**Rješenje:** Učitaj .ls program u TP Program List

### Problem: "Invalid Configuration NUT000"
**Rješenje:** 
- Provjeri dostupne konfiguracije: **Menu → Utility → Vision → Config browser**
- Promijeni u KAREL programu na odgovarajuću (npr. `FUT000`, `NUT001`)

### Problem: Robot se ne pomiče
**Rješenje:**
- Provjeri je li **PR[1]** definiran
- Provjeri je li robot u TEST modu
- Provjeri je li grupa 1 enabled

### Problem: UFRAME not defined
**Rješenje:** 
- Definiraj UFRAME[8] i UFRAME[9] u Setup → Frames
- Ili koristi UFRAME[0] (promijeni validaciju u programu)

## 📊 Primjer outputa

```
================================
   PALETIZACIJA - HARTL
================================

Generiranje koordinata...
Spremljeno 16 paletnih mjesta u PR[20]-PR[35]

--- NOVA ITERACIJA ---
Unesite ID paletnog mjesta (1-16):
> 1
Unesite UFRAME (8 ili 9):
> 8
Unesite brzinu [10-100]:
> 50

Parametri postavljeni:
  - Paletno mjesto: 1
  - PR registar: PR[20]
  - UFRAME: 8
  - Brzina: 50 %

Pokretanje TP programa...
Gibanje zavrseno.

--- NOVA ITERACIJA ---
Unesite ID paletnog mjesta (1-16):
> 0
Unesite UFRAME (8 ili 9):
> 0
Unesite brzinu [10-100]:
> 100

Parametri postavljeni:
  - Paletno mjesto: 1
  - PR registar: PR[20]
  - UFRAME: 8
  - Brzina: 100 %

Pokretanje TP programa...
```

## 📝 Napomene

- **Beskonačna petlja:** Program radi u beskonačnoj petlji (`REPEAT...UNTIL FALSE`)
- **Prekid:** Unesi `999` u bilo koje polje za prekid
- **Zadržavanje vrijednosti:** Unesi `0` da zadržiš prethodnu postavku
- **UFRAME ograničenje:** Prema zadatku, podržani su samo UFRAME 8 i 9
- **Brzina ograničenje:** Prema zadatku, raspon je 10-100%

---

**Autor:** Krešimir Hartl  
**Datum:** 2026-02-12  
**Zadatak:** Roboguide-FANUC Zadatak 1 - Paletizacija
