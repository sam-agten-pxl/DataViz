# D3 Data Processing

In het vorige lab hebben we gewerkt met statische data maar we zouden graag werken met data die we krijgen vanuit een (.csv) file of zelfs van een API. Gelukkig kan een javascript library, D3, ons daarbij helpen. Zet je strak in je stoel, dit is het moeilijkste deel van dit vak.

## 1. D3

Maar Sam, we hebben het al moeilijk met Vue, moeten we nu echt weer iets nieuws toevoegen? 
Luister, File IO is niet van de poes. D3.js is een javascript library die ons kan helpen om gemakkelijker data te koppelen aan visuele elementen, maar heeft ook methodes om gemakkelijk csv files te verwerken. Er wordt wel eens gezegd dat D3 een grafische library is om grafieken te tekenen maar je zal snel merken dat d3 veel meer te bieden heeft dan dat. Sterker nog, als je op zoek bent naar een library om grafieken te tekenen raad ik aan dat je chart.js gebruikt. Een van de krachtigste tools die d3 aanbiedt is selecties: het selecteren van DOM-elementen uit onze html om dan eigenschappen van dat element te koppelen aan data. Wij gaan D3 echter enkel en alleen gebruiken om onze .csv files in te lezen.

### D3 installeren

Ale, nog een laatste keer:
We starten met een nieuw vue project. 

    vue create data-processing

Vergeet niet te duiken in die nieuwe projectfolder:

    cd data-processing

Vervolgens installeren we het d3 pakket voor ons project met behulp van node package manager:

    npm install d3

Vergeet ook niet even te controleren of je VUe project start:

    npm run serve

## 2. CSV

CSV files, of `'comma seperated value'` zijn eigenlijk tekstbestanden die data bevatten. Elke regel in een csv bestand kan je vergelijken met de rij in een excel bestand. We duiden de kolomscheidingen met een `,`. Vandaar spreken we van `comma seperated`. Als we bijvoorbeeld de volgende excel data hebben:

    POSTCODE    CITY
    3680        Neeroeteren
    3620        Lanaken
    ...

Als een .csv file ziet er dat zo uit:

    POSTCODE,CITY
    3680,Neeroeteren
    3620,Lanaken
    ...

Geen magie dus.

### Eerste CSV inlezen
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

Vooreerst hebben we wat gemakkelijke data nodig om mee te werken. Bij dit Lab vind je de csv file: `harry_potter.csv`. Dit is een eenvoudige csv file om mee te oefenen. Kopieer dit bestand naar de `public` folder van je project.

Het inlezen van deze data is gelukkig poepsimpel. D3 voorziet een methode `csv` om csv data in te laden. Die methode is echter async. Dat betekent dat het even duurt, op de achtergrond zijn ding doet en geeft dan pas later een resultaat terug. Dat is gedaan omdat data files natuurlijk heel erg groot zijn en we willen niet dat het inlezen van de data de applicate volledig opeist en blokkeert. D3 noemt dit `promises` of beloftes. Vergelijk het met een ticket. Je bestelt bij D3 data verwerking en als die gereed is kan je je ticket inruilen voor het resultaat. De code voor het inladen van de csv file ziet er zo uit:

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

Start je app, open je console, en normaal gezien krijg je dan de data uit de file te zien in je console (Het kan zijn dat je een promise krijgt, klap die wat open en ga op zoek naar het resultaat). Dat is goed nieuws want nu zit die data dus in een simpele array van objecten, zoals we gedaan hebben op het einde van het vorige lab. Daarmee is de cyclus compleet! We lezen data in van een CSV file naar een array van objecten. Die array van objecten kunnen we dan visualiseren. De code die deze magie veroorzaakt is dit:

    const data = await d3.csv('harry_potter.csv');
    console.log(data);

We moeten het keyword `await` gebruiken omdat de uitvoer van de `csv` methode async is. Het resultaat vangen we op in de variabele `data`. Ten slotte printen we het resultaat af. Belangrijk: merk op dat er nu een nieuw keyword staat bij onze `mounted()` functie: `async`. Elke keer als je async functions gebruikt in mounted (of eender welke js functie) moet je dat aangeven door de methode te markeren als `async`.

