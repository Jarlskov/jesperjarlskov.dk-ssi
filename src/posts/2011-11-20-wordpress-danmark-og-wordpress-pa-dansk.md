---
title: 'WordPress Danmark og WordPress på dansk'
date: 2011-11-20
---

Som jeg nævnte i forrige indlæg havde vi en diskussion efter sidste weekends WordCamp, vedrørende <a href="http://wp-danmark.dk">WordPress Danmark</a>, og hvad folk i communitiet mener der kan gøres for at <a href="http://jesperjarlskov.dk/wordpress-danmark-hvad-sa-nu/">forbedre WordPress Danmark</a>.

Som nævnt har jeg meldt mig som tovholder på oversættelsesteamet. Ikke i den forstand at jeg skal have hånd i hanke med hvad der oversættes og hvordan, slet ikke, men i den forstand at jeg skal se på mulighederne for at gøre det lettere at finde plugins og temaer der er oversat til dansk, samt gøre det lettere for folk at bidrage til oversættelserne.

Jeg har ikke alle de rigtige svar, men jeg har selvfølgelig gjort mig nogle tanker om hvad der evt. kunne gøres.

<ol>
<li>Gøre det lettere hjælpe</li>
<li>Gøre det let at tilføje projekter til førnævnte GlotPress, så folk med interesse i et specifikt plugin eller let kan komme i gang med at oversætte netop det</li>
<li>Gøre det muligt at søge efter oversatte plugins</li>
</ol>

Første punkt kunne evt. klares ved at opsætte en GlotPress installation på <a href="http://wp-danmark.dk">wp-danmark.dk</a>, for at samle oversættelsesindsatsen, samt færdige oversættelser et sted. Det behøver selvfølgelig ikke nødvendigvis være GlotPress, men det kunne være et fornuftigt valgt, da det er <a href="http://codex.wordpress.org/Translating_WordPress#Translation_Tools">anbefalede værktøj</a>, og det bygger på <a href="http://bbpress.trac.wordpress.org/wiki/BackPress">BackPress</a>, som er en fælles kodebase for WordPress relaterede projekter.

Andet punkt kan hjælpes på vej ved evt. ved at se på muligheden for at kunne trække filer til oversættelse direkte fra WordPress.org's plugin og theme repository, og evt. gøre det let for oversættere at tilføje nye projekter, til det software der blev diskuteret i punkt 1.

Hvis de ovenstående 2 punkter bliver en succes, vil det selvfølgelig betyde at vi med tiden "automatisk" får opbygget en pæn samling oversatte plugins. Derudover vil det være relevant at kunne hooke ind i søgefunktionen på WordPress.org, for her i gennem at få adgang til de oversatte plugins og themes der allerede findes.
Der er pt. <a href="http://api.wordpress.org/plugins/info/1.0/">ingen officielt dokumentation</a> til WordPress.org søge API'et, men der kan findes nogle små optegnelser hos <a href="http://dd32.id.au/projects/wordpressorg-plugin-information-api-docs/">DD32</a>. Desuden kan der jo søges både i plugins og themes direkte fra WordPress admin interfacet, hvilket betyder at findes fungerende kode i WordPress, som der kan rodes i.

Har du andre forslag til hvordan det kan gøres lettere at finde danske plugins og themes, eller hvordan det kan gøres lettere for folk at hjælpe med at oversætte? Eller har du lyst til at hjælpe med at implementere ovenstående? Smid en kommentar herunder, eller endnu bedre, deltag i debatten på <a href="http://udvikling.wp-danmark.dk/">WordPress Danmark's udviklingsblog</a>.