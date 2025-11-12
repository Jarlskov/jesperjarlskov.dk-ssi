---
title: 'Teknikken bag en Facebook konkurrence'
date: 2011-11-29
---

I sidste uge lancere min arbejdsplads en konkurrence hvor man kan vinde en skiferie. For at sprede ordet valgte vi bl.a. at dele konkurrencen på Facebook. I forbindelse med Facebookdelen rendte jeg ind i et par problemer som jeg havde svært ved at finde dokumenteret online. Konkurrencen blev oprettet som en facebook page app.

<h2>Facebooksiden</h2>
Facebooksiden i sig selv var ikke et stort problem. Jeg lavede en app på <a href="https://developers.facebook.com/apps">Facebook Developers</a> siden. Dette giver en række muligheder, bl.a. muligheden for at vælge hvordan din app skal integreres med Facebook.

For at kunne bruge applikationen som et element på en Facebook page, vælges "page tab". Teknisk fungerer det ved at du selv hoster din app, som en almindelig html side, der så indlejres på din Facebook Page, i en iframe. En page tab har 4 indstillinger:
<ul>
<li>Page Tab Name - Den navn din app skal have i page menuen</li>
<li>Page Tab URL - Den side Facebook skal integrere i sin iframe</li>
<li>Secure Page Tab URL - En HTTPS-version af den side der skal vises på Facebook</li>
<li>Page Tab Edit URL - Link til din app's admin modul. Jeg skippede dette, da mit projekt ikke havde behov for customization</li>
</ul>
I følge Facebook selv har mere end 9.6mio brugere valgt kun at kunne tilgå Facebook via https. Hvis du ikke har tilføjet en HTTPS-version af din app vil alle de mennesker hverken kunne se eller bruge din app.

Et af de problemer jeg kæmpede mest med, var at gøre det muligt for folk at dele appen med deres venner. Ikke fordi det var svært rent kodemæssigt, men jeg fandt ud af at det kræver at appen udover at være oprettet som 'page tab' som forklaret ovenfor, også kræver at den er lavet som en normal 'app on facebook' (se billede ovenfor). Når du vælger 'app on facebook' får du 2 indstillingsmuligheder. Canvas URL, og Secure Canvas URL. Et Facebook app canvas fungerer på samme måde som en Facebook page tab, bortset fra at det er en selvstændig app, og ikke tilknyttet en page. Da jeg ikke havde brug for en selvstændig app valgte jeg at lave mine canvas URLs pege på en tom side, der kun bestod af et JavaScript redirect, til min page tab.

<h2>Selve appen</h2>
Da opsætningen var på plads var der bare tilbage at lave selve appen. Det sker som nævnt tidligere vha. html, css og javascript, og der er altså ikke meget magi her. For at få adgang til Facebooks funktionalitet, skal Facebook SDK'et loades.
<pre lang="Javascript" line="1" escaped="false">
<div id="fb-root"></div>
<script>
	(function(d){
		var js, id = 'facebook-jssdk'; 
		if (d.getElementById(id)) {return;}
		js = d.createElement('script'); 
		js.id = id; 
		js.async = true;
		js.src = d.location.protocol + "//connect.facebook.net/en_US/all.js";
		d.getElementsByTagName('head')[0].appendChild(js);
	}(document));
	
	window.fbAsyncInit = function() {
		FB.init({
			appId: 'YOUR-APP-ID',
			status: true,
			cookie: true,
			xfbml: true,
		});
	}
</script>
</pre>
Linjen:
<pre lang="JavaScript" line="1">
js.src = d.location.protocol + "//connect.facebook.net/en_US/all.js";
</pre>
kan bruges til at styre hvilket sprog Facebooks ting skal vises på. Tilgængelige sprog kan findes i <a href="http://www.facebook.com/translations/FacebookLocales.xml">Facebook's Locale XML</a>.

Nu er der adgang til Facebooks funktionalitet via. Javascript. I dette projekt blev det brug til 2 ting. Først skal der være mulighed for at folk kan dele appen med deres venner:
<pre lang="Javascript" line="1">
function tellafriend() {
	FB.ui({
		method: 'apprequests',
		message: 'Hvilken besked skal folk sende til deres venner?',
		title: 'Overskrift.'
	}, function (response) {
		poststory();
	});
}
</pre>
Den sidste del:
<pre lang="Javascript" line="1">
function (response) {
	poststory();
}
</pre>
Sørger selvfølgelig for at funktionen poststory() kaldes så snart folk har valgt hvem af deres venner de gerne vil dele appen med. Det er også muligt at trykke cancel og på den måde ikke dele med nogen, poststory() vil dog stadig blive kaldt.
Formålet med poststory() er at lade folk poste et link til appen på deres egen væg:
<pre lang="Javascript" line="1">
function poststory() {
	FB.ui({
		method: 'feed',
		name: 'Overskrift på vægopslaget',
		link: 'Link der skal deles',
		picture: 'link til billede der skal inkluderes på opslaget',
		caption: 'Billedetekst.',
		description: 'Brødtekst på indlægget'
	}, function(response) {
		redirectuser();
	});
}
</pre>
Efter poststory() kaldes funktionen redirectuser(), som vha. et normalt Javascript redirect videresender brugeren til vores egen konkurrenceside.

Jeg håber indlægget kan hjælpe med at komme udenom nogen af de små fælder jeg selv rendte ind i :-)
Husk i forbindelse med konkurrencer at overholde Facebook's egne regler for konkurrencer. Ellers kan det blive en kort fornøjelse. Kasper Blom har skrevet en god kort <a href="http://casperblom.dk/facebook-konkurrence-regler-opsumering/">opsummering af konkurrencereglerne</a>.