Vergeet ook niet deze regel:

    import * as d3 from "d3"

...Anders kan je d3 niet gebruiken in je script.

### Type conversie
We kunnen aan de slag met de data die we ingelezen hebben van de csv, maar zoals je al gemerkt hebt in de les, is de data die we krijgen typisch niet 'clean'. Dat komt omdat die data vaak door mensen wordt ingegeven en misschien staan de datums bijvoorbeeld niet allemaal in hetzelfde format of is een categorie soms met een hoofdletter geschreven en soms zonder hoofdletter. Daarom proberen we bij het visualiseren van de data altijd de data eerst op te schonen. 

Bij dit lab zit ook een andere csv file: `movies.csv`. Elke film in die dataset heeft een categorie, bijvoorbeeld actie, komedie, etc. We willen graag een visualisatie maken van de opbrengsten per categorie. Verdienen actiefilms bijvoorbeeld doorsnee meer geld dan komedies?

We laden eerste de csv file in:

    const data = await d3.csv('movies.csv');

Deze data is verre van perfect. Zo zien we bijvoorbeeld dat sommige budgetten ingegeven zijn als tekst (strings) en niet als getallen. De genres van de films zijn ook een json-object in tekstvorm. Als je niet goed weet wat ik daarmee bedoel, json is een manier om een javascript object textueel neer te schrijven, en is net als .csv eigenlijk niet zo speciaal.

Voordat we kunnen beginnen aan een visualisatie moeten we dus wat types omzetten naar bruikbare types. In D3 kunnen we aan de csv methode een functie meegeven die voor elke rij opgeroepen wordt voordat die in het eindresultaat komt. Daarmee kunnen we ook filteren op de data die we nodig hebben. We gaan daarvoor al eens een functie toevoegen aan onze component. Als we een functie willen toevoegen aan onze component doen we dat onder een array, `methods`. Dat is allemaal hoe dat Vue het graag heeft, dus dat moet je vanbuiten leren. We hebben nog al functies gebruikt, zoals `mounted()` of ` data()`, maar dat zijn ingebouwde functies. Nu gaan we een volledig eigen creatie toevoegen. Spannend!

    export default {
    name: 'App',
    components: {
    },
    async mounted() {
        
        const data = await d3.csv('movies.csv', this.typeConversion);
        console.log(data);
    },
    methods: {
        typeConversion: function()
        {
        }
    }
    }


### Nog even wat opfrissing over Javascript functies
Tijd voor  wat theorie! Ik heb de functie `typeConversion` genoemd. Op dit moment doet die functie helemaal niks en wordt die ook nergens in de code gebruikt. Je had de functie ook kunnen schrijven als volgt:

    ...
    methods: {
        typeConversion()
        {

        }
    }

Dat is gewoon een andere notatievorm. Een functie is een miniprogrammatje. Dat betekent dat een functie input krijgt, berekeningen maakt en output teruggeeft. We gaan deze functie zo schrijven dat ze elke rij in het csv bestand gaat verwerken. De input voor ons programma is een rij uit ons bestand, de output is een javascript object dat de waardes bevat die wij interessant vinden. Als we naar de .csv file kijken zien we welke kolommen er allemaal zijn. Voor onze visualisatie is het voldoende om een object te hebben dat per film het genre bijhoudt, het budget, en de revenue. Zoiets dus:

    {
        genre: "...",
        budget: ...,
        revenue: ...
    }

We moeten ook input krijgen, dat gaat uiteindelijk de rij zijn van ons csv bestand. Input voor een funtie noemen we ook wel de parameters, die staan altijd tussen de haakjes. Als je dit moeilijk vindt, hier is een voorbeeld van een functie die we zonet gebruikt hebben:

    const data = await d3.csv('movies.csv');

