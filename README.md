# TSC_PROIECT2026
# InkTime Smartwatch PCB

## Descriere generală

Proiectul constă în realizarea unui PCB pentru un smartwatch open-source numit **InkTime**, bazat pe microcontroller-ul **nRF52840**. Scopul a fost integrarea tuturor componentelor necesare (procesare, senzori, alimentare, interfață utilizator) pe o placă compactă, care să respecte atât constrângerile electrice cât și cele mecanice impuse de carcasă.

Design-ul a fost realizat în **Fusion 360 Electronics**, urmând schema electrică oferită și regulile de layout discutate la laborator. S-a pus accent pe rutare corectă, planuri de masă și integrarea componentelor într-un spațiu limitat.

---

## Diagramă bloc

```
                        ┌─────────────┐
                        │   USB-C     │
                        │   (J4)      │
                        └──────┬──────┘
                               │ VBUS (5V)
                        ┌──────▼──────┐
                        │  ESD Prot.  │
                        │   (D3)      │
                        └──────┬──────┘
                               │
                ┌──────────────▼──────────────┐
                │     BQ25180 (Charger)       │
                │         U1                  │
                └──────┬──────────────┬───────┘
                       │ VBAT         │ I2C (SCL/SDA)
              ┌────────▼────────┐     │
              │  DC/DC (IC9)    │     │
              │  TPS62160       │     │
              └────────┬────────┘     │
                       │ 3V3         │
        ┌──────────────▼──────────────▼───────────────┐
        │                                             │
        │              nRF52840 (MCU)                 │
        │                                             │
        ├── SPI ──► E-Paper Display (J1)              │
        ├── I2C ──► IMU - BMA423 (U2)                 │
        ├── I2C ──► Haptic Driver - DRV2605           │
        ├── I2C ──► Fuel Gauge - MAX17048 (U3)        │
        ├── GPIO ──► Butoane (SW_UP, SW_EN, SW_DN)    │
        ├── ANT ──► Chip Antenna 2450AT18B100E        │
        └── SWD ──► Debug Connector TC2030 (J2)       │
                                                      │
        ┌─────────────────────────────────────────────┘
        │
        ▼
   ┌─────────┐
   │ Baterie  │
   │  Li-Po   │
   └─────────┘
```

---

## BOM (Bill of Materials)

