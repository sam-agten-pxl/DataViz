# D3 Joins

D3 is een interessant framework voor data visualisatie omdat het ons toestaat om data te koppelen aan de attributen van visuele elementen. We hebben in het vorige lab gezien hoe we met behulp van selecties enkele html elementen kunnen selecteren om daar dan attributen van aan te passen. In dit lab gaan we die veranderingen koppelen aan data.

## Data, zegt u?

Ja. Data. Zoals jullie weten kan data in allerlei formaten voorkomen. Voor dit vak gebruiken we bijvoorbeeld vooral het .csv formaat om data voor te stellen. We willen ons in dit lab echter toespitsen op het gebruik van D3 en daarom gaan we nog niet meteen werken met een .csv bestand. We gaan daarom vandaag aan de slag met zelfverzonnen, statische data. Hoe kunnen we die voorstellen in javascript? Het meest simpele voorbeeld is een rij van getallen. Dat kan in javascript als volgt:

    data = [10, 20, 30, 40, 50]

Hier declareren we variabele `data` en we kennen daar een rij aan toe van getallen: 10, 20, 30, 40 en 50. Dat is natuurlijk al behoorlijk hip, maar typisch werken we liever met een rij van objecten. Een rij van objecten ziet er bijvoorbeeld als volgt uit:

    data = [
        {
            category: 'Cat',
            value: 10
        },
        {
            category: 'Dog',
            value: 15
        },
        {
            category: 'Pig',
            value: 35
        },
        {
            category: 'Cow',
            value: 25
        }
    ]

Dit lijkt er al meer op. Nu hebben we 4 datapunten die elk 2 waardes bevatten: een categorie en een getal. Elk object in deze rij kan je dus bezien als de rij uit een excel tabel of een .csv bestand. Voor dit lab gebruiken we telkens statische data, daarmee bedoelen we dat de data niet dynamisch uitgelezen wordt, maar wel "hardcoded" in ons programma zit. De data verandert niet. Statisch dus.

## D3 Joins

We starten weer met een eenvoudige Vue app. Amputeer de HelloWorld component en start van eenvoudige html in App.vue:

    <template>
    <div id="app">
        <svg width=800 height=300>
            <circle cx=50 cy=50 r=30 />
        </svg>
    </div>
    </template>

<svg width=800 height=120>
    <circle cx=50 cy=50 r=30 />
</svg>

Vergeet ook niet dat je d3 moet installeren:

    npm install d3

Zet een eenvoudige constructie op zodat je d3 code kan uitvoeren op de `mounted()` functie. Bijvoorbeeld:

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    mounted() {
        this.render()
    },
    methods: {
        render: function() {
        
            //d3 code komt hier

        }
    }
    }
    </script>

(Bovenstaande code zal je niet kunnen uitvoeren omdat we tegen Vue zeggen dat we de d3 component gaan gebruiker (import * as d3...) maar dat doen we niet. Vue noemt ons dus een leugenaar en weigert uit te voeren). Daarom voegen we eerst nog wat js code toe in de render functie:
    
    let data = [40, 10, 20, 60, 30]

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')

Start je project en je gaat zien dat er ...helemaal niets gebeurt. Of toch? Tijd om dit eens wat dieper te bekijken.

De eerste regel maakt een rij van statische data aan en steekt die in een variabele genaamd `data`.
Dan selecteren we het eerste `svg` element (`d3.select('svg')`). Binnen dat element selecteren we alle cirkel elementen (`selectAll('circle')`), dat is er maar eentje. Niets nieuws onder de zon hier. Het is de volgende regel die bijzonder is: `data(data)`. Deze regel kan een beetje verwarrend zijn omdat de `data` tussen de haakjes verwijst naar de variabele data (je weet wel, die variabele met de rij van getallen). We geven die rij aan d3 en zeggen dat we die data gaan binden aan de cirkel elementen (`join('circle')`). D3 doet dus wel degelijk iets. We starten met 1 cirkel, maar als je je project opstart en dan kijkt met de developer console zal je zien dat de html van de webpagina verandert is. Er zijn nu 5 `<circle/>` elementen! Waarom 5? Een voor elk datapunt dat we gegeven hebben aan D3, dus voor elk getal 1! Waanzin!

Dit noemen we een <b>join</b>. We zeggen tegen d3 dat we een cirkel element nodig hebben voor elk punt. D3 ziet dan vanuit onze huidige selectie dat er niet voldoende cirkels zijn en voegt cirkels toe om voor elk datapunt een cirkel te hebben. Wat doet d3 als er te veel cirkels zijn? Start bijvoorbeeld eens met 7 cirkels. Snoeit d3 er dan 2 weg? Of blijven de cirkels intact? Probeer het zelf eens uit.

