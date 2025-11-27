# Lab 02
In dit lab leren we over de anatomie van een Chart.js object en leren we enkele nieuwe soorten grafieken op te stellen. We spelen met enkele opties om onze grafieken kleiner of groter te maken, leren werken met meerdere datasets en lezen onze data in van een javascript object.

## Anatomie van een grafiek in Chart.js

We werken verder op de code van ons vorig labo. Als je wilt, kan je de vorige oefening kopieren als je graag de oplossing van de vorige oefening behoudt, of je kan opnieuw het labo volgen om opnieuw de kennis van het vorig labo op te frissen.

Dit is de code zoals we die nu hebben in main.js:

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

We maken steeds een grafiek in Chart.js in de volgende vorm:

    new Chart (context, config)

Er zijn dus 2 stukken input nodig voor een grafiek: een context en een config object. De context is waar de grafiek getekend mag worden dus ons werk focust zich vooral op het config object.

Hier is de anatomie van het config object:

    {
        type: 'bar',            // Type grafiek
        data: {                 // Data object
            labels: [...],      // Labels op de x-as
            datasets: [...]     // De feitelijke data, kunnen meerdere reeksen zijn!
        },
        options: {          
            responsive: true,
            plugins: { ... },
            scales: { ... },
            ...
        }
    }

Het is belangrijk dat de labels overeenkomen met de data. Dus, als we 5 datapunten hebben, hebben we ook 5 labeltjes nodig [^1]. 

## Data Object
Uiteindelijk gaan we de data inlezen van een externe bron: van een file, service, server, etc. Laat ons dus een beginnen met de data niet zo rechtstreeks in te vullen in onze chart. We maken een eigen data object aan dat alles bevat dat we willen visualiseren:

    const data = [
        {color: 'Red', value: 12},
        {color: 'Blue', value: 19},
        {color: 'Yellow', value: 3},
        {color: 'Green', value: 5},
        {color: 'Purple', value: 2},
        {color: 'Orange', value: 3},
    ];

Willen we nu een lijst van alle kleuren (['Red', 'Blue', 'Yellow', ...]) dan kan zo[^2]:

    data.map(r => r.color);

✅ Denk nu zelf eens na over hoe je rij van values kan krijgen. Probeer nu (zonder naar de oplossing hier beneden te kijken), deze data te gebruiken in onze chart, in de plaats van de hardgecodeerde cijfers die er nu staan. De oplossing (1) staat vanonder in het labo.

## Meerdere Datasets
We gaan onze data nu uitbreiden om te tonen dat we ook met meerdere datasets kunnen werken:

    const data = [
        {color: 'Red', value: 12, likes: 5},
        {color: 'Blue', value: 19, likes: 15},
        {color: 'Yellow', value: 3, likes: 8},
        {color: 'Green', value: 5, likes: 2},
        {color: 'Purple', value: 2, likes: 7},
        {color: 'Orange', value: 3, likes: 9},
    ];

✅ Als ik je nu zou vragen om mij een lijst van alle likes in een variabele te stoppen, zou je dat kunnen?

Het `datasets` object dat onze chart nodig heeft is een array en dat betekent dat ik er meerdere object kan insteken. Een element in de `datasets` array bestaat onder andere uit:

- `label`: Een beschrijving of label dat bijvoorbeeld gebruikt wordt voor de legende.
- `data`: Dit is het belangrijkste, onze feitelijke data zoals onze lijst van values of likes.
- `backgroundColor`: De kleur van elke bar in een bar chart die bij deze dataset hoort, of de kleur van de lijn in een lijngrafiek, etc.
- `borderColor`: De kleur van de rand van een bar, bijvoorbeeld
- `borderWidth`: De breedte van de border als we die zouden willen

We zouden bijvoorbeeld meerdere elementen in de dataset kunnen steken als volgt:

    datasets: [
      {
        label: 'Values',
        data: [12, 19, 3, 5, 2, 3],
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        borderColor: 'rgba(75, 192, 192, 1)',
        borderWidth: 1
      },
      {
        label: '# of Likes',
        data: [5, 15, 8, 2, 7, 9],
        backgroundColor: 'rgba(255, 159, 64, 0.2)',
        borderColor: 'rgba(255, 159, 64, 1)',
        borderWidth: 1
      }
    ]


✅ Probeer het nu zelf eens! Zorg dat we onze likes ook meetekenen. Gebruik daarvoor ons `data` object! De oplossing vind je vanonder als oplossing 2.

✅ Verander ook het chart type eens naar `line`.

✅ Zorg dat de 2e lijn, die van likes, groen is, en ook dikker.

## Oefening: Pie Chart
Nu is het aan jou! Spannend!
Je gaat voor mij een pie chart maken. Als type gebruik je dan `pie`. Je gebruikt de volgende data:

    const data = [
        {label: 'Apples', sales: 10, color: 'rgba(255, 99, 132)'},
        {label: 'Bananas', sales: 14, color: 'rgba(255, 206, 86)'},
        {label: 'Cherries', sales: 7, color: 'rgba(54, 162, 235)'},
    ];

