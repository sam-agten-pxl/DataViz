# Lab 03
In dit lab gaan we nog wat dieper in op enkele aspecten van Chart.js. Merk ook op dat we in dit lab niet altijd de volledige code zullen tonen om het kort te houden. We kunnen bijvoorbeeld enkel het data object van een grafiek tonen, het is aan jou om te weten waar deze code thuishoort!

## Interactiviteit - basis
Zodra onze grafiek getekend is kunnen we die ook nog wijzigen. Ter opfrissing, hier was onze Pie Chart:

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

En hier is het data object dat we gebruikt hebben:

    const data = [
        {label: 'Apples', sales: 10, color: 'rgba(255, 99, 132)'},
        {label: 'Bananas', sales: 14, color: 'rgba(255, 206, 86)'},
        {label: 'Cherries', sales: 7, color: 'rgba(54, 162, 235)'},
    ];

We kunnen onze data wijzigen en onze grafiek laten aanpassen. Daarvoor moeten we uiteraard wel een referentie bijhouden naar onze grafiek zodat we er opnieuw naar kunnen verwijzen. Dus bij het aanmaken van onze grafiek:

    const myGraph = new Chart(...)

We wijzigen na het maken van onze grafiek onze data:

    data.push( { label: "Blueberries", sales: 15, color: 'rgba(86, 255, 132'});

Daarna gebruiken de `update` functie van Chart.js om onze grafiek aan te passen maar eerst moeten we natuurlijk deze aangepaste data opnieuw voederen aan onze grafiek:

    myGraph.data.labels = data.map(r => r.label);
    myGraph.data.datasets[0].data = data.map(r => r.sales);
    myGraph.data.datasets[0].backgroundColor = data.map(r => r.color);
    myGraph.update();

We kunnen ook een bepaalde set uitzetten, hier zetten we onze dataset uit:

    myGraph.setDatasetVisibility(0, false);
    myGraph.update();

    
✅ Zoek zelf eens uit hoe je een specifiek datapunt kan uitzetten vanuit code. Zet appels uit. De oplossing vind je onderaan als oplossing 1.

 We kunnen ook onze options aanpassen:

    myGraph.options.plugins.legend.position = 'right';
    myGraph.update();

## Interactiviteit - knop
We voegen een knop toe aan onze HTML:

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

Zoek zelf eens uit hoe je een specifiek datapunt kan uitzetten vanuit code. Zet appels uit. De oplossing vind je onderaan als oplossing 1.
We gaan een lijndiagramme maken. Gebruik opnieuw de volgende data

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

✅ Voeg een knop toe. Als je op de knop klikt krijg je voor elke maand een willekeurig cijfer tussen 10 000 en 30 000. Je kan daarvoor `Math.random()` gebruiken (Oplossing 3).
✅ Pas de oefening aan, zorg nu dat de data vanzelf om de 3 seconden wordt geupdatet. Gebruik je beste google-fu of prompt engineering! Maar zorg ervoor dat je oplossing snapt! (Oplossing 4)

# Oplossingen

### Oplossing 1
Wil je gewoon een datapunt uitzetten, dat kan zo:

    myGraph.toggleDataVisibility(0, false);
    myGraph.update();

Hier zetten we de appels uit.

### Oplossing 2

    function refreshData()
    {
        data.push( { label: "Blueberries", sales: 15, color: 'rgba(86, 255, 132'});
        myGraph.data.labels = data.map(r => r.label);
        myGraph.data.datasets[0].data = data.map(r => r.sales);
        myGraph.data.datasets[0].backgroundColor = data.map(r => r.color);
        myGraph.update();
        console.log("Refresh data");
    }

### Oplossing 3


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

### Oplossing 4

Hiervoor kan je simpelweg `setInterval` gebruiken. Die functie verwacht 2 soorten input: de functie die periodiek opgeroepen moet worden, en de tijd die tussen elke oproep moet zitten, in milliseconden. 3 seconden is 3000ms dus je kan deze regel toevoeg aan je JS:

    window.setInterval(refreshData, 3000);