# Lab 04
In dit lab gaan we leren hoe we data inladen als echte developers. Tot nu toe hebben we onze data ingelezen via een eigen variabele, dat is al een stap beter dan data die we rechtstreeks invoeren in onze Chart. Een realistischer scenario is dat de data komt van een bestand of dat we data inladen van een online bron: een API. 

## Databestand
We doen nog eens alles van tevoren:

    npm create vite@latest

Ik noem mijn project `hellodata`. Kies Vanilla, kies javascript.

    cd hellodata
    npm install

In de public folder maak je een nieuw bestand aan, noem het `sales.json`. JSON staat voor Javascript object notation. Ter herinnering, een javascript object ziet er zo uit:

    const data = [
        {month: 'Jan', sales: 12000},
        {month: 'Feb', sales: 15000},
        {month: 'Mar', sales: 13500},
        {month: 'Apr', sales: 16000},
        {month: 'May', sales: 18000},
        {month: 'Jun', sales: 17500},
        {month: 'Jul', sales: 20000},
        {month: 'Aug', sales: 22000},
        {month: 'Sep', sales: 21000},
        {month: 'Oct', sales: 23000},
        {month: 'Nov', sales: 25000},
        {month: 'Dec', sales: 30000},
    ];

Enfin, eigenlijk is dit meer dan een javascript object, het is een `statement`, waarmee we een javascript object in een variabele steken genaamd `data`. Het pure javascript object is dit:

    [
        {month: 'Jan', sales: 12000},
        {month: 'Feb', sales: 15000},
        {month: 'Mar', sales: 13500},
        {month: 'Apr', sales: 16000},
        {month: 'May', sales: 18000},
        {month: 'Jun', sales: 17500},
        {month: 'Jul', sales: 20000},
        {month: 'Aug', sales: 22000},
        {month: 'Sep', sales: 21000},
        {month: 'Oct', sales: 23000},
        {month: 'Nov', sales: 25000},
        {month: 'Dec', sales: 30000},
    ]

Een JSON file is simpelweg een bestand dat een object bevat in javascript notatie, met een kleine uitzondering: alle tekst, dus ook de naam van de eigenschappen (`month` en `sales`) moeten tussen "" staan. We zetten dus het volgende in ons `sales.json` bestand:

    [
        {"month": "Jan", "sales": 12000},
        {"month": "Feb", "sales": 15000},
        {"month": "Mar", "sales": 13500},
        {"month": "Apr", "sales": 16000},
        {"month": "May", "sales": 18000},
        {"month": "Jun", "sales": 17500},
        {"month": "Jul", "sales": 20000},
        {"month": "Aug", "sales": 22000},
        {"month": "Sep", "sales": 21000},
        {"month": "Oct", "sales": 23000},
        {"month": "Nov", "sales": 25000},
        {"month": "Dec", "sales": 30000}
    ]

Als we met D3 bezig zijn kijken we nog naar een ander bestandsformaat: CSV.

We maken nu onze HTML gereed:

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <link rel="icon" type="image/svg+xml" href="/vite.svg" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Vite App</title>
    </head>
    <body>
        <div id="graph-container">
        <canvas id="graph-canvas"></canvas>
        </div>
        <script type="module" src="/src/main.js"></script>
    </body>
    </html>

We passen nu onze JS aan (ik heb de import statement voor de stylesheet laten staan):

    import './style.css'

    const ctx = document.getElementById('graph-canvas');

    console.log("Hello world");

merk op dat we hier weer weinig doen. We hebben al veel toegevoegd en we hebben dat allemaal nog niet getest, dus ik voeg hier het strikt noodzakelijke toe. We testen met:

    npm run dev

