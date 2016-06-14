#Reeks C

##Vraag 1: DHCP leaseprocessen en relay-agents (2.1.2 & 2.1.3)
###Geef een overzicht van de verschillende types DHCP berichten. Hoe wordt in het bericht het type aangeduid ?
Het type wordt aangeduid door middel van de optie nummer 53 te zetten. De beschikbare opties zijn 

1. **DHCP-Discovery**
2. **DHCP-Offer**
3. **DHCP-Request**
4. **DHCP-Decline**
5. **Positief DHCP-Acknowledgement bericht**
6. **Negatief DHCP-Acknowledgement bericht**
7. **DHCP-Release bericht**

##Bespreek in detail de opeenvolgende stappen van beide DHCP leaseprocessen.
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
	4. *Server stuurt een **positief DHCP-Acknowledgement***
		* Bevestigt lease aan client
		* Stuurt meteen ook de gevraagde opties mee
		* Andere DHCP-servers geven de aangeboden reservering ook terug vrij  
		Opmerking: *In zeldzame gevallen: **negatief DHCP-Acknowledgement***
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
		- Wachten tot $T_2$ verstreken is (de tijd voor rebinding, ongeveer $$$\frac{7}{8}$$$ van totale lease)
		- Wanneer de rebinding status bereikt wordt probeert client huidige lease te vernieuwen bij willekeurige
			beschikbare DHCP.
	3. **Er kan een server reageren met *DHCP-Acknowledgement*** en de client kan zoals ervoor verdergaan
	4. **Indien er niet "gerebind" kon worden**: periodiek DHCP-Request trachten uit te sturen (4+,8+,16+)s
	5. **Wanneer client dan nog geen antwoord krijgt** dan **moet** de client zijn lease loslaten (ook al zou hij in principe  			nog het adres kunen blijven gebruiken. En *moet leaseproces (1) gestart worden*.


##Bespreek het doel en (in detail) de werking van DHCP relay-agents. Welke velden in DHCP berichten helpen deze functie realiseren ?
