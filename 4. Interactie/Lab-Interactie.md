# Interactiviteit

Tot nu toe hebben we data ingelezen, kunnen we eenvoudige verwerkingen doen en kunnen die zelfs al verdienstelijk tonen. Wat we nog missen is een vorm van interactiviteit. Vandaag brengen we daarom de dansbaarheid van Taylor Swift nummers in kaart.

## 1. Opzetten van onze visualisatie
We moeten uiteraard iets hebben om interactief te maken. Vandaar gaan we snel wat data inlezen. Maak een nieuw Vue project en zet `taylorswift.csv` in je public folder. Vergeet niet om `cd` te doen naar je nieuwe folder. Run je project met `npm run serve`. We gaan iets nieuws toevoegen: Vuetify.

### Vuetify
Vuetify is voor Vue wat bootstrap is voor css. In de plaats van een heleboel css klasses die we kunnen gebruiken, geeft Vuetify ons een heleboel Vue componenten die we kunnen gebruiken. We moeten wat werk doen om Vuetify aan de praat te krijgen, maar daarna hebben we toegang tot een heleboel componenten die we kunnen gebruiken. Aangezien we heel wat interactieve elementen willen toevoegen kan Vuetify ons heel wat werk uitsparen.

Installeer vuetify met het volgende commando:

    vue add vuetify

Selecteer Vue 3/CLI. Probeer opnieuw je project te runnen met `npm run serve`. Als je een error krijgt dat `vuetify/styles` niet gevonden kan worden, update dan even je plugin met het volgende commando:

    vue upgrade

Als je nu kijkt naar App.vue, zal je zien dat die ook wat veranderd is. 

### Verwerken van de data
Installeer d3:

    npm install d3

Voeg de d3 import statement toe aan App.vue:

    import * as d3 from "d3"

Voeg ook je `mounted()` functie toe:

    async mounted() {
        const rawData = await d3.csv('taylorswift.csv');
        console.log(rawData);
    }

Als je niet goed weet waar deze zaken zouden moeten staan, kijk dan zeker terug naar de vorige labs, ondertussen zou je dat moeten weten.
Inspecteer de data die je krijgt in de console van je browser. Verwijder de `HelloComponent` zoals we al enkele keren gedaan hebben en controleer opnieuw of je website blijft draaien.

We gaan onze data nu verwerken en opslaan in een eigen structuur. We zijn geïntereseerd in de volgende kolommen van onze csv: `track_name`, `danceability`, `energy`, `tempo`, `album_name` en `album_release_year`. Voeg eerst een lege array toe als componentsvariabele zodat we ons harde werk ook kunnen opslaan:

    ...
    data() {
        return {
        data: []
        }
    },
    ...

We voegen `methods` toe en maken een functie die de de kolommen voor ons kan converteren en verwerken:

    ...
    methods: {
        filterData: function(d) {
            return {
                track_name: d.track_name,
                danceability: +d.danceability,
                energy: +d.energy,
                tempo: +d.tempo,
                release: +d.album_release_year,
                album: d.album_name
            }
        },
    }
    ...

We roepen deze functie op bij het inlezen, en kennen de data toe aan onze componentsvariabele:

    const rawData = await d3.csv('taylorswift.csv', this.filterData);
    this.data = rawData;

De bedoeling is dat de gebruiker enkele nummers selecteert van Taylor Swift. <b>We houden dus een lijst bij van alle geselecteerde tracks.</b> Die nummers worden dan getoond op een grafiek. De y-as van de grafiek is altijd de dansbaarheid van het nummer. De x-coordinaat is ofwel de "energy" van het nummer, of het "tempo" van het nummer. <b>We houden dus bij of de gebruik energy of tempo wilt weergeven</b>.
Taylor Swift heeft schijnbaar ook heel erg veel nummers. Om het de gebruiker gemakkelijker te maken kan je je in je zoektocht naar een nummer filteren op album. <b>We hebben daarom ook nog een lijst nodig van alle albums uit de dataset en welk album de gebruiker heeft geselecteerd</b>. We breiden op basis van deze inzichten onze data uit:

    data: () => ({
        data: [],
        albums: [],
        selectedTracks : [],
        selectedAlbum: "Taylor Swift",
        yFilters: ['Energy', 'Tempo'],
        selectedFilter: 'Energy'
    }),

