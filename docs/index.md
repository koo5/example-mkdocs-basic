# Advanced Mail Transfer Protocol (AMTP) - dokumentace

**UPOZORNĚNÍ:** Toto je pouze návrh, nejedná se o konečnou verzi protokolu.

**Verze návrhu: 1**

## Obsah

`1.` [**Úvod**](#about)  
`2.` [**Předmět dokumentace**](#subject)  
`3.` [**Důvody vytvoření**](#arguments)  
`4.` [**Cíle protokolu**](#objectives)  
`   4.1.` [**Modularita**](#objectives-modularity)  
`   4.2.` [**Distribuovaná síť**](#objectives-distnet)  
`   4.3.` [**Zabezpečení**](#objectives-security)  
`5.` [**Zvolené standardy**](#standards)  
`6.` [**Srovnání s jinými protokoly**](#comparison)  
`   6.1.` [**Co má AMTP společného se SMTP**](#comparison-smtp-similar)  
`   6.2.` [**Čím je AMTP odlišný od SMTP**](#comparison-smtp-diff)  
`7.` [**Architektura**](#architecture)  
`   7.1.` [**Síť**](#architecture-network)  
`   7.2.` [**Server**](#architecture-server)  
`8.` [**Spojení**](#connection)  
`9.` [**Formát dat**](#data-format)  
`10.` [**Identita**](#identity)  
`11.` [**Zabezpečení**](#security)  
`   11.1.` [**Šifrovaný přenos dat**](#security-transfer)  
`   11.2.` [**Koncové šifrování**](#security-e2e)  
`   11.3.` [**Bezpečné přihlašování**](#security-login)  
`12.` [**DNS**](#dns)  
`   12.1.` [**Konfigurace DNS**](#dns-config)  
`13.` [**Příkazy**](#commands)  
`   13.1.` [**Připojení klient-server**](#commands-c2s)  
`        13.1.1.` [**Příkazy správce**](#commands-c2s-admin)  
`               13.1.1.1.` [**Přihlášení správce**](#commands-c2s-admin-login)  
`               13.1.1.2.` [**Systémové informace pro správce**](#commands-c2s-admin-sysinfo)  
`               13.1.1.3.` [**Odhlášení správce**](#commands-c2s-admin-logout)  
`               13.1.1.4.` [**Seznam domén**](#commands-c2s-admin-domains-list)  
`               13.1.1.5.` [**Přidání nové domény**](#commands-c2s-admin-domains-add)  
`               13.1.1.6.` [**Úprava domény**](#commands-c2s-admin-domains-set)  
`               13.1.1.7.` [**Smazání domény**](#commands-c2s-admin-domains-del)  
`               13.1.1.8.` [**Seznam uživatelů**](#commands-c2s-admin-users-list)  
`               13.1.1.9.` [**Přidání nového uživatele**](#commands-c2s-admin-users-add)  
`               13.1.1.10.` [**Úprava uživatele**](#commands-c2s-admin-users-set)  
`               13.1.1.11.` [**Smazání uživatele**](#commands-c2s-admin-users-del)  
`               13.1.1.12.` [**Seznam aliasů**](#commands-c2s-admin-aliases-list)  
`               13.1.1.13.` [**Přidání nového aliasu**](#commands-c2s-admin-aliases-add)  
`               13.1.1.14.` [**Úprava aliasu**](#commands-c2s-admin-aliases-set)  
`               13.1.1.15.` [**Smazání aliasu**](#commands-c2s-admin-aliases-del)  
`               13.1.1.16.` [**Doménový koš**](#commands-c2s-admin-catchall)  
`               13.1.1.17.` [**Seznam správců**](#commands-c2s-admin-admins-list)  
`               13.1.1.18.` [**Přidat nového správce**](#commands-c2s-admin-admins-add)  
`               13.1.1.19.` [**Úprava správce**](#commands-c2s-admin-admins-set)  
`               13.1.1.20.` [**Smazání správce**](#commands-c2s-admin-admins-del)  
`        13.1.2.` [**Příkazy uživatele**](#commands-c2s-user)  
`               13.1.2.1.` [**Přihlášení uživatele**](#commands-c2s-user-login)  
`               13.1.2.2.` [**Informace o serveru pro uživatele**](#commands-c2s-user-serverinfo)  
`               13.1.2.3.` [**Odhlášení uživatele**](#commands-c2s-user-logout)  
`               13.1.2.3.` [**Informace o uživateli**](#commands-c2s-user-userinfo)  
`   13.2.` [**Připojení server-server**](#commands-s2s)  
`   13.3.` [**Komunikace s moduly**](#modules-communication)  
`14.` [**Přiklady**](#examples)  

## Úvod <a name="about"></a>

**Advanced Mail Transfer Protocol (AMTP)** je nová generace síťového protokolu elektronické pošty (e-mailu), která překračuje omezení tradičních protokolů [**SMTP**](https://cs.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol), [**POP**](https://cs.wikipedia.org/wiki/Post_Office_Protocol) a [**IMAP**](https://cs.wikipedia.org/wiki/Internet_Message_Access_Protocol) využitím nových technologií a modulární koncepce. Plně tyto protokoly nahrazuje a dokonce významně rožšiřuje jejich použitelnost. Zároveň používá modernější, bezpečnější metody přenosu dat v reálném čase.

## Předmět dokumentace <a name="subject"></a>

**Tento dokument popisuje**:
- **síťový protokol** - vysvětluje způsoby, kterými jsou data přenášena, formátována, přeposílána, způsoby zabezpečení z vnějšku apod.

**Tento dokument nepopisuje**:
- **implementaci** - specifikace implementace serverové a klientské aplikace - např. způsoby ukládání a zpracování dat - toto spadá do kompetence konkrétních softwarových implementací
- **moduly** - specifikace jednotlivých serverových a klientských modulů - např. zasílání zpráv, diskuze, funkce sociálních médií apod. - moduly jsou popsány v samostatných dokumentech

## Důvody vytvoření <a name="arguments"></a>

Vzhledem k tomu, že tradiční emailové protokoly **SMTP**, **POP** a **IMAP** byly poprvé vyvinuty v 80. letech minulého století, vznikly po čase potřeby, které tyto protokoly již nejsou schopny efektivně vyřešit kvůli svému stáří a omezeným funkcím. Konkrétní data vytvoření dokumentace a posledních aktualizací:

**SMTP**:

- První dokumentace: [**RFC 821**](https://www.rfc-editor.org/rfc/rfc821.html) (1982)  
- Poslední aktualizace: [**RFC 5321**](https://www.rfc-editor.org/rfc/rfc5321.html) (2008)

**POP**:

- První dokumentace: [**RFC 918**](https://www.rfc-editor.org/rfc/rfc918.html) (1984)
- Poslední aktualizace: [**RFC 1939**](https://www.rfc-editor.org/rfc/rfc1939.html) (1996)

**IMAP**:

- První dokumentace: [**RFC 1064**](https://www.rfc-editor.org/rfc/rfc1064.html) (1988)  
- Poslední aktualizace: [**RFC 9051**](https://www.rfc-editor.org/rfc/rfc9051.html) (2021)

I přesto, že byly tyto protokoly v průběhu času aktualizovány, stále nepřinášejí dostatečná řešení pro nové výzvy, jako jsou komunikace v reálném čase, komplexní zabezpečení, strukturovanou komunikaci, snadnou rozšiřitelnost funkconalit a mnoho dalšího. To je způsobeno především snahou těchto aktualizací o navázání na původní koncepty, což je velmi limitující faktor, který vyžaduje změnu přístupu.

- TODO - sepsat podrobněji limitující faktory původních protokolů

## Cíle protokolu <a name="objectives"></a>

Cílem vytvoření tohoto protokolu je nahrazení současných e-mailových protokolů **SMTP**, **POP** a **IMAP** pomocí nové, robustní, moderní a bezpečné alternativy, která taktéž přináší snadnou rozšiřitelnost a díky tomu i dlouhodobou udržitelnost a to pouze jedním univerzálním protokolem, který plně nahrazuje a rozšiřuje funkčnost současných technologicky zastaralých řešení.

- **Komunikace v reálném čase**: Tento přístup, díky technologiím jako je [**WebTransport**](https://www.w3.org/TR/webtransport/), umožňuje uživatelům pohodlněji vést konverzace, rychleji reagovat na zprávy a využívat řadu dalších funkcionalit, kde je okamžitá komunikace nezbytná (např. videohovory, sdílení obrazovky, přenos velkých souborů apod.) a to navíc paralelně díky vícekanálové komunikaci. Tím jsou sníženy latence a zvýšena propustnost při přenosu dat.
- **Bezpečná komunikace**: Protokol klade velký důraz na zabezpečenou komunikaci, bezpečné přihlašování, koncové šifrování a možnost tyto bezpečnostní prvky v budoucnu aktualizovat a rozšířit pro ještě více bezpečnou komunikaci. Tím jsou minimalizována rizika spojená s únikem dat, odposlechy a dalšími formami kybernetických útoků.
- **Rozšiřitelnost**: Protokol umožňuje snadno vytvářet rožšíření o různé funkcionality modulárního konceptu, ať už jde o vytváření jednoduchých jednoúčelových aplikací (např. zasílání zpráv z [**IoT zařízení**](https://cs.wikipedia.org/wiki/Internet_v%C4%9Bc%C3%AD) uživateli) až po multifunkční [**super-aplikace**](https://en.wikipedia.org/wiki/Super-app) s četnou řadou funkcionalit (např. zasílání rychlých zpráv, funkce sociálních médií, e-commerce platformy, přenosy velkých souborů, přenos dat v online hrách, komunikace s AI apod.).
- **Univerzálnost**: Přenášená data jsou sestavena strukturovaně ve formátu [**BSON**](https://en.wikipedia.org/wiki/BSON), který kombinuje výhody strojově i lidsky snadno čitelného formátu [**JSON**](https://cs.wikipedia.org/wiki/JSON), s efektivitou binárního formátu. To umožňuje, aby data byla snadno přístupná a zpracovatelná jak pro vývojáře, tak pro aplikace.

### Modularita <a name="objectives-modularity"></a>

Modulární koncept architektury protokolu poskytuje několik klíčových vlastností:

- **Přizpůsobení**: Díky modulární struktuře mohou vývojáři snadno vybírat specifické funkcionality dle svých potřeb nebo potřeb jejich uživatelů. Správci či uživatelé mohou vybrat pouze ty moduly, které potřebují, což umožňuje vytvořit řešení šité na míru bez zbytečného nadbytečného softwaru. Například školy a firmy mohou využívat jiné moduly, než domácí uživatelé apod.
- **Rychlý a snadný vývoj**: Modulární architektura umožňuje různým týmům pracovat nezávisle na sobe na různých modulech, což zrychluje celkový vývoj systému, usnadňuje správu kódu a zvyšuje přehlednost v kódu. To je zvláště užitečné ve velkých projektech. Taktéž to šetří čas tím, že nemusí vytvářet celý systém od základu, ale jen specifické funkcionality.
- **Snadná údržba a aktualizace**: Moduly lze nezávisle na sobě aktualizovat a vylepšovat. Takovéto aktualizace jednotlivých modulů mohou proběhnout bez přerušení zbylé části systému nebo nutnosti celý systém překompilovat či restartovat. To zároveň přináší i dlouhodobou udržitelnost systému tím, že usnadní integraci nových funkcionalit.
- **Testování**: Každý modul lze testovat samostatně, což zjednodušuje proces odstranění chyb a zvyšuje celkovou kvalitu softwaru. Integrace nových funkcí je také snazší a méně náchylná k chybám, jelikož se jednotlivé moduly testují a ověřují nezávisle.
- **Odolnost proti chybám**: V případě, že jeden z modulů selže, neovlivní to fungování ostatních částí systému, což přispívá k vyšší stabilitě, odolnosti celého systému a omezení rizik spojených s narušením celého systému.
- **Bezpečnost**: Některé složitější implementace protokolu mohou modulům udělit pouze omezená práva k systému a tím zvýšit bezpečnost celého systému.
- **Multiverzování**: V rámci jednoho serveru může běžet stejný modul ve více verzích najednou. To umožňuje serveru být kompatibilní pro nové i starší verze klientských aplikací.
- **Interoperabilita**: Moduly mohou být navrženy tak, aby mohly komunikovat a spolupracovat mezi sebou a mezi různými dalšími systémy a aplikacemi, se kterými taktéž mohou sdílet data.
- **Geografická nezávislost** - Moduly mohou běžet na různém hardware umístěným na různých místech v síti.
- **Finanční efektivita** - Vývojáři mohou snadno vytvářet pouze určitou část systému, nikoliv celý systém a zároveň je jim umožněno svou funkcionalitu nabídnout již existující komunitě lidí, kteří systém využívají. To značným způsobem šetří náklady a zvyšuje potenciální výnosnost.

### Distribuovaná síť <a name="objectives-distnet"></a>

Distribuovaná síťová architektura přináší mnoho výhod, které jsou klíčové pro efektivní, bezpečné a odolné elektronické komunikační systémy. Zde jsou hlavní důvody, proč byl zvolen tento přístup:

- **Odolnost proti výpadkům** - Distribuovaná síťová architektura AMTP zajišťuje, že selhání jednoho uzlu má lokální dopad na uživatele připojené k tomuto uzlu, avšak nemá žádný přímý vliv na funkčnost a dostupnost ostatních uzlů v síti. Toto omezení dopadu umožňuje, aby byla zajištěna kontinuita služby pro ostatní uživatele, kteří jsou připojeni k ostatním uzlům. Tím je zajištěna vyšší celková robustnost a odolnost celé sítě vůči izolovaným technickým problémům nebo geograficky lokalizovaným výpadkům, což je klíčové pro udržení nepřetržité globální komunikace.
- **Odolnost proti cenzuře**: Rozptýlení dat a služeb mezi velké množství serverů v různých jurisdikcích může znesnadnit úsilí o cenzuru nebo úplné blokování služby, což zajišťuje větší svobodu a nezávislost informací. Pokud je uživatel cenzurován ze strany konkrétního provozovatele serveru, může si vybrat jiného provozovatele, který nabízí svobodnější alternativu. Pokud provozovatel služby je cenzurován ze strany regulačních orgánů (např. státu), může přesunout svá data do jiné, svobodnější jurisdikce.
- **Konkurence mezi poskytovateli** - V distribuované síti existuje více nezávislých poskytovatelů služeb, kteří spravují různé uzly. To podporuje zdravou konkurenci, která vede k lepší kvalitě služeb, zavádění nových či vylepšení původních funkcionalit a jiným inovacím. Také může vést i ke snížení nákladů za placené služby pro koncové uživatele. Konkurence mezi poskytovateli také umožňuje uživatelům vybírat z různých služeb na základě kvality, ceny, ochrany soukromí a dalších faktorů, což zvyšuje možnosti volby a zlepšuje celkovou uživatelskou spokojenost.
- **Nezávislost** - Pokud uživatel nemá zájem využívat služeb žádného poskytovatele služby, má možnost si spustit vlastní server s vlastními pravidly a nastaveními dle svých individuálních potřeb.
- **Geografický výběr poskytovatele** - Distribuce serverů geograficky blíže k uživatelům může výrazně snížit latenci a zlepšit rychlost odezvy, což zlepšuje uživatelskou zkušenost při komunikaci a přenosem dat. Taktéž umožňuje vybrat si poskytovatele, který lépe vyhovuje lokálním potřebám, jako je podpora ve stejném jazyce, možnost osobního kontaktu nebo snadnější komunikace a servis. To zvyšuje komfort, spokojenost a posiluje důvěru uživatelů v poskytované služby.
- **Místní přizpůsobení**: Distribuované sítě umožňují lokálním poskytovatelům služby se lépe přizpůsobit specifickým regionálním požadavkům uživatelů a nutnost dodržovat pouze lokální právní normy a nebýt tak svázán dodržováním regulací a norem z celého světa tak, jak je to u centralizovaných globálních služeb.
- **Podpora pro inovace**: Distribuovaná síťová architektura umožňuje experimentování a inovace na jednotlivých uzlech bez rizika narušení celého systému. To může sloužit jako testovací prostor pro nové technologie a modely před jejich širším nasazením.
- **Bezpečnost**: Útoky zaměřené na jednotlivé uzly nemají vliv na celou síť. Distribuovaná struktura komplikuje útočníkům snahu ovlivnit celý systém. Nežádoucí ovlivnění více bodů v síti zvyšuje složitost a náklady na potenciální útoky.
- **Ochrana soukromí**: Distribuovaná síťová architektura může přispět k lepší ochraně soukromí uživatelů tím, že data menších poskytovatelů nejsou uchovávána a zpracovávána velkými korporacemi, které mohou mít tendence velké množství dat uživatelů různými způsoby zpracovávat nebo sdílet pro různé účely (reklamní, trénink AI, spolupráce s vládami apod.)

### Zabezpečení <a name="objectives-security"></a>

- **Zabezpečení v rámci WebTransport** - TODO
- **Koncové šifrování** - TODO

## Zvolené standardy <a name="standards"></a>

- **WebTransport** - TODO
- **BSON** - TODO

## Srovnání s jinými protokoly: <a name="comparison"></a>

### Co má AMTP společného se SMTP: <a name="comparison-smtp-similar"></a>

- Typ identity (ve tvaru **user@domain.tld**).
- Topologie sítě (distribuovaný model)

### Čím je AMTP odlišný od SMTP: <a name="comparison-smtp-diff"></a>

- Komunikace v reálném čase (pomocí **WebTransport**)
- Typ zápisu přenášených dat (pomocí **BSON**)
- Pokročilé způsoby zabezpečení (zabezpečené šifrované spojení, koncové šifrování, přihlašování klienta k serveru přes asymetrické šifrování apod.)
- Modularita (možnosti rozšíření jako např. rychlé zprávy, funkce sociálních médií, e-commerce apod.)

- TODO: v čem je lepší / jiný než konkurence (Matrix.org atd.)

## Architektura <a name="architecture"></a>

- TODO

### Síť <a name="architecture-network"></a>

<p align="center"><img src="img/AMTP-network-CZ.png" alt="Síť" /></p>

### Server <a name="architecture-server"></a>

Ačkoliv tato dokumentace nepopisuje implementaci serverové aplikace, je důležité zmínit, že tento protokol počítá s použitím modulární struktury serveru a podle toho vypadá i komunikace v rámci příkazů jak mezi klientem a serverem, tak i servery mezi sebou.

Následující obrázek nastiňuje zjednodušený příklad toho, jak může vypadat architektura serverové aplikace:

<p align="center"><img src="img/AMTP-server-CZ.png" alt="Architektura serveru" /></p>

Klientská aplikace se spojí s WebTransport serverem se kterým naváže persistentní spojení a následuje obousměrný přenos dat. Server následně zpracovává data a odpovídá a to buď přímo, pokud byl příkaz od klienta určen jádru serveru a nebo je předán modulu, pokud byl pro modul určen a očekává odpověď od modulu, kterou jen prostě přepošle klientovi.  
Způsob komunikace serverového jádra s moduly není součástí této dokumentace, každá softwarová implementace může mít vlastní řešení (například výměna dat uvnitř aplikace, externě běžící služby propojené unixovými sockety či perzistentní síťové spojení přes WebTransport apod.).

## Spojení <a name="connection"></a>

Komunikace a výměna dat v reálném čase je zajištěna díky trvalému spojení klienta se serverem pomocí protokolu **WebTransport**.

Server-server spojení nemusí být perzistentní a mohou být ukončeny jednou nebo druhou stranou kdykoliv to jedna ze stran uzná za vhodné (např. server odesilatele po dokončení doručování dat nebo server příjemce po vypršení určitého časového limitu, kdy mezi servery nedošlo k žádné výměně dat). Podmínky za kterých dochází k odpojování serverů mezi sebou nejsou standardizováné touto dokumentací, jsou plně v kompetenci konkrétní implementace serverové aplikace. Důvodem je to, že některé servery mohou mít omezený počet síťových portů, ke kterým je možné se připojit, případně jiných dalších prostředků které má server k dispozici omezeně, a tak server může spojení s ostatními servery častěji odpojovat a naopak servery, které mají dostatečný počet síťových portů, či jiných prostředků, může udržovat otevřené spojení s ostatními servery pro rychlejší komunikaci, bez nutnosti opětovného navazování spojení.

## Formát dat <a name="data-format"></a>

- Data při spojeních klient-server i server-server se předávají v datových objektech ve formátu **BSON**.

## Identita <a name="identity"></a>

- Uživatelská identita je ve tvaru e-mailové adresy
- TODO: multi-aliasy (speciální adresa bez mailboxu, která přeposílá zprávu jednomu nebo více příjemcům) a doménový koš (když příjde něco na neexistující adresy, přepošle se to jednomu či více příjemcům) - specifikovat v AMTP nebo implementace?
- TODO: mají chat groupy a news group taky e-mailovou adresu? (přece jen jsou v messengeru mezi konverzacema)
- TODO: vse pred @ nepovolovat specialni znaky jako !#& apod. (stary e-mail to tak ma)
- TODO: Viditelne jmeno a za tim <email> se uz se nepouziva, protoze viditelne jmeno se nacita ze serveru vc. dalsich udaju, jako fotka apod.

## Zabezpečení <a name="security"></a>

### Šifrovaný přenos dat <a name="security-transfer"></a>

- TODO

### Koncové šifrování <a name="security-e2e"></a>

- TODO

### Bezpečné přihlašování <a name="security-login"></a>

Proces ověřování uživatelů a správců je navržen tak, aby bylo zajištěno, že citlivé informace, jako je soukromý klíč, zůstanou omezeny na zařízení uživatele. Toho je dosaženo pomocí asymetrické šifrovací strategie zvané **výzva-odpověď**, která je popsána následovně:

#### 1. Vygenerování klíčů:
- Po zahájení procesu registrace klient vygeneruje na svém koncovém zařízení jedinečný soukromý klíč. S využitím šifrovacího algoritmu kompatibilního s algoritmy podporovanými serverovou aplikací je z tohoto soukromého klíče odvozen odpovídající veřejný klíč. Tato dokumentace nespecifikuje seznam podporovaných šifrovacích algoritmů, to je plně na implementaci serveru a klientských aplikací a dává to možnost změny algoritmů v budoucnu. Soukromý klíč je bezpečně uložen v klientském zařízení.

#### 2. Registrace účtu:
- Veřejný klíč spolu s identifikací použitého šifrovacího algoritmu se předává serveru při registraci účtu na serveru. Server poté zaznamená veřejný klíč a identifikaci šifrovacího algoritmu do svého úložiště (např. databáze) pro budoucí procesy ověřování.

#### 3. Ověřování při přihlášení:
- Při ověřování odešle klient serveru žádost o přihlášení spolu se svým uživatelským jménem.
- V odpovědi pro klienta server vygeneruje náhodný řetězec (doporučuje se délka řetězce alespoň 40 znaků), který je následně zašifrován pomocí veřejného klíče klienta a poté je takto zašifrovaný odeslán zpět klientovi.
- Po přijetí klient použije svůj soukromý klíč k rozšifrování přijatého řetězce a odešle rozšifrovaný řetězec zpět na server k ověření.
- Server porovná rozšifrovaný řetězec s původně vygenerovaným řetězcem. Pokud se shodují, potvrdí server identitu klienta a udělí mu přístup vydáním tokenu relace. Platnost tokenu je v kompetenci implementovaného řešení serverové a klientské aplikace.

Tato metoda ověřování využívá výhody asymetrického šifrování k ochraně uživatelských dat a zajišťuje, že citlivé informace, jako jsou soukromé klíče, zůstanou chráněny v rámci uživatelských koncových zařízení. Tento přístup nejen zvyšuje bezpečnost, ale je také v souladu s osvědčenými postupy pro bezpečnou komunikaci v moderních aplikacích.

- TODO: přidat obrázek challenge-response
- TODO: možná zakomponovat JWT?
- TODO: kombinace privátního klíče s heslem?
- TODO: další možnosti přihlášení - digitální podpis čehokoliv a následné ověření veřejným klíčem na serveru?
- TODO: další možnosti ověření: OAuth / social loginy, pouze heslo (hash) atd. bude podporováno nebo bude v separátním modulu?

## DNS <a name="dns"></a>

Pro zajištění komunikace mezi servery v rámci distribuované sítě, je využita sada specifických záznamů **DNS**. Tyto záznamy jsou nezbytné pro správné a bezpečné směrování provozu mezi servery na základě domény příjemce. Pro zapojení serveru do sítě pro účel předávání dat mezi servery, jsou nezbytné záznamy v DNS u domén, které jsou serverem využívány. Bez těchto záznamů není možné předávat data mezi servery.

- TODO: zmínit podobnost s klasickými e-mailovými služby využívajícími záznamy A, PTR, MX, SPF, DKIM a DMARC
- TODO: Proč DNS a ne křížová kontrola? (rychlost)

### Konfigurace DNS <a name="dns-config"></a>

**AMTP servers TXT záznam**:

- Tento záznam definuje cílové servery, na kterých jsou uživatelské účty pro určitou doménu (kombinuje funkcionalitu podobnou záznamům MX a SPF známým z e-mailu).
- Seznam serverů začíná řetězcem "amtp-servers=" a jednotlivé servery jsou odděleny čárkou (",")
- Servery jsou seřazeny podle priority. Pokud je některý server nedostupný, použije se pro připojení další server v řadě.
- Pokud není zadán žádný port, použije se standardně port 443.

**Příklad:**

```
subdomain.domain.tld. IN TXT "amtp-servers=amtp1.domain.tld,amtp2.domain.tld:12345,123.123.123.123:23456"
```

Tento záznam definuje, že pro účty na doméně **@subdomain.domain.tld** jsou používány následující servery **AMTP**:

- **amtp1.domain.tld** na portu 443
- **amtp2.domain.tld** na portu 12345
- **123.123.123.123** na portu 23456

- TODO: můžeme vůbec použít jen IP adresu, když tam běží WebTransport, který vyžaduje certifikační autoritu (jde obejít na Chrome jen když je spuštěn s parametrem, na Firefoxu to snad ani vůbec nejde)

**AMTP DKIM TXT záznam**:

```
amtp1.domain.tld. IN TXT "amtp-dkim=v=DKIM1;h=sha256;k=rsa;p=123456789abcdef"
```

Tento záznam definuje, že pro server **amtp1.domain.tld** je využíván veřejný klíč "**123456789abcdef**". Další parametry jsou vysvětleny v [**RFC dokumentaci DKIM**](https://www.dkim.org/specs/rfc5585.html)

- TODO: je potřeba něco jako DMARC, SPF (odděleně), PTR (IP spoofing?)?

## Příkazy <a name="commands"></a>

- TODO - úvod

### Spojení klient-server <a name="commands-c2s"></a>

- TODO - úvod

#### Příkazy správce <a name="commands-c2s-admin"></a>

Všechny příkazy začínající "**admin_**" jsou určeny pouze pro správce. Aby bylo možné provádět tyto příkazy, musí být správce přihlášen. Pokud je přihlášení úspěšné, získá správce token relace, který musí být odeslán v každém správcovském požadavku. Tento token se liší od tokenu uživatele.

##### Přihlášení správce <a name="commands-c2s-admin-login"></a>

**Příklad žádosti:**

```json
{
 "command": "admin_login_request",
 "user": "admin",
}
```

**Příklad odezvy:**

```json
{
 "command": "admin_login_request",
 "error": 0,
 "data": {
  "verification_string": "1A2B3C4D5E6F1A2B3C4D5E6F1A2B3C4D5E6F1A2B3C4D5E6F",
 }
}
```

**Příklad žádosti:**

```json
{
 "command": "admin_login_verify",
 "user": "admin",
 "verification_string": "abcdefghijklmnopqrstuvwxyz1234567890abcd"
}
```

**Příklad odezvy:**

```json
{
 "command": "admin_login_verify",
 "error": 0,
 "data": {
  "admin_token": "1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"
 }
}
```

##### Systémové informace pro správce <a name="commands-c2s-admin-sysinfo"></a>

**Příklad žádosti:**

```json
{
 "command": "admin_sysinfo",
 "admin_token": "1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"
}
```

**Příklad odezvy:**

```json
{
 "command": "admin_sysinfo",
 "data": {
  "app_name": "Yellow Server",
  "app_version": "0.01",
  "os_name": "Linux",
  "os_version": "5.10.0-19-amd64",
  "cpu_model": "Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz",
  "cpu_cores": 40,
  "cpu_arch": "x64",
  "cpu_load": 0,
  "ram_total": 8332619776,
  "ram_free": 7842000896,
  "hostname": "mail",
  "networks": [
   {
    "ens1": [
     "192.168.0.5",
     "12ab::34cd:56ef:7890:1234"
    ]
   }, {
    "ens2": [
     "40.30.20.10",
     "12ab::34cd:56ef:7890:4321"
    ]
   }
  ],
  "uptime": "8 days, 17 hours, 32 minutes, 52 seconds"
 }
}
```

##### Odhlášení správce <a name="commands-c2s-admin-logout"></a>

- TODO

##### Seznam domén: <a name="commands-c2s-admin-domains-list"></a>

**Příklad žádosti:**

```json
{
 "command": "admin_get_domains",
 "admin_token":"1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"
}
```

**Příklad odezvy:**

```json
{
 "command": "admin_get_domains",
 "data": {
  [
   {
    "id": 1,
    "name": "domain1.tld",
    "created":"2023-11-25 14:57:03"
   }, {
    "id": 2,
    "name": "domain2.tld",
    "created": "2023-11-26 16:21:08"
   }
  ]
 }
}
```

##### Přidání nové domény <a name="commands-c2s-admin-domains-add"></a>

- TODO

##### Úprava domény <a name="commands-c2s-admin-domains-set"></a>

- TODO

##### Smazání domény <a name="commands-c2s-admin-domains-del"></a>

- TODO

##### Seznam uživatelů <a name="commands-c2s-admin-users-list"></a>

- TODO

##### Přidání nového uživatele <a name="commands-c2s-admin-users-add"></a>

- TODO

##### Úprava uživatele <a name="commands-c2s-admin-users-set"></a>

- TODO

##### Smazání uživatele <a name="commands-c2s-admin-users-del"></a>

- TODO

##### Seznam aliasů <a name="commands-c2s-admin-aliases-list"></a>

- TODO

##### Přidání nového aliasu <a name="commands-c2s-admin-aliases-add"></a>

- TODO

##### Úprava aliasu <a name="commands-c2s-admin-aliases-set"></a>

- TODO

##### Smazání aliasu <a name="commands-c2s-admin-aliases-del"></a>

- TODO

##### Doménový koš (catch-all) <a name="commands-c2s-admin-catchall"></a>

- TODO: domyslet, rozepsat

##### Seznam správců <a name="commands-c2s-admin-admins-list"></a>

- TODO

##### Přidat nového správce <a name="commands-c2s-admin-admins-add"></a>

- TODO

##### Úprava správce <a name="commands-c2s-admin-admins-set"></a>

- TODO

##### Smazání správce <a name="commands-c2s-admin-admins-del"></a>

- TODO

#### Příkazy uživatele <a name="commands-c2s-user"></a>

Všechny příkazy začínající "**user_**" jsou určeny pro uživatele. Aby bylo možné provádět tyto příkazy, musí být uživatel přihlášen. Pokud je přihlášení úspěšné, získá uživatel token relace, který musí být odeslán v každém správcovském požadavku. Tento token se liší od tokenu správce.

##### Přihlášení uživatele <a name="commands-c2s-user-login"></a>

- TODO: popsat challenge-response (už je popsáno v části security - přesunout sem a odtamtud jen odkazovat?)
- Pokud se uživatel úspěšně přihlásí, získá token relace, který následně používá ve všech dalších požadavcích
- TODO: expirace tokenu?

##### Informace o serveru pro uživatele <a name="commands-c2s-user-serverinfo"></a>

- TODO

##### Odhlášení uživatele <a name="commands-c2s-user-logout"></a>

- TODO

##### Informace o uživateli <a name="commands-c2s-user-userinfo"></a>

- TODO: uživatelé mohou požádat o informace o ostatních uživatelích jako jméno, foto apod.etc.

### Spojení server-server <a name="commands-s2s"></a>

- TODO: předávání, ověřování identit atd.

### Komunikace s moduly <a name="modules-communication"></a>

- TODO

## Příklady <a name="examples"></a>

- TODO

