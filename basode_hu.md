# **basode: balanced software development lifecycle**

# **hozzáállási framework \+ implementációs guide v0.56**
License: GNU GPLv3. Author: akos@docca.org

Mit balanszíroz? A saját szakemberi életidőnk-figyelmünk és token költség közötti egyensúlyozás kérdéseit feszegetem, és írok le perc-életű gondolatokat, megoldásokat, és igyekszem újakat gyűjten, amik újabb perc-életeket élnek. Mert permanens forradalomban vagyunk, mint Lenin szerint a szocializmus.

Rokon törekvések, címkék: 

* spec driven development (SDD)
* Domain Storytelling
* harness engineering, platform engineering  
* delivery system-target system
* szakértői figyelem mint bottleneck

**A fő kihívások: a PRD kezelése, és a AI-ember munkamegosztási optimum keresése**

*Ez a rész elég száraz, érdemes átugrani. Utólag talán triviális.*

Nagyon tömören ez a doksi arról szól, hogy egy szoftver fejlesztési projekt esetén \- tekintettel a 2026\. Q2 helyzetre \- a fő feladat szerintem:

-  a Product Requirements Document (PRD) nagyon tudatos és hatékony menedzselése, (“name of the game: manage the PRD at the lowest cognitive effort”)   
- és a PRD változásokat megvalósító erőforrások észszerű használata. E másodikról nagyon sok szó esik a szoftveres körökben \-, YT-on, X-en, stb., különféle SDLC-k, harness-ek,  skill-ek, workflow-k, rule-ok, stb. formájában \- tehát én itt erről kevesebben fogok beszélni: mert erősen termékfüggő, gyorsan változik, és mert más források jobbak, mint amit én tudok erről mondani.

Megjegyzés: az itt tárgyalt fejlesztési szervezeti működés azonos az emberi programozók által kivitelezett fejlesztésekkel, azonban eltolódtak a súlyok a agentek és az emberi programozók erősségei és gyengeségei különbözősége miatt (pl. ár, sebesség, átfogó kép érzékelése).

A fenti mondások ezen egyszerű helyzetkép következményei: 

- Az agentek \_tényleg\_ azt valósítják meg végül, amit kérünk tőlük. Amit kérünk tőlük, az a PRD.   
  - Ezért ennek kell egyértelműnek lennie, azaz a PRD kapcsán a SSOT és DRY elvek szigorú betartása a projekt hatékonyságának az egyik kulcsa.   
  - Teljes legyen-e a PRD? Erre nincs szükség, sőt, általában lehetőség sincs (hiszen általában egy ötlettel kezdünk \- ami majd fejlődik, amint kipróbáljuk az első verziót). Egyúttal előnyös, ha a kezdeti PRD minél közelebb van a teljességhez, mert az első fejlesztési iterációban, azaz az első “mega-sprint” során relatíve olcsón kapunk egy elég jó eredményt.  
  - Hatékonyan menedzselhető PRD kell. Erre hozok itt javaslatot \- egyúttal feltételezem, hogy ebben sok új ötlet várható, amint ez a felismerés elterjedtté válik.   
    - Röviden a lényeg: inkább történetekben mondjuk el, hogy hogyan működik a rendszer, és majd abból az AI-tól várjunk száraz, logikus, hagyományos spect (amit nekünk el sem kell olvasnunk). Miért? Mert nekünk könnyebb történteket írni, érteni, hogy a *“Dezső kapott egy új munkát emailben, itt a rendszerben készített hozzá egy projektet az “új munkaszám” funkcióval, és meghívta Ágit, Ferit és Katit, akik ezáltal minden doksit láttak, amelyek ehhez a projekthez már le voltak mentve, sőt szerkesztésükben is részt tudnak venni, és kell is, hiszen ők fordítják le három nyelvre a doksikat”* \- mint tech spect írni: *“bal menü: “új munkaszám”, ez létrehozza egy új MUNKASZÁM  objektumot, amelyhez USER-eket lehet rendelni. USER jogosult a MUNKASZÁM-hoz tartozó FILE objektumokra RW joggal.”*  
