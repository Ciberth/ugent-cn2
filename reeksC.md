#Reeks C

##Vraag 1: DHCP leaseprocessen en relay-agents (§2.1.2 & §2.1.3)
###a) Geef een overzicht van de verschillende types DHCP berichten. Hoe wordt in het bericht het type aangeduid ?
Het type wordt aangeduid door middel van de optie nummer 53 te zetten. De beschikbare opties zijn 

1. **DHCP-Discovery**
2. **DHCP-Offer**
3. **DHCP-Request**
4. **DHCP-Decline**
5. **Positief DHCP-Acknowledgement bericht**
6. **Negatief DHCP-Acknowledgement bericht**
7. **DHCP-Release bericht**

###b) Bespreek in detail de opeenvolgende stappen van beide DHCP leaseprocessen.
1. **Client vraagt en ontvangt een adreslease**
	1. *Client verzendt **DHCP-Discovery***
		* Broadcast bericht naar het lokale subnet
		* Onderhandelen over leasetijden en welke gegevens precies gewenst zijn
			- *optie 51* : leasetijd aanvragen
			- *optie 55* : geordende lijst met opties
		* IP adres van client in bericht opgevuld met : 0.0.0.0
	2. *DHCP-servers kunnen reageren met **DHCP-Offer***
		* biedt IP-adreslease aan client
		* opties waarover server beschikt in volgorde waarop client ze aanvroeg
		* server reserveert alvast het adres voor de client
		* DHCP-offer bericht
			- *optie 54* : serveridentificatie (IP van server)
		* Wanneer er *GEEN* antwoord komt van een server
			- stuurt **client** met oplopende tussenpozen (2+,4+,8+,16+)s
			- indien dan nog geen antwoord om de 5min proberen tot er toch een offer komt
	3. *Client reageert op 1 van de offers door **DHCP-Request***
		* serveridentificatie in *optie 54*
		* Geen keuzestandaard
		* Meestal 1e aanbieding die aan alle verlangens kan voldoen
	4. *Server stuurt een **positief DHCP-Acknowledgement** *
		* Bevestigt lease aan client
		* Stuurt meteen ook de gevraagde opties mee
		* Andere DHCP-servers geven de aangeboden reservering ook terug vrij  
		Opmerking: *In zeldzame gevallen: **negatief DHCP-Acknowledgement** *
		* Aanvraag voor subnet waar de server geen DHCP-scope beschikbaar voor heeft
		* Er moet terug van stap 1 gestart worden
	5. *Client start configuratie*
		* Merkt client foute parameter (vb. IP reeds in gebruik): **DHCP-Decline** en begint terug van stap 1
	6. *Bijkomende vragen van de client*  
		Wanneer de client reeds een lease succesvol aanvroeg kan hij nog volgende acties ondernemen.  
		* **DHCP-Inform** : aanvullende informatie opvragen
		* **DHCP-Release**: vrijwillig zijn lease afstaan
2. **Client wil zijn lease vernieuwen**  
*Client probeert doorgaans op ongeveer de helft van zijn leaseperiode opnieuw de lease bij de server te vernieuwen (kan via serveroptie gemanipuleerd worden).*
	1. *Client stuurt **DHCP-Request** naar server die hem het adres eerder toe wees*.
	2. *Server stuurt **DHCP-Acknowledgement** om de lease te vernieuwen.
		* Server stuurt ook opnieuw zijn opties, zodat client bij wijzigingen zijn configuratie kan bijwerken.