De selectie die we doen in het begin (svg->circle) is belangrijk want dat is de container waarbinnen D3 aanpassingen mag maken. Als onze html er bijvoorbeeld zo uitzag:

    <div>
        <svg width=800 height=120>
            <circle cx=50 cy=50 r=30 />
        </svg>
        <svg width=800 height=120>
            <circle cx=50 cy=50 r=30 />
        </svg>
    </div>

Dan zou D3 enkel aanpassingen maken aan de eerste svg, niet de tweede.

## Databinding

Die joins zijn allemaal wel tof, maar in ons vorig voorbeeld zagen we helemaal niets. We hebben wel dynamisch cirkels aangemaakt maar dat waren allemaal lege `<circle/>` elementen. Die cirkels hebben geen straal of x/y coordinaten en kunnen we dus niet zien.

We zouden de attributen van onze cirkels kunnen aanpassen op de manier die we gezien hebben door onze D3 code uit te breiden:

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', 50)
        .attr('cy', 50)
        .attr('r', 30)
        .style('fill', 'tomato')

Nu krijgen we exact 1 cirkel te zien omdat al onze cirkels dezelfde coordinaten gekregen hebben: cx=50 en cy=50. Daar schieten we niet veel mee op, we willen graag andere waarden hebben voor elke cirkels. We kunnen dat doen door bijvoorbeeld functies te gebruiken:

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i){
          return 50 + i * 100
        })
        .attr('cy', 50)
        .attr('r', 30)
        .style('fill', 'tomato')

We gebruiken nu een functie voor de x-coordinaat en de `i` die de functie binnenkrijgt is de index van de cirkel: 0 voor de eerste cirkel, 1 voor de tweede, etc. Als we deze code uitvoeren zien we onze cirkels netjes op een rij.

<svg width=800 height=120>
    <circle cx=50 cy=50 r=30 fill='tomato' />
    <circle cx=150 cy=50 r=30 fill='tomato' />
    <circle cx=250 cy=50 r=30 fill='tomato' />
    <circle cx=350 cy=50 r=30 fill='tomato' />
    <circle cx=450 cy=50 r=30 fill='tomato' />
</svg>

Maar wat is nu de `d` die die functie binnenkrijgt? Dat is het datapunt dat D3 aan de cirkel gekoppeld heeft. In ons geval is dat dus 40 voor de eerste cirkel, 10 voor de tweede, 20 voor de derde, etc. Laat ons dat eens uittesten en de straal van de cirkels aanpassen:

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i){
          return 50 + i * 100
        })
        .attr('cy', 75)
        .attr('r', function(d, i){
          return d
        })
        .style('fill', 'tomato')
    
De kans bestaat dat als je dit probeert uit te voeren dat je een foutmelding krijgt:

    31:32  error  'i' is defined but never used  no-unused-vars

    ✖ 1 problem (1 error, 0 warnings)

Vue probeert ons te vertellen dat we de variabele i in `.attr('r', function(d, i){` wel declareren, maar niet gebruiken. Vue is dus weer aan het zagen dat we te veel aan het doen zijn... Laat ons Vue tevreden houden en de regel aanpassen zodat we `i` niet meegeven aan de functie:

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i){
          return 50 + i * 100
        })
        .attr('cy', 75)
        .attr('r', function(d){
          return d
        })
        .style('fill', 'tomato')

Hier kunnen we veel mee! Laat ons bijvoorbeeld de cirkels oranje maken als het datapunt boven de 30 is:

    d3.select('svg')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i){
          return 50 + i * 100
        })
        .attr('cy', 75)
        .attr('r', function(d){
          return d
        })
        .style('fill', function(d){
          if(d > 30)
          {
            return 'orange'
          }
          else
          {
            return 'tomato'
          }
        })

<svg width=800 height=150>
    <circle cx=50 cy=75 r=40 fill='orange' />
    <circle cx=150 cy=75 r=10 fill='tomato' />
    <circle cx=250 cy=75 r=20 fill='tomato' />
    <circle cx=350 cy=75 r=60 fill='orange' />
    <circle cx=450 cy=75 r=30 fill='tomato' />
</svg>

Proficiat! Je hebt zonet je eerste datavisualisatie gemaakt met D3!

## Complexere Data

De data die we nu gebruikt hebben is heel simpel:

    data = [40, 10, 20, 60, 30]

Zoals we besproken hebben, werken we liever met objecten. Zo bijvoorbeeld:

    data = [
        {
            category: 'Cat',
            value: 10
        },
        {
            category: 'Dog',
            value: 15
        },
        {
            category: 'Pig',
            value: 35
        },
        {
            category: 'Cow',
            value: 25
        }
    ]