- Védjük a pénztárcánkat: az agentek képességei limitáltak, de szívesen próbálkoznak mindaddig, amíg a pénztárcánk vagy más erőforrásunk engedi. Bár az agentek próbálkozásai is költségesek, de részeredményeket menedzselni \- az emberi idő értéke miatt \- is  költséges. A feladat tehát az, hogy ebben megtaláljuk az optimumot.  
- (??? készítsük el ennek a rövid változatát és keressünk helyet a bővebbnek ???) Legalább két userrel dolgozzunk: az agentek olyan mint egy távirányítható robot a lakásunkban: tud hasznosan tenni-venni, de látszólag random módon ijesztő dolgokat tehet, esetleg teljesen átvehetik felette az uralmat távolról, és ezért megváltozhat a viselkedése. Ezért a lakásunk olyan részébe engedjük csak, ahol nincs láncfűrész, nincs ott a bankkártyánk, és nincs benzines kanna és gyufa, \- sőt: telefon sincs, amivel intézkedni tud szabadon, pl. benzint és gyufát tud rendelni. 

  Visszatérve a hasonlatból az AI agentre: a viselkedését eltérítheti a szándékunktól eltérő káros irányba több dolog, ezért az agent tevékenységét izolálni kell a károkozás lehetőségétől. Ezért eleve izolált környezetben (pl. külön vm), és azon belül is több OS és git user-rel kell indítsuk a fejlesztést: van a saját, emelt jogú userünk (pl. a fejlesztő  “tom”) \- és egy vagy több user az agentek részére (gyakorlatban pl. “toms\_agent\_for\_dev” nevű user), és e másodikkal kell bent legyünk a linux shell-ben, amikor a claude-ot indítjuk. Miért kell ez? Mert az agent viselkedése váratlanul eltérhet attól, amit mi jónak tartunk, és brutális károkat okozhat pillanatok alatt: publikálhatja vagy törölheti nem publikálásra vagy törlésre szánt dolgainkat (git repó, secrets, stb.). Ennek oka lehet:   
  - rossz szándék: egy támadó, rosszindulatú prompt injection,  
  - véletlen: bármi, amit mi okozunk az interakcióink során, vagy bármilyen szövegrész, ami véletlenül bekerül az agent promptájába, kontextusába.

## **1\. Miért kell ez? A kód percek alatt kész, és az embernek nyakába szakad… **

Az ember dolga egy szoftver fejlesztési projektben manapság ennyi: megmondja az AI agentnek, hogy mit csináljon, és utána ellenőrzi a produktumot, visszajelzést ad róla az agentnek \- ezt mindaddig, amíg eléri a kívánt állapotot.

Volt egy jó kis Antigravity-s projektem. Valamiért nem szeretem ezt a “vibe coding” kifejezést \- talán, mert szeretek programozni \-, de akár így is nevezhetjük. Nagyon jól indult, hetekig hasítottam, pörögtem, hajnalig ébren, csak előre, valósítottam meg az álmaimat. De... De egyre csak nőttek az apró eltérések, néha ez, néha az, néha az agent úgy elment a bozótba, hogy alig győztem a károkat helyreállítani. Számomra érthetetlen okból elkezdett olyan dolgokon rágódni, amiket napokkal előtte tisztán elkészített \- rosszabb esetben el is kezdte szétverni a korábbi jól működő részeket. Égette a tokeneket, és a lelkesedésemet. Szép lassan ott találtam magam, hogy apróbb és nagyobb feladatok sokasága szakadt a nyakamba, és elkezdtem azt is érezni, hogy bármihez nyúlok, minden mindennel összefügg, egyre mélyebb üregekbe jutok, és már emlékem sincs a korábbi napfényes világról, ahol nemrég még szárnyaltam. Egy nyomasztó pókhálóban találtam magam, ahol ezer kicsi szál húzott erre és arra, és bár egyenként mindegyik kezelhetőnek tűnt, de ahogy egyet húztam, jött vele még sok másik szál.

Ekkor önvizsgálatot kellett tartsak, és a projektemre is rá kellett nézzek. Mit is csinálok tulajdonképpen? Az AI agent működését \- rule-okat, skilleket, stb-t \- is folyamatosan csiszolom, a termékemnél mozgó célpontra lövök \- hiszen épp fejlesztek \-, és ennek megfelelően közben az infrát és a deploy folyamataimat is állandóan ehhez igazítom. Káosz? Követhető? Mit is kell most csináljak?\!

Ez a gondolati keretrendszer \- balanced SDLC (basode) \- azoknak szól, akik többé-kevésbé hasonlókat élnek meg, vagy ha okosabbak nálam: még előtte felismerik, hogy ezt el szeretnék kerülni.

### **Egyensúly az ember és AI agent munkamegosztásában**

Mivel az agentic fejlesztésben a programozási és programozott tesztelési munka már autonóm módon, ember nélkül zajlik, a projekt hosszát a a klasszikus fejlesztési projektek maradék \- emberre maradó \- feladatainak számossága és párhuzamosíthatósága adja, a költségét pedig az élőmunka és a tokenek.

A basode alapvető célja a humán-AI munkamegosztás optimalizálása. Másként fogalmazva: egy szoftver fejlesztési projekt fő szervező szempontja: az AI felé delegálás optimalizálása. Ha hatékonyságra optimalizálunk \- avagy az én esetemben: szeretném elkerülni a kiégést \-, akkor ilyesmi az egyik fő szabály: káros döntés bármilyen munkát vagy felelősséget átvenni az AI modelltől olyan területeken, amelyeket az AI gazdaságosabban képes elvégezni.

Hogyan tudhatjuk megmondani, hogy hol van ez a határ? Változó. A gazdaságosságok az is meghatározza, hogy az agent viselkedését mennyire tudjuk szabályozni, és a kívánt eredménytől való eltérő outputjait mennyire kevés élőmunkával és mennyire korai stádiumban \- kevés idő és token elégetése után \- tudjuk észlelni. Ha ez egy vállalati projekt, akkor a rosszul megválasztott határ vagy hibás szabályozás elkezdi emészteni a pénzt \- ha pedig hobby projekt, mint az enyém volt: az fejlesztő kikészül, belefullad a feladataiba. Miért? Mert részben brutálisan magas kognitív fókuszt és időt igénylő munka szakad rá: precíz és egyértelmű specifikálás, és eredmény doksik alapos átolvasása, korrigálása, másrészt brutálisan sok favágó munka: a tesztelés. Legalábbis velem ez történt. Ez volt a kiindító oka, hogy ez a gondolati framework megszületett \- mert ez a nyomás volt az én személyes megélésem az AI támogatott fejlesztésben. Ha ez a vibe coding, akkor én kikészültem tőle.

