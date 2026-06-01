# DH_Multiline - KAUPALLISTAMINEN JA ALUSTA (substraatti, jakelu, IP, lisensointi)

*Varhaisvaiheen kaupallistamisstrategia. Taydentaa DH_visio_ja_strategia.md:ta (tuotteen
sielu) ja koskee eri kysymysta: milla alustalla, miten myydaan, miten suojataan ja
veloitetaan. Elava dokumentti - paivitetaan tarpeen tullen. Kirjoitettu DH-dokumenttien
ASCII-tyylilla.*

## Strateginen tikapuu (yhteenveto)

Paatosketju, jossa jokainen lenkki tukee seuraavaa ja palvelee annettuja motiiveja:

Kohdeasiakas (sisustusvarustelijat, EI telakat)
-> AutoCAD-natiivi (uskottavuus + pitka aikavali)
-> .NET/ARX + obfuskointi (max suoja + robustius)
-> vapaa App Store -listaus (loydettavyys, uskottavuus, nakyvyys = mainosarvo)
-> MoR-tilausmalli (kassavirta + ALV-vastuu ulkoistettu)
-> per-seat, fail-soft, tunnukseen sidottu, siirrettava

Loppu on toteutusta.

## 1. Markkina - kenelle

**Kaksi eri kerrosta, alA sekoita:**
- TELAKAT (laivanrakentajat): risteilypuolella maailmanlaajuisesti vain kourallinen
  (Meyer Werft, Meyer Turku, Fincantieri, Chantiers de l'Atlantique, + Vard
  expeditioneihin). EIVAT paaosin tee yksityiskohtaista sisustussuunnittelua itse.
- SISUSTUSVARUSTELUYRITYKSET (turnkey-urakoitsijat, moduulivalmistajat,
  suunnittelutoimistot): paljon suurempi, hajautuneempi joukko. Naiden suunnittelijat
  istuvat AutoCADissa koko paivan. **TAMA on kohdemarkkina.**

**Aukko (= nichen perusta):** raskaat meri-CAD-suitet (Cadmatic, AVEVA, FORAN, NAPA)
kattavat rungon, putkiston, IV:n, kaapeloinnin, teraksen - mutta EIVAT sisustusvarustelua
esteettis-toiminnallisessa mielessa (kalusteet, pintamateriaalit, hyttilayoutit,
yleisotilojen puusepantyot). Se tehdaan AutoCADissa / yleistyokaluilla -> aliedustettu
meriohjelmistoissa. Kimmon 30 v kokemus vahvistaa aukon sisalta pain.

**Markkinan koko (synteesi, ei virallista lukua):** ~5-6 tier-1-risteilytelakkaa;
~15-25 merkittavaa globaalia turnkey-sisustusurakoitsijaa; lisaksi kymmenia pienempia
alihankkijoita ja suunnittelutoimistoja. Yhteensa muutamia kymmenia - korkeintaan pari
sataa yritysta. Keskittyma Euroopassa, toissijaisesti Kiina, Intia/Vietnam paaosin
eurooppalaisten emoyhtioiden kautta.

**Edustavat toimijat alueittain:**
- Suomi (lahin, tihein klusteri): ALMACO, Makinen, Piikkio Works, Shipbuilding
  Completion, ENG'nD. Lisaksi alueella Cadmatic (Turku) - kilpailija/kumppani/resurssi.
- Saksa/Keski-Eurooppa: R&M Group, EMS PreCab.
- UK/Irlanti: MJM Marine, Mivan, Trimline, Atlantic Marine Interiors.
- Italia: Marine Interiors (Fincantieri).
- Muut: Bolici, Aros Marine (LT), US Outfitters, CBA, Maritime Montering (6 maata),
  July Marine (VN).

**Leveys vs arvo:** kaikissa aluksissa on vahintaan miehiston majoitusta. Mutta
miehistohytit = standardimoduuleja (vahan suunnittelua/alus); risteily/expedition/
lautta/jahti = suunnitteluintensiivisia, bespoke. Vahvin arvolupaus on
suunnitteluintensiivisissa segmenteissa.

## 2. Substraatti - milla alustalla

**PAATOS: AutoCAD-only. Toteutus .NET/ARX + obfuskointi.**

