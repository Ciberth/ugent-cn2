# Reeks B

## Draadloze netwerken (nota's & slides wireless meshes labo)

### a. Met welke opdrachten kan men op Windows toestellen nagaan welke de karakteristieken zijn van de eigen Wifi interface en van de Wifi zenders in de buurt. Geef aan welke karakteristieken vermeld worden.
#### Eigen interfaces
netsh wlan sh int

* Name
* Description
* GUID
* Physical address
* State
* SSID
* BSSID
* Network type
* Radio type
* Authentication 
* Cipher
* Connection mode
* Channel
* Receive rate (Mbps)
* Transmit rate (Mbps)
* Signal
* Profile
* Hosted network status

#### Zenders in de buurt
netsh wlan sh net

* SSID
* Network type
* Authenticatie
* Encryptie

### b. In welk opzicht zijn Ad Hoc routingprotocollen (in wireless meshes) anders dan de meer traditionele routingprotocollen (bedoeld voor internetwerken die uit bekabelde subnetwerken bestaan) ?
Deze protocollen moeten rekening houden met de gevolgen van draadloze verbindingen:
* mobiliteit van routers
* uitvallen en herstellen van links
* variabele kwaliteit van links
* extra metrieken:
	- stabiliteit verbinding
	- energie-verbruik
	- signaal/ruis verhouding
* minimale processing(energieverbruik) en netwerkbelasting
* enkel gedistribueerde aloritmen

### c. Geef de twee fundamenteel verschillende manieren om Ad Hoc routingprotocollen te realiseren, inclusief hun relatieve voor- en nadelen en hun optimaal toepassingsgebied.
* Proactive of table driven protocollen
	- Houdt alle actieve routes bij
	- Berekend aan de hand van een metriek
	- Routes worden aangepast bij wijziging linkkarakteristieken
	- Grote netwerkbelasting
	- Routes onmiddelijk bruikbaar (ook als ze weinig gebruikt worden)

Vb: Optimized link state routing (OLSR)

Te gebruiken indien vertragingen op moment van gegevensoverdracht niet wenselijk zijn.

* Reactieve of on-demand protocollen
	- Routes worden opgebouwd indien nodig
	- Caching van route discoveries om sneller andere routes te vinden
	- Geen statische netwerkbelasting
	- Werkt in grote netwerken (duizenden knooppunten) indien er relatief weinig communicerende koppels zijn
	- Vertraging (of ICMP Unreachable) voor gegevensoverdracht door route eerst op te moeten bouwen  

Vb: Ad hoc on demand distance vector routing (AODV)

Te gebruiken indien er te veel knooppunten in het netwerk zijn om alle routes bij te houden of belasting van het netwerk minimaal gehouden moet worden.

### d. Bespreek een concreet voorbeeld van een implementatie die tot één van deze categorieën behoort, met vooral aandacht voor de verschillen met het traditionele routingprotocol (voor bekabelde internetwerken), waarvan het is afgeleid.
#### Optimized link state routing (OLSR) -> proactief

Gebaseerd op OSPF. Indien OSPF gebruikt zou worden zouden te veel adjacencies overlappen, waardoor het flooding mechanisme het netwerk te veel zou belasten door berichten (LSA's) meerder malen naar buren te sturen. Dit wordt opgelost door multipoint relays (MPR) te introduceren.

* Eerste hop van route is steeds een MPR
* Selectieve flooding zorgt voor minder overhead
* LSA's worden naar een beperkt aantal buren (MPR's) doorgestuurd
	MPR CRITERIUM: beperkte deelverzameling (minimaal) bidirectionele buren van de router zodat alle 2-hop routers direct bereikbaar zijn vanuit minstens één van de MPR's

* Het protocol garandeert niet dat de bekomen route een optimale metriek heeft

### e. Bespreek een concreet voorbeeld van een implementatie die tot de andere categorie behoort, nu met een gedetailleerde beschrijving van hoe de routingtabellen door specifieke berichtuitwisselingen ingevuld worden.

#### Ad hoc on demand distance vector routing (AODV) -> reactief
* Bidirectionele verbindingen vereist
* ttl van een route verhoogt bij elk gebruik
* 3 berichttypes voor implementatie
	- bron broadcast **Route request** (RREQ)
		+ bron die data wenst te versturen maar beschikt over geen recente route naar doel(recent -> ttl!=0)
		+ verwijzing naar bron en doel, volgnummer
		+ eenmalig doorsturen door intermediaire routers (geen bevestiging -> verlies door **collision** mogelijk)
		+ Intermediaire routers construeren bij ontvangt **Reverse path** naar bron
		+ (Worden niet doorgestuurd door het doel)
	- unicast van **route reply** (RREP)
		+ na ontvangst RREQ
		+ door doel of intermediaire router met recente route naar het doel
		+ Gebruikt **Reverse Path** naar bron
		+ Intermediaire routers en bron passen routing tabel aan met bepaalde route
		+ Wordt gebruikt als hello bericht (buren detecteren) RREP met ttl=1 (gaat maar 1 hop ver)
	- **Route error** (RERR)
		+ Update routingtabellen: vb routers zijn mobiel en verbinding verbroken


	Voor een router een RREQ verstuurd verhoogt deze z'n eigen sequentienummer. 

	Voor een router een RREP verstuurd past hij z'n sequentienummer aan naar dat van het ontvangen RREQ indien deze hoger is dan het eigen sequentienummer. Zoniet blijft het eigen sequentienummer behouden.

Dit systeem van sequentienummers voorkomt routing loops en count to infinity problemen. Berichten met hetzelfde sequentienummer worden niet opnieuw doorgestuurd.

* Wifi broadcasting is foutgevoeliger en gelijktijdig sturen naar zelfde router mogelijk (**collision**)

Zie slides 18-25 voor uitgewerkt vb. bepalen route en invullen routingtable
