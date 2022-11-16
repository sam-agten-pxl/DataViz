# Data Arrays
Voorlopig werken we in deze labos terug naar de bron. In het vorige lab hebben we gezien hoe we verschillende SVG elementen kunnen tekenen met `v-for` en hoe we ons leven gemakkelijker kunnen maken met behulp van componenten. We willen nu het uiterlijk van die SVG elementen aanpassen aan data. Die data komt typisch van een file, databank of Web API, maar voor dit lab maken we gebruik van statische data. We loopen nu over objecten in de plaats van een getal `n` en gebruiken hun eigenschappen om onze SVG vorm te geven.

## 1.  Nieuw project
We starten voor dit lab opnieuw een Vue project. Dit is het laatste lab waar we dit nog overlopen. Voor alle andere labos mag je steeds een nieuw project starten op dezelfde manier. We gebruiken `vue create` in een lege folder naar keuze:

    vue create data-arrays

Vergeet niet te duiken in die nieuwe projectfolder:

    cd data-arrays

Vergeet ook niet even te controleren of je Vue project start:

    npm run serve


## 2. Data, zegt u?

Ja. Data. Zoals jullie weten kan data in allerlei formaten voorkomen. Voor dit vak gebruiken we bijvoorbeeld vooral het .csv formaat om data voor te stellen, maar die data kan van eender welke file komen of zelfs typisch van een database of Web API. We willen ons in dit lab echter toespitsen op de visualisatie en daarom gaan we nog niet meteen werken met een .csv bestand. We gaan daarom vandaag aan de slag met zelfverzonnen, statische data. Hoe kunnen we die voorstellen in javascript? Het meest simpele voorbeeld is een rij van getallen. Dat kan in javascript als volgt:

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

Dit lijkt er al meer op. Nu hebben we 4 datapunten die elk 2 waardes bevatten: een categorie en een getal. Elk object in deze rij kan je dus bezien als de rij uit een excel tabel of een .csv bestand. Voor dit lab gebruiken we telkens statische data, daarmee bedoelen we dat de data niet dynamisch uitgelezen wordt, maar wel "hardcoded" in ons programma zit. De data verandert niet. Statisch dus. We starten zo dadelijk eenvoudig, met een rij van getallen. Als we dat onder de knieëen hebben gaan we kijken naar een rij van objecten.

## 3. Rij van getallen
We zetten eerst een statische visualisatie op en koppelen dan daarna data aan die visualisatie.

## Basisvisualisatie
We beginnen met de laatste oefening van het laatste labo:

<svg width=700 height=175>
    <g transform="translate(0, 0)">
        <rect width=20 height=100 fill=orange />
        <text x="5" y="110" fill="orange" transform="rotate(50, 5, 110)">Pigs</text>
    </g>
    <g transform="translate(30, 70)">
        <rect width=20 height=30 fill=orange />
        <text x="5" y="40" fill="orange" transform="rotate(50, 5, 40)">Cats</text>
    </g>
    <g transform="translate(60, 40)">
        <rect width=20 height=60 fill=tomato />
        <text x="5" y="70" fill="tomato" transform="rotate(50, 5, 70)">Chickens</text>
    </g>
    <g transform="translate(90, 20)">
        <rect width=20 height=80 x=0 fill=orange />
        <text x="5" y="90" fill="orange" transform="rotate(50, 5, 90)">Dogs</text>
    </g>
</svg>

De oplossing (zonder `v-for`) daarvoor is:

    <svg width=700 height=175>
        <g transform="translate(0, 0)">
            <rect width=20 height=100 fill=orange />
            <text x="5" y="110" fill="orange" transform="rotate(50, 5, 110)">Pigs</text>
        </g>
        <g transform="translate(30, 70)">
            <rect width=20 height=30 fill=orange />
            <text x="5" y="40" fill="orange" transform="rotate(50, 5, 40)">Cats</text>
        </g>
        <g transform="translate(60, 40)">
            <rect width=20 height=60 fill=tomato />
            <text x="5" y="70" fill="tomato" transform="rotate(50, 5, 70)">Chickens</text>
        </g>
        <g transform="translate(90, 20)">
            <rect width=20 height=80 x=0 fill=orange />
            <text x="5" y="90" fill="orange" transform="rotate(50, 5, 90)">Dogs</text>
        </g>
    </svg>

De hoogtes van de rechthoeken zijn (van links naar rechts): `100, 30, 60` en `80`.  We willen die getallen in een array steken en werken met een `v-for`. Maar voor we dat kunnen doen moeten we eerst eens kijken naar hoe we code kunnen schrijven voor onze `App.vue` component.