Wanneer de DHCP server niet (meteen) zou antwoorden worden door de client volgende acties ondernomen. 
	1. Wanneer de client geen gehooor zou krijgen, probeert hij in tussenpozen (2+,4+,8+,16+)s de DHCP-request te versturen*
	2. Is client nog steeds niet instaat om te communiceren met oorspronkelijke DHCP:  
		- Wachten tot $$T_2$$ verstreken is (de tijd voor rebinding, ongeveer $$\frac{7}{8}$$ van totale lease)
		- Wanneer de rebinding status bereikt wordt probeert client huidige lease te vernieuwen bij willekeurige
			beschikbare DHCP.
	3. **Er kan een server reageren met *DHCP-Acknowledgement*** en de client kan zoals ervoor verdergaan
	4. **Indien er niet "gerebind" kon worden**: periodiek DHCP-Request trachten uit te sturen (4+,8+,16+)s
	5. **Wanneer client dan nog geen antwoord krijgt** dan **moet** de client zijn lease loslaten (ook al zou hij in principe  			nog het adres kunen blijven gebruiken. En *moet leaseproces (1) gestart worden*.


###c) Bespreek het doel en (in detail) de werking van DHCP relay-agents. Welke velden in DHCP berichten helpen deze functie realiseren ?
####Doel
*DHCP-relay software* stuurt DHCP-aanvragen en antwoorden van het ene lokale subnetwerk naar het andere. 

- Routers laten immers geen broadcast berichten door
- Hierdoor gaan alle *DHCP-Discovery berichten* naar andere subnetwerken verloren
- **Een router** gaat functioneren als DHCP/BOOTP relay-agent

####Werking

1. DHCP-cliënt broadcast een DHCP-Discovery bericht in een bepaald subnet
2. Relay agent ontvangt DHCP (broadcast) berichten:  
	* Controleert *Gateway-IP adres*, indien 0.0.0.0 -> vul IP-adres van router in. 
3. Relay agent geeft DHCP bericht door (na eventuele vertragingsinterval):
	* Naar de gekende andere DHCP-servers
	* OF: opnieuw via broadcast naar alle andere externe subnetten waarmee hij verbonden is
	* Aantal keren dat zo'n bericht gebroadcast is wordt doorgaan beperkt (Windows Server (4) ).
4. DHCP-server in ander subnet ontvangt Discovery bericht.
	* Controle van gateway IP adres, geeft indicatie uit welke scope een lease verleend moet worden
	* Remote servers antwoorden **enkel** als ze over een juiste scope beschikken
5. DHCP-server antwoordt met een *DHCP-Offer* bericht rechtstreeks tot relay agent gericht (gateway IP-adres gebruiken).
6. De router geeft de adreslease aanbieding vervolgens door aan de desbetreffende cliënt.
	* Cliënt heeft nog geen IP, dus het **moet** gebroadcast worden.
Hierna volgen nog de *DHCP Request en DHCP-Acknowledgement* volgens dezelfde manier als de hierboven beschreven procedure.

####Belangrijke veld(en) in DHCP relay berichten
*Gateway-IP adres*

###d) Beschrijf wat er gebeurt op DHCP niveau bij het heropstarten van een Windows Server of een Linux toestel, nadat men een shutdown heeft uitgevoerd.
####Linux
- Het bestand */var/lib/dhcp/dhclient.leases* bevat info over huidige lease
- Bij opnieuw opstarten kan de client proberen de lease in dat bestand te verlengen door *DHCP-Request* naar de server te sturen.
- Als DHCP-server actief is dan bevestigt hij deze request.

####Windows
- Lease wordt bewaard op schijf in een register genaamd *hive*
- Onmiddelijk na opstarten wordt gepoogd de oude lease te verlengen door *DHCP-Request*
- **EXTRA:** Is de server *niet* bereikbaar, pogen de Windows cliënts adhv een ping opdracht naar default gateway te detecteren of ze nog 
  steeds in hetzelfde subnetwerk opgesteld staan. Indien dit zo is blijven ze stilzwijgend de lease verder te gebruiken die hen voor
  het uitschakelen toegekend was.

###e) Beschrijf wat er gebeurt indien een Windows Server of Linux DHCP-cliënt in eerste instantie geen DHCP-server kan bereiken.
Ze configureren zichzelf een tijdelijke IP-configuratie:  

* APIPA (=automatic private IP addressing) 
	- Adres uit klasse B (169.254/16)
	- Auotmatische conflictdetectie (Gratuitous ARP)
* Blijven in achtergrond nog steeds een geldige DHCP lease te verkrijgen
	- Indien geldige lease verkregen wordt, vervalt APIPA configuratie 

##Vraag 2: IPv6 Adressering (§5.2 behalve §5.2.5)
###a) Bespreek de structuur en numerieke voorstelling van IPv6 adressen.

* **Lengte**: 16 bytes lang (128 bits) (<-> 4 bytes bij IPv4)
* **Structuur**: P:Q:R:S:T:U:V:W
	- 8 segementen
	- 1 segment = 2 adresbytes
	- Halve adresbyte per hexadecimaal cijfer
	- Elk segment is hexadecimaal geheel getal van vier cijfers
* **Bijkomende regelgeving**:
	- Voorlopnullen zijn *niet* vereist (dus je mag :1 schrijven ipv :0001)
	- Groepen opeenvolgende 0000:-strings mogen vervangen worden door :: (vb. *2001:0410:0001:0000:0000:0000:0000:0000* -> *2001:0410:0001::*)
	- De ::-string mag slechts 1x voorkomen in een adres
	- *::*  = het *niet-gespecifieerde adres*
	- *::1* = loopback adres (~ 127.0.0.1 van ipv4)
	- IPv4 in IPv6 elk segment van 2 adresbytes mag in IPv4 notatie geschreven worden (vb. FE80:0:0:0:0:0:5EFE:C0A8:291E -> FE80::5EFE:192.168.41.30)
* **Netwerkprefix en interface-id**:
	- Kan enkel door "/-notatie" aangeduid worden (vb. 2001:410:1::/48)

###b) Geef en bespreek de verschillende types IPv6 adressen. Geef onder andere van elk van deze types de structuur, hun interpretatie, relevante voorbeelden en eventuele subtypes.

1. **Globale unicast adressen**
	- Specifiëren elk 1 interface (~ IPv4)
	- Er **moet minstens 1 unicast adres** geassocieerd zijn met een interface
	- *Globaal* = Is een Unicast adres dat uniek moet zijn over de hele wereld
	- **Delen van globaal unicast adres mogelijk**: als hardware op 1 of andere manier in staat is netwerkbelasting over interfaces te verdelen (interessant voor server farms).
	- **Gekenmerkt door *001* patroon als eerste 3 bits** *= formaatprefix (FP)* (verzameling van alle globale unicast adressen = 2000::/3)
	- Geen variabele onderdelen, maar vaste delen
		* **Top-Level Aggregation Identifier (TLA)** : hoogste niveau van routing hiërarchie, gelinkt met tier-1 ISPs
			- veld van 13 bits lang
			- default-free routers kunnen ten hoogste 8192 routes bevatten
			- er is nog reserver indien dit niet zou volstaan in de toekomst (8 bits)
		* **Next-Level Aggregation Identifiers (NLAs)** : provider based verdeeld
			- TLA+NLA (eerste drie segementen van colon-hexadecimal notatie) bepaald door topologie van het Internet
			- Grote ondernemingen met verschillende ISPs: meerdere unicast adressen aan bepaalde interface toegekend
			- Op provider gebasseerde adressen moeten veranderd worden bij veranderen van ISP
				* Oplosbaar door uitwisselingsentiteit uit te schrijven.
		* **Site-Level Aggregation Identifier (SLA)** : Voor klant bedoeld om adressering zoveel mogelijk af te stemmen op topologie van zijn privaat netwerk
			- 4-de segment
			- 16 bits ter berschikking)
		* **Interface ID**  : interface identificeren
			- laatste 4 segmenten
			- kan willekeurig
			- kan eenduidig bepaald laten worden door MAC-adres van de interface (probleem MAC adres is maar 6 bytes)
				* IEEE EUI-64 formaat 
				* Eerste 3 bytes (6 hexadecimale cijfers) van MAC
				* dan string FF:FE 
				* en dan de laatste drie bytes van het MAC adres
				* ten slotte op 1 na lagste bit van 1ste byte complementeren
				* **voorbeeld:**  
				2a02:1812:2525:e200:5627:1eff:fef6:f9d8/64 (ip-adres van WLAN interface)    
				MAC: 54:27:1e:f6:f9:d8 -> 01010100 00100111 00011110 11110110 11111001 11011000  
				Complementeer in 1ste bytes 01010100 het op 1 na laagste bit -> 01010110  
				MAC adres wordt nu 5627:1E..:..F6:F9D8  
				zet hierbij FF:FE op de plaats van de puntjes en je krijgt het unicast adres
			