A két tanulság, amit leszűrtem: a humán aktor szerepe akkor van jól minimalizálva, ha:

* Feladat definiálása az agent számára: itt szigorúan minimális transzformáló feladatokat kell vállalni. *Erre még visszatérünk \- mert kucsfontosságú. Röviden: mesével, történettel definiálom a feladatot.*   
* Végrehajtás ellenőrzése: az eredmény validálása is a legkisebb időt és kognitív terhet rak az humán szereplőre.  
* Visszajelzés: minimális munkával. Az egész körforgás egyik kifejezetten élőmunka igényes része lehet, hogy pontos visszajelzést adjunk.

Erről fog szólni ez az iromány.

—-

### **Az igényspecifikáció (PRD): a fejlesztés programja. Ezért ez sok figyelmet igényel.**

Hagyományos szoftverfejlesztési környezetben a projektmenedzsment konzisztenciáját a humán aktorok soft skilljei, a formalizált SOP-k (Standard Operating Procedures), valamint az íratlan közösségi normák és implicit munkaköri leírások adják.

Az AI programozó agentek belépésével ez a helyzet megváltozott. Az ágensek nem rendelkeznek a számukra megmutatott adatokon kívül a helyi tudással; működésük szigorúan az explicit módon biztosított kontextusra korlátozódik. A munkavégzés szabályai immár nem a projekten kívül lebegő fejlesztői közösség (cég, open source közösség, stb.) kultúrájának részei, hanem a projekt elválaszthatatlan, kódként kezelt (**Docs-as-Code**) komponensei.

A **basode** keretrendszer erre az architekturális kényszerre reagál. A korábban lágy, humán-vezérelt folyamatokat gépileg értelmezhető, nyomon követhető szabályrendszerré konvertálja. Ez radikálisan kitágítja az AI ágensek autonómiájának határait, képessé téve őket komplexebb és vertikálisan kiterjedtebb fejlesztési iterációk önálló végrehajtására.

## **2\. Hasonlatok: a tó víztükre és a kettős könyvelés**

A basode egyik fő pillére a rendszer pillanatnyi állapotának transzparenciája: ránézésre egyértelműnek kell lennie, hogy a rendszer stabil (stable), vagy változás zajlik benne.

### **A tó és a hullámok**

A stabil rendszer egy tó, amelynek tükörsima a felülete. Az új igény egy impulzus, amit valaki beledobott a tóba. A basode a fizikai törvények gyűjteménye, amelyek a tó működését modellezik azzal a céllal, hogy a végén újra sima legyen a vízfelület.

Ha rossz a modellezés (rossz a fizika), az igények egymásra hatása nem csitul el, a tó állandóan hullámzik, és rengeteg erőforrás (token, idő, pénz) megy el a víztükör helyreállítására. A jó fizika biztosítja, hogy minden inger – legyen az nagy szikla vagy kicsi tavirózsa – gyors és hatékony csillapodáshoz vezessen.

### **A számviteli analógia és a kettős könyvelés**

A számvitelben egy új számla (impulzus) hatása legkésőbb az év végi mérlegben foglalja el a helyét, kialakítva az új egyensúlyt. Egy utalásos számla esetén a nyilvántartás kettéválik:

1. Az üzleti eredményt azonnal befolyásolja a bizonylat összege (kötelezettség).  
2. Egy másik szinten várjuk a bankszámlán a fizikai pénzmozgást (teljesítés).

A basode ezt az elvet adaptálja. Szigorú kötelezettség, hogy minden új igényt két helyre "könyveljünk":

* **Állapot-kimozdítás a dokumentációban (SSOT):** Beírjuk a kész rendszer működési dokumentációjába egy explicit WIP (work in progress) jelöléssel.  
* **Állapot-kimozdítás a feladatkezelőben (Kanban):** Létrejön a feladat, amely a fizikai megvalósulást várja.

Ahogy a feladaton végrehajtjuk a műveleteket, a megvalósítással eltűnik a "tartozás": lezárjuk a jegyet, és kivesszük a WIP jelzést a dokumentációból. A tó elcsitult, a kő leért az aljára, új stabil állapot jött létre.

## **3\. Egyszerűen: két rendszer, a target és a delivery system**

### **A kettős termékmodell (3D nyomtató analógia)**

A basode állítása, hogy \- minimál helyzetben \- egyidejűleg két rendszert fejlesztünk:

* **Target System (Termék A \- a "pohár"):** A létrehozandó szoftverproduktum.  
* **Delivery System (Termék B \- a "3D nyomtató"):** Az előállító módszer, infrastruktúra és folyamatrendszer. A Delivery System módosítása mindig prioritást élvez a Target Systemmel szemben, mivel rosszul kalibrált gépsorral nem lehet jó terméket gyártani.

