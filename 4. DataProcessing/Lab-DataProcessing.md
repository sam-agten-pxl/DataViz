# D3 Data Processing

In het vorige lab hebben we gewerkt met statische data maar we zouden graag werken met data die we krijgen vanuit een file of zelfs van een API. Gelukkig kan D3 ons daarbij helpen.

## Eerste CSV inlezen

We werken in dit lab pas op het einde visueel, we zijn vooral geïnteresseerd in het verwerken van data. Start daarom een nieuwe Vue app. We voegen wat code toe aan de `mounted()` functie van `App.vue`. Ter herinnering, dat zier er dan zo uit:

    export default {
    name: 'App',
    components: {
        HelloWorld
    },
    mounted() {
        console.log('Hello')
    }
    }

Vergeet ook niet om d3 te installeren

    npm install d3

Vooreerst hebben we wat gemakkelijke data nodig om mee te werken. Bij dit Lab vind je de csv file: `harry_potter.csv`. Dit is een eenvoudige csv file om mee te oefenen. Kopieer dit bestand naar de `public` folder van je project.

Het inlezen van deze data is gelukkig poepsimpel. D3 voorziet een methode `csv` om csv data in te laden. Die methode is echter async. Dat betekent dat het even duurt, op de achtergrond zijn ding doet en geeft dan pas later een resultaat terug. Dat is gedaan omdat data files natuurlijk heel erg groot zijn en we willen niet dat het inlezen van de data de applicate volledig opeist en blokkeert. D3 noemt dit `promises` of beloftes. De code voor het inladen van de csv file ziet er zo uit:

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    async mounted() {
        
        const data = await d3.csv('harry_potter.csv');
        console.log(data);
    }
    }
    </script>

Start je app, open je console, en normaal gezien krijg je dat de data uit de file te zien in je console. Dat is goed nieuws want nu zit die data dus in een simpele array van objecten, zoals we gedaan hebben op het einde van het vorige lab. De code die deze magie veroorzaakt is dit:

    const data = await d3.csv('harry_potter.csv');
    console.log(data);

We gebruiken het await keyword omdat de uitvoer van de `csv` methode async is. Het resultaat vangen we op in de variabele `data`. Ten slotte printen we het resultaat af. Belangrijk: merk op dat er nu een nieuw keyword staat bij onze `mounted()` functie: `await`. Elke keer als je async functions gebruikt in mounted moet je dat aangeven door de methode te markeren als `async`.

## Type conversie

We kunnen aan de slag met de data die we ingelezen hebben van de csv, maar zoals je al gemerkt hebt in de les, is de data die we krijgen typisch niet 'clean'. Dat komt omdat die data vaak door mensen wordt ingegeven en misschien staan de datums bijvoorbeeld niet allemaal in hetzelfde format of is een categorie soms met een hoofdletter geschreven en soms zonder hoofdletter. Daarom proberen we bij het visualiseren van de data altijd de data eerst op te schonen.

Bij dit lab zit ook een andere csv file: `movies.csv`. Elke film in die dataset heeft een categorie, bijvoorbeeld actie, komedie, etc. We willen graag een visualisatie maken van de opbrengsten per categorie. Verdienen actiefilms bijvoorbeeld doorsnee meer geld dan komedies?

We laden eerste de csv file in:

    const data = await d3.csv('movies.csv');

Deze data is verre van perfect. Zo zien we bijvoorbeeld dat sommige budgetten ingegeven zijn als tekst (strings) en niet als getallen. De genres van de films zijn ook een json-object in tekstvorm.

Voordat we kunnen beginnen aan een visualisatie moeten we dus wat types omzetten. In D3 kunnen we aan de csv methode een functie meegeven die voor elke rij opgeroepen wordt voordat die in het eindresultaat komt. Daarmee kunnen we ook filteren op de data die we nodig hebben. Laat ons meteen eens kijken naar hoe dat er uitziet:

    export default {
    name: 'App',
    components: {
    },
    async mounted() {
        
        const data = await d3.csv('movies.csv', this.typeConversion);
        console.log(data);
    },
    methods: {
        typeConversion: function(d)
        {
        return {
            genre: d.genre,
            budget: d.budget,
            revenue: d.revenue
        }
        }
    }
    }

We hebben een methode `typeConversion` toegevoegd aan onze component. Dat is de functie die d3 per rij van de data gaat oproepen. We geven deze functie mee aan de csv oproep.