Je hebt nu maar 1 dataset, maar wel meerdere kleuren. ` backgroundColor` is namelijk ook een array, probeer maar eens de lijst van `color` in te vullen als waarde voor `backgroundColor`! De oplossing vind je vanonder als oplossing 3.

✅ Voeg nu zelf een vierde fruit toe met een eigen kleur en verkoopscijfer.

## Aanpassingen
We werken verder aan onze Pie Chart. Kopieer oplossing 3 indien nodig.

Als je nu naar de Pie Chart kijkt zal je merken dat die veel te groot is, en dit ondanks dat we onze chart eigenlijk in een elementje gestoken hebben dat als width en height slechts 400px x 200px zou moeten zijn. We gaan daar iets aan doen. We starten door onze html aan te passen. We gaan de chart stoppen in een container:

    <div class="chart-container">
      <canvas id="myChart"></canvas>
    </div>

Voeg een link toe naar `style.css` (of een eigen css) in de header:

    <link rel="stylesheet" href="/src/style.css" />

We voegen stijl toe voor onze `chart-container`:

    .chart-container
    {
        width: 500px;
        height: 500px
    }

Laat ons nu eens wat meer kijken naar het 'options' object dat je kan meegeven aan een grafiek. Dit object kan snel zeer complex worden en het meeste van de documentatie van Chart.js gaat daarover. Met het 'options' object gaan we het gedrag aanpassen van onze grafiek. Ons options object ziet er nu als volgt uit:

    options: 
    {
        scales: { y: { beginAtZero: true } } 
    }

Ik ga beginnen met de volgende 2 regels toe te voegen:

    options: 
    {
        maintainAspectRatio:false, 
        responsive: true, 
        scales: { y: { beginAtZero: true } } 
    }