Je leest dat best van rechts naar links. De functie is `csv`, de input voor die functie is `movies.csv` en de output, die steken wij in een variabele genaamd `data`. Wat daar precies inzit, wel dat hebben we net al bekeken in onze `console.log`. Onze `typeconversion` functie gaat dus ruwweg deze vorm moeten krijgen:

    typeConversion: function(d)
    {
        return {
            genre: ...,
            budget: ...,
            revenue: ...
        }
    }

De `...` gaan we nog invullen. `d` is hier de naam die we aan onze input geven.  Dat mag eender wat zijn. Javascript is een typeloze taal, we zeggen nergens wat `d` precies moet zijn. Andere programmeertalen zijn strengen, die verwachten dat je voor een parameter ook zegt wat die input mag zijn (bijvoorbeeld een integer of een string), dan aanvaardt die functie geen input die daar niet aan voldoet. Dat is typisch (ha!) veel beter, maar javascript is het wilde westen en helaas de ruggegraat van een groot deel van het internet. Als we iets geven aan onze functie waar die niet mee overweg kan zal dat fouten geven.

Genoeg achtergrond, we gaan nu terug naar onze visualisatie.

###  Typeconversie, vervolg

Laat ons meteen eens kijken naar hoe dat er uitziet:

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

Er zijn 2 grote wijzigingen. We hebben onze `typeConversion` methode afgewerkt, maar belangrijker, die wordt nu ook gebruikt. We hebben onze functie namelijk als extra input meegegeven aan de `csv` functie van D3. Dat zorgt ervoor dat D3 de .csv file regel per regel gaat inlezen en die voedt elke regel aan onze `typeConversion` methode. Die `typeConversion` methode gaat dan de regel "cleanen". Dat gecleanede object gaat D3 toevoegen aan een rij en als alle regels overlopen zijn krijg je dat resultaat terug. In Pseudocode doet `csv` dus het volgende:

    1. Laat de csv file in van het geheugen
    2. Maak een nieuwe lege array "resultaat"
    3. Voor elke regel in het bestand, doe:
        3.1 Deel de regel op volgens , en steek dat in een javascript object
        3.2 Geef dat javascript object aan de functie die we als 2e input gekregen hebben. Als we geen 2e input hebben, negeer dan deze stap
        3.3 Steek het resulterende object in onze array "resultaat"
    4. Geef "resultaat" als output ('return')

De functie `typeConversion` krijgt dus een volledige rij `d` binnen en geeft een nieuw object terug. Dit is een veel kleiner object dat maar bestaat uit 3 eigenschappen: genre, budget en revenue. Voor elk van deze eigenschappen kopieren we gewoon de waarde uit de rij `d` die we binnenkrijgen.  Dus op zich wordt er nu nog niks "gezuiverd" door `typeConversion`, het enige wat we doen is kolommen negeren.

Als je dit uitvoert zal je merken dat onze data in de console nu versimpelt is, we hebben enkel de drie waardes per rij. We willen echter ook typeconversie doen op die waardes en die niet zomaar kopieren. Elk stukje data dat je krijgt uit een .csv file is tekst. Daar zijn we niet zoveel mee. We kunnen niet rekenen met tekst. Door een `+` te zetten voor onze waardes probeert javascript tekst te converteren naar een getal:

    typeConversion: function(d)
    {
      return {
        genre: d.genre,
        budget: +d.budget,
        revenue: +d.revenue
      }
    }

Nu zullen budget en revenue eerst omgezet worden naar een getal vooraleer we ze gebruiken. Je kan hier heel ver in gaan en de conversie uitgebreid doen. Zo krijg je consistentie in je data en dat is heel belangrijk om daar fatsoenlijke visualisatie op te doen. We hebben het hier gehouden op een simpel voorbeeld maar dit is dus de plaats waar je je data zou opschonen. Pfoe. Dat was heel wat. Neem een slokje drinken. Pak even pauze, want we zijn nog niet klaar.

### Data filtering

