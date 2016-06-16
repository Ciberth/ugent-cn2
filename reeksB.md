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
* enkel gedistribueerde algoritmen

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

## Configuratie van DNS servers onder Linux
*De figuur in bijlage stelt een intranet bestaand uit een aantal Linux computers voor, met corresponderend IP-adres, van de vorm 192.168.16.z. Het getal z lees je af links van de naam van de computer. De getallen rechts van de naam van de computer moet je negeren. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. 
De rechthoeken die domeinen groeperen stellen dan weer een zone voor. Stippellijnen duiden op een domein/subdomein relatie. De pijlen laten toe om de primaire nameserver van elke zone te achterhalen. Je hoeft geen reverse DNS te configureren.*

### a. Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers, waarbij je er rekening moet mee houden dat elk van deze servers ook secundaire nameserver is voor alle zones van de andere server: Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
```
ToDo
```
### b. Bespreek in detail het begrip secundaire nameserver, inclusief voordelen, beperkingen en problemen. (§3.1 & §3.3.3) 
* Houden precies dezelfde informatie bij als de primaire nameserver
* Worden gebruikt om de taak van de primaire nameserver te verlichten en om de fouttolerantie te verhogen
	- Bij uitvallen van de primaire kan netwerkbeheerder een secundaire tot primaire van een zone promoten
* Op de secundaire kunnen *GEEN* wijzigingen aangebracht worden, dit gebeurt op de primaire
	- Scundaire nameservers zoeken regelmatig contact met primaire om een nieuwe kopie van de gegevens
* Secundaire nameserver configuratie in Linux (in */etc/named.conf*)
	- Het *type* veld: bij een secundaire nameservers vul je hier *slave* in.  
		bvb.  
		 
			zone 'iii.hogent.be'{  
				type slave;  
				file "iii.hogent.be";  
				masters{ 192.168.16.16; };  
			};  
	- Het *file* veld bevat de naam van de file waar een kopie onderhouden wordt van de primaire nameserver.
		* De secundaire kan door dit bestand blijven werken als de primaire uitvalt
* *Nadeel* is dat secundaire nameservers niet automatisch kunnen gepromoveerd worden tot primaire, maar dat dit steeds door manuele interventie van de netwerkbeheerder dient te gebeuren.
* **Stub** : vergelijkbaar met secundaire nameserver, maar dupliceert enkel NS-records van opgegeven master-servers.
		
## Configuratie van DNS servers onder Linux
*De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.*

### a. Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
```
ToDo
```
### b. Bespreek in detail het formaat van een zonebestand en zijn records. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve records en formaten beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.1 & §3.3.4)
Alle informatie over zones wordt opgeslaan in *zonebestanden*

