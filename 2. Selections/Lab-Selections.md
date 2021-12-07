# D3 - Selections

D3.js is een javascript library die ons kan helpen om gemakkelijker data te koppelen aan visuele elementen. Er wordt wel eens gezegd dat d3 een grafische library is om grafieken te tekenen maar je zal snel merken dat d3 veel meer te bieden heeft dan dat. Sterker nog, als je op zoek bent naar een library om grafieken te tekenen raad ik aan dat je chart.js gebruikt. Een van de krachtigste tools die d3 ons aanbiedt is selecties: het selecteren van DOM-elementen uit onze html om dan eigenschappen van dat element te koppelen aan data. 

## D3 installeren

We starten met een nieuw vue project. 

    vue create d3-selections

Vergeet niet te duiken in die nieuwe projectfolder:

    cd d3-selections

Vervolgens installeren we het d3 pakket voor ons project met behulp van node package manager:

    npm install d3

Vergeet ook niet even te controleren of je VUe project start:

    npm run serve

## Introductie

Laat ons nog eens kijken naar onze `App.vue` code:

    <script>
    import HelloWorld from './components/HelloWorld.vue'

    export default {
    name: 'App',
    components: {
        HelloWorld
    }
    }
    </script>

Tussen de `<script>` tags staat de javascript code voor onze App component. We zien dat die component bestaat uit een variabele `name` en een lijst van components: `components` (denk eraan dat een lijst gebruik maakt van de `{}` haakjes).

We schonen eerst onze component even op en verwijderen het gebruikt van de HelloWorld component (verwijder ook gerust `HelloWorld.vue`):

    <script>
    export default {
    name: 'App',
    components: {
        
    }
    }
    </script>

Vergeet ook niet de HelloWorld component uit de html van onze App component te halen.

We kunnen bij Vue nog elementen hieraan toevoegen die automatisch uitgevoerd worden door Vue. Zo is er de methode `mounted()` die we kunnen toevoegen. Elke code die daar toegevoegd wordt, wordt uitgevoerd bij het inladen van de applicatie. Probeer bijvoorbeeld het volgende maar eens:

    <script>
    export default {
    name: 'App',
    components: {
        
    },
    mounted() {
        console.log("Hello")
    }
    }
    </script>

Start nu je applicatie en open je developer console in je browser (F12 in google chrome). Je zal zien dat de applicate `Hello` heeft afgeprint in de console.
Let er wel op dat de functie `mounted()` enkel wordt afgeroepen bij het begin van de applicatie, dat is dus niet de geschikte plaats voor het reageren op interacties met de gebruiker. Het is echter wel een goede plaats om eens te experimenteren met D3 code.

We kunnen ook nog extra methodes of functies declareren op onze component. Dat kan bijvoorbeeld als volgt:

    <script>
    export default {
    name: 'App',
    components: {
        
    },
    mounted() {
        this.printHello()
    },
    methods : {
        printHello()
        {
        console.log("Hello")
        }
    }
    }
    </script>

`methods` is een lijst van methodes die we kunnen hergebruiken. Dat is een betere plaats om onze print te zetten zodat we die methode later zouden kunnen hergebruiken.

Voordat we D3 kunnen gebruiken willen we iets waar we D3 op kunnen loslaten. We gebruiken hiervoor enkele SVG elementen (cirkels). Verander de html van de App component:

    <template>
    <div id="app">
        <svg width=800 height=250>
        <g>
            <circle cx=50 cy=50 r=20 />
            <circle cx=150 cy=50 r=20 />
            <circle cx=250 cy=50 r=20 />
        </g>
        </svg>
    </div>
    </template>

We krijgen zo drie cirkels te zien:

<svg width=800 height=100>
<g>
    <circle cx=50 cy=25 r=20 />
    <circle cx=150 cy=25 r=20 />
    <circle cx=250 cy=25 r=20 />
</g>
</svg>

## Eerste Selectie