## Fetch
Nu we ons databestand hebben, gaan we het inladen. Het inladen van een bestand via een externe bron (een file, of van een API) is steeds een asynchrone operatie. Een **synchrone** operatie is een codeinstructie die meteen uitgevoerd kan worden. De regel wordt uitgevoerd en de code gaat verder. Als je belt met iemand dan voer je typisch een gesprek met vraag/antwoord. Je stelt een vraag, en je krijgt antwoord van je gesprekspartner.
Een **asynchrone** operatie is een code instructie die typisch wat complexer is en de medewerking vereist van een externe bron, zoals een website. Code moet snel uitgevoerd kunnen worden en als een operatie blijft hangen op een instructie kan het programma niet reageren op gebruikersinvoer. We willen niet dat ons programma blokkeert wanneer we het bestand aan het inladen zijn. Daarmee kan een asynchrone operatie helpen.
De instructie wordt uitgevoerd maar je krijgt niet meteen resultaat. Op latere tijdstippen in de code kan je controleren of er al resultaat beschikbaar is van je operatie. Vergelijk het met een berichtje of een email sturen. Je verstuurt de instructie en kijkt op latere tijdstippen of je al antwoord hebt. Goede concrete voorbeelden zijn het inladen van afbeeldingen op een website: we willen niet dat de website niet reageert terwijl dat inladen gebeurt.
Het inlezen van een bestand is ook zo een asynchrone functie, specifiek gebruiken wij de asynchrone `fetch` functie.
Functies die asynchrone operaties uitvoeren zijn vanzelfsprekend zelf ook asynchroon want die hebben ergens een 'wachtmoment' ingebouwd. We geven dit aan door de functie te markeren met het keyword `async`. We maken een asynchrone functie:

    async function loadChart()
    {
        const file = await fetch("sales.json");
        const json = await file.json();

        console.log(json);
    }

Een paar zaken die het vermelden waart zijn hier:

- We markeren onze functie als `async` omdat onze functie asynchrone functies gebruikt: specifiek 2 functies: `fetch()` en `json()`.
- In de eerste regel lezen we ons bestand in (dat moet in de public folder staan!) door middel van `fetch`. Om een asynchrone functie op te roepen heb je verschillende manieren:
    - `await`: dit is de simpelste manier. Je geeft aan dat de browser de functie moet uitvoeren en wacht tot de asynchrone functie klaar is. Dat is het equivalent van regelmatig te staren naar je GSM om te kijken of je lief al geantwoord heeft. Je denkt nu misschien dat dit hetzelfde is als wat we altijd doen maar met onnodige extra stappen. Er is echter wel een heel belangrijk verschil en dat gaan we zo dadelijk helder maken: op het moment dat deze regel uitgevoerd wordt zal deze functie stoppen en wordt er andere code eerst uitgevoerd zodat onze website reponsief blijft, de browser keert dan later terug naar deze regel en voert de rest van de code uit.
    - Je kan ook werken met een callback. In dat geval zeg je eigenlijk: roep deze functie op zodra de asynchrone functie klaar is.
    - Je kan ook een `handle` opvragen. Dat is een leeg object dat het resultaat van de functie zal bevatten zodra deze klaar is. Je moet dan zelf regelmatig controleren of dit object nog leeg is.
- We gebruiken niet 1 maar 2 asynchrone functies. Zodra we het resultaat hebben van `fetch`, hebben we toegang tot het bestand. Dat bestand is rauwe tekst (weliswaar in JSON formaat). Door de `json()` functie te gebruiken ga je deze leesbare tekst feitelijk omzetten naar een bruikbaar javascript object, ook dat is een asynchrone operatie.
- Zodra dat allemaal achter de rug is gaan we het resultaat afprinten.

Op dit moment roepen we de code nog niet op, dus laat ons dat eens proberen: 

    async function loadChart()
    {
        const file = await fetch("sales.json");
        const json = await file.json();

        console.log(json);
    }

    loadChart();
    console.log("Hello world");

✅ Deze code werkt wel maar er ontbreekt iets. Wat? Probeer op basis van wat je weet de fout zelf eens te zoeken. De oplossing vind je in oplossing 1.

