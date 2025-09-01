# Lab 03
In dit lab gaan we nog wat dieper in op enkele aspecten van Chart.js. Merk ook op dat we in dit lab niet altijd de volledige code zullen tonen om het kort te houden. We kunnen bijvoorbeeld enkel het data object van een grafiek tonen, het is aan jou om te weten waar deze code thuishoort!

## Andere Datastructuren
Tot nu toe hebben we een lijngrafiek als volgt getekend:

    const cfg = {
    type: 'line',
    data: {
        labels: ['A', 'B', 'C'],
        datasets: [{
        data: [10, 15, 20],
        }]
    }
    }

We kunnen de labels ook weglaten en rechtstreeks meegeven met de data. In dat geval is elk punt ook een array. Dus in dit geval niet 10, 15 en 20, maar wel [A, 10], [B, 15] en [C, 20]:

    const cfg = {
    type: 'line',
    data: {
        datasets: [{
        data: [["A", 10], ["B", 15], ["C", 20]],
        }]
    }
    }

Nog beter: je kan er ook javascript object van maken. Dus in de plaats van [A, 10] verduidelijken we als tot {x:A, y:10}. Dat geeft dan:

    const cfg = {
    type: 'line',
    data: {
        datasets: [{
        data: [{x:"A", y:10}, {x:"B", y:15}, {x:"C", y:20}],
        }]
    }
    }

Veel vaker werken we met object die we inlezen van bestanden (zie Lab 04), en krijgen we niet object die netjes zeggen wat er op de x-as moet en op de y-as, maar hebben die vaker beschrijvende eigenschapsnamen. Bijvoorbeeld:

    {
        day: 'Mon', 
        steps: 5423,
    }

Zoals uit deze stappentellerdata, bijvoorbeeld:

    const stepsData = [
        { day: "Mon", steps: 5423 },
        { day: "Tue", steps: 6345 },
        { day: "Wed", steps: 7120 },
        { day: "Thu", steps: 6890 },
        { day: "Fri", steps: 8002 },
        { day: "Sat", steps: 10543 },
        { day: "Sun", steps: 9340 }
    ];

We kunnen deze data rechtstreeks meegeven aan onze grafiek, zonder dat we opnieuw de `map` functie moeten gebruiken:

    const cfg = {
        type: 'line',
        data: {
            datasets: [{
            data: stepsData,
            }]
        }
    }

Dit werkt helaas niet, omdat Chart.js op zoek is naar de eigenschappen `x` en `y`, die hier niet bestaan. We kunnen echter meegeven naar welke eigenschappen er gezocht moet worden met behulp van het `options` object:

    const cfg = {
        type: 'line',
        data: {
            datasets: [{
            data: stepsData,
            }]
        },
        options: {
            parsing: {
                xAxisKey: 'day',
                yAxisKey: 'steps'
            }
        } 
    }

Je kan de parsing methode ook rechtstreeks in het data object zetten:

    const cfg = {
        type: 'line',
        data: {
            datasets: [{
            data: stepsData,
            parsing: {
                xAxisKey: 'day',
                yAxisKey: 'steps'
            }
            }]
        },
    }

Vanaf nu verkiezen we deze werkwijze.

Voor taartdiagrammen moeten we nog steeds een array van labels voorzien en spreken we bij de parsing van een `key` in de plaats van `xAxisKey` en `yAxisKey`:

    const cfg = {
    type: 'pie',
    data: {
        labels: fruit.map(r => r.label),
        datasets: [{
                label: 'Value',
                data: fruit,
                parsing : {
                    key: 'sales',
                },
                backgroundColor: fruit.map(r => r.color),
                borderWidth: 1
            }
        ]
    },
    }

En hier nog eens de data die we in dit voorbeeld gebruikt hebben:

    const fruit = [
        {label: 'Apples', sales: 10, color: 'rgba(255, 99, 132)'},
        {label: 'Bananas', sales: 14, color: 'rgba(255, 206, 86)'},
        {label: 'Cherries', sales: 7, color: 'rgba(54, 162, 235)'},
    ];

