# Lab 01
In dit lab maken we voor het eerst kennis met Chart.js, een softwarelibrary om grafieken te tekenen in javascript.

## Hello Chart.js
Voor we aan de slag kunnen met Chart.js en D3.js, 2 software libraries die we in deze cursus gaan gebruiken om grafieken mee te tekenen en om data mee te verwerken, gaan we eerst onze ontwikkelingsomgeving in orde moeten krijgen.

### NPM
NPM (Node Package Manager) is de tool die wij gebruiken om onze softwarepakketen te beheren. Normaal gezien heb je NPM al geinstalleerd in een ander vak maar we gaan dat toch even controleren, en, zo nodig, opnieuw installeren.

Open je favoriete IDE (ik gebruik voor dit Visual Studio Code) en open een terminal. Gebruik het volgende commando ```npm -v```. De output die je krijgt zou dan iets zoals dit moeten zijn:

    11.5.2

Als je een foutmelding krijgt, dan is het waarschijnlijk dat npm niet geinstalleerd is. Ga naar https://nodejs.org/en/download en volg de instructies. Vergeet daarna niet je IDE en terminal opnieuw op te starten!
Wil je je versie van npm updaten, dan kan je het volgende commando proberen: ```npm install npm@latest```.

### Vite
Als we een webpagina bekijken dan is onze browser aan het communiceren met een server en wordt code (javascript) door beide partijen uitgevoerd. Om op je eigen computer een website te kunnen ontwikkelen hebben we dus ook een server nodig, die weliswaar ook op onze eigen computer zal draaien. Daarvoor gebruiken wij Vite (https://vite.dev/guide/).

Nu gaan we een simpele webpagina opzetten. Zorg dat je terminal geopend is in een folder waarin je graag je oefeningen wilt bijhouden voor dit vak. Doe nu:

    npm create vite@latest


Als je een prompt krijgt om vite te installeren, kies ```y```. Als Projectnaam kies je maar ```hello-chart``` (Vite houdt niet van hoofdletters, dus gebruik geen hoofdletters), kies ```Vanilla``` en dan ```Javascript```. Dit maakt een nieuwe folder aan met daarin je project, vergeet dus niet deze map in te navigeren:

    cd hello-chart

Bekijk de folder van je project even. Er zijn 2 belangrijke elementen die ik even wil belichten: node_modules en package.json. De softwarepakketten in je project worden bijgehouden in een manifest: package.json. Je node_modules folder bevat alle softwarepakketten die je website nodig heeft. Die folder kan bijzonder groot worden. Je package.json file is een manifest (zoals een passagierslijst op een vliegtuig) en bevat een lijst van alles softwarepakketten (en hun versie) die geinstalleerd zijn in de node_modules folder. je denkt nu waarschijnlijk: "Maar ik heb helemaal geen node_modules folder!". De node-modules folder kan je ten allen tijden weggooien want die kan terug gemaakt worden op basis van de informatie in je package.json. Dat doe je met het volgende commando (voer dat nu uit):

    npm install

Je kan gerust je node_modules folder verwijderen. Voer dan gewoon opnieuw dat commando uit. Let op dat je uiteraard wel internet nodig hebt om die pakketten opnieuw te downloaden, dit zou dus geen goed idee zijn als het internet uitstaat (zoals bij een examen, bijvoorbeeld).

Als je voor dit vak een oefening instuurt of je examen indient, verwijder dan ten allen tijden de node_modules folder, dan is je project veel kleiner! Ik kan zelf gerust die node_modules folder opnieuw samenstellen.

Een vite project geeft onze al een simpele ```index.html``` pagina die verwijst naar een javascript file (```main.js```) in je ```src``` folder. Kijk maar eens naar ```index.html``` en je zal de verwijzing vinden naar naar ```main.js```:

    <script type="module" src="/src/main.js"></script>

Je zal merken dat er nog een 2e javascript file is: ```counter.js```. Die is onderdeel van het startersvoorbeeld van vite en hebben we feitelijk niet nodig. Voor dit lab laten we die gewoon staan maar in de volgende labo's zal ik die opkuisenn. 
We testen nu onze webpagina. Run het commando:

    npm run dev

De output zal je een link geven naar je blitse nieuwe webpagina, hoera! Als je hier trouwens een foutmelding krijgt, dan zou het goed kunnen dat je je Node versie moet updaten.
Je kan de server terug afsluiten met CTRL+C.

### Simpele Webpagina
Verander ```index.html```:

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <link rel="icon" type="image/svg+xml" href="/vite.svg" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Hello Chart</title>
    </head>
    <body>
        <canvas id="myChart" width="400" height="200"></canvas>
        <script type="module" src="/src/main.js"></script>
    </body>
    </html>

Dit is feitelijk een lege webpagina. De body bestaat uit 2 elementen, weliswaar, maar het eerste element is een leeg canvas van 400px x 200px genaamd ```myChart``` en het tweede element is een verwijzing naar onze (lege) javascript code: ```main.js```.

## Chart.js
We gaan het in de komende lessen nog uitgebreid hebben over Chart.js, maar de doelstelling voor vandaag is dat ding gewoon eens in actie zien. Dus laat ons meteen de library toevoegen met onze blitse npm:

    npm install chart.js

In je package.json zal je zien dat een regel toegevoegd is onder 'dependencies':

    "chart.js": "^4.5.0"

Open ```main.js```, verwijder alles en begin met de volgende regel:

    import Chart from 'chart.js/auto';

Deze regel vist de chart.js uit onze node_modules map. Dat is nodig want niet ieder script gaat altijd elke library nodig hebben uit onze node_modules. Het is nodig dat we expliciet zijn in het gebruik van onze libraries. 
Buiten de library inladen doet deze code helemaal niks, maar, denk eraan, deze code wordt wel al uitgevoerd. In onze HTML hebben we namelijk een link gelegd naar deze code. Ik vind het een goed idee om dit al meteen eens te testen met ```npm run dev```. 

Sluit de server opnieuw af met CTRL+C en schrijf de volgende code voor je ```main.js```:

    import Chart from 'chart.js/auto';

    const ctx = document.getElementById('myChart');

    new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
        label: '# of Votes',
        data: [12, 19, 3, 5, 2, 3],
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        borderColor: 'rgba(75, 192, 192, 1)',
        borderWidth: 1
        }]
    },
    options: { scales: { y: { beginAtZero: true } } }
    });

In dit stuk code zoeken we eerst aansluiting bij onze HTML code door het HTML elementje op te vragen met het id ```myChart```. Dat is ons Canvas.
Dan komt onze Chart.js code die een bar chart zal tekenen op dat canvas. 

Run de code met ```npm run dev``` en probeer de code te interpreteren en onderstaande opdrachten uit te voeren.

## Opdrachten
Voer de onderstaande opdrachten uit:

✅ Verander de labeltjes naar de dagen van de week.

✅ Verander naar een ```line``` chart.

✅ Voeg een 2e dataset toe met een andere kleur.
