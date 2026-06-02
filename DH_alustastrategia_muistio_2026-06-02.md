# DH_CAD / DH_Multiline — kaupallistamisen alusta- ja positiointistrategia

**Muistio — 2.6.2026**
**Tila:** kehitystyö alkuvaiheessa, tuote arviolta vuoden päässä. Tämä on ajattelun nykytila, ei lukittu päätös.

---

## Tiivistelmä

DH_Multiline ei ole geneerinen määrälaskentatyökalu vaan meriteollisuuden varustelusuunnittelun kevyt data- ja prosessikerros (PLM-tyyppinen) natiivissa AutoCADissa. Kysyntä tälle on jo todistettu aiemman Revit-kokeilun kautta — erityisesti hankinnan (prokurement) puolella — mutta Revit epäonnistui toimitusvälineenä. Keskeinen jännite: ala ajaa kustannussyistä AutoCAD LT:tä, mutta tuote ei voi toimia LT:ssä. Tästä BricsCAD nousee strategiseksi ratkaisuksi: se on murto-osa AutoCADin hinnasta, poistaa adoption suurimman taloudellisen vastalauseen ja sopii yhteen Bricsysin oman kasvustrategian kanssa. Tavoitearkkitehtuuri: yksi LISP-pohjainen koodipohja, joka toimii sekä AutoCADilla että BricsCADilla.

---

## 1. Mikä tuote oikeasti on

Erottelu on tehtävä selväksi heti: **kyseessä ei ole "yleinen rakentamisen määrälaskenta", vaan meriteollisuuden varustelusuunnittelun data- ja prosessikerros natiivissa AutoCADissa.** Geometriamoottori (DH_MLINE) on yleiskäyttöinen, mutta varsinainen arvo ja vallihauta on sen päällä oleva domain-kerros.

Toiminnallisuus:

- **Littera-perustainen rakennusvaihe-erittely** suunnitteluosille (teräsvarustelu, seinä-, lattia-, katto-, kalusterakenteet), joka seuraa telakan ja koko KT-toimitusketjun rakentamisfilosofiaa eli telakan litteraperusteista rakentamisjärjestelmää.
- **Hankinnan elinkaariseuranta:** mikä materiaali on tilaajan hyväksymää, mikä on hintakyselyssä, mikä otettu, mikä viety laivaan, mikä asennettu. Hankintalista syntyy tästä.
- **Paino- ja palokuormalaskennan syöte** (kriittisiä meriteollisuudessa: stabiliteetti ja SOLAS-paloturvallisuus).
- **Tyyliperustainen nimike- ja tiedonmuokkaus** (CSV-pohjainen tyylijärjestelmä).
- **Sertifikaattitiedon tallennus tuotteisiin** luovutusdokumentteja varten.

Moottorin tekninen ydin: topologia- ja geometriamoottori, liitostarkat pituudet, vinot raot risteäville putkille, T-liitosten luokittelu, XData-metatieto, reaktorivetoinen elävä regenerointi, CSV-tyylien hallinta.

**Suhde kilpailuun:** geneeriset take-off-työkalut (esim. AppisCAD AutoCAD-puolella, PlanSwift/Bluebeam/STACK PDF-puolella) laskevat seiniä joko PDF:n päältä uudelleen mitaten tai polyline-valinnasta summaten. DH_Multiline tuottaa määrät suoraan tekijän älykkäästä DWG-geometriasta ja lisää siihen meriteollisuuden domain-kerroksen. Tämä ei ole sama kategoria — kilpailuvertailu geneerisiin työkaluihin on harhaanjohtava, eikä hinnoittelu saa perustua "seinien laskentaan".

---

## 2. Markkina ja alusta lyhyesti

AutoCAD hallitsee DWG-pohjaista 2D-piirtämistä ylivoimaisesti. **Luotettavaa markkinaosuustaulukkoa tälle nichelle ei ole:** Autodesk ei erottele AutoCADia muusta, ja haastajien käyttäjäluvut ovat markkinointia (kumulatiivisia, eivät aktiivisia maksavia istuimia).

Haastajat ja maantiede:

- **BricsCAD** (Bricsys, perustettu 2002 Gentissä Belgiassa; Hexagonin omistuksessa 2018→, siirtymässä itsenäiseksi Octave-yhtiöksi): eurooppalainen/läntinen premium-vaihtoehto, oma moottori, ~300 000 käyttäjää. **Läsnä Pohjois-Euroopassa — eli juuri kohdemarkkinalla.**
- **ZWCAD ja GstarCAD** (kiinalaiset): vahvoja Kiinassa, Kaakkois-Aasiassa, Latinalaisessa Amerikassa, Etelä- ja Itä-Euroopassa sekä Lähi-idässä ja Afrikassa. Heikkoja Pohjois-Amerikassa (ZWCAD ei myy perpetual-lisenssejä USA/Kanada). Pohjoiseurooppalaiselle marine-kohderyhmälle käytännössä epärelevantteja.
- **Pohjois-Amerikka** pysyy ylivoimaisesti AutoCADina.