## Oefening: Fitnessdata
We gaan onze kennis nog eens oefenen. Gebruik de volgende data:

    const fitnessData = [
        { day: "Mon", steps: 5400, calories: 220 },
        { day: "Tue", steps: 6800, calories: 260 },
        { day: "Wed", steps: 7200, calories: 280 },
        { day: "Thu", steps: 6100, calories: 240 },
        { day: "Fri", steps: 8900, calories: 310 },
        { day: "Sat", steps: 12000, calories: 420 },
        { day: "Sun", steps: 10200, calories: 380 }
    ];

✅ Maak een grafiek die zowel de stappen als de calorieën weergeeft. Voor de stappen gebruik je een `bar` chart en voor de calorieën een `line`. Je kan het type van grafiek mee in de dataset zetten zodat je beide grafieken samen kan tekenen, je tekent dus 2 datasets op 1 grafiek. Gebruik de methode die we zonet gezien hebben, dus vermijdt het gebruik van `map`. Zorg er bovendien voor dat:

- De kleur van de bar chart is rgb(54, 162, 235, 0.6)
- De kleur van de lijn is blauw
- De lijn is tevens een stippellijn.
- Zorg dat de lijndikte 1 is.

Gebruik ook het volgende options object voor je grafiek:

    options: {
        responsive: true,
        scales: {
            y: { beginAtZero: true, title: { display: true, text: "Steps" } },
            y1: {
                beginAtZero: true,
                position: "right",
                grid: { drawOnChartArea: false },
                title: { display: true, text: "Calories" }
            }
        }
    }

En voeg de volgende eigenschap toe aan je lijn-dataset: 

    yAxisID: "y1",

De oplossing vind je als oplossing 1, vanonder aan dit document.

## Interactiviteit - basis
Zodra onze grafiek getekend is kunnen we die ook nog wijzigen. We werken even verder op het taartdiagramme.
We kunnen onze data wijzigen en onze grafiek laten aanpassen. Daarvoor moeten we uiteraard wel een referentie bijhouden naar onze grafiek zodat we er opnieuw naar kunnen verwijzen. Dus bij het aanmaken van onze grafiek:

    const pie = new Chart(ctx, cfg);

We wijzigen na het maken van onze grafiek onze data:

    fruit.push( { label: "Blueberries", sales: 15, color: 'rgba(86, 255, 132'});

Daarna gebruiken de `update` functie van Chart.js om onze grafiek aan te passen maar eerst moeten we natuurlijk deze aangepaste data opnieuw voederen aan onze grafiek:

    pie.data.labels = fruit.map(r => r.label);
    pie.data.datasets[0].backgroundColor = fruit.map(r => r.color);
    pie.update();

We kunnen ook een bepaalde set uitzetten, hier zetten we onze dataset uit:

    pie.setDatasetVisibility(0, false);
    pie.update();

✅ Zoek zelf eens uit hoe je een specifiek datapunt kan uitzetten vanuit code. Zet appels uit. De oplossing vind je onderaan als oplossing 2.

 We kunnen ook onze options aanpassen:

    pie.options.plugins.legend.position = 'right';
    pie.update();

## Interactiviteit - knop
Dit stuk is niet specifiek voor Chart.js, maar opfrissing voor wat we nodig hebben voor de oefening hieronder. We voegen een knop toe aan onze HTML:

    <button id="refreshDataButton">Refresh Data</button>

In onze JS vragen we deze knop op:

    const btn = document.getElementById('refreshDataButton');

We maken een voorlopige functie om onze knop te testen:

    function refreshData()
    {
        console.log("Refresh data");
    }

Nu zorgen we er voor dat onze functie aangeroepen wordt als op de knop geklikt wordt:

    btn.addEventListener("click", refreshData);

TIP: Ik laat de knop nu nog niets doen buiten een boodschap afprinten. Probeer altijd zo weinig mogelijk code toe te voegen voor je test. Als er dan iets mis loopt heb je minder zoekwerk om de fout op te sporen. Dat werkt ook omgekeerd! Als er iets misloopt, begin stelselmatig te verwijderen tot het terug werkt, zo kan je opsporen waar de fout zit.