### **Kétrétegű… és N-rétegű termékdoksi (doclayer1…N)**

Egy-egy rendszer fajsúlyos, emberi munkát igénylő része \- az agentek korában \- az igényspecifikáció \- ezt hívom én a dokumentáció első rétegének, röviden doclayer1. Ebből az agentek szorgos munkával képes előállítani a végterméket, akár egy lépésben, akár több lépésben, azaz több doclayer által. 

* **doclayer0 (Ideák):** Strukturálatlan, rendszeren kívüli igények (a repülő kő).  
* **doclayer1 (TargetSpec):** A legnagyobb emberi felelősség: ember által ellenőrzött igényspecifikáció, a globális SSOT (a becsobbant kő) \- szoftver esetén ez a PRD.  
* **doclayer2...N-1 (Átmeneti modellek):** Technikai specifikációk, ábrák, ahol az aktorok pontosítják a feladatot. BDD, Gherkin.   
* **doclayerN-a (Aktív implementáció):** Tesztek, futó forráskód és infrastruktúra (a tó medre és a víz).  
* **doclayerN-b (Passzív projekciók):** User manual, tudásbázis,. A rendszer automatikusan generálja ezeket a ciklus végén (tájékoztató táblák a tó partján).

### **Triage és Instant-issue**

* **Triage:** A pillanat, amikor az idea zöld utat kap, és megtörténik a kettős könyvelés (WIP injektálás és Kanban jegy nyitása).   
* **Instant-issue:** Az "Andon cord" megfelelője. Alulról építkező kritikus hiba esetén a pipeline azonnal befagy, és soron kívüli hatásvizsgálat, majd javítás következik.

## **4\. Bonyolítsuk: sok rendszer rendszer, mint a hagyma héjai \- és a tulipán: a termék**

A valóságban a "Target" és a "Delivery" fogalma relatív \- bár az esetek jelentős részére elégséges (pareto elv, és ne bonyolítsuk, amit nem kell). A rendszerünk: rendszerek rekurzív láncolatából áll. A rendszerünk: egy tulipán hagymája. Indulunk a gondolattal \- ez a tulipán magja, amiből elkezd fejlődni a hagyma, ahogy fejlődik, azzal hozzuk létre egyre beljebb az újabb és újabb héjakat, végül egy csodálatosan komplex eredményt, amiből majd szerencsés esetben kinő egy szép virág. Először a külső héj: a projekt ötletgazdája megalkotja a projekt célokat, a következő szerepkörök termékké formálják, fejlesztési munkafolyamatokat állítananak fel (agent-barát SDLC-t, harness-t), infrát, stb., és legbelső mag, a termék, amit létrehozunk.

* **syslayer1: ötlet, vízió, FOUNDER**  
* **syslayer2a: Delivery Architect, DA (vagy bármi más titulus. A lényeg: aki az SDLC szabályaiért, a harness építésért és karbantartásért felel. Pl. a basode-t implementálja a helyzetre.)**   
* **syslayer2b: Product Owner, PO.** A vízióból (kezdeti doksi) és a user igényekből (a tóba dobált kövek, avagy a kanban-ba jövő ötletek, bugfixek, stb.) készít igényspecifikációt, azaz target system/doclayer1-et  
* **(ezt nem használom: syslayer2c: Marketing** A víziót viszi a “piacra”: közösség elé, befektetők felé, vevők felé, stb.)   
*  **syslayer3a: az infrastruktúra és deploy felelős, INFRA (vagy DevOps, vagy Platform Engineer):** a dev infra, és CI/CD tervek és pipeline-ok felelőse. Lehetne egy syslayer2c is, most nálam 3a  
* **syslayer3b: DEV, (a Dev Tools / Agents):** Fejlesztési szabályok, ágens profilok, szkriptek.  
* **syslayer4 (Product):** A belső mag (üzleti specifikáció és kód). Ez a gyönyörű tulipán, amit a hagyma hozott létre: a külső héjtól \- az ötlettől/víziótól kifejlődött a hagyma, és legbelső részéből kibújt a tulipán.

A feladatkövetés ebben a modellben lehet elosztott (külön csapatok esetén) vagy monolitikus (agentic fejlesztésnél), ahol egyetlen Kanban táblán metaadatok jelölik az érintett hagymahéjat.

## **5\. \-**

## **6\. A keretrendszerünk rokonsága: felmenők, oldalági rokonság**

A basode nem vákuumban jött létre, hanem bevált iparági sztenderdek és architekturális elvek rendezgetése az AI agentic development optimalizálása céljából. A keretrendszer az alábbi rendszerekből merít inspirációt, és azokat adaptálja az Agentic AI korszakára:

### **Tartalmi és módszertani átfedések**