2. **Lokale unicast adressen**:
	- Adressering ruimte voor unicast adressen die enkel intern in een organisatie gebruikt worden
	- Afgezien van prefix is er niets verschillend met globale variant
		* Prefix start met FEC0:0:0:0/48 = site lokale (verwerken van verkeer op intranet, zonder versturen naar het Internet)
			- 4de segment is SLA dat willekeurige subnetting toestaat
		* Prefix start met FE80:0:0:0/64 = link lokale (vergelijkbaar met het APIPA mechanisme maar blijft behouden ook al is er globale of sitelokale adressering)
	- Ook hier weer adhv IEEE EUI-64 standaard
3. **Multicast adressen**:
	- Mogen enkel als bestemmingsadres gebruikt worden
	- Clients kunnen inschrijven op IP multicast adressen
	- **Gekenmerkt door: **: een 1ste bit op 1 van het eerste byte
		* FFvs : (v = vlag veld, zijn 4 bits waarvan enkel de laatste momenteel gedefinieerd is)
		* v op 0 -> permanent (door IANA) toegezen
			- FF0s::1 -> alle knooppunten
			- FF0s::2 -> alle OSPF routers
			- FF02::5 -> alle OSPF DR
			- FF02::6 -> alle RIP routers
			- FF02::1 vervangt broadcast adressen van IPv4
		* v op 1 -> veranderlijke multicast adressen (bijvoorbeeld voor een bepaalde videoconferentie, een netwerkgame, ...)
		* **s veld** : geeft de scope aan (Internet of niet)
			- 1 = node
			- 2 = link
			- 5 = site
			- 8 = organization
			- E = global
	- Netwerkkaart beschikt over software matige lijst van multicast adressen waarop geluisterd moet worden