- Vrije naamkeuze van het bestand
- Hiernaar wordt verwezen in het configuratiebestand van de DNS-server
- 1 lijn per DNS record (uitzonderingen SOA en WKS of splitsen over meerdere lijnen dmv ronde () haakjes)
- Een DNS record bestaat uit 5 delen: ```naam ttl IN recordtype waarde```
	* **naam**: van een machine of een domein
		- eindigend op (.) punt is een absolute domeinnaam, om verwarring te vermijden gebruiken admins overal absolute domeinnamen ook al is dit niet echt nodig
		- @ = speciale naam verwijzend naar huidige oorsprong (kan enkel gebruikt worden in SOA record bovenaan bestand
		- $ORIGIN opdracht waarmee de oorsprong veranderd kan worden
	* **TTL**: geeft aan hoe lang DNS-record geldig is en dus hoelang het door andere computers gecached mag worden zonder opnieuw opgevraagd te hoeven worden
		- mag weggelaten worden
		- in SOA record staat immers een default TTL
	* **IN**: staat voor Internet
	* **type**: gebruikte types; A, PTR, MX, SOA, NS en CNAME
	* **waarde by type**: vb. bij A record ip adres bij opgegeven naam; en bij een MX record het IP-adres van de bijhorende mailserver.
	
De types van naderbij bekeken:

- **A record** : verbind een IP adres met een DNS naam (zorgen ervoor dat bij surfen naar www.google.be een IP-adres kan geassocieerd worden).  
*Uittreksel van /var/named/iii.hogent.be*
```C
	astoria	IN	A	192.168.16.27
```
- **SOA record** : *Start of Authority*; geeft het begin aan van de records voor een bepaalde zone.  
	vb.  
```C 
		@		IN SOA satan.iii.hogent.be. joris.moreau.hogent.be. (   
					2001041600  ; serial  
					10800       ; refresh  
					3600	    ; retry  
					3600000	    ; expire  
			 	    	86400)	    ; minimum  
		...
	  	    satan 	IN A 192.168.16.16	
```

	* 1 zonebestand per zone gebruiken! Dus maar 1 SOA record per zonebestand...
	* De waarde bij het SOA record is als volgt:
		- *DNS-naam van primaire nameserver* (absolute naam, dus eindigt op .)
		- *Het emailadres* van de verantwoordelijke voor deze server (@ vervangen door .)
		- *Volgnummer*: nummer gebruikt door secundaire DNS servers om na te gaan of ze nog up-to-date zijn (verhoog dit nummertje bij wijzigingen!)
		- *refresh interval*: na hoeveel tijd moet secundaire DNS server zijn database aanpassen
		- *retry interval*: hoelang moet secundaire server wachten na mislukte refresh poging
		- *expire interval*: hoelang moeten gegevens bewaard worden als refreshen niet lukt
		- *default TTL*: geef maar gerust een hoge waarde (604800 seconden, meer als een week minimum)
- **NS record**: sommen de namen op van alle primaire en secundaire nameservers van dit domein en al zijn gedelegeerde deeldomeinen  
	vb.  
```C
		IN 	NS	satan
		IN 	NS	beelzebub
```
- **MX record**: voor de naam van de mailserver wordt ook nog de prioriteit meegegeven.
	vb.  
```C
	IN	MX	10 gonzo
	IN	MX	20 mserv.hogent.be.
```
	* indien er meerdere MX records zouden bestaan voor dezeflde naam zullen mailservers volgens de opgegeven prioriteit uitgeprobeerd worden (laagste eerst)
	* *in het voorbeeld:* eerst naar *gonzo.iii.hogent.be sturen, indien die niet zou reageren dan pas naar mserv.hogent.be.
- **CNAME record**: alias opgeven voor een bepaalde DNS naam.
	vb.  
```bash
	gonzo	IN	A	    192.168.16.17
	mail 	IN	CNAME	gonzo
```
	* *OPGELET!:* deze CNAME records mogen niet gebruikt worden om er DNS records mee te associëren, los dit op door ipv CNAME twee A records te maken voor 1 IP.

**Delegeren of niet**  
Stel je hebt **iii.hogent.be**, maar je hebt ook (een klaslokaal) 219 met als DNS-naam **219.iii.hogent.be**. Je hebt nu de keuze om het subdomein te behren in het bovenliggende domein zelf
of via een extra delegatie naar een andere nameserver.

- *In het domein zelf: extract van* ```/var/named/iii.hogent.be```
```C
	caliban.219	    IN	A	192.168.16.73
	elbereth.219	IN	A	192.168.16.76
```
- *Delegeren aan andere nameserver: extract van * ```/var/named/iii.hogent.be```
```C
	;mozart.225.iii.hogent.be nameserver van 225.iii.hogent.be
	225		    IN	NS	mozart.225   
	mozart.225	IN	A	192.168.16.142	;glue record
```
	* referentie naar subdomein is enkel nodig in bovenliggende nameservers omdat DNS oproepen naar beneden doorgegeven worden
	* In de gedelegeerde nameserver kan nu een "normaal" zonebestand opgesteld worden

##Configuratie van DNS servers onder Linux
*De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.*

###a. Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
###b. Bespreek in detail het formaat van een configuratiebestand. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve opdrachten en sleutelwoorden beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.3)
Vanaf versie 8: bestand heet */etc/named.conf*
```C
	options{
		directory "/var/named";
		forwarders { 193.190.173.1; }:
	};
	zone "iii.hogent.be"{
		type master;
		file "iii.hogent.be";
	};
	zone "."{
		type hint;
		file "named.ca";
	}
```

- basisinstelling voor de serverdaemon (**options**)
	* **directory padnaam;** : geeft aan waar de zone bestanden zich bevinden
	* **forwarders { ip-addressen; };** : een lijst van IP-adressen waaraan named eerst vragen zal richten wanneer hij bepaalde DNS info niet in zijn cache vindt. Indien deze het ook niet weten 
		dan gaat hij pas naar rootservers vragen (Internetbelasting verminderen)
	* **alow-transfer { ip-adressen; };**: lijst van computers die een zone transfer kunnen uitvoeren (toelating is noodzakelijk voor secundaire nameservers). Indien lijn ontbreekt kan om het even 
		welke computer zone transfereren.
- namen van de zonebestanden (**zone**) : OPBOUW: *zone "naan" { ... } *
	* **naam** : de naam van de zone
	* **configuratie-informatie**: hangt nagenoeg volledig af van het **type**, dit maakt onderscheid tussen verschillende functies van de nameserver
		+ **master**: primaire nameserver voor de opgegeven zone.  
			* file: bevat de naam van het zonebestand dat alle DNS-informatie over de gegeven zone bevat (relatief tov basis *directory*)
			* allow-update: alle toestellen die die dynamische updates mogen uitvoeren (standaard uitgeschakeld, optioneel)
			* allow-transfer: heeft voorrang op deze in de globale opties (optioneel)
		+ **slave**: betekent dat named secundaire server is voor deze zone
			* file: hier komt zonebestand want wordt door server *automatisch aangemaakt* met kopie van de zoneinformatie op opgegeven domein
			* masters { 192.168.16.16; }; : belangrijke lijn die vertelt wie een hogere is (kunnen zowel primaire- als secundaire NS zijn)
		+ **stub**: gedraagt zich zoals secundaire nameserver, maar dupliceert enkel de NS-records van de opgegeven master-servers
		+ **forward**: stuurt alle aanvragen door naar andere nameservers
			* forwarders { ...; }; stuurt door naar deze IPs (opm: kan zowel in globale opties als in specifieke zone voorkomen)
		+ **hint**: altijd in combinatie met zonenaam "."
			* file "named.ca"; : verwijst naar bestand die een lijst rootservers bevat die gebruikt wordt bij opstarten van de nameserver om de namen van de echte rootservers terug te 					vinden
			* het bestand komt mee met de BIND software (je hoeft het dus nooit met de hand aan te passen)

##Configuratie van reverse DNS onder Linux
###a. Wat wordt beoogd met reverse DNS ? Hoe wordt dit gerealiseerd ? (§3.3.2)
**Reverse DNS**= het opzoeken van een domeinnaam horende bij een gekend IP-adres.  
**PTR-records**= records die omgekeerde informatie bevatten.  

*Probleem(pje)*: omdat we geen domeinnaam weten, kunnen we niet zomaar te weten komen waar we de server ergens moeten gaan zoeken.  
***Oplossing***: pseudo-domein met de naam *in-addr.arpa*.  
*De volgorde van de bytes van een IP-adres moeten wel omgekeerd worden!*: zo wordt 15.16.192/24 dus 192.16.15.in-addr.arpa.  

**Configuratie files**: weinig extra werk (naamvan de file is de naam van het subnetwerk vb. */var/named/192.168.16*)

* SOA record volledig vergelijkbaar met forward DNS
* Vervolgens staan er PTR records (naam absoluut wil je DNS naam gebruiken, anders zou hij er *.in-addr.arpa.* aan toevoegen).
```C
	@ 	IN 	SOA 	satan.iii.hogent.be. joris.moreau.hogent.be. (
					2001041600	; serial
					10800		; refresh
					3600		; retry
					3600000		; expire
					86400 )		; minimum
		IN 	NS 	satan.iii.hogent.be.
		IN	NS 	beelzebub.iii.hogent.be.
	11	IN	PTR	astoria.iii.hogent.be.
	27	IN	PTR	beelzebub.iii.hogent.be.
	16	IN	PTR	satan.iii.hogent.be.
```

###b. De figuur in bijlage stelt een intranet voor bestaand uit een aantal Linux computers. Alle computers hebben een IP-adres van de vorm 172.x.y.z. De getallen *x* en *y* lees je af rechts van de naam van de computer. Het getal z lees je af links van de naam van de computer. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. De rechthoeken die domeinen groeperen stellen dan weer een zone voor. Stippellijnen duiden op een domein/subdomein relatie. De pijlen laten toe om de primaire nameserver van elke *forward DNS* zone te achterhalen. De *reverse DNS* van de volgende adresruimte worden gedelegeerd aan (...). Geen enkele zone heeft een secundaire nameserver. Stel het configuratiebestand en de zonebestanden op van alle servers die een rol spelen bij reverse DNS resolving van IP-adressen behorend tot de adresruimten (...) en (...). Je hoeft hierbij enkel de configuratie informatie te vermelden die noodzakelijk is voor reverse DNS. Gebruik relatieve DNS namen waar mogelijk. Het gebruik van forwarders is hier niet toegelaten!
