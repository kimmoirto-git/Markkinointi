# DH_CAD — materiaalilistan tietomalli ja laskentalogiikka

**Muistio — 2.6.2026**
**Perusta:** `Main_Material_List_DRAFTHOUSE.xlsx` (aiemman projektin havainnemateriaali, projekti 721 NYK).
**Suhde muihin dokumentteihin:** tämä on `DH_alustastrategia_muistio` -muistion tekninen pari. Strategiamuistio vastaa kysymykseen *miksi ja mille alustalle*; tämä vastaa kysymykseen *mitä laskennan pitää tuottaa ja millä rakenteella*.

---

## Tiivistelmä

Havainne-Excel on tuotteen konkreettinen output-spesifikaatio. Keskeinen suunnittelupäätös: **yksi DWG-elementti tuottaa yhden rivin listaan** (elementtitaso), ei automaattista purkua materiaaliriveiksi. Perustelu on hankinnan vaiheittaisuus ja se, että geometriasta generoitu kerros pysyy 1:1 piirustuksen kanssa ja regeneroitavissa. Materiaalirakenne (runko + paneeli + profiilit) elää **tyylissä** koostekertoimina, ei erillisinä riveinä. Laskenta = geometrian määrä × tyylin kertoimet → paino / palokuorma / CO2, elävänä; hankinnan tila hallitaan erillään elinkaaren yli.

---

## 1. Havainnemateriaalin rakenne

Kahdeksan välilehteä kattavat koko elinkaaren:

- **Preliminary Material List (for enquiries)** — alkuvaihe, elementtitaso, litterakoodattu, **ei purettu** materiaaleiksi.
- **Material List** — yksityiskohtainen vaihe, **purettu** yksittäisiksi materiaaliriveiksi (esim. Smoking Room: Rectangular Tube + Honeycomb Panel + C-Profile + Arrow-Profile).
- **Denomination** — kontrolloitu osasanasto (master-katalogi).
- **Door list** — erillinen oviluettelo.
- **List for enquiries** — kyselyvaiheen poiminta (sis. sertifikaatti- ja prefab-tiedot).
- **Summary** — koonti hankinnan statuksen mukaan.
- **Revisions** — revisioseuranta.

**Tärkein yksittäinen havainto:** koko päälistassa vain sarake **Weigh [KG]** on kaavavetoinen (`=Quant. × Weight/Unit`). Sekä määrä että yksikköpaino syötetään käsin. Lista on siis tällä hetkellä käsityönä piirustuksista poimittu transkriptio + käsin haettu materiaalitieto — ja juuri tämä käsityö on se, minkä työkalu poistaa.

---

## 2. Suunnittelupäätös: yksi elementti = yksi rivi

**Päätös:** geometriasta generoidaan yksi rivi per piirretty elementti (esim. yksi lining-seinä, yksi kattoalue, yksi lattia-alue), ei automaattista purkua materiaaliriveiksi.

**Perustelu (KI):** tuotteet ostetaan ja hankitaan vaiheittain eri jaksoina. Elementti pysyy yhtenä seurattavana rivinä, jonka status etenee ajassa, eikä geometriakerros räjähdä moneksi materiaaliriviksi, joita pitäisi hallita erikseen.

**Havainnemateriaalin tuki tälle:** Preliminary-välilehti on täsmälleen elementtitasolla (rivi = "Lining" + litterakoodi `0721_400_O_--_--_--_03_40_3150`, ilman materiaalierittelyä), kun taas yksityiskohtainen Material List on purettu. Eli "yksi elementti = yksi rivi" vastaa juuri sitä vaihetta, joka generoidaan toistuvasti suunnittelun edetessä.

**Tärkein seuraus arkkitehtuurille:** materiaalirakenne (runko + paneeli + profiilit) koodataan **tyyliin** (CSV), ei listaan. Tyyli "Smoking Room lining MA-SMO-08" kantaa koostekertoimet (paino/m², palokuorma/m², CO2/m²) koko rakenteeltaan, ja yksi rivi antaa koostemäärän. Yksittäisiin osiin purku (kontrolloitu Denomination-katalogi) on myöhempi, yksityiskohtainen askel — ei osa elävää generointia.

**Miksi tämä on oikein:** se erottaa kaksi kerrosta, joilla on eri elinkaari:
- **Elävä, regeneroitava kerros** = elementtirivit, 1:1 piirustuksen kanssa, turvallinen regeneroida suunnittelun muuttuessa.
- **Vaiheittainen hankintakerros** = vakaa, käsin hallittu, materiaaleiksi purettu.

Yksi elementti = yksi rivi estää työkalua tallaamasta vaiheittain tehtyjä hankintapäätöksiä regeneroinnin yhteydessä. Tämä on käsin ylläpidetyn listan keskeinen kipupiste, jonka malli ratkaisee.

---

## 3. Tietomalli: neljä kerrosta (per elementtirivi)

**1. Geometriasta johdettava (take-off)**
`Quant.` + `Unit` ← DH_MLINE. Elementin oma mitta: pituus, pinta-ala tai kappalemäärä. Tämä on se osa, joka nyt syötetään käsin.

**2. Tyylistä / materiaalikirjastosta johdettava (koostekertoimet)**
`Weight/Unit`, `Calorific (MJ/kg)` & `(MJ/m²)`, `EPD (kg CO2e)`, sekä `Part`, `Size`, `Model/Type`, `Surface`. Resolvoituvat **Arc.codesta** CSV-tyylikirjaston kautta. Tyyli = koko rakenteen kooste.