We gaan nu D3 gebruiken om deze cirkels een andere kleur te geven. Daarvoor gebruiken we D3 selecties: een manier om specifiek DOM elementen te selecteren. Je kan het vergelijken met CSS, waar we ook DOM elementen selecteren met een `selector` om die daarna een stijl toe te kennen.

Allereerst moeten we D3 importeren in onze component:

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    ...


Ik voeg de volgende methode toe aan de methods van App-component (we gaan nu niet meer de hele component bekijken):

    transformCircles()
    {
      d3.selectAll('circle').style('fill', 'orange')
    }

We voeren deze methode uit in de `mounted()` functie:

    mounted() {
        this.transformCircles()
    }

Start de applicatie. De cirkels zullen nu oranje zijn:
<svg width=800 height=75>
<g>
    <circle cx=50 cy=35 r=20 fill='orange' />
    <circle cx=150 cy=35 r=20 fill='orange' />
    <circle cx=250 cy=35 r=20 fill='orange' />
</g>
</svg>

Dit is dus de eigenlijke d3 code die uitgevoerd is:

    d3.selectAll('circle').style('fill', 'orange')

D3 gebruikt een concept genaam <b>method chaining</b>. Ter verduidelijking schrijven we de code daarom ook liever zo:


    d3.selectAll('circle')
        .style('fill', 'orange')

Er word eerst de methode `selectAll` gebruikt en op het resultaat van die berekening wordt er `style` gebruikt. Je werkt dus steeds verder op het resultaat van de vorige methode: <b>Method Chaining</b>, oftewel, een aaneenschakeling van methodes.

We vertrekken vanuit alle html elementen (in ons geval: div, svg, g, circle, etc.). De eerste methode `selectAll` is een D3 selectie. We maken duidelijk dat we alle elementen willen selecteren die `circle` heten. In ons geval zijn dat de 3 cirkels. De drie cirkels zijn dus het resultaat van de eerste methode.

De volgende methode, `style`, zal het style attribuut van de geselecteerde elementen veranderen. Hier veranderen we dus de `fill` van onze 3 cirkels naar oranje.

We kunnen hier met method chaining nog op verder borduren. Bijvoorbeeld:

    d3.selectAll('circle')
        .style('fill', 'orange')
        .attr('r', 10)

Dit zal de cirkels oranje maken en dan het `r` attribuut van onze oranje cirkels (= de straal) op 10 pixels zetten. Met de `attr` methode kunnen we dus de eigenschappen van een element wijzigen. In de plaats van een vaste waarde, 10, kunnen we ook een berekende waarde meegeven:

    d3.selectAll('circle')
        .style('fill', 'orange')
        .attr('r', function() {
          return 10 + Math.random() * 40;
        })

Nu zullen de cirkels een willekeurige straal krijgen tussen 10 en 50. Denk eraan: deze code wordt uitgevoerd als de applicatie ingeladen wordt. Dus elke keer als je de pagina refreshed zal de straal van de cirkels anders zijn! Spannend, toch?

Met D3 hebben we dus een selectie kunnen uitvoeren van enkele DOM elemenen (circle) en hebben op die elementen enkele attributen kunnen aanpassen (straal, kleur).

## Selecties in depth