---

## 3. Revit-opetus: kysyntä validoitu, väline väärä

Aiempi 4–5 vuoden yritys viedä Revit-suunnittelu meriteollisuuteen oman yrityksen kautta onnistui teknisesti (workflow ja käyttöfilosofia saatiin sopimaan laivanrakennukseen), ja **kysyntä oli aitoa — erityisesti prokurement-puolella.** Esteeksi muodostuivat:

1. Revitin pitkä ja jyrkkä oppimiskynnys (oikeasti kankea oppia).
2. Alan kustannusleikkauskulttuuri ja halu pysyä AutoCAD/AutoCAD LT -linjalla.

**Strateginen johtopäätös:** tietomallinnus ei todennäköisesti tule koskaan osaksi varustelusuunnittelua, hypetyksestä huolimatta. Siksi: **pidä validoitu kysyntä, vaihda hylätty väline** — sama parametri- ja hankintakerros, mutta toimitettuna työkaluun, jota ala ei hylkää.

---

## 4. LT-paradoksi

Ala ajaa kustannussyistä AutoCAD LT:tä. Mutta DH_MLINE nojaa LISPiin, ActiveX/COM:iin, multiline-objektien ohjelmalliseen luontiin ja reaktoreihin — **eikä se toimi LT:ssä.** AutoCAD LT 2024+ sai rajoitetun LISP-tuen, mutta se ei riitä: LT estää kolmannen osapuolen automaatiokirjastot, multiline-objektien VLA*-luonnin (vain täysi AutoCAD), ARX/.NET-funktiot ja rajoittaa ActiveX-automaatiota.

Tämä on rakennettavan tuotteen ydinjännite: **full-AutoCAD-tuote LT-lukittuun markkinaan.** Se pakottaa alustakysymyksen ratkaistavaksi ennen kaupallistamista, ei sen jälkeen.

Täysversioita kyllä hankitaan alalla, kun on pätevä syy — ja teesinä tuote voi *olla* se pätevä syy. Tämä on toimiva positio, mutta sillä on taloudellinen heikkous (kohta 5).

---

## 5. Hinta-analyysi ja split-incentive -ongelma

Indikatiiviset listahinnat (USD; EU- ja jälleenmyyjähinnat vaihtelevat — suhdeluvut ovat olennaisia):

| Tuote | Tilaus / vuosi | Ikuinen (perpetual) |
|---|---|---|
| AutoCAD LT | ~$540 | — |
| AutoCAD (täysi) | ~$2 095 | — |
| BricsCAD Lite | ~$345 | ~$780 |
| BricsCAD Pro | ~$780 | ~$1 752 |

**Teesin "tuote oikeuttaa täyden AutoCADin" heikkous:** erotus LT → täysi AutoCAD on luokkaa **$1 500 / istuin / vuosi — joka vuosi, ikuisesti** — plus oma tuote päälle. Arvo on lähes varmasti olemassa (hankinnan hukka risteilyfit-outissa on moninkertainen). Ongelma on rakenteellinen: premium osuu **suunnittelutoimiston ohjelmistobudjettiin** (juuri se leikkauspaineen alainen kustannuspaikka), kun taas säästö syntyy **prokurementissa**. Budjetin haltija ei ole hyötyjä → leikkauskulttuurissa tämä kaataa hyvänkin työkalun, vaikka yhtiötason ROI olisi positiivinen.

**BricsCAD ei ole vain halvempi — se poistaa koko vastalauseen.** BricsCAD Pro perpetual (~$1 752 kertaluontoinen) vs. täysi AutoCAD (~$2 095/vuosi): takaisinmaksu alle vuodessa, sen jälkeen ilmainen. LT:hen verrattuna BricsCAD Lite (~$345/v tai ~$780 ikuinen) on **halvempi kuin LT itse.** Eli LT → BricsCAD on kustannusten *lasku*, ei nousu. Tällöin suunnittelutoimistolla ei ole budjettisyytä vastustaa, ja split-incentive-ongelma katoaa.

---

## 6. Vipustrategia (ydinoivallus)

Positioi tuote **vipuna**: nettokustannus (vaihto BricsCADiin + plugin) jää pienemmäksi kuin AutoCADissa pysyminen. Silloin pluginin kustannus on tavallaan mitätöity — se on osa rahaa säästävää liikettä, ei lisäkulu. Tämä kääntää tavanomaisen "taas yksi maksullinen työkalu" -vastalauseen päälaelleen, ja tarkoituksellinen BricsCAD-osoitus voi synnyttää laajemmankin oivalluksen halvemmasta ohjelmistovaihtoehdosta — mikä palvelee omia päämääriä.