**3. Luokittelu ja konteksti**
`Denomination` (rakennusvaihe: Steel Outf. / Lining / Ceiling / Furniture / Floor), `Deck`, alue (`MW Area` + `NIT Area` — kaksi rinnakkaista koodistoa), litterakoodi (`Drawing no.`), `Certificate` (MED / Type Approval / Noncombustible / Marine use / ADA), `Prefab.` (YES/NO). Tulevat objektin tasosta/tyylistä/kontekstista sekä kontrolloiduista sanastoista.

**4. Hankinnan tila (elinkaari, ylläpidetään ajassa)**
`Status` (In work → Engineered → Inquired → Ordered → Received → On board%), `Supplier`, `Order no.`, `Delivery week/place`, `Received`, `On board%`. Tila elää vaiheittain → XDataan elementille tai linkitettyyn rekisteriin.

---

## 4. Laskentaketju

```
Geometria (DWG-elementti)
   → määrä (elementin mitta, Quant. + Unit)
   × tyylin koostekertoimet (Weight/Unit, MJ/m², CO2/m²) kirjastosta Arc.coden kautta
   → Weigh [KG], palokuorma, CO2  (johdetut, elävät)
   → koonti littera + rakennusvaihe + kansi mukaan
   → Summary: % per status
```

Elävyys: kertoimet ja määrä päivittyvät geometrian muuttuessa; hankinnan tila säilyy. Tämä on koko arvolupaus tiivistettynä: lista generoituu piirustuksesta ja pysyy ajan tasalla, sen sijaan että se ylläpidettäisiin käsin erillään.

---

## 5. Arc.code = selkäranka

`Arc.code` (esim. `MA-SMO-08`) on liitosavain geometrian ja materiaalikirjaston välillä: annat koodin → osatyyppi, koko, pintakäsittely, yksikköpaino, palokuorma ja CO2 resolvoituvat. Jos se on robusti, koko lista generoituu; jos löysä, mikään ei resolvoidu.

Huomattava reunaehto: **Denomination-katalogi on kontrolloitu sanasto** — osanimien uudelleennimeäminen ei ole sallittua, ja osanumerot ovat kiinteät (Steel outfitting: A-door=100 … Rectangular Tube=123 … U-Support=125). Tyylikirjaston (CSV) on linjattava näiden kontrolloitujen sanastojen kanssa.

---

## 6. Yksikködualiteetti ja vakiotavaramuunnos

Rivit ovat osin `pcs`, osin `m`/`m²`. Periaate mallissa: **elävä elementtirivi kantaa geometrisen mitan** (pituus/ala); muunnos vakiomittaisiksi kappaleiksi (esim. 6000 mm putki → N kpl) kuuluu myöhempään yksityiskohtaiseen purku-/hankintavaiheeseen, ei elävään elementtikerrokseen. Tämä materiaalikohtainen katkaisu-/nestauslogiikka on domain-spesifistä syvyyttä, jota geneeriset työkalut eivät tee.

---

## 7. Kontrolloidut sanastot (työkalun on kartoitettava / pakotettava nämä)

- **Denomination / Part** — master-katalogi, kiinteät nimet ja numerot.
- **Surfaces** — Galvanized, Painted, Anodized, Brushed, Electrogalvanization, …
- **Certificate** — MED, Type Approval, Noncombustible, Marine use, ADA compliant.
- **Prefab.** — YES / NO (+ prefab-piirustusnumero).
- **Status** — In work → Engineered → Inquired → Ordered → (Received → On board%).

Kaksi rinnakkaista aluekoodistoa (MW + NIT) on jo havainnemateriaalissa → vahvistaa monitelakka-/konfiguroitavuustarpeen (vrt. strategiamuistion littera-huomio).

---

## 8. Alustaneutraalius

Elementtirivit + XData-tila + CSV-tyylit + LISP-geometria → kaikki toimii sellaisenaan myös BricsCADissa. Excel-lista on vain renderöinti lopputuloksesta. Tämä konkreettinen tavoite vahvistaa dual-platform-teesin tietomallin tasolla, ei vain teknisenä mahdollisuutena.

---

## 9. Avoimet kysymykset ja seuraavat päätökset

1. **Materiaalipurku:** miten ja milloin elementti → osakatalogi puretaan — käsin, vai erillisenä generoituna askeleena, jota tyylin "resepti" ohjaa?
2. **Hankinnan tilan sijainti:** XData elementillä vs. ulkoinen linkitetty rekisteri (vaiheittainen monijaksoinen seuranta, monen käyttäjän tilanne)?
3. **Sertifikaatti:** elementtitason kenttä vai materiaalitason (sertifikaatti on usein tuotekohtainen)? Vaikuttaa siihen, riittääkö elementtirivi luovutusdokumentteihin vai vaaditaanko purku.
4. **Tyylikirjaston laajuus:** montako koostekerrointa per tyyli (paino/m², MJ/m², CO2/m²) ja miten ne ylläpidetään ja versioidaan.
5. **EPD (CO2e):** havainnemateriaalissa tyhjä sarake — sama laskentakaava kuin painolla; automatisointi on erottautumistekijä, joka kulkee EU-sääntelyn myötätuulessa.

---

*Päivitettävä elävä muistio. Pohjautuu yhteen havainneprojektiin (721 NYK); yleistettävyys muille telakoille varmistettava aluekoodistojen ja sanastojen osalta.*