Laat ons eens nu eens samengooien wat we geleerd hebben. We gaan een Vue component `BarChart` schrijven en we gaan een bar chart maken die de bovenstaande data visualiseert. Pak een koffietje, energiedrankje, waterke of stukje fruit, en start dan een nieuw Vue project.

### Bar chart zonder Vue component

We openen met wat simpele html voor onze `App.vue`:

    <template>
    <div id="app">
        <svg width=800 height=300>
            <rect x=50 width=30 height=100 />
        </svg>
    </div>
    </template>

Dit tekent een saaie balk. We willen nu een balk per datapunt. We gebruiken opnieuw de `mounted()` functie om onze D3 uit te voeren. Vergeet ook niet om D3 te installeren, te importeren en onze statische data te declareren:

    let data = [
        { category: 'Cat', value: 10 },
        { category: 'Dog', value: 15 },
        { category: 'Pig', value: 35 },
        { category: 'Cow', value: 25 }
      ]

      d3.select('svg')
        .selectAll('rect')
        .data(data)
        .join('rect')
        .attr('width', 30)
        .attr('height', 100)
        .attr('x', function(d, i){
          return 50 + i * 100
        })

Nu hebben we 5 balken! Laat ons eens kijken of we de hoogte van de balk kunnen aanpassen op basis van ons datapunt. Elk datapunt `d` is nu geen getal maar een object. Bijvoorbeeld: `{ category: 'Cat', value: 10 }` voor de eerste rechthoek. Willen we de `value` van dit object dat schrijven we `d.value`:

    d3.select('svg')
        .selectAll('rect')
        .data(data)
        .join('rect')
        .attr('width', 30)
        .attr('height', function(d){
          return d.value * 3
        })
        .attr('x', function(d, i){
          return 50 + i * 100
        })

(De `*3` doen we zodat de balken niet te klein zijn).

Het nadeel is dat de balken van boven naar beneden getekend worden. We kunnen dat oplossen door een beetje wiskunde. Ons canvas is 300 hoog. De eerste balk is 30 pixels hoog (10 x 3). De y positie van de balk moet dus 270 zijn (300 - 30). We passen onze code aan:

    d3.select('svg')
        .selectAll('rect')
        .data(data)
        .join('rect')
        .attr('width', 30)
        .attr('height', function(d){
          return d.value * 3
        })
        .attr('x', function(d, i){
          return 50 + i * 100
        })
        .attr('y', function(d) {
          return 300 - (d.value * 3)
        })

Die aanpassing is wel wat vervelend want als we de grootte van ons canvas aanpassen, moeten we dus ook deze code aanpassen. 

### Bar chart met gebruik van een Vue component

We hebben voor deze bar chart een simpel `rect` element gebruikt, maar typisch willen we wat complexer gedrag. Zo willen we bijvoorbeeld graag labels bij onze rechthoeken. We kunnen onze bovenstaande code uitbreiden maar in dit plaats gaan we ons harde werk verhuizen naar een eigen component: `BarChart`. 
Begin met het toevoegen van een nieuw bestand: `BarChart.vue` in de components map (componenten moet strikt genomen niet in die map staan maar dat is netjes.). De html voor deze component begint als een eenvoudige rechthoek:

    <template>
        <g>
            <rect x=50 width=30 height=100 />
        </g>
    </template>

We zorgen er ook voor dat onze nieuwe component openbaar wordt gesteld:

    <script>
    export default {
        name: 'BarChart',
        props: {
        },
        mounted() {

        }
    }
    </script>

Merk op dat we al een mounted functie hebben voorzien. We gaan namelijk alle complexiteit van in `App.vue` verhuizen naar hier. In de component `App` wordt de data opgehaald en we gaan die dan doorspelen naar onze component `BarChart`. Er gaat nu heel wat verhuizen, maar geen paniek, dat is ook het enige dat we aan het doen zijn: onze code een betere plaats geven. De logica achter de code verandert niet. 

Breidt de `BarChart` component uit:

    <template>
        <g>
            <rect x=50 width=30 height=100 />
        </g>
    </template>

    <script>
    import * as d3 from "d3";

    export default {
        name: 'BarChart',
        props: {
            data: Object
        },
        mounted() {
            this.DrawChart();
        },
        methods: {
            DrawChart: function() {
                d3.select('g')
                .selectAll('rect')
                .data(this.data)
                .join('rect')
                .attr('width', 30)
                .attr('height', function(d){
                return d.value * 3
                })
                .attr('x', function(d, i){
                return 50 + i * 100
                })
                .attr('y', function(d) {
                return 300 - (d.value * 3)
                })
            }
        }
    }
    </script>

Dat is een hele brok, en het is voornamelijk copy paste van wat we al hadden. Er is echter 1 groot verschil: data is hier niet gedefinieerd, dat gaan we in `App.vue` doen en we geven dan die brok data door aan deze bar chart. Daarom geven we `BarChart` een property:

    props: {
            data: Object
        },