* **Számvitel (IFRS / Kettős könyvvitel):** A tranzakciók determinisztikus, szinkronizált rögzítése a rendszer stabilitása érdekében. A WIP injektálás és a Kanban jegykezelés analógja az üzleti eredmény és a bankszámla-egyenleg vezetésének.  
* **Agile / Kanban:** Az iteratív végrehajtás és a vizuális feladatkövetés (pull system), amely egyértelműen jelzi a rendszerben lévő feszültséget.  
* **ITIL / ITSM (Változáskezelés):** A basode *idea-to-delivery* ciklusa a klasszikus ITIL Change Management (Változáskezelés) folyamatának modern adaptációja. Az ITIL-ben a Request for Change (RFC) életciklusa – a rögzítéstől az értékelésen (Triage) és engedélyezésen át a megvalósításig és lezárásig – logikailag megegyezik a basode állapotvezérelt Kanban és WIP-injektálás metódusával. Emellett az Instant-Issue eljárás szorosan rokonítható az ITIL Emergency Change (sürgősségi változtatás) protokolljával és az Andon cord megszakítási mechanizmussal.  
* **GitOps / DevOps:** Az infrastruktúra, a folyamatok és a specifikációk kódként való kezelése (Docs-as-Code), a Single Source of Truth primátusának biztosítása.

### **A legfontosabb elvi metszéspontok**

* **Karbantarthatóság (DRY \+ SOLID \+ LoD):** Mindhárom alapelv célja, hogy ha egy helyen belenyúlunk a rendszerbe, ne törjön el tíz másik helyen. A basode a Delivery és Target System, valamint a doclayer és syslayer rétegek szétválasztásával biztosítja ezt az architekturális izolációt.  
* **Egyszerűség és fókusz (YAGNI \+ SMART \+ Pareto-elv):** Ezen elvek mindegyike a fókuszt védi. Azt mondják: *"Ne csinálj többet, mint ami a cél eléréséhez feltétlenül szükséges."* Az Agentic AI fejlesztésben ez kritikus: az ágensek token- és kontextusablak-fogyasztásának, valamint a felesleges "hullámverésnek" a minimalizálását szolgálja.  
* **Helyesség és konzisztencia (SSOT \+ DRY):** Ez a duó garantálja, hogy ne legyen két különböző verzió ugyanabból az igazságból (legyen az adat, algoritmus vagy specifikáció). A kettős könyvelés ellenére a basode szigorúan védi az egyetlen igazságforrást (SSOT), megakadályozva az állapot-töredezettséget.

## **7\. Gyakorlat: megfontolandók**

### **7.1. Specifikálás: Az SSOT előállítása (Transzformáló szerep)**

A projekt indítása előtt stratégiai döntést kell hozni: mi lesz a rendszer legfelsőbb szintű leíró módja (doclayer1 SSOT), és mik a származékos (read-only) kimenetek (doclayerN-b)?

Főbb opciók a normatív SSOT felépítésére:

* **NEM AJÁNLOM: Hagyományos igényleírás / User Manual stílus:** (pl. *"A rendszernek képesnek kell lennie PDF formátumban riportokat készíteni..."*). Emberi oldalról rendkívül munkaigényes és kognitívan kimerítő a fenntartása.  
* **AJÁNLOM: Sztorik / Forgatókönyvek összessége:** Mesélő, történetalapú specifikáció (User Stories/Scenarios). Sokkal természetesebb a humán aktor számára, ugyanakkor a fejlett LLM-ek kiválóan képesek ebből pontos technikai követelményeket és architektúrát generálni.

### **7.2. Ellenőrzés, megértés: az emberi felelősség \- és munka**

Az emberi fókusz drága erőforrás. A két legnagyobb időrabló tevékenység a validációs fázisban:

1. **Humán tesztelés (UAT):** A megvalósult, futó termék manuális, emberi kipróbálása.  
2. **Generált dokumentációk felelős auditja:** A végső (eredmény) dokumentációk átolvasása és jóváhagyása. Gyakorlatban ez azt jelenti, hogy a “user manual” jellegű doksit ugyanúgy outputjai az agent munkájának, mint a programkód \-\> se user manual doksit, se program kódot nem írok, hanem agent írja.

Ezeket a feladatokat tervezni kell, és ahol lehetséges, ágens-alapú verifikációs tesztekkel (pl. E2E Gherkin) kell helyettesíteni az emberi kattintgatást.

## **8\. Gyakorlati alkalmazás (Playbook)**

### **8.1. Zöldmezős (Greenfield) projekt checklist**

Induló projekt esetén a basode bevezetése egy determinisztikus önértékeléssel kezdődik:

1. **Rendszerek azonosítása:** Melyek a syslayer-ek (pl. van-e dedikált DevOps)?  
   * én mindegyiknek készítek egy “docs\_of\_\$role” vagy “docs\_of\_\$syslayer” foldert, és abban “dl1\_of\_\$role” és … “dlN\_of\_\$role” foldereket, néha dl1a\_of\_\$role és és dl1b\_of\_\$role \- de ez egyéni ízlés kérdése, hogy alatta bontja-e valaki folderekre, vagy itt fent.  