Die zorgen ervoor dat de grafiek beter reageert op andere resoluties en niet zo stug zal vasthouden aan zijn eigen aspect ratio.
Het volgende dat we gaan doen is het gedrag aanpassen van de tooltips. Een tooltip krijg je als je met je muis over een stuk van onze taart zweeft. Op dit moment krijg je dan de waarde te zien van dat taartstuk. Dat kunnen we aanpassen:

    options:
    {
        plugins: {
            legend: {position: 'bottom'},
            tooltip: {
                callbacks: {
                    label: function(context) {
                    return 'Sam is leuk';
                    }
                }
            }
        } ,
        ...

Dit ziet er wat complex uit, dus ik zal het even stap voor stap overlopen:

- Sommige functionaliteit van het 'options' object zit verstopt in zogenaamde plugins, die spreken we aan door ze te verzamelen in een array genaamd `plugins`. Of een optie een plugin is of niet zal je moeten ontdekken in de documentatie of vanbuiten leren.
- de `legend: {position: 'bottom'}` zet de legende aan de onderkant van de grafiek. Andere waarden die hier zouden kunnen zijn bijvoorbeeld `right` of `left`.
- Dan gaan we het gedrag aanpassen van onze tooltips. Een tooltip roept een javascript functie op als we zweven boven een datapunt (taartstuk in ons geval). De functie die opgeroepen wordt krijgt als input een `context` object en wordt verwacht een stukje tekst terug te geven dat getoond kan worden door de tooltip. Het `context` object bevat onder andere informatie over het datapunt waar we over zweven maar ook over alle andere datapunten. In dit geval negeren we al die informatie en geven we gewoon de belangrijke boodschap mee: 'Sam is leuk'.

Probeer deze code maar eens en je zal zien dat je tooltip aangepast is naar een veel nuttigere boodschap.

✅ Tijd voor een uitdagendere oefening! Probeer nu per taartstuk eens het percentage weer te geven van dat taartstuk als we er over zweven met de muis. Daarvoor heb je uiteraard nog wat extra informatie nodig over hoe dat `context` object werkt, dus hier de ingredienten die je nodig hebt:
- `context.dataset.data` geeft je de volledige lijst van data mee die betrekking heeft op het elementje waar je over zweeft. In ons geval is dat `[10, 14, 7]`.
- `context.parsed` geeft je de data van het element waar je over zweeft, dus in ons geval 10, 14 of 7, afhankelijk van het element waar je over zweeft.
Oplossing 4 en 5 geven 2 verschillende manieren om dit op te lossen.



[^1]: Probeer het eens! Als je minder labels hebt dan data, dan wordt er maar evenveel data getekend als je labeltjes hebt. Als je meer labels hebt dan data, dan gaat Chart.js er van uit dat je data 0 is voor de labeltjes die geen data hebben.
[^2]: r kan je hier vervangen door eender wat. Ik kies hier voor r omdat ik doel op het woord 'row' (voor elke rij in data, doe...)

# Huiswerk: Mini-dashboard

Maak mij een mini dashboard dat bestaat uit 3 grafieken. De 3 grafieken staan netjes op een rij (dus niet onder elkaar!). We gebruiken onderstaande data. Steek deze data netjes in een javascript object:

| Month | Online Sales | In-Store Sales | **Total Sales** |
| ----- | ------------------ | -------------------- | --------------------- |
| Jan   | 8,000              | 4,000                | 12,000                |
| Feb   | 9,000              | 6,000                | 15,000                |
| Mar   | 8,500              | 5,000                | 13,500                |
| Apr   | 10,000             | 6,000                | 16,000                |
| May   | 11,000             | 7,000                | 18,000                |
| Jun   | 10,500             | 7,000                | 17,500                |
| Jul   | 12,000             | 8,000                | 20,000                |
| Aug   | 13,000             | 9,000                | 22,000                |
| Sep   | 12,500             | 8,500                | 21,000                |
| Oct   | 14,000             | 9,000                | 23,000                |
| Nov   | 15,000             | 10,000               | 25,000                |
| Dec   | 18,000             | 12,000               | 30,000                |

De eerste grafiek die we gaan maken is een `bar` chart van de maandelijke verkoopscijfers. Maak een bar chart die de totale verkoop laat zien per maand. Je gebruikt dus 'Month' en 'Total Sales'. De kleuren zijn vrij te kiezen.

Maak nu een `line` chart die de trendlijn aangeeft van onze sales. Gebruik voor deze grafiek 2 datasets: de Online Sales en de In-Store sales. Gebruik dezelfde data als voor je bar chart. Je x-as zijn de maanden, net zoals bij de bar chart.

En ten slotte een `pie` chart die de volgende data gebruikt:

| Category      | Sales |
| ------------- | ----- |
| Electronics   | 85000 |
| Clothing      | 65000 |
| Home & Garden | 50000 |
| Sports        | 30000 |
| Beauty        | 20000 |

Indienen doe je via blackboard. Veel succes!

# Oplossingen

### Oplossing 1:

    const ctx = document.getElementById('myChart');

    const data = [
        {color: 'Red', value: 12},
        {color: 'Blue', value: 19},
        {color: 'Yellow', value: 3},
        {color: 'Green', value: 5},
        {color: 'Purple', value: 2},
        {color: 'Orange', value: 3},
    ];

    new Chart(ctx, {
    type: 'bar',
    data: {
        labels: data.map(r => r.color), 
        datasets:[
        {
            label: '# of Votes',
            data: data.map(r => r.value),
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
            borderColor: 'rgba(75, 192, 192, 1)',
            borderWidth: 1
        }
    ]
    },
    options: { scales: { y: { beginAtZero: true } } }
    });

### Oplossing 2:

    import Chart from 'chart.js/auto';

    const ctx = document.getElementById('myChart');

    const data = [
        {color: 'Red', value: 12, likes: 5},
        {color: 'Blue', value: 19, likes: 15},
        {color: 'Yellow', value: 3, likes: 8},
        {color: 'Green', value: 5, likes: 2},
        {color: 'Purple', value: 2, likes: 7},
        {color: 'Orange', value: 3, likes: 9},
    ];


    new Chart(ctx, {
    type: 'bar',
    data: {
        labels: data.map(r => r.color), 
        datasets:[
        {
            label: 'Values',
            data: data.map(r => r.value),
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
            borderColor: 'rgba(75, 192, 192, 1)',
            borderWidth: 1
        },
        {
            label: '# of Likes',
            data: data.map(r => r.value),
            backgroundColor: 'rgba(255, 159, 64, 0.2)',
            borderColor: 'rgba(255, 159, 64, 1)',
            borderWidth: 1
        }
    ]
    },
    options: { scales: { y: { beginAtZero: true } } }
    });

### Oplossing 3:

    import Chart from 'chart.js/auto';

    const ctx = document.getElementById('myChart');

    const data = [
        {label: 'Apples', sales: 10, color: 'rgba(255, 99, 132)'},
        {label: 'Bananas', sales: 14, color: 'rgba(255, 206, 86)'},
        {label: 'Cherries', sales: 7, color: 'rgba(54, 162, 235)'},
    ];Giv

    new Chart(ctx, {
    type: 'pie',
    data: {
        labels: data.map(r => r.label), 
        datasets:[
        {
            label: 'Value',
            data: data.map(r => r.sales),
            backgroundColor: data.map(r => r.color),
            borderWidth: 1
        }
    ]
    },
    options: { scales: { y: { beginAtZero: true } } }
    });

### Oplossing 4:

    label: function(context) {
        let total = 0;
        for (let i = 0; i < context.dataset.data.length; i++)
        {
        total += context.dataset.data[i];
        }
        const part = context.parsed / total;
        const percentage = Math.round(part * 100);
        return percentage + '%';
    }

### Oplossing 5:

    label: function(context) {
        let total = context.dataset.data.reduce((a,b) => a+b, 0);
        const percentage = Math.round(context.parsed/total * 100);
        return percentage + '%';
    }