### Basiscode voor App.vue

Laat ons nog eens kijken naar de `App.vue` code in een nieuw project:

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

Vergeet ook niet de HelloWorld component uit de html van onze App component te halen. We doen dit vanaf nu voor elk lab, tenzij anders vermeld.

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
Let er wel op dat de functie `mounted()` enkel wordt afgeroepen bij het begin van de applicatie, dat is dus niet de geschikte plaats voor het reageren op interacties met de gebruiker. Het is echter wel een goede plaats om eens te experimenteren met code.

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

`methods` is een lijst van methodes die we kunnen hergebruiken. Dat is een betere plaats om onze print te zetten zodat we die methode later zouden kunnen hergebruiken. We gaan die methods nog vaak gebruiken.

Zo kunnen we dus functionaliteit toevoegen aan onze component, maar waar zit dan de data van onze component? We hebben in het vorige lab al gezien hoe we eigenschappen aan een component kunnen toevoegen met behulp van `props`. Maar daar steken we liever de stukken data die van buitenaf ingevuld worden. Voor onze eigen data gebruiken we `data()`. Elke component kan ook een functie bevattem die `data()` heet. Die functie zal een object retourneren dat alle data van de component bevat. Dit verschilt sterk van `methods`, `props` of `components` want dat zijn arrays. `data()` is een javascript functie, net als `mounted()`, dus de syntax is anders. We voegen data als volgt toe:

    <script>
    export default {
        name: 'App',
        components: {
        },
        data() {
            return {
                //Dit is een leeg object.
            };
        }
    }
    </script>

Als je dat duidelijker vindt mag je het ook als volgt schrijven:

    <script>
    export default {
        name: 'App',
        components: {
        },
        data: function() {
            return {
                //Dit is een leeg object.
            };
        }
    }
    </script>

Dit zal helemaal niks doen. De functie `data()` wordt immmers nooit gebruikt en geeft bovendien op dit moment ook nog een leeg object terug; In dit leeg object kunnen we een hoop variabele declareren die we dan kunnen gebruiken in onze component. Laat ons onze array van getallen bijvoorbeeld eens toevoegen:

    ...
    data() {
        return {
            myArray: [100, 30, 60, 80]
        };
    }
    ...

Nu bevat ons data-object een variabele: `myArray`, en dat is een rij van getallen.

Je kan dit nu gemakkelijk testen in je `mounted()` functie:
    
    <script>
    export default {
        name: 'App',
        components: {
        },
        data() {
            return {
            myArray: [100, 30, 60, 80]

            };
        },
        mounted() {
            console.log(this.myArray);
        }
    }
    </script>

Test opnieuw in je developer console en je zal zien dat de rij van getallen weergegeven wordt in je console. 

<b>Opmerkingen</b>: 
* de `this.` voor myArray is essentieel. Waarom die daar staat is buiten de scope van dit vak. Onthou gewoon dat als we data willen oproepen van onze component, we dat moeten doen met `this.`.
* Degene met adelaarsogen zullen zich misschien afvragen waarom we de functie `data()` niet rechtstreeks aanroepen om aan het data-object te geraken. Met andere woorden: moet er niet `console.log(this.data().myArray)` staan? Als je dat probeert ga je geen compiler error krijgen, maar je zal wel een foutmelding krijgen als je de website probeert in te laden. De `data()` functie wordt ingelezen door Vue om een instantie van te maken. Je kan rechtstreeks aan dat object door `$data` te gebruiken. Dus: `console.log(this.$data.myArray)`, maar dat is niet nodig. De uitleg is behoorlijk technisch, maar als je zin hebt, kan je er hier meer over te weten komen: https://vuejs.org/api/options-state.html.

### Databinding

Onze component heeft nu een stukje data! `myArray`! Wat is ie mooi! Tijd om daar gebruik van te maken. Om dit deel wat gerichter te maken verwijder ik voorlopig de labels van onze visualisatie:

    <svg width=700 height=350>
      <g transform="translate(0, 0)">
        <rect width=20 height=100 fill=orange />
      </g>
      <g transform="translate(30, 70)">
          <rect width=20 height=30 fill=orange />
      </g>
      <g transform="translate(60, 40)">
          <rect width=20 height=60 fill=tomato />
      </g>
      <g transform="translate(90, 20)">
          <rect width=20 height=80 x=0 fill=orange />
      </g>
    </svg>