### Opbouwen van een lijst van albums
We gaan eerst nog die lijst van albums samenstellen. Dit is basisjavascript, dus probeer zeker eerst eens zelf of je dit lukt. Ik heb een methode `getAlbums` geschreven die ik toegevoegd heb aan de `methods`:

    getAlbums: function() {
      const filtered = this.data.filter((r, index) => {
        const idx = this.data.findIndex(x => {
          return x.album == r.album
        });
        return idx == index
      });
      return filtered.map(x => x.album);
    },

Deze methode doet 2 dingen. Eerst wordt de variabele `filtered` berekend: dat is een versie van onze grote `data` lijst maar dan met enkel nog unieke albums. De manier waarop we dat doen is door wat stiekem te zijn met de methode `findIndex`. `findIndex` zal de index teruggeven van het eerste element dat voldoet aan de voorwaarde (`x.album == r.album`). Als we een nieuw element van onze array tegenkomen filteren we dat eruit als de index niet hetzelfde is als het eerste element van dat album. Op die manier blijven we enkel nog over met unieke elementen. 
Het tweede wat hier gebeurt is dat we overschakelen van een rij van objecten naar een rij van albums met behulp van de `map` methode. `map` maakt een nieuwe rij door een oude rij te overlopen en een bewerking uit te voeren op elk element. In dit geval overlopen we elk object `x` uit de `filtered` rij en maken we nieuwe array waar elk element de `album` property is van elke `x`. Zowel de `map` als de `filter` functie kan je zelf schrijven. Ik raad je zelfs aan deze oplossing niet zomaar over te nemen. Schrijf zelf maar wat langere code die je zelf goed begrijpt! Een korte oplossing is niet altijd de betere!

We roepen deze methode ten slotte op in `mounted` en steken het resultaat in onze componentsvariabele:

    this.albums = this.getAlbums();


### Helpermethodes

Buiten onze `getAlbums()` methode gaan we nog enkele handige methodes toevoegen:

    ...
    getTracks: function(album) {
        const filtered = this.data.filter(r => {
            return r.album == album
        });
        return filtered.map(x => x.track_name);
    },

    getTrackData : function(track) {
      return this.data.find(x => {
        return x.track_name == track;
      })
    },
    ...

De `getTracks` methode zal alle liedjes terugggeven gegeven een album. De input is dus de naam van een album, de output is een lijst van liedjes. `getTrackData` neemt als input de naam van een liedje en zoekt dat liedje op in onze grote `data` lijst. Je zou ondertussen deze 2 methodes zeker zelf moeten kunnen verzinnen/schrijven.

### Visualisatie
Tijd om wat te werken aan onze UI. We zetten eerst wat assen op:

    <v-app>
    <v-main>
        <svg width="750" height="400">
            <rect x="0" y="0" height="500" width="2" />
            <rect x="0" y="398" height="2" width="750" />
        </svg>
    ...