**Perustelut:**
- Uskottavuus: AutoCAD-natiivius + Autodeskin App Store -testaus antavat heti eri tason
  uskottavuuden toimintavarmuudesta. (Huom: tama tulee alustasta + jakelusta, ei
  substraatista - substraatti ansaitsee paikkansa SUOJAN ja ROBUSTIUDEN kautta.)
- Pitka aikavali: AutoCAD/DWG ei katoa. Riski ei ole katoaminen vaan
  kehittajapinnan heiluminen (ks. vastapuhe alla).
- "Nukun yoni paremmin" = validi tuotannontekija yksinyrittajalle: vakaumus toteutuu
  paremmin. Gut ja koodi osoittavat tassa samaan suuntaan.

**Suojausspektri (kasvava):**
- .lsp = NOLLA suojaa (lahdekoodi luettavissa).
- .fas/.vlx = kaannettya p-koodia, rima korkeammalla mutta purettavissa; EI aja
  BricsCADissa (AutoCAD-spesifi).
- .NET (managed) = IL, dekompiloitavissa -> vaatii obfuskaattorin; obfuskoituna
  selvasti .fas:ia kovempi. Lisaksi Overrule + Document/Database-tapahtumat =
  robustimpi kuin vlr-reaktorit villissa ymparistossa.
- Natiivi C++ ObjectARX = konekoodi, vahvin suoja; korkein kustannus, versiolukko
  (uudelleenkaannos per AutoCAD-major). VAIN ytimelle, myohemmin, jos piratismi
  materialisoituu.
- **Kalibrointi:** aloita managed .NET + kunnon obfuskaattori koko sovellukselle
  (pysayttaa ~95% uhasta). Natiivi ARX ytimelle vasta tarpeen mukaan.

**LT = ulkona.** LT 2024+ ajaa LISPia (.lsp/.fas/.vlx), MUTTA ActiveX on LT:ssa
rajoitettua; DH on ActiveX-raskasta (vla-*/vlax-curve-*) eika kayatannossa toimi LT:ssa.
.NET/ARX ei aja LT:ssa lainkaan. Ala mitoita LT:ta markkinaan.

**BricsCAD = tietoisesti pois (toistaiseksi).** Olisi leventanyt halpaa tavoitettavuutta
(LISP-yhteensopivuus ~100%), mutta valittiin AutoCAD-only uskottavuuden ja suojan vuoksi.
Jos markkina joskus vaatii: .fas-suoja ei kanna BricsCADiin; vaatisi BRX/.NET-haaran.

**Sunk cost - tarkea reframe:** LISP-tyo EI ole hukassa. Kallista ei ollut LISPin
kirjoittaminen vaan geometrian/topologian RATKAISEMINEN (etumerkki ristitulosta, vino
aukko per viiva, T-haaran lahipuoli-pariteetti + uhrinvalinta). LISP = ratkaisun
suoritettava spesifikaatio. .NET/C++-portti on TRANSKRIPTIO ratkaistusta logiikasta, ei
uudelleenratkaisu - viikkoja, ei vuosia. Vain syntaksi on kertakayttoista.

**Rehellinen vastapuhe (silmat auki):** valitset yhden toimittajan tiekarttaan
keskitetyn riskin. ARX rikkoutuu binaaritasolla ~joka major-versiossa (yllapitovero);
Autodeskilla historia tilausmallin pakottamisesta; painopiste voi valua kohti
webia/pilvea. -> (1) painota managed .NETtia natiivin sijaan missa voit, (2) pida ydin
ankarasti eristettyna halpana uudelleen-isanta-vakuutuksena.

## 3. Arkkitehtuuriperiaate - tarkein teko NYT

**Eristae geometria-/topologiaydin puhtaaksi moduulikseen, irti komento-, CSV- ja
UI-liimasta.** Kielesta riippumaton, arvokkain yksittainen teko tana paivana:
- hyvaa suunnittelua muutenkin,
- tekee tulevasta *pelkan ytimen* portista siedettavan (glue jaa LISPiin / portataan
  erikseen).

PDF:n "eristetty VPLAYER-primitiivi, .NET-valmius" -vaisto on oikea - laajenna se kuri
koko ytimeen.

