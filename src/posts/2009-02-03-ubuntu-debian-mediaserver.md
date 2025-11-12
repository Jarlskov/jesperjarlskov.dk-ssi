---
title: 'Ubuntu/Debian medieserver med Playstation 3 support'
date: 2009-02-03
---

I denne artikel vil jeg fortælle lidt om hvordan man opsætter sin <a href="http://jesperjarlskov.dk/2009/asus-eee-box-som-debian-server/">Ubuntu eller Debian server</a> som medieserver, dvs. en maskine der indeholder Video/lyd-filer, og deler dem på netværket. Her mener jeg selvfølgelig ikke piratkopiering, men jeg mener at dele medierne med andre enheder i hjemmet.
Min egen grund til at opsætte en medieserver var for at dele musik og film med min playstation 3, så de kan blive afspillet via. fjernsynet, da jeg pt. ikke har nogle ordentlige højtalere tilknyttet min computer.
<h2>UPnP</h2>
Playstation 3 understøtter den protokol der hedder <abbr title="Universal Plug and Play">UPnP</abbr>. Det er en protokol der bl.a. bygget på <abbr title="HyperText Transfer Protocol">HTTP</abbr> og <abbr title="Transmission Control Protocol">TCP</abbr>/<abbr title="Internet Protocol">IP</abbr>, der gør det muligt for et antal enheder at arbejde sammen uden at man skal rode med drivere, opsætning af forbindelse osv.

<h2>Mediatomb</h2>
For at få Playstationen til at arbejde sammen med medieserveren er det altså bare nødvendigt at finde noget mediecentersoftware der understøtter UPnP. En anden begrænsning er at det skal køre på den server jeg har opsat uden <abbr title="Graphical User Interface">GUI</abbr>.
Til dette formål har jeg fundet programmet <a href="http://mediatomb.cc/">MediaTomb</a>, dette findes i repositories, så det er let:
<blockquote>
aptitude install mediatomb
</blockquote>
Efter installationen startes programmet med kommandoen:
<blockquote>
mediatomb
</blockquote>
Hvis du tidligere har installeret <a href="http://jesperjarlskov.dk/2009/screen-irssi/">Screen på din Ubuntu/Debian server</a> kan du åbne et screenvindue og starte mediatomb i dette. Det giver mulighed for at lade medieserveren køre selvom du logger af, og vende tilbage til den på et senere tidspunkt.

Selve administrationen sker igennem et webinterface på en port som MediaTomb selv indstiller. Du bliver informeret om den adresse du skal connecte til når du starter MediaTomb. Denne adresse tastes altså bare ind i en webbrowser, og du vil straks have adgang til administratorinterfacet på din medieserver. Så mangler du bare at ligge medier på serveren :-)