Die hoogte en breedte (750x400) gaan we nog nodig hebben om te rekenen, dus we voegen die meteen toe als componentsvariabelen:

    data() {
    return {
      width: 750,
      height: 400,
      ...
    
We passen onze html aan zodat die waardes gebruikt worden en we voegen ook wat labels toe:

    <v-app>
    <v-main>
        <svg :width="this.width + 300" :height="this.height">
        <rect x="0" y="0" :height="this.height" width="2" />
        <rect x="0" :y="this.height - 2" height="2" :width="this.width" />

        <g transform="rotate(90)" >
            <text y ="-10">DANSBAARHEID</text>
        </g>
        <text :x="this.width - 75" :y="this.height - 10"> {{this.selectedFilter.toUpperCase() }} </text>
        
        </svg>
    ...

## 2. Eerste interactie
We voegen UI controls toe onder onze `svg`. We maken hier gebruik van `Vuetify`. We beginnen met het toevoegen van radio buttons. Bekijk de documentatie van radio buttons op website van vuetify: https://vuetifyjs.com/en/components/radio-buttons/. 

### Radio Buttons
We breidden onze html uit:

    ...
    </svg>
    <div id="controls">
    <v-container fluid>
      <v-radio-group inline v-model="selectedFilter">
        <v-radio
          v-for="n in yFilters"
          :key="n"
          :label="n"
          :value="n"
          
        ></v-radio>
      </v-radio-group>
    </v-container>
    ...
    </div>
    ...

De `v-container` is niet per se nodig, maar geeft een mooie "div css" aan onze radio buttons. We hebben enkele `v-radio` elementen genesteld in een `v-radio-group` (Dat zijn er meerdere want er staan een `v-for`!). De radio group zorgt ervoor dat er maar een elementje kan geselecteerd worden. Dat onderscheidt een radio button van een checkbox. We maken een radio button voor elk elementje uit `yFilters`. Die rij ziet er als volgt uit:

    yFilters: ['Energy', 'Tempo']

Er komen dus 2 radio buttons te staan. `:label` zorgt ervoor dat de radio buttons het correcte label krijgen ('Energy' voor de eerste, 'Tempo' voor de tweede). `:value` is wat moeilijker te begrijpen. In ons geval is de value en het label hetzelfde maar dat hoeft niet zo te zijn. We kunnen bijvoorbeeld afspreken dat de "value", de waarde waar we mee gaan rekenen, verschilt van het label. Zo zou de value 1 kunnen zijn als 'Energy' geselecteerd is, of 2 als 'Tempo' geselecteerd is. Dat is een keuze die je zelf moet maken. Die value wordt weggeschreven naar een variabele die we toewijzen aan de `v-radio-group` met behulp van `v-model`. In dit geval wordt de waarde weggeschreven naar de componentsvariabele `selectedFilter`. Dit is 2 way binding. Dat betekent dat als wij de waarde aanpassen van `selectedFilter` vanuit code naar een geldige waarde voor de radiobuttons, dan zal die ook automatisch geselecteerd worden. Omgekeerd, als de gebruiken een andere radio button aanklikt, zal de waarde van `selectedFilter` veranderen. Er is ook 1-way binding. Dat betekent typisch dat het aanklikken van het UI element door de gebruiker wel een verandering van de variabele teweegbrengt, maar dat de variabele zelf aanpassen vanuit code niet het UI element zal doen updaten.
Ten slotte staat er nog `inline` bij de radio group, dat is zodat de radio buttons horizontaal getekend worden.


### V-Select
We gaan nog wat controls toevoegen. `Vuetify` werkt net als bootstrap met kolommen en rijen: `v-col` en `v-row`:  

    <v-container fluid>
      <v-row>
        <v-col>
          
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          
        </v-col>
        <v-col>
          
        </v-col>
      </v-row>
      
    </v-container>
    </div>

We voegen nu een `v-select` toe:

    ...
    <v-container fluid>
      <v-row>
        <v-col>
          
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-select
            label="Album"
            :items="this.albums"
            v-model="this.selectedAlbum"
            ></v-select>
        ...

Een `v-select` is een dropdown. We geven als `items` onze lijst van albums mee die we berekend hebben. Net als bij de radio group wordt het geselecteerde album met 2-way binding weggeschreven naar een variabele met `v-model`. Hier kiezen we voor onze componentsvariabele `selectedAlbum`. Bekijk zeker ook de documentatie van het `v-select` element op de website van `Vuetify`: https://vuetifyjs.com/en/components/selects/.

Ten slotte gaan we nog een aantal `v-checkboxes` toevoegen. We willen een checkbox toevoegen voor elke track uit het geselecteerde album:

        ...
          <div v-for="(track, index) in this.getTracks(this.selectedAlbum)" :key="index">
            <v-checkbox :label="track" :value="track" v-model="selectedTracks"></v-checkbox>
          </div>
        </v-col>
        ...

We gebruiken opnieuw een `v-for` om dynamisch een aantal checkboxes te tekenen. We tekenen op basis van het geselecteerde album. We gebruiken hier onze `getTracks` methode om een lijst van liedjes te krijgen. We vragen de liedjes op van ons huidig geselecteerd album (dit werd ingevuld door de `v-select`). We loopen dan over al die liedjes en maken een checkbox voor elk liedje. Zoek zelf eens naar de documentatie van `v-checkbox` en ontcijfer wat `label`, `value` en `v-model` precies doen. 

Op dit moment hebben we eigenlijk al heel wat interactie, maar nog geen visualisatie. TIjd om daar eens aan te sleutelen

## 3. De Visualisatie
We kunnen nu met al onze mooie UI controls liedjes selecteren. Die komen ook allemaal netjes terecht in `selectedTracks`. Een cirkel tekenen per track is triviaal, maar het berekenen van de x- en y-waardes hebben we nog wat werk aan. 


### Y-waarde
De y-waarde is altijd de danceability. Die ligt ook altijd tussen [0..1]. De y-waarde vinden we dan door de danceability te vermenigvuldigen met de hoogte van onze y-as. Nog een klein detail dat we niet mogen vergeten: de y-as begint in ons canvas te tellen van boven, niet van de onderkant van de as. Als onze y-as bijvoorbeeld 500 pixels hoog is en onze danceability 0.2 is, dan willen we dat ons liedje 100 pixels (500 * 0.2) boven de y-as wordt getekend. In effectieve canvascoordinaten, die van boven naar beneden tellen, betekent dat dat het punt op y=400 getekend moet worden. De uiteindelijke formule voor de y-coordinaat is dan:

    height - danceability * height

We gieten dat even om naar fatsoenlijke code en zetten dat in een methode:

    getY : function(track)
    {
      return this.height - this.getTrackData(track).danceability * this.height;
    }

Gegeven de naam van een liedje, gaat deze functie ons de y-waarde geven.

### X-waarde
De x-waarde verschilt op basis van onze radio button. Ofwel tonen we energy op de x-as, ofwel tempo. Energy ligt tussen [0..1] dus die berekening is heel eenvoudig:

    width * energy

We hebben hier niet hetzelfde probleem als bij de y-as, de tekencoordinaten tellen x ook van links naar rechts. Tempo vormt wel een probleem, die waarde ligt immers niet tussen [0..1]. We gaan hier dezelfde techniek gebruiken als we in andere labo's gebruikt hebben om de straal van een cirkel te berekenen. We gaan de d3 `extents` funtie gebruiken. Die geeft als output een functie dus we voorzien een nieuwe componentsvariabele:

    ...
    tempoScale : null,
    ...

Merk op dat ik je nu niet meer zeg waar je dit moet toevoegen. Ondertussen zou je echt wel moeten weten waar je een componentsvariabele kan toevoegen. We breiden onze `mounted` functie uit en laten d3 de uiteinde van onze data berekenen:

    const tempoExtents = d3.extent(rawData, r => r.tempo);
    this.tempoScale = d3.scaleLinear()
      .domain([0, tempoExtents[1]])
      .range([0, this.width]);

Nu bevat vertaalt `tempoScale` de tempo data van [0..?] naar [0..width]. De x-coordinaat voor tempo is dan:

    tempoScale(tempo)

Welke radio button geselecteerd is houden we via de radio buttons bij in de variabele `selectedFilter`. De volledige functie voor x is dan (probeer eerst zelf):

    getX : function(track) 
    {
      if(this.selectedFilter == this.yFilters[0])
      {
        return this.width * this.getTrackData(track).energy;
      }
      else
      {
        return this.tempoScale(this.getTrackData(track).tempo);
      }
    }

We zijn nu gewapend om onze liedjes te tekenen op ons canvas.

### Liedjes

De liedjes tekenen is niets nieuws. Hieronder vind je de oplossing. 

    <g v-for="track in this.selectedTracks" :key="track.name">
        <g :transform="`translate(${this.getX(track)}, ${this.getY(track)})`">
            <circle r = "5" />
            <text x="15" y="15"> {{track }} </text>
        </g>
    </g>

Analyseer zelf hoe deze code werkt, er zou hier niks mogen tussenzitten dat je niet begrijpt.

## 4. Extra's
We gaan nog enkele zaken toevoegen aan onze visualisatie. 

### (De)select all tracks
Breidt eerst je html uit met 2 `v-btn` elementen. Je mag zelf kiezen waar je die zet:

    ...
    <v-btn>Select all tracks</v-btn>
    ...
    <v-btn>Deselect all tracks</v-btn>
    ...

Om events toe te voegen aan die button gebruiken we `v-on`. Je geeft dan mee over welk event het gaat en ook de methode die je wenst aan te roepen als dat event zich voor doet. Bijvoorbeeld:

    ...
    <v-btn v-on:click="selectAllTracks">Select all tracks</v-btn>
    ...
    <v-btn v-on:click="deselectAllTracks">Deselect all tracks</v-btn>
    ...

`v-on:` wordt vaak afgekort tot `@`. Dus je kan ook schrijven:

    ...
    <v-btn @click="selectAllTracks">Select all tracks</v-btn>
    ...
    <v-btn @click="deselectAllTracks">Deselect all tracks</v-btn>
    ...

De methodes `selectAllTracks` en `deselectAllTracks` bestaan nog niet dus die moeten we nog toevoegen aan onze component. De namen zouden moeten duidelijk maken wat deze methodes horen te doen. Probeer eerst zelf de methodes te schrijven. Een oplossing vind je hieronder:

    selectAllTracks: function()
    {
      const allTracks = this.getTracks(this.selectedAlbum);
      allTracks.forEach(t => 
      {
        if(!this.selectedTracks.includes(t))
          this.selectedTracks.push(t);
      });
    },

    deselectAllTracks: function()
    {
      console.log("deselect");
      const allTracks = this.getTracks(this.selectedAlbum);
      this.selectedTracks =  this.selectedTracks.filter(t => !allTracks.includes(t));
    }

### Highlighten
We willen dat er ook iets gebeurd als je met de muis zweeft over de cirkeltjes. We voegen eerst wat css toe:

    circle {
        transition: all 200ms ease;
    }

    circle:hover {
        fill: orange;
    }

We voegen een extra <b>componentsvariabele</b> toevoegen die bijhoudt welke cirkel er 'gehighlight' is:

    highlightedTrack: null,

We passen onze cirkels aan zodat ze nu ook luisteren naar events. Bij de buttons hebben we het `click` event gebruikt. Hier gebruiken we `mouseover` en `mouseleave`:

    <circle @mouseover="highlightCircle(track)" @mouseleave="clearSelection"
        r = "5"
    />

Deze functies/methodes zijn zeer eenvoudig. Als er met de muis over een cirkel bewogen wordt, dan wordt de "inhoud" van die cirkel onze `highlightedTrack`. Als de muis wegbeweegt maken we de `highlightedTrack` opnieuw leeg:

    highlightCircle: function(d) {
      this.highlightedTrack = d;
    },

    clearSelection: function() {
      
      this.highlightedTrack = null;
    },

Wat ons nog rest is onze css wat opschonen. Dat laat ik als oefening.