**Portin luonne kerroksittain:**
- Ydin (matematiikka) = TRANSKRIBOIDAAN. AutoCADin .NET-API paljastaa samat
  geometriapalvelut joita kutsutaan nyt COM:in kautta (GetClosestPointTo,
  GetFirstDerivative, IntersectWith, offset-kayrat) -> "kutsu samaa enginea toisin", ei
  "kirjoita geometria uusiksi". (Tarkat allekirjoitukset .NET-API-dokumentaatiosta.)
- Kayttaytymiskerros (alykas viiva) = UUDELLEENSUUNNITELLAAN Overrulella/tapahtumilla.
  ALA kaanna vlr-reaktoripohjaa rivi riviltä.

## 4. Jakelu - miten myydaan

**Autodesk App Store, listaus VAPAANA.** Vapaa listaus palvelee loydettavyytta,
uskottavuutta (Autodesk testaa jokaisen sovelluksen) ja nakyvyytta = se on
mainosarvo-motiivi. Monetisointi tapahtuu oman lisenssikerroksen kautta -> AutoCAD-leima
EI menetetA subscriptioniin siirtymalla.
- **ADN-jasenyys** hoidettava aikaisin (paasy Autodesk-ohjelmistoon kehityskayttoon,
  tukeen, kehittajatietoon).
- **Kopiosuoja/lisensointi = julkaisijan vastuu.** App Storen sisaanrakennettu
  Entitlement API hoitaa lisensoinnin ilman omaa infraa, MUTTA tukee vain kertamaksua
  (perpetual) - EI subscriptionia. -> siksi vapaa listaus + oma lisenssikerros.

## 5. Lisensointi - miten veloitetaan

**PAATOS: subscription.** Perustelut: (1) taloudellinen jatkuvuus, (2) asiakkaan
luottamus, (3) mainosarvo - tuote pysyy markkinoilla ja elavana pitkaan.

**Laskutus: Merchant of Record (MoR), EI paljas maksunvalittaja.**
- Stripe = maksunvalittaja -> jaat ITSE vastuuseen ALV:sta joka lainkayttoalueella
  (EU: ALV-rekisterointi/OSS + verotyokalut). Yksinyrittajalle suhteeton kuorma.
- MoR (Paddle, FastSpring ym.) = juridinen myyja loppuasiakkaalle: hoitaa maksun, verot
  (ml. EU:n B2B reverse charge), chargebackit, petokset. Maksut ~5-8% + ~0,50.
- FastSpring vahva tyopoyatasovellusten lisenssiavaimissa + B2B-laskutuksessa; Paddle
  SaaS-oletus. Monet niputtavat tilauslaskutuksen + lisenssiavaimet + ominaisuusportin
  yhteen SDK:hon -> sama toimittaja molempiin, yksi integraatio. (Varmista ehdot ennen
  sitoutumista - liikkuvat.)

**Lisenssimalli: per-seat.** Paivittainen, koko paivan kaytto -> ei joutokapasiteettia
-> floating ei saastaisi mitaan, lisaisi vain konkurrenssimittauksen mutkikkuuden ja
"kaikki paikat varattu" -seinan. Per-seat myos yksinkertaisin laskuttaa
(seat-maara x hinta).

**Aktivointitapa: tunnukseen sidottu, lisenssi seuraa kirjautumista
(Rhino Cloud Zoo -tyyli) - EI laitesormenjalki, EI SolidWorksin manuaalinen irrotus.**
- Laitesormenjalki = huonoin: raudan/asennuksen muutos kuluttaa slotit -> tiketteja.
- SolidWorks-malli (irrota A:lla, aktivoi B:lla): kayttaja hallitsee, mutta klassinen
  vikatila - jos unohti irrottaa tai kone hajosi, EI etadeaktivointia -> jumissa.
- Rhino Cloud Zoo -malli: lisenssi seuraa tunnusta; kirjaudut sinne missa tyoskentelet;
  ei irrotusrituaalia; toimii offline kirjautumisen jalkeen (Rhino: ~kerran viikossa
  riittaa); yksi kayttaja kerrallaan. Poistaa SolidWorks-mallin sudenkuopan. Sopii DH:n
  filosofiaan: lisenssi seuraa KAYTTAJAA, ei rautaa.