De functie `typeConversion` krijgt dus een volledige rij `d` binnen en geeft een nieuw object terug. Dit is een veel kleiner object dat maar bestaat uit 3 eigenschappen: genre, budget en revenue. Voor elk van deze eigenschappen kopieren we gewoon de waarde uit de rij `d` die we binnenkrijgen.

Als je dit uitvoert zal je merken dat onze data in de console nu versimpelt is, we hebben enkel de drie waardes per rij. We willen echter ook typeconversie doen op die waardes en die niet zomaar kopieren. Door een `+` te zetten voor onze waardes converteert javascript die bijvoorbeeld naar een getal:

    typeConversion: function(d)
    {
      return {
        genre: d.genre,
        budget: +d.budget,
        revenue: +d.revenue
      }
    }

Nu zullen budget en revenue eerst omgezet worden naar een getal vooraleer we ze gebruiken. Je kan hier heel ver in gaan en de conversie uitgebreid doen. Zo krijg je consistentie in je data en dat is heel belangrijk om daar fatsoenlijke visualisatie op te doen. We hebben het hier gehouden op een simpel voorbeeld maar dit is dus de plaats waar je je data zou opschonen.

## Data filtering

Er zijn sommige films die geen genre hebben of een budget of revenue die 0 zijn. We willen die data niet mee opnemen in onze visualisatie. We gaan deze data dus ook nog filteren. We kunnen dat rechtstreeks doen nadat de data is ingeladen maar we gaan in de plaats een nieuwe methode gebruiken die we toevoegen onder de `methods` van onze component:

    dataLoaded : function(d)
    {
      const filteredMovies = d.filter(r => {
        return r.budget > 0 && r.genre && r.revenue > 0
      });
      console.log(filteredMovies)
    }

De functie pakt de volledige rij van objecten binnen en filtert die met behulp van de ingebouwde `filter` functie van javascript. Enkel objecten die voldoen aan de voorwaarde worden behouden. In dit geval zijn dat enkel objecten waarvan het budget en de revenue groter is dan 0, en waar het genre niet leeg is. We moeten deze functie nog oproepen nadat onze data is geprepareerd:

    const data = await d3.csv('movies.csv', this.typeConversion);
    this.dataLoaded(data);

Als je nu uitvoert zie je dat we overblijven met 3474 films.

## Data transformatie

De data waar we nu mee werken is niet de data die we willen visualiseren. We willen deze data graag in categorieën stoppen per genre, om dan de revenue te vergelijken.
Opnieuw snelt D3 ons te hulp met de wat verwarrende functie: `rollup`. Als je ooit met SQL gewerkt heb en de group by functionaliteit, gaat dit je bekend voorkomen.

de rollup methode ziet er ruwweg zo uit:

    d3.rollup(data, reducer, groupBy)

`data` is simpelweg de gefilterde data die we doorspelen aan D3. `groupBy` is de waarde waarop we willen groeperen. Dat is in ons geval het genre. `reducer` is wat ingewikkelder. Het zijn de waardes waarin we geinteresseerd zijn per groep. In ons geval is dat de gecombineerde revenue van alle films in een groep. Ik denk dat we dit best uitleggen met een voorbeeld.

 We voegen daarom een nog een functie toe aan `methods`:

    prepareBarChart: function(d)
    {
      const dataMap = d3.rollup(
        d,
        r => d3.sum(r, x => x.revenue),
        d => d.genre
      )

      const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
      return dataArray;
    }

de reducer in dit geval is:

    r => d3.sum(r, x => x.revenue)

Laat ons die even ontleden. Dit is een functie die een argument binnenpakt: `r`. De reducer krijg per definitie een groep binnen. Dus r zou een array kunnen zijn van al onze datapunten waarvan het genra animation is of actie of ... Deze functie neemt die `r` en geeft die door aan `d3.sum`. Dit is een functie die een bepaald veld gaat optellen. Die functie krijgt de hele groep binnen en overloopt die per rij, zo een rij noemen we hier even `x`. Van elke rij `x` nemen we de revenue eigenschap en daar maken we de som van.

De laatste 2 regels kunnen hier ook verwarrend zijn:

    const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
      return dataArray;