<svg width=700 height=120>
    <g transform="translate(0, 0)">
    <rect width=20 height=100 fill=orange />
    </g>
    <g transform="translate(30, 70)">
        <rect width=20 height=30 fill=orange />
    </g>
    <g transform="translate(60, 40)">
        <rect width=20 height=60 fill=tomato />
    </g>
    <g transform="translate(90, 20)">
        <rect width=20 height=80 x=0 fill=orange />
    </g>
</svg>

We kunnen nu met een `v-for` loop door onze `myArray` lopen:

    <svg width=700 height=350>
      <g v-for="(number, index) in this.myArray" :key="index">
        <g :transform="`translate(${index * 30}, ${100-number})`">
            <rect width=20 :height=number fill=orange />
        </g>
      </g>
    </svg>

Ok - dit moeten we even uitpakken...
We beginnen met `v-for="(number, index) in this.myArray"`. Deze for-lus zal loopen door onze array: `myArray`. Dat betekent dat onze lus 4x uitgevoerd zal worden, want we hebben 4 getallen in onze rij staan. Daarom krijgen we 4 rechthoeken of `rect`. Wat is dan `(number, index)`? Dat is onze lusvariabele. Dit zijn variabele die enkel leven binnen onze for-lus en de waarde die daar inzit zal verschillen per iteratie. Je had ook enkel dit kunnen schrijven: `v-for="number in this.myArray"`. In dat voorbeeld zal de lus 4 iteraties kennen. In de eerste iteratie bevat de variabele `number` het eerste getal uit onze rij: `100`. In de tweede iteratie bevat `number` het tweede getal: `30`, enzovoort. Normaal gezien zouden we daar genoeg mee hebben, maar om de visualisatie goed te kunnen doen moeten we ook weten de <i>hoeveelste</i> rechthoek we aan het tekenen zijn, anders kunnen we de x-coordinaat van de rechthoek niet berekenen. Daarom vragen we aan Vue om naast de inhoud van de array in een variabele `number` te steken elke iteratie, om ook in een variabele bij te houden op welke iteratie we zitten. We hebben hier die variabele `index` genoemd. De namen `number` en `index` zijn zelf te kiezen. Samengevat gebeurt het volgende: Vue zal wat er tussen de for-lus staat 1x uitvoeren per waarde in `myArray`. Voor elke iteratie zullen er 2 variabele ter beschikking staan: `number` en `index`. `number` bevat een getal uit de array, en `index` bevat een getal dat begint op 0 en dan optelt per iteratie:

    1e iteratie: number = 100 en index = 0
    2e iteratie: number = 30 en index = 1
    3e iteratie: number = 60 en index = 2
    4e iteratie: number = 80 en index = 3

De rest is eigenlijk simpel. Voor elke eigenschap waar een `:` voor staat, gebruiken we deze variabele om die eigenschap te berekenen. De x-coordinaat van een rechthoek is bijvoorbeeld 0 voor de eerste rechthoek, 30 voor de tweede, 60 voor de derde, etc. Met andere woorden, de x-coordinaat is: `index * 30`. We verwerken die x-coordinaat in de `transform` eigenschap zoals we gezien hebben in het vorige lab. De rest van de code zou je nu zelf moeten kunnen ontcijferen.

## 4. Rij van objecten

We werken bijna nooit met een rij van getallen, maar wel met een rij van objecten. Denk eraan, een rij van objecten in javascript ziet er zo uit:

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

### Databinding van eenvoudige objecten
We veranderen nu onze `myArray` in een reeks van objecten:

    ...
    data() {
    return {
      myArray: [
        {
          animal: 'Pigs',
          amount: 100
        },
        {
          animal: 'Cats',
          amount: 30
        },
        {
          animal: 'Chickens',
          amount: 60
        },
        {
          animal: 'Dogs',
          amount: 80
        }
      ]
    };
  },
  ...

Dit ziet er allemaal complexer uit dan het eigenlijk is. Hiervoor was een element uit onze array een getal. Zo was `myArray[1]` bijvoorbeeld `30` (denk eraan, we beginnen de index altijd te tellen vanaf 0, dus 1 is het tweede element uit de rij). Nu is `myArray[1]` een object, en dat object heeft 2 eigenschappen: `animal` en `amount`. `myArray[1]` is dus een "rij uit een excel sheet met 2 kolommen". Willen we nu die `30`, dan moeten we specifiek polsen naar de eigenschap `amount`. Dat doen we zo:

    `myArray[1].amount`

Binnen onze Vue component moeten we `this.` gebruiken om onze data aan te spreken dus correcter is:

    `this.myArray[1].amount`