D3 heeft twee functies om selecties te maken: `d3.select` en `d3.selectAll`. Met `select` selecteer je enkel het eerste element, met `selectAll` selecteer je alle overeenkomstige elementen. Het argument dat je meegeeft is van het type `string` en mag eender welke CSS `selector` zijn (bv: #my-chart, g:first-child, div.svg, etc.). 

Buiten constante waardes mee te geven kunnen we dus ook functies meegeven. Hoewel we dat in bovenstaand voorbeeld niet gedaan hebben, hebben de functies die de waarde berekening typisch twee parameters: `d` en `i`. Bovenstaand voorbeeld hadden we dus beter herschreven als:

    d3.selectAll('circle')
        .style('fill', 'orange')
        .attr('r', function(d, i) {
          return 10 + Math.random() * 40;
        })

Wat de `d` doet komen we nog op terug. De `i` is de index van het geselecteerde element. Aangezien we 3 cirkels hebben zal `i` voor de eerste cirkel 0 zijn, voor de tweede cirkel 1 en voor de derde cirkel 2. We kunnen die `i` bijvoorbeeld gebruiken om de cirkels trapsgewijs naar onder te laten gaan:

    d3.selectAll('circle')
        .style('fill', 'orange')
        .attr('cy', function(d,i){
          return 50 + i * 20;
        })

## Interactie met D3

De code die opgeroepen wordt binnen `mounted()` wordt maar 1x afgeroepen. We willen echter graag interactie kunnen opbouwen met D3 zodat we kunnen reageren op allerlei events zoals de gebruiker die op een knop klikt. Daarvoor kunnen we gebruik maken van D3 event handlers.

We kunnen luisteren naar events binnen D3 met de `on` methode. Aan die methode moeten we 2 argumenten meegeven: 
- Naar welk event (bv muisklik) we willen luisteren. Dit is een string.
- Welke code uitgevoerd moet worden als het event voorkomt. Dit is een functie.

Hier zijn enkele belangrijke events:
- click: gebeurt als het element aangeklikt wordt
- mouseenter: gebeurt als de muis over het element beweegt
- mouseleave: gebeurt als de muis wegbeweegt van het element

De code voor met een event te werken kan er wat eng uizien in het begin. Hier is een heel simpel voorbeeld:

    d3.selectAll('circle')
        .on('click', function(){
          d3.select(this)
            .style('fill', 'orange')
        })


We selecteren eerst all cirkels. Voor elke cirkel koppelen we een functie aan het `click` event:

    function(){
          d3.select(this)
            .style('fill', 'orange')
        }

Die functie zal dus uitgevoerd worden als op het element wordt geklikt. De code die we uitvoeren is: selecteer het huidige element (`select(this)`) en zet de `fill` op oranje.

Een wat uitgebreider voorbeeld ziet er zo uit:

    d3.selectAll('circle')
        .on('mouseenter', function(){
          d3.select(this)
            .style('fill', 'orange')
        })
        .on('mouseleave', function(){
          d3.select(this)
            .style('fill', 'gray')
        })

Nu worden de cirkels oranje als de muis over de cirkel komt en wordt de cirkel grijs als de muis wegbeweegt.

## Elementen dynamisch toevoegen

Je kan nog heel veel met D3 selecties, maar we sluiten dit lab af met nog 1 handige feature: het dynamisch toevoegen van elementen.
We vertrekken vanuit eenvoudige html:

    <g class="item" transform="translate(40, 50)">
        <circle r="40" fill='orange' />
    </g>
    <g class="item" transform="translate(160, 50)">
        <circle r="40" fill='orange' />
    </g>
    <g class="item" transform="translate(280, 50)">
        <circle r="40" fill='orange' />
    </g>

We kunnen DOM elementen dynamisch toevoegen aan onze selectie met de `append` methode. We laten bijvoorbeeld de volgende code los op deze html:

    d3.selectAll('g.item')
        .append('text')
        .text('A')

We selecteren hier alle groepselementen (`g`) met de css klass `item` uit onze html. Vervolgens voegen we aan elk element nu een stuk tekst bij met de waarde 'A'. De resulterende html ziet er dan zo uit:

    <g class="item" transform="translate(40, 50)">
        <circle r="40" fill='orange' />
        <text>A</text>
    </g>
    <g class="item" transform="translate(160, 50)">
        <circle r="40" fill='orange' />
        <text>A</text>
    </g>
    <g class="item" transform="translate(280, 50)">
        <circle r="40" fill='orange' />
        <text>A</text>
    </g>

Oftewel visueel:

<svg>
    <g class="item" transform="translate(40, 50)">
        <circle r="20" fill='orange' />
        <text>A</text>
    </g>
    <g class="item" transform="translate(160, 50)">
        <circle r="20" fill='orange' />
        <text>A</text>
    </g>
    <g class="item" transform="translate(280, 50)">
        <circle r="20" fill='orange' />
        <text>A</text>
    </g>
</svg>
