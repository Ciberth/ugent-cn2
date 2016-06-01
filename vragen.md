# Interessante vragen

### Leg het verschil uit tussen single en double quotes in bash. 
Bij dubbel quotes worden metacharacters/wildcards nog steeds geïnterpreteerd, bij single(en dus strong quotes) worden deze characters letterlijk genomen.

### Leg het verschil uit tussen /dev/null en /dev/zero.
bij /dev/null zal alle data die hier naartoe geschreven worden genegeerd en weggeschreven. Indien een file wordt aangemaakt met /dev/null zal deze ook volledig leeg zijn. Indine met /dev/zero gebruikt zal de file opgevuld worden met nullen. /dev/zero wordt vaak als een default gebruikt.

### Leg het verschil uit tussen /dev/random en /dev/urandom.
Bij random komt de beste random kwaliteit, maar een potentiële trage uitvoer is mogelijk. Urandom geeft geen delay indien entropy pool op is.

### Waarom is `while read x < f; do echo $x ; done` **not done** en hoe los je dit op met file descriptoren?

### Bespreek verschillende backup en restore mogelijkheden.

### Leg de opstartprocedure uit van een Linux-systeem.

### Leg de afsluitprocedure uit van een Linux-systeem.

### Geef een overzicht van de verschillende runlevels.

### Leg uit wat er gebeurd als men `command` intypt in de bash-shell. Gebruik volgende kernwoorden in je antwoord: $PATH, intern (builtin) commando, "command command not found".

### Leg het verschil uit tussen hard en soft links. Geef ook van elk een voorbeeld.

### Welke soort partities bestaan er? Geef een overzicht van de schijfindeling als je weet da er maar 1 partitie op de schijf is.

### Wat is een superblok? Wat is een inode?

### Wat is het nut van de init.d en rcX.d mappen en bestanden. Verduidelijk kort. 

### Stel dat je je eigen script zou moeten schrijven voor adduser. Welke stappen zou je allemaal moeten ondernemen?

### Leg uit wat een sticky bit is. Leg uit SUID en SGID uit.

### Leg uit waarom Bash geen spaties rond het = teken wil bij het toekennen van een variabele. 

### Leg uit waarom een variabele enkel van ouder naar kind kan en niet andersom. Maak gebruik van de woorden subshell en export in je antwoord.

### Leg uit wat een Here document is.