- **Kaksi konetta per kayttaja** (eta + toimisto): toteutuu tunnusmallissa jo YHDEN
  yhtaaikaisen istunnon mallilla (kayttaja vain kirjautuu sinne missa on). Halutessa
  katto 2 yhtaaikaiseen istuntoon, jos molemmat saavat olla kirjautuneina rinnakkain.
  Rajaa AKTIVOINNEISSA/istunnoissa, ALA vaadi heartbeat-leasea (toisi takaisin
  verkkoriippuvuuden).
- Itsepalvelu-vapautus (web-portaali) + seat siirrettavissa uudelle tyontekijalle
  tilitasolla (tyopaikanvaihto).

**Fail-soft = pakollinen (ei "kiva").** 8h/pv nakyva tyokalu: tarkistus vain
kaynnistyksessa/taustalla, EI KOSKAAN mattoa alta kesken istunnon. Antelias
offline-armonaika (paivia ilman verkkoa); telakkaverkko epavarma. Vain kattely +
ajoittainen virkistys tarvitsevat lyhyen online-hetken; tyo jatkuu offline. Aidosti
verkottomille koneille erillinen offline-aktivointipolku.

**DRM-budjetti oikeaan kohtaan:** suoja YTIMEN IP:hen (obfuskointi/natiivi), EI
aggressiiviseen ajonaikaiseen valvontaan. Niche-B2B:ssa liikevaihdon tappaa tyytymaton
maksava asiakas, ei piraatti. Krakattu lisenssi kapealla tyokalulla maksaa ~nolla;
lukkiutunut plugin kesken telakan toimituksen maksaa koko asiakkuuden + puskaradion.

## Avoimet kysymykset (paatettavaa myohemmin)

- Hinnoittelu (korkea hinta harvoille): tarkka hintapiste; kertahinta vs vuosihinta-tunne.
- Trial-malli (pituus, ominaisuusrajoitus vs aikaraja).
- MoR-toimittajan + lisensointi-SDK:n lopullinen valinta.
- Offline-leasen pituus (paivat) - tasapaino mukavuus vs suoja.
- Natiivi-ARX-ytimen kovennuksen ajoitus (vain jos piratismi ilmenee).
- BricsCAD uudelleenharkinta jos markkina vaatii (vaatisi BRX/.NET-haaran).
- Web/pilvi-AutoCADin tulevaisuus tyopoytapluginin nakokulmasta.
- Toinen tyokalu (DH_FILTTERI_TO_STATE) tarvitsee BricsCADille oman haaransa, JOS
  BricsCAD joskus mukaan (LayerState-COM ei BricsCADissa; vl-layerstates-* -workaround).

## Lahteet ja perusta

- Alusta-/jakelu-/verofaktat varmennettu webista (kesakuu 2026): App Store -kaytannot ja
  Entitlement-rajat; AutoCAD LT 2024+ LISP/ActiveX-rajat; BricsCAD-yhteensopivuus;
  MoR vs Stripe + EU-ALV/B2B reverse charge; Rhino Cloud Zoo vs SolidWorks-lisensointi.
- Markkina-/yritysfaktat: toimialalahteista (telakat, sisustusurakoitsijat) - tarkkaa
  yrityskokonaislukua ei julkaistu, luvut synteesia rakenteesta.
- Lisensointimekaniikka: nimettyjen tuotteiden (Rhino, SolidWorks) ennakkotapausten
  pohjalta.
- Substraatti-/IP-/arkkitehtuurinakemykset: strategista paattelya + Kimmon 30 v
  domain-kokemus (sisalta pain vahvistettu).

## Aikajanne

~1 vuosi kaupalliseen tuotteeseen (DH_visio). Vaiheistettu sekvenssi:
1. Jatka LISPilla: ratkaise algoritmi loppuun (T-haaran loput, trim/extend,
   paatykappaleet), validoi piloteilla (luotetut kontaktit, matala IP-riski; .fas/.vlx
   kevyena pidakkeena). ERISTA YDIN jo tassa.
2. Transkriboi vakiintunut ydin .NETtiin + obfuskointi; kayttaytymiskerros uusiksi
   Overrulella.
3. Julkaisu: vapaa App Store -listaus + MoR-subscription (per-seat, tunnusmalli,
   fail-soft).