2. **Dokumentációs szintek definiálása:** Mettől meddig tart a doclayer1, és mik a read-only kimenetek?  
3. **Kanban topológia:** Monolitikus (egy tábla) vagy egyes hagymahéjak esetén saját feladatkövetés az optimális?   
4. **Egyszerűsítés (YAGNI):** Cél a struktúra végletes lecsupaszítása a felesleges komplexitás elkerülése végett (minimál mappa struktúra, alap .agent/\* fájlok rögzítése).  
5. **WIP wrapping szintakszis:**  
   * Mindenki kisütheti a sajátját, ez egy példa. Figyelem: aki Obsidian-t használ, vagy más md editort, utólag látom, hogy vannak szabványos megoldások \- érdemes ezeket megfontolni. Egyúttal valahogy limitáltak, ezért végülis nem volt olyan erős érv, hogy migráljak át azokra.  
   * Most tehát már kicsit másként csinálnám, de jelenleg: **\*\*WIP \[CREATE\|BUGFIX\|DEPRECATED\] kanban=”akármi\_NNN”\*\* …itt van a tartalom… \*\*/WIP kanban=”akármi\_NNN”\*\***  
   * **Bugfix vs változás kérés:** az természetes, hogy ha befogadunk egy ideát (változás kérést), akkor a DL1-en WIP injektálással ezt jelezzük. A bugfix eltérő: egyrészt itt egy belső megegyezés szerinti szintaktikával jelezzük, hogy egy bugfix. Másrészt a DL1-be nem végső módosításként, hanem átmenetileg kerül be. Létének célja DL1 dokumentációban \- WIP injekcióval \- az, hogy ha esetleg több bejelentés is érkezne azonos bug kapcsán, akkor a “tóba csobbanás” előtt az újabb, azonos kéréseket ki tudjuk szűrni, és elutasítani, hiszen bugfix-WIP bejegyzés által látjuk, hogy ez a téma már napirenden van. Amint sdlc flow ezen jegyre véget ér, azaz amint a hiba javításra került, akkor a DL1-ből eltávolítható a teljes WIP injektum.  
   * “Deprecated”: desztillációkor töröljük a wrapperek által közrefogott részt.  
6. Antigravity által adott “implementation\_plan” hasznos dokumentum, de konvertálandó:  
   * Opció 1.: kanban jegyekre \- és ha egy teljesen új témakört hoz létre, akkor a megfelelő SSOT dokumentumokat is létre kell hozatni az agenttel (és ide jön a WIP injekciók jelentős része az új kanban jegyekből)  
   * Opció 2.: esetenként le lehet programoztatni direkben, ha tudjuk garantálni, hogy a doclayer-ekben utána tényleg le lesz dokumentálva, ha a tesztek szükségtelenek, stb. \- azaz valamiért az SDLC lépései elhagyhatóak, és nem akarjuk égetni a tokeneket a formalitások betartatása érdekében. Nyilván: óvatosan ezzel, mert megbosszúlhatja magát a spórolás.

### **8.2. Futó projekt migrációja (Brownfield / Rescue)**

Amikor egy meglévő agentic projekt a "szétesés jeleit" mutatja (kontextusvesztés, token-égetés, hibás kódmódosítások), a basode implementációja az alábbi szigorú iterációt követi:

1. **Ideális állapot definiálása:** A 8.1-es pont alapján a célstruktúra felvázolása.  
2. **Gap analízis (Önvizsgálat):** Mi a valóság? (Folderek, folyamatok, .agent fájlok állapota, az SSOT töredezettsége).  
3. **Migrációs terv:** Készítsünk ágens-vezérelt szkripteket, teszteket és hozzuk létre a hiányzó dokumentációs rétegeket Ez maga is AI feladat \- de átnézni, javítani: itt van a munka\! Ez kőkemény, időigényes, nem kreatív \- és ezért veszélyesen unalmas feladat **Vigyázat\! Ez maga a lényegi feladat\!** Nekem pl. ez egy 230 oldalas generált doksi volt, mesék, történetek, itt buktak ki azok a fogalmi zavarok, megismételt történetek, amelyek kis eltéréssel ugyanazt tartalmazták, de korábban félrevitték a fejlesztést.   
4. **Nyitott kanban jegyek konverziója:** WIP injektumok létrehozása. Ezt agent csinálja, két aleset van:  
   * A jegy egy, a dl1-ben már  leírt helyzetről szól. Ez esetben a doksiban csak a jegy által megvalósítandó részt tesszük WIP wrappingbe \- és esetleg a jegyre visszaírjuk a WIP mezőbe az injektumot, de ez nem annyira fontos.  
   * A jegy egy olyan ötlet, változás “köve a tóban”, ami még nincs rávezetve a dl1-re. Ez esetben fullos WIP injekció: a jegyre is, és a dl1-be is. 

*Megjegyzés:* A basode keretrendszerben \- legalábbis nálam \- a syslayer-ek (a hagymahéjak) a projekt legvégéig folyamatos fejlesztés alatt állnak. Az ember-gép elvárások közötti eltérések is folyamatosan visszahatnak a rendszer elemeire.

### **8.3 basode átszervezés: megvastagodott layerek szétválasztása **

Ez akkor áll elő, amikor a kiindulási állapothoz képest megváltoznak a hangsúlyok, és például egy tesztelési komponens egy kis agent workflow lépésből vagy egy rule-ból önálló dokumentációs layer-ré változik. Erre nincs kialakult gondolatom, inkább csak gyakorlatom. Az előző fejezetbe viszem ezt be: a feladat szerkezetében azonos, kiterjedtségében kisebb. 