Er zijn sommige films die geen genre hebben of een budget of revenue die 0 zijn. We willen die data niet mee opnemen in onze visualisatie. We gaan deze data dus ook nog filteren. We kunnen dat rechtstreeks doen nadat de data is ingeladen:

    ...
    const data = await d3.csv("movies.csv", this.typeConversion);
    const filteredMovies = data.filter(r => {
      return r.budget > 0 && r.genre && r.revenue > 0
    });
    console.log(filteredMovies);
    ...

We gebruiken opnieuw een ingebouwde functie: `filter`. Als die ingebouwde functies kunnen intimiderend zijn. ik verwacht dat je enkel de ingebouwde functies leert die we ook gebruiken in de labs. Als je voor het examen een andere functie nodig hebt, dan zal die gegeven zijn.
Wat doet `filter`? `filter` werkt op een een array van objecten  (we roepen hier de functie op op onze variabele `data`, een rij van objecten). Dan overloopt `filter` elke object in de array en filtert er de objecten uit die niet voldoen aan de voorwaarde die ook als input wordt meegegeven (dat is het `r => {...}` gedeelte). Onze voorwaarde is dit:

    r.budget > 0 && r.genre && r.revenue > 0

`r` is hier een object uit onze array. Enkel een object waarvan het budget groter is dan 0, waarvan het genre bestaat (niet leeg is) en waarvan de revenue groter is dan 0 mag blijven. Belangrijk detail: `filter` wijzigt niet rechtstreeks de array waar we op werken (`data`), maar maakt een kopie en filtert daarop. Daarom dat we het resultaat van `filter` opvangen en in een nieuwe array steken. We hebben nu dus 2 arrays: `data` en `filteredMovies`. Het is netjes om deze filter ook in zijn eigen methode (of functie) te steken. We voegen een nieuwe methode toe onder de `methods` van onze component:
    
    ...
    typeConversion(d)
    {
      ...
    },
    dataLoaded : function(d)
    {
      return d.filter(r => {
        return r.budget > 0 && r.genre && r.revenue > 0
      });
    }
    ...

`dataLoaded` krijgt input `d` en voert daar de `filter` functie op uit. Deze functie zou dus enkel werken als we de functie ook daadwerkelijk  een input geven waar filter op kunnen toepassen. Als je de functie als volgt zou oproepen kan dat bijvoorbeeld niet:

    dataLoaded(5)

5 is immers een getal, en de functie `filter` bestaat niet voor een getal, enkel voor een rij van objecten. We moeten deze functie nog oproepen nadat onze data is geprepareerd:

    ...
    const data = await d3.csv("movies.csv", this.typeConversion);
    const filteredMovies = this.dataLoaded(data);
    console.log(filteredMovies);
    ...

Als je nu uitvoert zie je dat we overblijven met <b>3474</b> films.

## 3. Data transformatie

De data waar we nu mee werken is niet de data die we willen visualiseren. We willen deze data graag in categorieën stoppen per genre, om dan de revenue te vergelijken.
Opnieuw snelt D3 ons te hulp met de wat verwarrende functie: `rollup`. Als je ooit met SQL gewerkt heb en de <i>group by</i> functionaliteit kent, gaat dit je bekend voorkomen.

de rollup methode ziet er ruwweg zo uit:

    d3.rollup(data, reducer, groupBy)

`rollup` verwacht dus 3 soorten input. `data` is simpelweg onze gefilterde data die we doorspelen aan D3. `groupBy` is de waarde waarop we willen groeperen. Dat is in ons geval het genre. `reducer` is wat ingewikkelder. Het zijn de waardes waarin we geinteresseerd zijn per groep. In ons geval is dat de gecombineerde revenue van alle films in een groep. Ik denk dat we dit best uitleggen met een voorbeeld.

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

Laat ons die even ontleden. Dit is een functie die een argument binnenpakt: `r`. De reducer krijg per definitie een groep binnen. Dus r zou een array kunnen zijn van al onze datapunten waarvan het genre `animation` is, of `actie`, of ... Deze functie neemt die groep `r` en geeft die door aan een andere functie: `d3.sum`. Dit is een functie die een bepaald veld gaat optellen. Die functie krijgt de hele groep binnen en overloopt die per rij, zo een rij noemen we hier even `x`. Van elke rij `x` nemen we de revenue eigenschap en daar maken we de som van.