Om onze code werkende te houden moeten we dus maar een kleine aanpassing doen:

    <svg width=700 height=350>
      <g v-for="(number, index) in this.myArray" :key="index">
        <g :transform="`translate(${index * 30}, ${100-number.amount})`">
            <rect width=20 :height=number.amount fill=orange />
        </g>
      </g>
    </svg>

Wat me hier erg aan stoort is dat we het `number` gebruiken om te verwijzen naar een element in uit `myArray`. Daarvoor hield dat steek omdat een element uit de array een getal was. Nu is dat een complexer object. Ik zou dus ook de naam veranderen naar iets toepasselijker:

    <svg width=700 height=350>
      <g v-for="(element, index) in this.myArray" :key="index">
        <g :transform="`translate(${index * 30}, ${100-element.amount})`">
            <rect width=20 :height=element.amount fill=orange />
            
        </g>
      </g>
    </svg>

### Labels
Tijd om onze labels terug te brengen. Eerst voegen we een statisch label toe aan elke rechthoek:

    <svg width=700 height=350>
      <g v-for="(element, index) in this.myArray" :key="index">
        <g :transform="`translate(${index * 30}, ${100-element.amount})`">
            <rect width=20 :height=element.amount fill=orange />
            <text x="5" :y=element.amount+10 fill="orange" :transform="`rotate(50, 5, ${element.amount+10})`">Pigs</text>
        </g>
      </g>
    </svg>

Merk op dat we weer enkel attributen laten varieëren op basis van de `amount` van elk element in `myArray`. 

De laatste stap is erg simpel. We willen de text `Pigs` vervangen door de `animal` property van `element`. Die is namelijk `Pigs` voor het eerste element, `Cats` voor het tweede, enzovoort. Dat kan als volgt:

    <svg width=700 height=350>
      <g v-for="(element, index) in this.myArray" :key="index">
        <g :transform="`translate(${index * 30}, ${100-element.amount})`">
            <rect width=20 :height=element.amount fill=orange />
            <text x="5" :y=element.amount+10 fill="orange" :transform="`rotate(50, 5, ${element.amount+10})`"> {{element.animal}}</text>
        </g>
      </g>
    </svg>

Merk op dat als we vue code willen steken in de <i>waarde</i> van een HTML element, dat we daar `{{}}` rond moeten zetten. Bij een HTML-attribuut gebruiken we `v-bind`, bij de waarde, of inhoud, gebruiken we `{{}}`.


<b>OPDRACHT</b>: Verander nu je Vue code zodat de balk een eigen Vue component wordt. Zorg ervoor dat je App.vue html er als volgt uitziet:

    <svg width=700 height=350>
      <g v-for="(element, index) in this.myArray" :key="index">
        <Bar
          v-bind:x=index*30
          v-bind:height=element.amount
        />
      </g>
    </svg>

Hieronder overlopen we de oplossing daarvoor, stap voor stap, maar probeer zeker eerst zelf. Je hebt nu alles geleerd om dat zelf te doen.

## 4. Gebruik van een Vue component
Maak een nieuwe file aan `Bar.vue` onder de map `components`. We exporteren de component door `script` tags toe te voegen aan deze file:

    <script>
    export default {
        name: 'Bar',
        components: {
            
        },
        props: {
            x: Number,
            height: Number,
            label: String
    }
    }
    </script>

We hebben hier ook meteen al enkele properties blootgesteld. Dit zijn de properties die gevraagd worden in de oplossing: `x`, `height` en `label`.

De html van onze component komt onder de `template` tags:

    <template>
        <g :transform="`translate(${x}, ${100-height})`">
            <rect width=20 :height=height fill=orange />
            <text x="5" :y=height+10 fill="orange" :transform="`rotate(50, 5, ${height+10})`"> {{label}}</text>
        </g>
    </template>

Deze html is bijna gelijkaardig aan de html die we gebruikt hadden in `App.vue`. Het verschil is dat we hier gebruik maken van de interne properties. Die worden uiteindelijk ingevuld in `App.vue`, in de for-lus. 

Het laatste dat we moeten doen is onze `App.vue` aanpassen zodat we ook wel degelijk `Bar` kunnen gebruiken in de html van `App.vue`. Dat doen we door de juiste import statements toe te voegen bij `App.vue`:

    ...
    <script>
    import Bar from "./components/Bar.vue"

    export default {
    name: 'App',
    components: {
        Bar
    },
    ...

Je hebt nu een werkende datavisualisatie. We hebben een array van objecten die elk eigen data bevatten. We lezen die data in en visualiseren die in een bar chart. Bovendien maken we gebruik van Vue componenten zodat we een herbruikbaar `Bar` componentje hebben.