## **9\. Az agentic működés felállítása, karbantartása: harness development (Claude Code és AGY)**

Ahhoz, hogy az adminisztráció és a kettős könyvelés ne emberi teher legyen, magukat az autonóm ágenseket kell felkészíteni a basode szabályainak betartására.

### **Ágens képességek (Skills)**

* **Információ-allokációs logika:** Az ágensnek képesnek kell lennie eldönteni, hogy egy újonnan generált vagy kapott információ a DRY és az SSOT elvek alapján melyik syslayer melyik doclayer rétegébe való. Nem duplikálhat, hanem a meglévő helyre kell fűznie, vagy pointert kell létrehoznia.

### **Ágens szabályok (Rules)**

* **SSOT Registry iteráció:** Információ keresése esetén az ágensnek ismernie kell a dokumentációs láncolatot. Tudnia kell, hogyan navigáljon a legfelsőbb narratív szinttől a technikai megvalósításig anélkül, hogy elveszne a kontextusban.  
* **Injektálási jogosultság (Write protocol):** Szabályozott hozzáférés és szintaktikai fegyelem (\<wip\> markerek elhelyezése) az információk dokumentációs rendszerekbe történő beírásakor, garantálva a kettős könyvelés szinkronitását.

### **Ágens folyamatok (AGY: workflows, CC: commands)**

Itt vannak az sdlc lépései (nálam: specifikálás, tesztfejlesztés/fejlesztés, tesztelés, desztillálás), továbbá karbantartó folyamatok.

## **10\. DRY+SSOT miatt dilemma: a segéd-scriptek és a help-jük szorossága**

A fejlesztő (és PO és mindenféle pozícióban lévő) ember/agent csomó segéd programot (nevezzük most így: script)  használ, pl.: rendszer indításra, debuggolásra, rendszeres konverziókra, stb. (A jellemzően a scripts/ vagy hack/ folderben lévő segédprogikról beszélek.) A hatékony munkához ezeket ismerni kell, mindig a megfelelőhöz nyúlni \- különben pazaroljuk az időt, tokent. 