## Oefening: Willekeurige data
Genoeg taartdiagrammes en tijd om alles nog eens bij elkaar te gooien! Probeer de volgende oefening op te lossen, de oplossing staat vanonder maar probeer niet te kijken. Ik raad je ook aan in dit stadium enkel hulp te gebruiken van AI voor syntax, zeker niet om de oplossing te laten genereren.

✅ Maak een lijn diagramme. Gebruik de volgende data:

| Month | **Sales** |
| ----- | --------------------- |
| Jan   | 12,000                |
| Feb   | 15,000                |
| Mar   | 13,500                |
| Apr   | 16,000                |
| May   | 18,000                |
| Jun   | 17,500                |
| Jul   | 20,000                |
| Aug   | 22,000                |
| Sep   | 21,000                |
| Oct   | 23,000                |
| Nov   | 25,000                |
| Dec   | 30,000                |

✅ Voeg een knop toe. Als je op de knop klikt krijg je voor elke maand een willekeurig cijfer tussen 10 000 en 30 000. Je kan daarvoor `Math.random()` gebruiken (Oplossing 4).
✅ Pas de oefening aan, zorg nu dat de data vanzelf om de 3 seconden wordt geupdatet. Gebruik je beste google-fu of prompt engineering! Maar zorg ervoor dat je oplossing snapt! (Oplossing 5)

# Oplossingen

### Oplossing 1
    const cfg = {
        data: {
            datasets: [{
                type: 'bar',
                label: 'Steps',
                data: fitnessData,
                borderColor: 'rgba(54, 162, 235, 0.6)',
                parsing: 
                {
                    xAxisKey: 'day',
                    yAxisKey: 'steps'
                }
            },
            {
                label: 'Calories',
                type: 'line',
                data: fitnessData,
                borderColor: 'rgb(0, 0 ,255)',
                borderWidth: 1,
                borderDash: [5, 5],
                yAxisID: "y1",
                parsing: 
                {
                    xAxisKey: 'day',
                    yAxisKey: 'calories'
                }
            }
            ]
        },
    
        options: {
            responsive: true,
            scales: {
                y: { beginAtZero: true, title: { display: true, text: "Steps" } },
                y1: {
                    beginAtZero: true,
                    position: "right",
                    grid: { drawOnChartArea: false },
                    title: { display: true, text: "Calories" }
                }
            }
        }
    }
    

### Oplossing 2
Wil je gewoon een datapunt uitzetten, dat kan zo:

    myGraph.toggleDataVisibility(0, false);
    myGraph.update();

Hier zetten we de appels uit.

### Oplossing 3

    function refreshData()
    {
        data.push( { label: "Blueberries", sales: 15, color: 'rgba(86, 255, 132'});
        myGraph.data.labels = data.map(r => r.label);
        myGraph.data.datasets[0].data = data.map(r => r.sales);
        myGraph.data.datasets[0].backgroundColor = data.map(r => r.color);
        myGraph.update();
        console.log("Refresh data");
    }

### Oplossing 4


    import Chart from 'chart.js/auto';

    const ctx = document.getElementById('myChart');
    const btn = document.getElementById('refreshDataButton');
    btn.addEventListener("click", refreshData);

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

    const myGraph = new Chart(ctx, {
    type: 'line',
    data: {
        labels: data.map(r => r.month), 
        datasets:[
            {
                label: 'Sales',
                data: data.map(r => r.sales),
                borderColor: 'rgb(75, 192, 192)',
                borderWidth: 1
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

    function refreshData()
    {
        for(let i = 0; i < data.length; i++)
        {
            data[i].sales = Math.floor(Math.random() * 20000) + 10000;
        }
        myGraph.data.datasets[0].data = data.map(r => r.sales);
        myGraph.update();
    }

### Oplossing 5

Hiervoor kan je simpelweg `setInterval` gebruiken. Die functie verwacht 2 soorten input: de functie die periodiek opgeroepen moet worden, en de tijd die tussen elke oproep moet zitten, in milliseconden. 3 seconden is 3000ms dus je kan deze regel toevoeg aan je JS:

    window.setInterval(refreshData, 3000);