**Varaus:** pidä selvänä, kumpi on sankari. Moottorin syvyys on arvo; BricsCADin edullisuus on vastalauseen poistaja. Älä anna varustelu-IP:n pelkistyä "siksi, mikä tulee halvan CADin mukana".

---

## 7. Bricsys-kumppanuusmahdollisuus

Intressi osuu yksiin Bricsysin oman strategian kanssa: BricsCADin koko kasvu nojaa kolmannen osapuolen sovelluksiin, jotka vetävät AutoCAD-käyttäjiä yli — siksi he rakensivat ARX-yhteensopivan BRX-rajapinnan ja ovat vuosia tavoitelleet AutoCAD-pluginkehittäjien huomiota. Rakennettava tuote on juuri sitä sovellustyyppiä, jonka varaan heidän kasvunsa perustuu.

Koska marine-niche on BricsCADilla alipalveltu, tarjolla voi aidosti olla app-store-näkyvyyttä, co-marketingia tai migraatio-case-yhteistyötä. **Asema potentiaalisena kumppanina, ei vain alustalla olevana toimittajana** — tätä kannattaa hyödyntää aktiivisesti, ei vain odottaa.

---

## 8. Rehelliset varaukset ja riskit

- **Editio kartoittuu suoraan koodivalintaan.** Jos DH_MLINE pysyy LISPinä, **BricsCAD Lite riittää** (Lite sisältää LISPin; rutiinit latautuvat ja toimivat lähes muutoksitta, usein nopeammin; portissa lähinnä pari asetusaskelta, esim. acad.lsp → on_doc_load.lsp ja ACADLSPASDOC-muuttuja). Jos osia siirretään käännettyyn BRX:ään (suorituskyky tai lähdekoodin suojaus myyntiä varten), tarvitaan **Pro** (ARX-yhteensopiva kehitysjärjestelmä).
- **ActiveX/COM-osat on testattava suoraan.** LISP-ydin on käytännössä drop-in, mutta COM-painotteiset `vlax-`-kutsut vaativat sovitusta — ei uudelleenkirjoitusta. Tämä on ainoa kohta, jossa Lite/Pro-raja ja sovitustarve pitää varmistaa kokeilemalla.
- **Vaihtopäätöksen tekee johto/IT, ei piirtäjä.** Kitkaa on aina (kuten Revit opetti), mutta BricsCAD-vaihto on murto-osa siitä: DWG-natiivi, lähes identtinen käyttöliittymä, LISP toimii. Voitettavissa toisin kuin Revit. Hintalogiikka yksin ei silti käännä — vaihdosta on tehtävä turvallisen tuntuinen.
- **Toimittajariski.** BricsCAD-edellä mentäessä ollaan riippuvaisia Bricsysin tiekartasta ja yritysjärjestelyistä (Hexagon → Octave -itsenäistyminen). **Kahden alustan tuki on siksi myös riskinhallintaa**, ei vain mukava lisä.

---

## 9. Tekninen periaate: dual-platform

Tavoite: **yksi yhteinen LISP-koodipohja**, joka toimii sekä AutoCADilla että BricsCADilla. Ohut platform-shim COM-kutsuille ja DCL:lle; geometria-, topologia- ja määrälogiikka jaettua ja siirrettävää. Juuri se, että moottori on LISP-pohjainen eikä käännettyä ARX:ää, tekee tämän helpoksi.

**Toimenpide nyt, työn ollessa alussa (halpa nyt, kallis myöhemmin):** eristä ActiveX/COM-riippuvaiset kutsut ohuen wrapperin taakse. Tällöin BricsCAD-tuki on myöhemmin viikkojen eikä kuukausien työ, ja molemmat asiakassegmentit (täyteen AutoCADiin siirtyvät talot *ja* BricsCAD-talot) pysyvät tavoitettavissa samasta koodista. Sama kuriperiaate kuin juuri pystytetyssä Git-työnkulussa — eri kerros.

---

## 10. Seuraavat askeleet

1. **Käy DH_MLINE läpi jaolla "puhdas siirrettävä LISP" vs. "COM-sidonnainen"** → näkyviin tulee BricsCAD-option todellinen koko (viikkoja vai kuukausia).
2. **Päätä editio-/arkkitehtuurilinja:** LISP → Lite, vai käännetty BRX → Pro, suhteessa suorituskyky- ja IP-suojaustarpeeseen.
3. **Rakenna ROI-tarina prokurementille** (materiaaliseuranta, ei tuplatilauksia, paino/palokuorma, nopeampi luovutusdokumentaatio), mutta pidä suunnittelijan käyttökokemus kitkattomana.
4. **Hahmota go-to-market:** kärki = vaativa sisustus-/varustelufit-out (meriteollisuus todisteena ja robustiuden takeena), kaksi alustaa, BricsCAD-kumppanuus.

---

*Päivitettävä elävä muistio. Tarkista hinnat ennen päätöksiä — listahinnat ja EU-hinnoittelu muuttuvat.*