Az egyik gyakorlati alapmondás: mindegyikhez készítek agent skill-t ([https\://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) \~ [https\://antigravity.google/docs/skills](https://antigravity.google/docs/skills) ).

Mivel törekszünk arra, hogy a kód (segéd scriptek mindenképp) és a doksija legyen nagyon szorosan összekötve (általában “script –help” módon is lekérhető), cél: ha valamit matatunk a scripten, akkor legyen nagyon az aktor szeme előtt (kontextben) maga a doksi is.   
Lehetőségek, amiket én találtam \- egyúttal egy dilemma is:

- shdoc, POD és hasonló kódba \- kommentként \- integrált doks készítés. Hátrány: DRY sértés. Mert:  
- a [SKILL.md](http://SKILL.md) \-ben \- az agentic fejlesztői rendszerek működése miatt \- ugyanez az infó kötelezően megjelenik. Ennek feloldása: a scriptek a [skill.md](http://skill.md)\-ből egy meghatározott frontmatter mezőből mutatják a saját help-jüket. Hátrány: nincs elég szorosan a scripthez kötve a help-je, doksija.  
- [skill.md](http://skill.md) mellé/alá pakolni. Valahogy ellenérzéseim vannak ezzel kapcsolatban jelenleg, mert elszakítja a projektben emberi aggyal megtalálható helyéről, és \- mivel beteszi a harness által meghatározott skill folderekbe \- harness specifikussá teszi.

## Appendix 1: todo, merge előtti ötletek

### izoláció agent eltérítési veszélyek miatt \#harness\_engineering

projekt alapozáskor: külön oprenszer user az agenteknek, név alapján is megkülönböztetni, hogy mi emberek, soha ne felejetsük el, hogy azzal a technikai user objektummal ne osszunk meg olyasmit, amiből baj lehet. 

### (git) repó izoláció hagymahéj rétegenként

vagy minimum: delivery system, target system

### Delivery system fájlok verziózása

fontos, mert anélkül, hogy tudnánk, hogy melyik állapot okozta a bajokat, nehéz \- inkább: lehetetlen \- hatékony önjavító intézkedéseket foganatosítani: scrip, skill, command, hack

### Agent\<-\>humán párbeszéd formája: chat helyett egy agent+user közösen szerkeszett/commentelt fájl

Alapban dolgozzunk plan\~canvas\~artifact fájlokon, és a chat legyen a kivétel.

Miért? Mert a chat humán szemmel/aggyal nem követhető: ha kapunk a chat egy pontján egy 15 pontos hibalistát, és abból belemegyünk az egyikbe, és elkezdünk foglalkozni vele, akkor hogy tekintjük át, hogy a 15-ből hogy állunk a többivel, pláne ha költsönhatásuk is van. Ennél jobbnak találom, hogy mint egy docx-et vagy google doksit: kommenteléssel és változáskövetéssel egyeztetnek le a felek \- itt nem veszik el semmilyen alapinfó, és addig tudjuk szerkeszteni a doksit, amíg minden kérdést nyugvópontig viszünk. Ehhez tehát szerintem fontos szempontok:

#### emberi szemmel könnyen követhető párbeszéd

ahogy egy docx-nél/g doksinál: látszódjon ki mit mondott, könnyen észlelhetően, egyértelműen, hogy mi a friss, nyitott ág, és mik a korábbi körök kérdés-válaszai, milyen témák vannak megválaszolva,

#### keep-it-lean: a irreleváns részek (eldöntött dolgok, szemét) folyamatos hátra pakolása

a vitális fájlból egy letisztultabb doksiba. miért?

* enélkül nehéz lesz áttekinteni, hogy miket kell még megbeszélni  
  * és sok tokent zabál a fájl léte a contextben, az oda-vissza mozgatása  
  * a letiszult doksi kelleni fog a végén

#### legyen jó helyen az a plan fájl, amin beszélgetünk

CC: a configban (settings.json) meghatározható a plan fájl helye ("plansDirectory") . megfontolások: mennyire közös a projekt többi emberi dolgozójával (azaz legyen-e gitben ez), és mennyire projekt specifikus, és a “gyakorlati megoldások” alatti workflow-hoz illeszkedjen, azaz optimalizáljunk arra, hogy ne kelljen másolgatni a harness/agent által szerkesztett fájt és azt, amit mi is nézegetünk-annotálunk-módosítunk.

#### gyarkolati megoldások

biztos vagyok benne, hogy erre van jobb megoldás, és szeretném valakitől eltanulni. egyelőre ezeket az irányokat vizsgáltam:

##### AGY: artifact

ez alapvetően nagyon oké volt, amikor 2026 tavaszáig AGY-t használtam

##### CC terminál: ctrl+G

ehhez érdemes választani egy kézre eső terminálos editort. előnye, hogy nagyon token kímélő.

##### CC desktop app: plan-re dialógus skill-ek kialakítása

én erre csak token-drága megoldást találtam, tehát ez kényelmes, de költséges \- egyúttal nekem ez az egyik fő használati formám, mert kényelmes, gyors

##### harness neutrális megoldások:

###### *ping-pong (git) repón keresztül, közte barátságos md editor, pl. Obsidian*

agent élettere dev-\>git-\>user friendly desktop md szerkesztő (pl. Obsidian) itt nézzük, szerkesztjük \-\>git-\>dev (én elég béna vagyok git ügyileg, ezért nekem ezzel állandó küzdelmem van, de aki ügyesebb, annak két klikkelés asszem \- egyúttal nekem ez az egyik fő használati formám)

###### *VS Code-on keresztül*

agent élettere dev-\>direkt elérés (pl. ssh tunnel) VS Code, itt nézzük, szerkesztjük \-\> save vagy push az agent környezetébe vissza.  
nem találtam arra megoldást, hogy md viewer-ben szerkeszthessem a fájlt (md forrás ctrl+shift+V), csak a forrásban, ami oké, de nem nyerek vele semmi a terminálhoz képest, ezért ezt elvetettem \- de könnyen lehet, hogy csak én nem találtam meg a megfelelő VS Code plugint

### A megőrült, kártékony agent veszélyei: “távirányított robot az otthonunkban” hasonlat

A “megőrült a robotom, eladta a házam 1 kifliért,egy láncfűrésszel szétvágta a családi bútorszéfünket, majd telefonon kihívott egy zárszakértők, hogy nyissa ki a bejárati ajtót, és kiengedtette magát, és elment” történet. Megvilágítani azt, hogy mik a határok a tool-ok, secretek megosztásában, hogyan egyensúlyozzunk a hatékonyságunk és a biztonság között.

### Hatékonyság: agent által kezelhető teszt környezetek

A harness/platform engineer feladata: nagyon fontos, hogy az agent tudjon dolgozni önállóan: módosítani, teszteket írni-végrehajtani, debuggolni. Enélkül megakad a szárnyalás: emberi favágómunka akasztja majd meg az olajozott sdlc-nket. Meghatározni az uat és prod környezetekben az emberi kontribúció fontosságát, különösen: titok kezelés és költség korlátok.

### Költség kontroll

Harness/platform engineer feladata megkeresni azokat a költség korlátozási lehetőségeket, amelyek az elszálló token- vagy más erőforrás költségeknek határt szab. SaaS szolgáltatói limitek beállítása, ahol nem lehet hard limitet, ott alertek.

### Erőforrás kontroll

Ha egy agent el kezd dolgozni, magától nem figyel arra, hogy a disk, memó vagy bármi más elfogy vagy észszerűtlenül nagy igénybevétel történik. Ezért harness/platform engineer feladat az is, hogy a megfelelő pontokra ezen szempontokat beszúrja. Velem megtörtént: a vm-em egyszercsak elérhetetlenné vált, ezért hard reboot kellett, mert olyan vagy olyan sok docker containert indított el az agent, amik kinyírták a gépet \- és ezért elvesztettem értékes fejlesztési állapotokat, hiszen a többi session alól kirúgra a gépet ez a session.  