Het probleem is dat de rollup functie een map teruggeeft. Als je eens wilt weten hoe er dat uitziet, print dan gerust eens `dataMap` af in de console. We werken in D3 echter liever met arrays, deze laatste regels converteren de map terug naar een array object en retourneert dat resultaat.

We moeten deze functie nog oproepen. De volledige code ziet er dan zo uit:

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    async mounted() {
        
        const data = await d3.csv('movies.csv', this.typeConversion);
        const filteredData = this.dataLoaded(data);
        const finishedData = this.prepareBarChart(filteredData);
        console.log(finishedData);
    },
    methods: {
        typeConversion: function(d)
        {
        return {
            genre: d.genre,
            budget: +d.budget,
            revenue: +d.revenue
        }
        },
        dataLoaded : function(d)
        {
        const filteredMovies = d.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
        return filteredMovies;
        },
        prepareBarChart: function(d)
        {
        const dataMap = d3.rollup(
            d,
            r => d3.sum(r, x => x.revenue),
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
        }
    }
    }
    </script>

De data waar we mee werken ziet er nu zo uit:

    0: {genre: 'Animation', revenue: 29900087791}
    1: {genre: 'Adventure', revenue: 66992153755}
    2: {genre: 'Comedy', revenue: 56864628292}
    3: {genre: 'Action', revenue: 79304756422}
    4: {genre: 'History', revenue: 1763533422}
    5: {genre: 'Drama', revenue: 51152213841}
    6: {genre: 'Crime', revenue: 10352578178}
    7: {genre: 'Fantasy', revenue: 15155568824}
    8: {genre: 'Science', revenue: 13004537408}
    9: {genre: 'Horror', revenue: 12121234371}
    10: {genre: 'Romance', revenue: 5843455308}
    11: {genre: 'Mystery', revenue: 3067920795}
    12: {genre: 'Thriller', revenue: 11693797086}
    13: {genre: 'War', revenue: 1692654029}
    14: {genre: 'Family', revenue: 7177419409}
    15: {genre: 'Music', revenue: 885786343}
    16: {genre: 'Documentary', revenue: 780123406}
    17: {genre: 'Western', revenue: 458255558}
    18: {genre: 'TV', revenue: 58252139}
    19: {genre: 'NA', revenue: 162301603}
    20: {genre: 'Foreign', revenue: 31741406}

## Bar chart

Dat was allemaal wat abstract. Tijd om nog eens visueel te werken.

We hebben al geleerd hoe we basisvisualisatie kunnen doen in D3 met selecties en joins. We gaan het hier echter wat meer code-driven aanpakken.

We passen de html van `App.vue` als volgt aan:

    <template>
        <div class="bar-chart-container">
        </div>
    </template>

We breiden onze `mounted()` functie uit:

    async mounted() {
    
        const data = await d3.csv('movies.csv', this.typeConversion);
        const filteredData = this.dataLoaded(data);
        const finishedData = this.prepareBarChart(filteredData);
        console.log(finishedData);

        const margin = { top: 40, right: 40, left: 40, bottom: 40};
        const width = 400 - margin.left - margin.right;
        const height = 500 - margin.top - margin.bottom;

        d3.select('.bar-chart-container')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')
  }

Er gebeurt hier eigenlijk niets spannends. In de plaats van een SVG element te zetten in onze html gebruiken we D3 om er eentje toe te voegen. Door dat te doen kunnen we onze width en height instellen vanuit de code. Op die manier is het wat gemakkelijker om bijvoorbeeld marges in te lassen.

We gebruiken nu een join met de data om enkele rechthoeken op het scherm te krijgen:

    const svg = d3.select('.bar-chart-container')
      .append('svg')
      .attr('width', width)
      .attr('height', height)
      .append('g')

    svg.selectAll('rect')
      .data(finishedData)
      .join('rect')
      .attr('x', 0)
      .attr('y', function(d, i)
      {
        return i * height/finishedData.length;
      })
      .attr('height', function ()
      {
        return height/finishedData.length - 5;
      })
      .attr('width', 200)

