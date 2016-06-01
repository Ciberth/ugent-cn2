# Interessante vragen

### Leg het verschil uit tussen single en double quotes in bash. 
Bij dubbel quotes worden metacharacters/wildcards nog steeds geïnterpreteerd, bij single(en dus strong quotes) worden deze characters letterlijk genomen.

### Leg het verschil uit tussen /dev/null en /dev/zero.
bij /dev/null zal alle data die hier naartoe geschreven worden genegeerd en weggeschreven. Indien een file wordt aangemaakt met /dev/null zal deze ook volledig leeg zijn. Indine met /dev/zero gebruikt zal de file opgevuld worden met nullen. /dev/zero wordt vaak als een default gebruikt.

### Leg het verschil uit tussen /dev/random en /dev/urandom.
Bij random komt de beste random kwaliteit, maar een potentiële trage uitvoer is mogelijk. Urandom geeft geen delay indien entropy pool op is.

### Waarom is `while read x < f; do echo $x ; done` **not done** en hoe los je dit op met file descriptoren?

exec 3<file
while read x<&3; do iets; done
exec 3<&-

### Bespreek verschillende backup en restore mogelijkheden. //hst 7

### Leg de opstartprocedure uit van een Linux-systeem.

### Leg de afsluitprocedure uit van een Linux-systeem.

### Geef een overzicht van de verschillende runlevels.
0 poweroff.target
1 rescue.target, systeemadministrator modus
2 multiuser modus zonder netwerkfaciliteiten
3 multiuser modus
4 door de administrator te definiëren systeemstaat, default niet gebruikt
5 graphical.target
6 reboot.target

### Leg uit wat er gebeurd als men `command` intypt in de bash-shell. Gebruik volgende kernwoorden in je antwoord: $PATH, intern (builtin) commando, "command command not found".
commando=intern of extern

intern: onderdeel van het shellproces
extern: bestand(binair of shellscript), naam bestand = naam commando

op zoek naar een commando
1. Externe variabelen aflopen in volgorde van PATH
2. builtin commandos aflopen
3. kijken in de lijst van functies
4. kijken in de lijst van aliassen of het erbij staat

Indien het commando niet gevonden kan worden wordt de boodschap command not found weergegeven

### Leg het verschil uit tussen hard en soft links. Geef ook van elk een voorbeeld.
hard links zijn links tussen bestanden of mappen op hetzelfde filesysteem, softlinks kunnen ook op een ander filesysteem links leggen.
Aanpassingen aan de hardlink resulteert in aanpassingen aan de file zelf, bij softlinks is dit niet het geval.

Voorbeeld? ln hlink ;ln -s slink?

### Welke soort partities bestaan er? Geef een overzicht van de schijfindeling als je weet da er maar 1 partitie op de schijf is.

### Wat is een superblok? Wat is een inode?
Elke file of directory heeft een inode. 

### Wat is het nut van de init.d en rcX.d mappen en bestanden. Verduidelijk kort. 

### Stel dat je je eigen script zou moeten schrijven voor adduser. Welke stappen zou je allemaal moeten ondernemen?

### Leg uit wat een sticky bit is. Leg SUID en SGID uit.
Een sticky bit wordt gebruikt zodat enkel de creator zijn files en directories kan verwijderen, geen andere gebruikers.
Dankzij de SUID bit kan de gebruiker dingen doen waarvoor hij anders bepaalde permissies nodig heeft. Zo kan hij dezelfde functionaliteiten hebben als een root gebruiker.
Als de GUID gezet werd dan zullen bestanden die in deze directory aangemaakt worden van diezelfde groep zijn van de directory en niet van de groep van de gebruiker zelf.

### Leg uit waarom Bash geen spaties rond het = teken wil bij het toekennen van een variabele. 

### Leg uit waarom een variabele enkel van ouder naar kind kan en niet andersom. Maak gebruik van de woorden subshell en export in je antwoord.
Variabelen zijn lokaal in een subshel behalve environment variabelen, dit zijn variabelen die geëxporteerd werden. Deze globale variabelen kunnen meegegeven worden naar een kind, die een nieuwe subshell is, maar elke shell heeft zijn eigen verzameling environment variabelen, een commando of subshell dat aangeroepen wordt in een shell kan environment variabelen overerven van de aanroepende shell, maar deze kan die variabelen niet uit de subshell overerven.

### Leg uit wat een Here document is.



