# Reeks B

## Draadloze netwerken (nota's & slides wireless meshes labo)

### a. Met welke opdrachten kan men op Windows toestellen nagaan welke de karakteristieken zijn van de eigen Wifi interface en van de Wifi zenders in de buurt. Geef aan welke karakteristieken vermeld worden.

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

### e. Bespreek een concreet voorbeeld van een implementatie die tot de andere categorie behoort, nu met een gedetailleerde beschrijving van hoe de routingtabellen door specifieke berichtuitwisselingen ingevuld worden.