✅ Bouw nu een lijngrafiek op basis van deze ingeladen data. Oplossing vind je in oplossing 2. Vergeet Chart.js niet te installeren!

## API
API staat voor Application Programming Interface en is een zeer brede term voor externe, aanspreekbare punten die je kan aanspreken vanuit je code. In principe is Chart.JS een API! Als het over data gaat spreken we vaak over een REST API. Dat gaat specifieker over een URL waar je naartoe kan om data op te halen en zelfs data naar te uploaden. We zullen het vaak in het kort hebben over een API maar in dit vak doelen we dan specifiek op een REST API.

Wil je eens een voorbeeld zien van een zo een 'endpoint' URL waar je data kan afhalen? Hier is er eentje : https://jsonplaceholder.typicode.com/posts Of hier: https://api.open-meteo.com/v1/forecast?latitude=40.7&longitude=-74&daily=temperature_2m_max,temperature_2m_min&timezone=auto.
Elke website die je bezoekt is simpelweg een documentje. Meestal is dat een HTML document maar dat kan dus ook een JSON file zijn zoals hier het geval is. URL staat dan ook voor Uniform Resource Locator, en dat impliceert dat er meer te vinden is op een URL dan enkel maar eenvoudige webpaginas.
Hoe je die URL opstelt is een kwestie van de documentatie volgen van de api. Zodra je de URL hebt kan je simpelweg opnieuw `fetch` gebruiken:

    const file = await fetch("https://api.open-meteo.com/v1/forecast?latitude=40.7&longitude=-74&daily=temperature_2m_max,temperature_2m_min&timezone=auto");

## Huiswerk: Weergegevens

Bestudeer https://open-meteo.com/en/docs. Gebruik `fetch` om de weersvoorspelling op te vragen voor Hasselt voor de komende 7 dagen. Je geeft deze weer in een lijngrafiek waarbij je de hoogste en laagste temperatuur per dag weergeeft. Je tekent dus 2 datasets op 1 grafiek. Vermijdt het gebruik van `map`. Zorg er bovendien voor dat:

- De lijn voor de hoogste temperatuur in het rood is getekend, en die van de lage temperatuur in het blauw.
- De blauwe lijn is tevens een stippellijn.
- Zorg dat de lijndikte 1 is voor beide lijnen.

Voorzie 3 knoppen: een knop met Hasselt op, een knop met Berlijn op en een knop met Kaapstad op. Als je op een knop klikt verandert de grafiek om de 7-daagse weersvoorspelling te tonen voor die stad. 
Indienen doe je via blackboard.

## Oplossingen

### Oplossing 1 

Asynchrone functies kan je op 3 manieren oproepen, de meest simpele manier is het gebruik van het `await` keyword:

    async function loadChart()
    {
        const file = await fetch("sales.json");
        const json = await file.json();

        console.log(json);
    }

    await loadChart();
    console.log("Hello world");

### Oplossing 2
    import './style.css';
    import Chart from 'chart.js/auto';

    const ctx = document.getElementById('graph-canvas');
    async function loadChart()
    {
    const file = await fetch("sales.json");
    const data = await file.json();

    console.log(data);
    
    const myGraph = new Chart(ctx, {
        type: 'line',
        data: {
            labels: data.map(r => r.month), 
            datasets:[
                {
                    label: 'Sales',
                    data: data,
                    borderColor: 'rgb(75, 192, 192)',
                    borderWidth: 1,
                    parsing: {
                        xAxisKey: 'month',
                        yAxisKey: 'sales'
                    }
                }
            ]
        },
        options: 
        {
            plugins: {
                legend: {position: 'bottom'},
                } ,
                maintainAspectRatio:false, 
                responsive: true, 
                scales: { y: { beginAtZero: true } } 
            }
        });
    }

    loadChart();
    