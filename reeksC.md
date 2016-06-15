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

2. **Extensie headers**
	- In toekomst makkelijk nieuwe extensies/opties te definiëren
	- Tussenliggende routers kunnen deze info zonder enige interpretatie doorzenden (bevalve Hop-By-Hop options)
	- **Structuur**
		* 2 bytes : type en grootte van de header
		* Gevolgd door extensie specifieke gegevens

###b) Geef en bespreek de verschillende soorten extensieheaders die momenteel voor IPv6 gedefinieerd zijn.