De laatste 2 regels kunnen hier ook verwarrend zijn:

    const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
      return dataArray;

Het probleem is dat de rollup functie een map teruggeeft. Als je eens wilt weten hoe er dat uitziet, print dan gerust eens `dataMap` af in de console. We werken echter liever met arrays om het simpel te houden.Deze laatste regels converteren de map terug naar een array object en retourneert dat resultaat.

We moeten deze functie nog oproepen. De volledige code ziet er dan zo uit:
    
    <script>
    import * as d3 from "d3" 

    export default {
    name: 'App',
    components: {
    },
    async mounted() {

        const data = await d3.csv("movies.csv", this.typeConversion);
        const filteredMovies = this.dataLoaded(data);
        const finishedData = this.prepareBarChart(filteredMovies);
        console.log(finishedData);
    },
    methods: {
        typeConversion(d)
        {
        return {
            genre: d.genre,
            budget: +d.budget,
            revenue: +d.revenue
        }
        },
        dataLoaded : function(d)
        {
        return d.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
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

## 4. Bar chart
Dat was allemaal wat abstract. Tijd om nog eens visueel te werken en alles netjes samen te brengen. We doen dat nog 1x stap voor stap. In de volgende labs gaan we hier sneller over. We starten met enkele statische bars om onze teen in het water te steken:

    <template>
    <div id="app">
        <svg width=700 height=350>
        <g :transform="`translate(0, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(30, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(60, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(90, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(120, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        </svg>
    </div>
    </template>

Nu tekenen we statisch 5 `rect` elementen. Ik wil er evenveel als in onze data zitten. Op dit moment zit die data in `finishedData`, maar `finishedData` is geen onderdeel van onze componentdata. We voegen dus nog even wat data toe aan onze component:

    ...
    components: {
    },
    data() {
        return {
        movieData: []
        }
    },
    async mounted() {
    ...

 `movieData` is een lege lijst. We steken onze `finishedData` in `movieData`:

    ...
    const finishedData = this.prepareBarChart(filteredMovies);
    ...

Nu tekenen we evenveel rechthoeken als we datapunten hebben. We passen onze HTML aan voor App.vue:

    <template>
    <div id="app">
        <svg width=700 height=350>
        <g v-for="(m, index) in this.movieData" :key="index">
            <g :transform="`translate(${index * 30}, 150)`">
            <rect width=20 height=150 fill=orange />
            </g>
        </g>
        </svg>
    </div>
    </template>

Als alles goed gaat, heb je nu 21 rechthoeken van dezelfde grootte.
De hoogte van de rechthoeken willen we nu laten afhangen van de revenue eigenschap. Maar de revenue gaat tot in de miljarden! Zoveel pixels hebben we helaas niet. We zouden dit getal kunnen delen door enkele miljoenen of miljarden maar dat is gokwerk. We laten D3 dat voor ons berekenen. D3 kan namelijk waardes herschalen. Zo kunnen we bijvoorbeeld de getallen tussen [20, 800] herschalen naar [0, 10]. We kunnen D3 zelfs waardes laten schalen naar kleur. Zo kan je bijvoorbeeld [0, 100] laten herschalen op een kleurenas waarbij 0 blauw is en 100 rood, 50 is dan de kleur tussen blauw en rood (paars). 
Voordat we kunnen herschalen moeten we weten wat de maximum revenue en de minimum revenue in onze data is. Dat kan eenvoudig met D3:

    const extents = d3.extent(finishedData, d => d.revenue)

Het resultaat is een array van 2 getallen: het minimum en het maximum. In ons geval:

    [31741406, 79304756422]

Het maximum is dus `extents[1]`. het minimum is `extents[0]`. We zijn nu klaar om D3 onze width te laten berekenen. D3 kan voor ons een dynamische functie maken die deze berekening doet:

    const xScale = d3.scaleLinear()
      .domain([0, extents[1]])
      .range([0, 200]);

`scaleLinear` zegt dat we geïnteresseerd zijn in een linear verloop, `domain` zegt dat we vertrekken van 0 tot `extents[1]` (het maximum van onze data), en `range` zegt dat we moeten herschalen naar [0, 200]. Het resultaat is een functie(!) die een getal tussen 0 en 79304756422 zal herschalen tussen 0 en 200. We kunnen `xScale` dus bijvoorbeeld als volgt gebruiken:

    xScale(20000000)

We willen die functie graag opslaan en gebruiken om onze HTML te berekenen met Vue, dus die moet onderdeel worden van de data van onze component:

    ...
    data() {
        return {
        movieData: [],
        scale: {}
        }
    },
    ...

In `mounted()` kennen we die functie toe:

    ...
    const finishedData = this.prepareBarChart(filteredMovies);
    const extents = d3.extent(finishedData, d => d.revenue);
    const xScale = d3.scaleLinear()
      .domain([0, extents[1]])
      .range([0, 200]);
    this.movieData = finishedData;
    this.scale = xScale;
    ...

We gebruiken dan die functie in onze HTML om de hoogte te berekenen:

    <template>
    <div id="app">
        <svg width=700 height=350>
        <g v-for="(m, index) in this.movieData" :key="index">
            <g :transform="`translate(${index * 30}, 150)`">
            <rect width=20 :height=this.scale(m.revenue) fill=orange />
            </g>
        </g>
        </svg>
    </div>
    </template>

Let nogmaals op de `:` voor `height`. Als je dit bekijkt zie je dat de rechthoeken verschillen van hoogte. De y-coordinaat staat nu wel nog statisch op 150, die zou ook moeten verschillen afhankelijk van de hoogte van de rechthoek, omdat de coordinaten van de rechthoek beginnen te tellen van de bovenkant van ons canvas. Denk gerust zelf eens na over die formule, hieronder vind je de oplossing:

    <template>
    <div id="app">
        <svg width=700 height=350>
        <g v-for="(m, index) in this.movieData" :key="index">
            <g :transform="`translate(${index * 30}, ${200 - this.scale(m.revenue)})`">
            <rect width=20 :height=this.scale(m.revenue) fill=orange />
            </g>
        </g>
        </svg>
    </div>
    </template>

Hier is nog eens de volledige code:

    <template>
    <div id="app">
        <svg width=700 height=350>
        <g v-for="(m, index) in this.movieData" :key="index">
            <g :transform="`translate(${index * 30}, ${200 - this.scale(m.revenue)})`">
            <rect width=20 :height=this.scale(m.revenue) fill=orange />
            </g>
        </g>
        </svg>
    </div>
    </template>

    <script>
    import * as d3 from "d3" 

    export default {
    name: 'App',
    components: {
    },
    data() {
        return {
        movieData: [],
        scale: {}
        }
    },
    async mounted() {

        const data = await d3.csv("movies.csv", this.typeConversion);
        const filteredMovies = this.dataLoaded(data);
        const finishedData = this.prepareBarChart(filteredMovies);
        const extents = d3.extent(finishedData, d => d.revenue);
        const xScale = d3.scaleLinear()
        .domain([0, extents[1]])
        .range([0, 200]);
        this.movieData = finishedData;
        this.scale = xScale;
        console.log(finishedData);
    },
    methods: {
        typeConversion(d)
        {
        return {
            genre: d.genre,
            budget: +d.budget,
            revenue: +d.revenue
        }
        },
        dataLoaded : function(d)
        {
        return d.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
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

Deze visualisatie is nauwelijks bruikbaar want we kunnen nu niet zien welke `rect` overeenkomt met welke categorie. In de oefeningen van dit Lab ga je zelf labels proberen toe te voegen aan de rechthoeken. 

Oef... wat een brok. Maar dit schept wel een totaalbeeld van het werken met data: we importeren data uit een file, we schonen die data op, we filteren de data, we transformeren de data voor de visualisatie die we willen en gebruiken ten slotte Vue.