| Componentă | Referință | Valoare/Tip | Capsulă | Link JLC Parts | Datasheet |
|---|---|---|---|---|---|
| nRF52840 | IC1 | MCU BLE | QFN-73 (BGA) | [JLC](https://jlcparts.com) | [Datasheet](https://infocenter.nordicsemi.com/pdf/nRF52840_PS_v1.1.pdf) |
| BQ25180 | U1 | LiPo Charger | BGA-8 | [JLC](https://jlcparts.com) | [Datasheet](https://www.ti.com/lit/ds/symlink/bq25180.pdf) |
| TPS62160 | IC9 | DC/DC Buck | BGA-15 | [JLC](https://jlcparts.com) | [Datasheet](https://www.ti.com/lit/ds/symlink/tps62160.pdf) |
| MAX17048 | U3 | Fuel Gauge | TDFN-8 | [JLC](https://jlcparts.com) | [Datasheet](https://datasheets.maximintegrated.com/en/ds/MAX17048-MAX17049.pdf) |
| BMA423 | U2 | IMU (Accel+Gyro) | LGA-12 | [JLC](https://jlcparts.com) | [Datasheet](https://www.bosch-sensortec.com/products/motion-sensors/accelerometers/bma423/) |
| DRV2605 | - | Haptic Driver | DFN-10 | [JLC](https://jlcparts.com) | [Datasheet](https://www.ti.com/lit/ds/symlink/drv2605.pdf) |
| 2450AT18B100E | ANT1 | Chip Antenna 2.4GHz | SMD | [JLC](https://jlcparts.com) | [Datasheet](https://www.johansontechnology.com/datasheets/2450AT18B100/2450AT18B100.pdf) |
| SI1308EDL | Q3 | MOSFET (E-Paper) | SOT-323 | [JLC](https://jlcparts.com) | [Datasheet](https://www.vishay.com/docs/68752/si1308edl.pdf) |
| USBLC6-2SC6 | D3 | ESD Protection | SOT-23-6 | [JLC](https://jlcparts.com) | [Datasheet](https://www.st.com/resource/en/datasheet/usblc6-2.pdf) |
| USB-C | J4 | Conector USB Type-C | SMD 16pin | [JLC](https://jlcparts.com) | - |
| S03480-2400 | J1 | Conector FPC E-Paper | 24 pin | [JLC](https://jlcparts.com) | - |
| TC2030-IDC | J2 | Conector SWD Debug | 6 pin | [JLC](https://jlcparts.com) | - |
| MBR0530 | D2, D4, D5 | Diode Schottky | SOD-323 | [JLC](https://jlcparts.com) | - |
| Rezistențe | R1-R18 | Diverse valori | 0201 | [JLC](https://jlcparts.com) | - |
| Condensatoare ≤100nF | C1-C25 | Diverse valori | 0201 | [JLC](https://jlcparts.com) | - |
| Condensatoare >100nF | C26-C43 | Diverse valori | 0402 | [JLC](https://jlcparts.com) | - |
| Inductor DC/DC | L7 | 4.7µH | 0402/0603 | [JLC](https://jlcparts.com) | - |
| Inductor E-Paper | L5 | 68µH | SMD | [JLC](https://jlcparts.com) | - |
| Cristal 32MHz | X1 | 32 MHz | SMD | [JLC](https://jlcparts.com) | - |
| Cristal 32.768kHz | X2 | 32.768 kHz | SMD | [JLC](https://jlcparts.com) | - |

---

## Funcționalitate hardware

### Alimentare

Alimentarea sistemului se realizează în mai multe etape:

**USB-C (J4)** furnizează 5V (VBUS) care trece prin **protecția ESD (D3 - USBLC6)** și ajunge la **charger-ul BQ25180 (U1)**. Charger-ul încarcă bateria Li-Po și oferă tensiunea VBAT (~3.7-4.2V) la ieșire. **DC/DC converter-ul TPS62160 (IC9)** convertește VBAT la **3V3** stabil, care alimentează întreg sistemul. **Fuel gauge-ul MAX17048 (U3)** monitorizează nivelul de încărcare al bateriei prin I2C.

Traseele de alimentare (VBUS, VBAT, 3V3, VREG) au fost rutate manual cu grosime de **0.3mm** conform cerințelor, pentru a asigura capacitatea de curent necesară. Condensatoarele de decupling (100nF) au fost plasate cât mai aproape de pinii de alimentare ai IC-urilor.

### Microcontroller (nRF52840)

nRF52840 este MCU-ul principal, un SoC cu Bluetooth Low Energy integrat. Acesta controlează toate perifericele prin interfețe standard. Pad-ul central expus (P$74 GND) este conectat la planul de masă prin multiple via-uri pentru disipare termică. Cristalul de 32MHz (X1) și cel de 32.768kHz (pentru RTC) sunt plasate foarte aproape de pinii XC1/XC2, respectiv XL1/XL2 ai MCU-ului.

### Comunicație

**I2C (SCL, SDA):** Bus comun pentru charger (BQ25180), IMU (BMA423), fuel gauge (MAX17048) și haptic driver (DRV2605). Liniile au pull-up-uri de 4.7kΩ la 3V3.

**SPI:** Utilizat pentru comunicația cu display-ul E-Paper prin conectorul J1. Semnalele includ MOSI, SCK, CS, plus semnale de control (DC, RST, BUSY).

**USB:** Pinii D+ și D- ai nRF52840 sunt conectați la USB-C (J4) prin protecția ESD (D3). Rezistențele de 5.1kΩ pe CC1/CC2 asigură detectarea corectă a conexiunii USB-C.

### E-Paper Display

Display-ul e-paper necesită tensiuni speciale (+15V, -15V) generate de un circuit boost converter format din inductorul L5 (68µH), diodele Schottky D2/D4/D5 (MBR0530), tranzistorul MOSFET Q3 (SI1308EDL) și condensatoare de stocare. Circuitul este controlat de MCU prin semnalele PWR_EPD, EP_TYPE_SEL și EP_DR.

### Senzori și periferice

**IMU (BMA423):** Accelerometru conectat prin I2C, cu întreruperi (INT1, INT2) către GPIO-urile MCU-ului. Folosit pentru detectarea mișcării/orientării ceasului.

**Haptic Driver (DRV2605):** Controlează motorul de vibrație prin I2C. Ieșirile OUT+ și OUT- alimentează motorul haptic. Are propriul pin de enable controlat prin GPIO.

**Butoane (SW_UP, SW_EN, SW_DN):** Trei butoane cu pull-up-uri de 10kΩ la 3V3 și condensatoare de debounce. Conectate la pinii GPIO ai nRF52840.

### Radio / Antena

Antena chip **2450AT18B100E** este plasată la marginea PCB-ului, cu matching network (inductori L1, L2, L3 și condensatoare) între pinul ANT al nRF52840 și antenă. Zona de sub antenă este liberă de cupru pe ambele straturi (Top și Bottom) și PCB-ul este decupat sub antenă conform cerințelor RF.

---

## Pini nRF52840 utilizați

### Interfața SPI (E-Paper Display)
- MOSI
- SCK
- CS (chip select)
- DC (data/command)
- RST (reset display)

### Interfața I2C
- SDA
- SCL

### GPIO-uri
- Haptic enable
- IMU interrupt
- PMIC interrupt
- Alte semnale de control

### Pini dedicați
- **XC1, XC2** → Cristal 32MHz
- **P0.00/XL1, P0.01/XL2** → Cristal 32.768kHz (RTC)
- **D+, D-** → USB
- **VBUS** → Detectare USB
- **ANT** → Ieșire RF către antenă
- **SWDIO, SWDCLK, RESET** → Debug SWD
- **DEC1-DEC6** → Condensatoare decupling intern
- **VDD, VDDH** → Alimentare 3V3
- **VSS** → Masă GND

Alegerea pinilor a fost făcută astfel încât să permită rutare cât mai simplă, să evite intersecțiile inutile și să respecte funcționalitatea perifericelor.

---

## Layout PCB

PCB-ul a fost proiectat pe **2 layere** cu grosime de **1mm**:

**Top layer:** toate componentele sunt amplasate exclusiv pe acest strat, împreună cu majoritatea traseelor de semnal și alimentare. Plan de masă GND turnat pe zonele libere.

**Bottom layer:** utilizat pentru rutare suplimentară (mai ales semnale care nu puteau trece pe Top din cauza aglomerării) și plan de masă GND.

### Reguli de rutare respectate
- Traseele de alimentare (VBUS, VBAT, 3V3, VREG, LX1, LX2): **width = 0.3mm**
- Traseele de semnal (GPIO, I2C, SPI, USB): **width ≥ 0.15mm**
- Unghiuri de rutare la **45°**, evitând unghiurile de 90°
- Condensatoarele de decupling plasate cât mai aproape de pinii VCC ai IC-urilor
- Via-uri utilizate pentru trecerea între layere, cu dimensiune minim 0.3mm drill

### Planuri de masă
- Polygon GND pe TOP (Isolate: 0.2mm, Rank: 6, Thermals: on)
- Polygon GND pe BOTTOM (Isolate: 0.2mm, Rank: 6, Thermals: on)
- Via stitching între cele două planuri, cu densitate mai mare în jurul zonei RF

### Zona antenei
- Zona de sub antenă **nu conține cupru** pe niciun strat (Top/Bottom)
- PCB-ul este **decupat sub antenă**
- Nu există trasee rutate sub antenă
- Keepout/Restrict aplicat pe ambele layere (tRestrict + bRestrict)

---

## Probleme întâmpinate și soluții

### Rutare manuală vs. Auto-route
Traseele de alimentare (VBUS, VBAT, 3V3, VREG, LX1, LX2, EPD_3V3) au fost rutate **manual** pentru a respecta grosimea de 0.3mm și a evita via-urile pe liniile de putere. Restul semnalelor au fost rutate cu auto-router, urmat de verificare și corecții manuale.

### DRC errors
Pe parcursul design-ului au apărut mai multe categorii de erori DRC: Overlap (componente suprapuse), Copper Clearance (distanță insuficientă între trasee), Copper-Restrict Clearance (trasee în zona de keepout a antenei) și Air Wire (conexiuni nerutate). Acestea au fost rezolvate prin reamplasarea componentelor, refacerea traseelor și adăugarea de via-uri GND.

### Planul de masă fragmentat
Inițial, auto-router-ul a plasat multe trasee pe Bottom care au fragmentat planul de masă. Soluția a fost darea de RIPUP pe traseele GND de pe Bottom și utilizarea planului de masă (polygon pour) cu via-uri de conectare acolo unde pad-urile GND rămâneau izolate.

### Zona antenei
Eliminarea completă a cuprului din zona antenei a complicat rutarea în colțul PCB-ului, dar a fost necesară pentru respectarea regulilor RF.

### Condensatoare de decupling
Condensatoarele de decupling ale DC/DC-ului (IC9) au fost mutate de mai multe ori pentru a fi cât mai aproape de pinii VREG și 3V3 ai IC-ului, reducând inductanța parazitară.

---

## Decizii de design și compromisuri

- Au fost acceptate câteva zone unde via-urile sunt foarte apropiate de pad-uri SMD, deoarece nu există suficient spațiu pentru un escape routing clasic fără a redesena complet layout-ul.
- În anumite zone, traseele sunt foarte apropiate unele de altele (aproape de limita DRC de 5mil / 0.127mm), dar rămân în toleranțele permise de regulile de fabricație JLCPCB.
- Unele rutări au fost realizate folosind via-uri direct din pad (via-in-pad parțial), pentru a permite scoaterea semnalelor din zone aglomerate (ex: zona MCU / pini multipli BGA).
- Via stitching nu a fost aplicat uniform pe toată placa, ci doar în zonele relevante (în special în jurul zonei RF și a planurilor de masă), pentru a evita aglomerarea inutilă.
- În zona antenei a fost eliminat complet planul de masă și orice traseu, chiar dacă acest lucru a complicat rutarea, pentru a respecta regulile RF.
- Unele erori DRC au fost marcate ca **Approved** în cazurile în care sunt false positive-uri sau situații inevitabile (ex: Board Outline Clearance la butoane/USB-C, Overlap-uri minore la conectori).
- Grosimea PCB-ului este de **1mm** (nu 1.6mm standard), conform cerințelor proiectului.

---

## Mechanical

Modelul 3D al PCB-ului a fost generat din Fusion 360 Electronics, cu modele 3D STEP importate pentru componentele principale (antenă 2450AT18B100E, conectori, IC-uri). Modelele 3D au fost preluate din biblioteca InkTime_v5.lbr și de pe Component Search Engine.

Carcasa furnizată de echipă a fost utilizată ca referință pentru plasarea componentelor (butoane, USB-C, conector FPC display), dar asamblarea completă (PCB + baterie + display + carcasă) nu a fost realizată în această etapă.

---

## Imagini

- Randări PCB 2D (Top + Bottom)
- Randări PCB 3D cu modele componente

---