We vangen het voorgaande resultaat op in een variabele `svg` en werken verder op die variabele. Dat doen we gewoon om de leesbaarheid wat te verbeteren en ook omdat we dan gemakkelijker assen kunnen toevoegen later. Vervolgens voegen een `rect` element toe per datapunt. De width stellen we statisch in op 200 (dat zullen we zo dadelijk veranderen). De y-coordinaat is wat lastiger: we pakken de hoogte van ons canvas en delen dat door het aantal datapunten. In ons geval hebben we als hoogte 420 en we hebben 21 datapunten. Volgens javascript is 420/21=20 (dankzij afronding).  De eerste rechthoek krijgt dus y=0, de tweede y=20, de derde y=40, etc. We gebruiken dezelfde berekening voor de hoogte (420/21), maar we trekken 5 pixels af om wat marge te houden met de andere rechthoeken.

Hoe gaan we nu de width instellen van onze rechthoeken? Een eenvoudige manier zou zijn:

    function(d) {
        return d.revenue
    }

Maar de revenue gaat tot in de miljarden! Zoveel pixels hebben we helaas niet. We zouden dit getal kunnen delen door enkele miljoenen of miljarden maar dat is gokwerk. We laten D3 dat voor ons berekenen. D3 kan namelijk waardes herschalen. Zo kunnen we bijvoorbeeld de getallen tussen [20, 800] herschalen naar [0, 10]. We kunnen D3 zelfs waardes laten schalen naar kleur. Zo kan je bijvoorbeeld [0, 100] laten herschalen op een kleurenas waarbij 0 blauw is en 100 rood, 50 is dan de kleur tussen blauw en rood (paars). 
Voordat we kunnen herschalen moeten we weten wat de maximum revenue en de minimum revenue in onze data is. Dat kan eenvoudig met D3:

    const extents = d3.extent(finishedData, d => d.revenue)

Het resultaat is een array van 2 getallen: het minimum en het maximum. In ons geval:

    [31741406, 79304756422]

Het maximum is dus `extents[1]`. het minimum is `extents[0]`. We zijn nu klaar om D3 onze width te laten berekenen. D3 kan voor ons een dynamische functie maken die deze berekening doet:

    const xScale = d3.scaleLinear()
      .domain([0, extents[1]])
      .range([0, width]);

`scaleLinear` zegt dat we geïnteresseerd zijn in een linear verloop, `domain` zegt dat we vertrekken van 0 tot `extents[1]` (het maximum van onze data), en `range` zegt dat we moeten herschalen naar [0, width]. Het resultaat is een functie. We kunnen `xScale` dus bijvoorbeeld als volgt gebruiken:

    xScale(20000000)

We gaan die xScale functie gebruiken in de berekening van de width van onze rechthoeken:

    .attr('width', function(d){
        return xScale(d.revenue)
      })

Hier is nog eens de volledige code:

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    async mounted() {
        
        const data = await d3.csv('movies.csv', this.typeConversion);
        const filteredData = this.dataLoaded(data);
        const finishedData = this.prepareBarChart(filteredData);
        console.log(finishedData);

        const margin = { top: 40, right: 40, left: 40, bottom: 40};
        const width = 400 - margin.left - margin.right;
        const height = 500 - margin.top - margin.bottom;

        const extents = d3.extent(finishedData, d => d.revenue);
        const xScale = d3.scaleLinear()
        .domain([0, extents[1]])
        .range([0, width]);

        const svg = d3.select('.bar-chart-container')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')

        svg.selectAll('rect')
        .data(finishedData)
        .join('rect')
        .attr('x', 0)
        .attr('y', function(d, i)
        {
            return i * height/finishedData.length;
        })
        .attr('height', function ()
        {
            return height/finishedData.length - 5;
        })
        .attr('width', function(d){
            return xScale(d.revenue)
        })
    },
    methods: {
        typeConversion: function(d)
        {
        return {
            genre: d.genre,
            budget: +d.budget,
            revenue: +d.revenue
        }
        },
        dataLoaded : function(d)
        {
        const filteredMovies = d.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
        return filteredMovies;
        },
        prepareBarChart: function(d)
        {
        const dataMap = d3.rollup(
            d,
            r => d3.sum(r, x => x.revenue),
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
        }
    }
    }
    </script>

Oef... wat een brok. Maar dit schept wel een totaalbeeld van het werken met D3: we importeren data uit een file, we schonen die data op, we filteren de data, we transformeren de data voor de visualisatie die we willen en gebruiken ten slotte selecties en joins om de data te tonen.

Er ontbreekt hier nog wel wat: assen en ook een vorm van interactiviteit. Dat laten we voorlopig nog in het midden, maar we bouwen daar wel naartoe.