4. **Anycast adressen**:
	- 1 anycast adres door meerdere knooppunten gedeeld
	- **slechts 1 enkel knooppunt ontvangt** het datagram dat naar anycast adres verstuurd werd
	- vb. timeservers, aanvraag via anycast dan beantwoord de dichtsbijzijnde server aan deze request.
	- Adressering uit de unicast adresruimte en kunnen dus niet van *gewone* unicast adressen onderscheiden worden
	- Elk lid van een anycast adres moet geconfigureerd worden dat het herkend wordt.

##Vraag 3: IPv6 berichtstructuur (§5.3)
###a) Bespreek in detail de structuur van IPv6 berichten.
* Header van uniforme grootte (-> efficiëntere routing)
* Extra opties in gelinkte lijst van aparte extensieheaders (opties enkel onderzoeken indien nodig)
  - Bevatten een *Next-Header veld* : geeft aan welk type data er na (extensie)header komt
  - 	0	hop-by-hop options extensieheader  
	43	routing extensieheader  
	44 	fragment extensieheader  
	51	authentication extensieheader  
	59	is laatste header  
	6	TCP data  
	17 	UDP data
1. **IPv6 header**
	- **Geen lengte veld** nodig (*constante grootte van 40 bytes*)
	- **checksum** weinig zin (foutcontrole wordt door de transportlaag uitgevoerd)
	- **version** veld = 6
	- **traffic class** = om gediferentieerde dienst aan te kunnen bieden (standaar 0'en)
	- **Payload length** = aantal bytes in datagram rekening houdende met headerextensies, maar niet met de header zelf
	- **Hop Limit** = bovengrens aan levensduur van datagrammen
	- **Bestemmings- en bronadressen** (16 bytes lang)
	- **Next header**
	- **Flow Label**: mechanisme om belangrijker verkeer te prioritiseren boven ander verkeer. (specifiek voor bepaalde bron- en eindbestemming, verschillende flows per koppel mogelijk)

![IPv6 header](https://raw.githubusercontent.com/Ciberth/ugent-cn2/master/media/ipv6.jpg)



###b) Geef en bespreek de verschillende soorten extensieheaders die momenteel voor IPv6 gedefinieerd zijn.
- In toekomst makkelijk nieuwe extensies/opties te definiëren
- Tussenliggende routers kunnen deze info zonder enige interpretatie doorzenden (bevalve Hop-By-Hop options)
- **Structuur**
	* 2 bytes : type en grootte van de header
		* Gevolgd door extensie specifieke gegevens
- Enkele headers:
	* **Hop-by-Hop Options**: opties door tussenliggende routers moeten verwerkt worden. Moet onmiddelijk na IPv6 header komen. Opties gecodeerd als *Type-length-value* (TLV). Momenteel zijn er 2  nuttige opties
		- **Jumbo Payloads** optie (type 194) : maakt afhandelen van datagrammen groter dan 65535bytes mogelijk (jumbodatagrammen).
		- **Router alert** optie (type 5): licht alle routers in dat er in het datagebied ook info staat die door de routers zelf verwerkt moet worden.
	* **De routing header (RH)**: specifieer route die het pakket moet afleggen (een lijst van adressen)
		- *Routing type* 0 is enige voorlopig geïmplementeerd (= initiële bestemmingsadres is eerste adres in lijst van de RH)
		- *Wanneer initiële bestemmingsadres dit datagram ontvangt dan stuurt hij dit door naar het tweede*
		- *Proces gaat door tot uiteindelijke bestemming bereikt*
		- **Segments left** veld geeft aan hoeveel routers er nog moeten bezocht worden alvorens eindbestemming bereikt is.	
	* **Fragment header (FH)**: ordeningsmechanisme om gefragmenteerde data terug te reconstrueren	
	* **Authentication header (AH)**: vergelijken van MD5 sums en antireplay bescherming (onderschepte datagrammen kunnen niet opnieuw verstuurd worden)
	* **Encapsulating Security Payload header (ESP)**: Staat toe geëncrypteerd te communiceren tussen de knooppunten, geeft aan dat datagram geëncrypteerd is en geeft bestemming
	    voldoende info om dit te kunnen ontcijferen. Te gebruiken in 2 modes:
		- Transparante/transport mode: enkel ingekapselde data van het pakket wordt versleuteld (bron- en eindbestemming nog identificeerbaar)
		- Tunnel mode: volledige datagram verpakt en in een ander datagram inpakken en versturen tussen security gateways

##Vraag 4: Overgang naar IPv6 en autoconfiguratie
###a) Bespreek de belangrijkste motieven die uiteindelijk een overgang naar IPv6 zullen veroorzaken. (§5.1)
* In de nabije toekomst zulen er **enorm aantal bijkomende adressen** moeten toegekend worden
	- 32 bits = 4 miljard adressen
	- CIDR Hierarchisch systeem maakt manuele adrestoewijzing overbodig, maar laat wel veel adresruimte verspillen (+- 7% wordt slechts gebruikt)
	- NAT probeert dit probleem te omzeilen maar strookt eigenlijk met de principes van IP adressering
* **Performantieproblemen** door de structuur van de IPv4 berichten
	- berichten met veel opties worden door routers als uitzonderingen gezien en worden met lagere prioriteit verwerkt
	- routerontwerp geoptimaliseerd voor IP verkeer met vaste headers (maar in IPv4 is dit zo niet)
	- Helaas is CIDR pas vanaf 1995 ingevoerd, en zijn de meeste tier-1 ISP’s verantwoordelijk voor meerdere niet-aggregeerbare adresblokken. Hierdoor bevatten de default-free routers (ASBR’s) van het Internet momenteel bijna tweehonderdduizend routes. Dit is zeer nefast voor het routingproces, en dus voor de globale werking van het Internet.
* **Mogelijkheden voor opties** zijn in IPv4 zeer beperkt (is nochtans de eneige manier om aan recentere noden te voorzien)
* **Geen aandacht besteed aan beveiligingsaspect**: de verantwoordelijkheid voor beveiliging ligt bij IPv4 in de hogere protocollagen
* **Mobiliteit van computers**: voorziening die dit beantwoorden zitten niet in IPv4 zelf maar moeten door protocollen uit de toepassingslaag geïmplementeerd worden (behalve APIPA).

###b) Hoe zal deze overgang worden gerealiseerd ? Van welke technieken zal men gebruik maken ? Je moet hierbij onder andere tunneling in algemene termen beschrijven, echter zonder dieper in te gaan op de praktische uitwerking ervan. (§5.7 zonder subsecties)

De overgang van IPv4 naar IPv6 zal geleidelijk en relatief langzaam moeten gebeuren.

* Upgrades van IPv4 knooppunten moeten op willekeurig tijdstip kunnen gebeuren (onafhankelijk van routers of andere knooppunten)
* IPv4 en IPv6 moeten onafhankelijk van elkaar kunnen blijven werken (IPv4 worden in IPv6 middens bijvoorbeeld ::FFFF:w.x.y.z, hiermee kunnen ze in IPv6 gerepresenteerd worden)
* Parallel trekken met eilandjes (IPv6 netwerken) en oceanen (IPv4 netwerken) die geleidelijk aan opdrogen.

Verschillende technieken:

1. **Dual-IP - IPv6/IPv4 systemen**: ondersteunen zowel IPv4 als IPv6
	* Beschikken over duale internetlaag (IPv4 en IPv6)
	* Maken zelf keuze tussen IPv4 of IPv6 adhv eindbestemming
2. **IPv6 over IPv4 tunneling**: IPv6 datagrammen die van IPv6 *eilanden* ingekapseld worden in een IPv4 datagram om de *oceaan* over te raken.
	* IPv6/IPv4 node geef aan het protocolveld in de IPv4 datagram nummer 41
	* Eindpunt verwijdert de IPv4 header om de IPv6 te achterhalen.
3. **Protocol header translation**: door IPv6/IPv4 routers uitgevoerd. IPv4 header omzetten naar IPv6 header en omgekeerd.
	* IPv4-gemapte adressen koppelen aan echte IPv6 adressen
	* Is een techniek voor de laatste migratiestappen om de laatste overgebleven IPv4 knooppunten te integreren in een bijna volledig IPv6 internetwerk. 

###c) Geef de alternatieve mogelijkheden voor autoconfiguratie in IPv6. Bespreek hierbij de opeenvolgende stappen. (§5.6)
1. **Statefull autoconfiguratie**  
*= houdt op centrale server alle knooppunten toetandsinformatie bij (ondersteund door protocol zoals DHCP) en deze info kan door knooppunten opgehaald worden*  
	***Nadeel:***
	- er is een speciaal serverprogramma voor nodig (in kleinere omgevingen vooral belangrijk)

	***Voordeel:***
	- Er kan meticuleus geregeld worden door de netwerkbeheerder wie al dan niet een adresconfiguratie krijgt.

Opmerking: DHCPv6 is een manier om aan Stateful autoconfiguratie te doen.

2. **Stateless autoconfiguratie**  
*= biedt aan individuele knooppunten om hun eigen IPv6 configuratie te bepalen zonder expliciet een server te hoeven ondervragen.*  

**Stappen om een geldige configuratie te bereiken**

1. **Tentatief linklokaal unicast adres**: adhv MAC adres (IEE EUI-64 principe)
	- Kan reeds communiceren met andere knooppunten op dezelfde verbinding
2. **Duplicate adress Detection:**
	- extra verificatie of adres uniek is
	- Wanneer hij toch een **Neigbor Advertissement** zou ontvangen dan moet het knooppunt *manueel* geconfigureerd worden.
3. Interface wordt geïnitialiseerd met geldig verklaarde linklokale unicast adres + multicast adres (overeenkomstig met MAC adres, maar eerste 3 bytes vervangen door 33-33-FF) wordt geregistreerd.
4. Interface probeert tot driemaal een **Router Sollicitation bericht** te versturen.
	- Router reageert met **Router Advertisement**: hiervan worden Hop Limit, Reachable Time, Retrans Timer en aanbevolen MTU afgeleid
	- Indien geen antwoord: wordt overgeschakeld op een procedure voor statefull autoconfiguratie
5. **Prefix Information optie in Advertisement**:  bepaald of autonomous of stateful moet gewerkt worden.
	- Indien *autonomous* : berekent op basis van prefix een tentatief unicast adres en wordt met Duplicate adress detection gecontroleerd
	- Bepalen van Preferred Lifetime en Valid Lifetime aand de hand van de Prefix Information optie.
6. **Managed Adress Configuration vlag** in router advertisement -> ga over op stateful autoconfiguratie om bijkomende adressen te bekomen
	- Waneer **Other Stateful Configuration vlag**: wordt er bijkomstig stateful autoconfig uitgevoerd om bijkomstige parameter te verkrijgen.

**Statussen tijdens autoconfiguratie:**

1. **Duplicate adress detection** fase: er wordt een adres toegekend als tentatief, het knooppunt is in staat tot *multicast, maar geen unicast* te ontvangen.
2. **Prefered of deprecated** fase: Het knooppunt beschikt over een geldig adres.
	- *Prefered* als het in staat is unicast verkeer te ontvangen
	- *Deprecated*: Levensduur van het adres loopt af, knooppunt moet opzoek naar ander adres of moet geldigheidsduur verlengen. 
3. **Invalid** fase: na het verstreiken van de levensduur wordt een adres als ongeldig beschouwd en kan het noch multicast, noch unicast ontvangen.

## Vraag 5: IPv6 Tunneling (subsecties §5.7)
###a) Wat is IPv6 over IPv4 tunneling, en waarvoor zal men deze techniek aanwenden ?
Een IPv6 over IPv4 tunnel is een IPv6 datagram dat door een startpunt geëncapsuleerd wordt in een IPv4 datagram.  
Hierdoor wordt het mogelijk om een IPv6 datagram van het ene IPv6 netwerk (*eiland*) naar het andere IPv6 netwerk (*eiland*) te sturen over een pad dat zowel IPv6 als IPv4 netwerken (*oceanen*)  kruist.   
***Deze techniek zal vooral gebruikt worden in de eerste fasen van de IPv4 naar IPv6 migratie***:  
*als een organisatie of geografisch deel al volledig naar IPv6 gemigreerd is en ze willen communiceren met andere organisaties of geografische delen die ook al naar IPv6 gemigreerd zijn. Op de weg tussen elkaar liggen echter nog IPv4 netwerken, waardoor er nood is aan encapsulatie van de IPv6 datagram om ze over het conventionele IPv4 netwerk te kunnen sturen.*

###b) Bespreek in detail de diverse, ook de meest recente, tunnel mechanismen waarop men een beroep kan doen. Bespreek telkens onder andere hun toepassingsgebied, vereisten, beperkingen, de relatieve voor- en nadelen, en de gebruikte addresseringsschema's.
* ***Automatische IPv6 over IPv4 tunneling***  
Beslissing adhv volgende criteria

	- **Doeladres IPv4**: gebruik IPv4
	- **Eindbestemming op zelfde subnet**: gebruik IPv6
	- **Route naar eindbestemming met IPv6 als eerste hop**: bericht via IPv6 naar die router (minder overhead)
		* Wanneer IPv6 router het bericht tunnelt noemt men dit *router-to-host* automatische tunneling
	- **Indien geen van de drie criteria voldaan zijn**: automatische tunneling toepassen
		* Router biedt geen tunneling aan dus host moet die zelf doen *host-to-host* automatische tunneling
		* IPv4 adres van andere host bepaalt door laatste bytes van het IPv4 compatibele doeladres
		* Hierdoor gaat het pakket wel tot eindbestemming over IPv4, en daarbovenop moet host nog IPv4 compatibel zijn.
	- 
* ***Geconfigureerde IPv6 over IPv4 tunneling***
	- Geconfigureerde tunnels worden manueel ingesteld en slechts in 1 enkele richting
	- 1 tunnel met een IPv6/IPv4 router als eindpunt kan voor connectiviteit zorgen met een volledig IPv6 internetwerk (router injecteert ingekapselde IPv6 datagram opnieuw in zijn stack)
		* Hierna kan pakket opnieuw getunneld worden of gerouted naar de eindbestemming.
	- **router-to-router tunnel**
		* beginpunt van tunnel op een border router van een ander IPv6 internetwerk
		* definieer in routing tabel exact welke routes via welke tunnelinterface bereikt kunnen worden
	- **host-to-router tunnel**
		* stel een host machine in als beginpunt
	- in principe is een tunnel nodig per koppel IPv6 sites dat wil communiceren (dus veel configuratiewerk aan beide zijden)
		* in de toekomst waarschijnlijk 1 tunnel naar ISP, die dan de verdere tunneling afhandelt
* ***6to4 tunneling***
	- Bijzondere vorm van geconfigureerde IPv6 over IPv4 tunneling
	- Specifiek adresseringsschema van de IPv6-knooppunten (2002:w.x.y.z::/48 met w.x.y.z/p het publieke IPv4 adres van het netwerk).
	- Een IPv6 knooppunt met zo een adres geconfigureerd noemt ment **6to4-knooppunten**
		* Knooppunten beschikken over volledige IPv6 functionaliteit
		* SLA segment is volledig beschikbaar om adressering zoveel mogelijk af te stemmen op de topologie van het netwerk.
		* Knooppunten hoeven niet dual stack te zijn.
	- **6to4 router**: is een dual-stack border router met een publiek IPv4 adres voor de tunnelinterface
		* adverteert globale 2002::/16 adresruimte naar het intranet van de organisatie
		* hierdoor is onderlinge communicatie mogelijk tussen alle 6to4 knooppunten
		* Communicatie over IPv4 oceaan
			1. Host geeft dit door aan de 6to4 router
			2. 6to4 router herkent adhv van het 2002 TLA waarde dat het IPv6 in IPv4 geëncapsuleerd moet worden
			3. IPv6 bericht wordt zonder tussenkomst van IPv6 providers over IPv4 gerouterd
			4. 6to4 router op andere site verwijdert encapsulatie en stuurt het IPv6 bericht op zijn netwerk
	- **6to4 relay routers**: maken verkeer mogelijk tussen 6to4 en andere IPv6 knooppunten
		* Beschikt zowel over 6to4 als over een regulier IPv6 adres

	Is door zijn eenvoud de belangrijkste techniek die tijdens overschakeling naar IPv6 gebruikt zal worden maar kent echter ook wat ***nadelen***:
	- Aan elke organisatie toegewezen 6to4 blok is relatief beperkt in grootte
	- Adressen zullen na globale migratie hernummerd moeten worden naar reguliere IPv6 adressen
	- 6to4 kan niet in combinatie met NAT gebruikt worden
	- Zolang van 6to4 gebruik gemaakt wordt bijlven de routing tabellen van de default-free routers hun huidige grootte behouden
* **ISATAP tunneling**
	***Probleem met 6to4***  
	*Elk eiland moet bij 6to4 beschikken over een publieke IPv4 adresruimt. Hierdoor kan 6to4 NIET toegepast worden tussen subnetten van een zelfde private netwerkinfrastructuur*  
	***ISATAP (=Intra-Site automatic tunnel Addressing protocol) lost deze tekortkoming op***
	- IPv4 adres van het eindpunt wordt afgeleid door de laatste 32 bits van het ISATAP adres (van de vorm 0:5EFE:w.x.y.z met w.x.y.z een willekeurig IPv4 adres dat aan interface toegekend werd).
	- **ISATAP knooppunten** zijn steeds dual stack
		* Twee knooppunten van verschillende eilanden kunnen IPv6 datagrammen *host-to-host* tunnelen indien ze gebruik maken van de linklokale prefix FE80::/64
		* Communicatie tussen *ISATAP knooppunten van verschillende organisaties* is meer configuratie vereist.
			- Knooppunt moet globale prefix ontvangen (via uitwisseling van Router Solicitation en Router Advertisement berichten met border router)
			- Routingtabel van knooppunt moet een default route opnemen die berichten voor buiten de site bestemd afgeeft aan de intranet interface van de border router  
			(kan via IPv6 of ISATAP tunneling)
			- Het bericht wordt hier wel tot 3-maal toe getunneld