En die property gebruiken we in onze D3 code:

    d3.select('svg')
        .selectAll('rect')
        .data(this.data)
        .join('rect')
        ...

Merk op dat nu het keyword `this` nodig is om te specifieren waar de data te vinden is. `App.vue` moeten we ook aanpassen:

    <template>
    <div id="app">
        <svg width=800 height=300>
            <BarChart v-bind:data="this.data" />
        </svg>
    </div>
    </template>

    <script>
    import BarChart from './components/BarChart.vue'

    export default {
    name: 'App',
    components: {
        BarChart
    },
    mounted() {
        this.render()
    },
    data: function () {
        return {
        data : [
            { category: 'Cat', value: 10 },
            { category: 'Dog', value: 15 },
            { category: 'Pig', value: 35 },
            { category: 'Cow', value: 25 }
        ]
        } 
    }
    }
    </script>

Het belangrijkste hier is dat we onze nieuwe component gebruiken in de html:

    <svg width=800 height=300>
        <BarChart v-bind:data="this.data" />
    </svg>

Daarvoor voegen we de juiste import toe en we declareren ook het gebruik in de `components` array van onze component. We voorzien die `BarChart` component ook van data door middel van het v-bind directive. Die `this.data` komt uit de data functie van de `App` component. In vue kan elke component een eigen blok van data bevatten. Dat moet een functie zijn die een object teruggeeft:

    data: function () {
        return {
        data : [
            { category: 'Cat', value: 10 },
            { category: 'Dog', value: 15 },
            { category: 'Pig', value: 35 },
            { category: 'Cow', value: 25 }
        ]
        } 
    }

In dit geval geeft de data functie een object terug waarin een eigenschap `data` in zit. Het is die eigenschap die we doorgeven aan onze `BarChart` component.
Laat je niet verwarren door al dat gebruik van het woord data.

We hebben nu een mooiere flow van data. De data wordt berekend (en later ingelezen) in `App.vue`. Die data wordt dan weergegeven door onze homemade `BarChart` component. We zouden nu bijvoorbeeld ook andere data kunnen doorgeven aan die bar chart.

## Uitbreiding van de Bar Chart

We zouden ook graag labeltjes willen bij onze rechthoeken. Daarvoor breiden we onze `BarChart` component uit. We voegen extra D3 code toe aan onze `DrawChart` functie:

    d3.select('g')
        .selectAll('text')
        .data(this.data)
        .join('text')
        .text(function(d){
            return d.category
        })

Deze code wordt dus uitgevoerd nadat onze rechthoeken getekend zijn. Dit doet in feite hetzelfde als bij onze rechthoeken: voor elk datapunt voegen we een svg `text` element toe. We zetten de waarde van die text op de inhoud van `d.category`. Voor de eerste is dat `Cat` voor de tweede `Dog`, etc.
Voer dit uit. Je zal merken dat je geen text ziet op je beeld. Ga neuzen in je html en je zal merken dat de textobjecten er wel degelijk zijn. Helaas zitten ze allemaal op de verkeerde plaats. We moeten namelijk nog een x en y coordinaat toevoegen. De x-coordinaat is simpel, die is dezelfde als de rechthoek. Voor y zullen we voorlopig even gokken:

    d3.select('g')
        .selectAll('text')
        .data(this.data)
        .join('text')
        .attr('x', function(d, i){
            return 50 + i * 100
        })
        .attr('y', function(){
            return 100
        })
        .text(function(d){
            return d.category
        })

Dat ziet er al niet slecht uit:

<svg width="800" height="300">
    <g>
        <rect fill='tomato' x="50" width="30" height="30" y="270"></rect>
        <rect fill='tomato' width="30" height="45" x="150" y="255"></rect>
        <rect fill='tomato' width="30" height="105" x="250" y="195"></rect>
        <rect fill='tomato' width="30" height="75" x="350" y="225"></rect>
        <text fill='tomato' fill='tomato' x="50" y="100">Cat</text>
        <text fill='tomato' x="150" y="100">Dog</text>
        <text fill='tomato' x="250" y="100">Pig</text>
        <text fill='tomato' x="350" y="100">Cow</text>
    </g>
</svg>

(De kleuren zijn hier op rood gezet voor leesbaarheid).

Die y kan echter beter. Probeer eerst zelf eens daar wat mee te prullen. Hier is mijn oplossing:

    d3.select('g')
        .selectAll('text')
        .data(this.data)
        .join('text')
        .attr('x', function(d, i){
            return 50 + i * 100
        })
        .attr('y', function(d){
            return 300 - (d.value * 3) - 10
        })
        .text(function(d){
            return d.category.toUpperCase()
        })


