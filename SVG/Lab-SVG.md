# SVG in HTML

Dit lab is bedoelt om je bekend te maken met de basis van het gebruikt van SVG (Scalable Vector Graphics) in HTML. We maken ook meteen gebruik van Vue zodat we gebruik kunnen maken van componenten om onze SVG tekeningen uitbreidbaar te maken.

## 1. Klaarzetten Vue-project
De details voor het opzetten van een Vue-project (en de nodige installaties) zien jullie in het vak Web-Expert, wij beperken ons hier tot de basis. Je vind ook meer informatie over het gebruik van Vue in VC code hierzo: https://code.visualstudio.com/docs/nodejs/vuejs-tutorial. Als je op zoek bent naar wat algemene informatie over Vue, dan raden we deze aan: https://vuejs.org/v2/guide/. Gebruik het 'create' commando om een nieuw project aan te maken. Dat project krijgt zijn eigen mapje. We raden aan om dit commando uit te voeren in een folder die je speciaal aanmaakt voor dit vak.

    vue create smiley-face

Vergeet daarna ook niet te navigeren naar de folder die aangemaakt is zodat je in je project zit:

    cd smiley-face  

Controleer eerst of je project werkt door vue te runnen. Dat kan je met het volgende commando:

    npm run serve

De uitvoer ziet er dan bijvoorbeeld zo uit:

    DONE  Compiled successfully in 84ms                                                                                                                                                                                                                                                                                                                1:16:58 PM

    App running at:
    - Local:   http://localhost:8080/
    - Network: http://192.168.1.121:8080/

Daar vind je het adres waar je website op draait. In dit voorbeeld is dat `http://localhost:8080/`. Open een web browser en surf naar dat adres om je website te zien. Nadat je je harde werk bewonderd heb, keer je terug naar terminal in VC code en gebruik je CTRL+C om de uitvoer stil te leggen. Bevestig met 'Y'.

### Verkenning van het project
Opnieuw laten we de basis van Vue over aan Web-Expert, maar hier is een snel overzicht van het project. We beginnen bij wat de gebruiker ziet en werken zo terug.

- Onder de map public vind je index.html. Dat is de webpagina die we zien als we het project laten runnen. Daar zie je dat de body van de html enkel bestaat uit een div met de id 'app'. Waar komt die app vandaan?
- Onder de map src vind je App.vue, dat lijkt een goed startpunt. Bovenaan die file zie je de Vue-declaratie van het 'app' element (tussen `<template>` tags). Het app-element bestaat uit een afbeelding van het Vue-logo en een element genaamd `HelloWorld`. 
- In Vue kunnen we eigen HTML-component definiëren. We noemen dat Vue componenten. In het bestand HelloWorld.vue kan je bekijken hoe het HelloWorld element er uitziet. We kunnen dus met andere woorden zelf nieuwe HTML elementen/snippets definiëren en die dan hergebruiken. 

### Snoeien

Het HelloWorld componentje hebben we niet nodig. Verwijder het HelloWorld elementje uit de App component. Die ziet er dan als volgt uit:

    <template>
    <div id="app">
        <img alt="Vue logo" src="./assets/logo.png">
    </div>
    </template>

Verwijder ook gerust het HelloWorld.vue bestandje uit je project (laat de map components nog even staan). Als je nu opnieuw het project probeert te runnen krijg je de volgende foutboodschap:

    Failed to compile with 1 error                                                                                                                                                                                                                                                                                                             12:59:41 PM
    This relative module was not found:

    * ./components/HelloWorld.vue in ./node_modules/cache-loader/dist/cjs.js??ref--13-0!./node_modules/babel-loader/lib!./node_modules/cache-loader/dist/cjs.js??ref--1-0!./node_modules/vue-loader/lib??vue-loader-options!./src/App.vue?vue&type=script&lang=js&

Dat komt omdat de App-component nog een verwijzing heeft naar een component genaamd HelloWorld, maar die hebben we net verwijderd. De verwijzing vind je in App.vue:

    <script>
    import HelloWorld from './components/HelloWorld.vue'

    export default {
    name: 'App',
    components: {
        HelloWorld
    }
    }
    </script>

Daar zie je dat de App component het HelloWorld componentje wilt importeren (1e regel). We zien ook dat de component nog eens extra gedeclareerd wordt onder de lijst `components`. We verwijderen deze referenties. Je blijft dan over met het volgende:

    <script>
    export default {
    name: 'App',
    components: {
        
    }
    }
    </script>

Als je nu je project opnieuw opstart krijg je enkel nog het Vue logo te zien. Dat was voorlopig genoeg Vue. We kunnen nu de html van de webpagina aanpassen door het App-componentje aan te passen. We hebben dus in feite nog niets gewonnen met Vue, maar het nut van Vue zal wel snel duidelijk worden als we data gaan inlezen voor onze visualizaties. Probeer nu zelf de html aan te passen van de App-component:

    <template>
    <div id="app">
        <h1>DataViz</h1>
        <p>Ik vind Vue wel leuk, maar niet zo leuk als Blender.</p>
    </div>
    </template>

Als je het Vue project niet afgesloten had met CTRL+C dan zal de verandering ook live doorgevoerd worden op de website als je het bestandje opslaat. Dat noemen ze ook wel hot-reload of dynamic reloading. Je website zou er nu zo moeten uitzien:



![Hello Vue!](First-Vue.PNG)

## 2. SVG
We kennen ondertussen het concept van Scalable Vector Graphics (SVG) van Illustrator. We weten dat die niet opgebouwd zijn uit statische pixels maar dat het eigenlijk berekeningen zijn op basis van primitieve vormen zoals rechthoeken, cirkels, etc. HTML 5 ondersteunt zelf ook het gebruik van SVG elementen. Je hebt dus in principe voor dit onderdeel geen Vue nodig, SVG elementen zitten standaard ingebakken in HTML. Als je bovenstaande niet gedaan hebt kan je hier meevolgen door zelf even een index.html bestandje aan te maken en rechtstreeks te werken in de body.

### Hello Circle
We starten met een cirkel, want cirkels zijn hip. Vooraleer we een cirkel kunnen tekenen moeten we echte beginnen met aan HTML te laten weten dat we SVG elementen willen toevoegen. dat doen we door een SVG tag toe te voegen aan de body. Daaraan moeten we ook meteen een breedte en hoogte meegeven:

    <svg width=700 height=350>
    </svg>

Als je zonder Vue werkt kan je deze tags rechtstreeks in de body zetten. Als je meegevolgd hebt met Vue, dan zijn we nog steeds onze aanpassingen aan het maken in de App-component:

    <template>
    <div id="app">
        <h1>DataViz</h1>
        <p>Ik vind Vue wel leuk, maar niet zo leuk als Blender.</p>

        <svg width=700 height=350>
        </svg>
    </div>
    </template>

Nu dan: Cirkel. Dat is eigenlijk heel simpel, je gebruikt een cirkel element binnen je svg groep:

    <svg width=700 height=350>
        <circle>
        </circle>
    </svg>

Helaas ga je zo nog niets kunnen zien want onze cirkel heeft nog geen afmetingen. Een cirkel heeft onder andere de volgende eigenschappen:
- cx: dit is de x-coördinaat van het middelpunt van de cirkel
- cy: dit is de y-coördinaat van het middelpunt van de cirkel
- r: dit is de straal van de cirkel

We voegen waardes voor die eigenschappen toe aan onze cirkel:

    <svg width=700 height=350>
        <circle cx=250 cy=50 r=50>
        </circle>
    </svg>

Als je nu uitvoert krijg je je eerste zwarte cirkel! Hoezee!

<svg width=700 height=100>
    <circle cx=250 cy=50 r=50>
    </circle>
</svg>

### Meer cirkels!

Breidt de bovenstaande code uit zodat je meer cirkels op je scherm krijgt. 

<svg width=960 height=100>
    <circle cx=50 cy=50 fill=orange r=20 />
    <circle cx=150 cy=50 fill=orange r=5 />
    <circle cx=250 cy=50 fill=orange r=10 />
    <circle cx=350 cy=50 fill=orange r=30 />
    <circle cx=450 cy=50 fill=orange r=15 />
</svg>


Maak gebruikt fan de `fill` eigenschap om de kleur van de cirkel te wijzigen.

### Het element `g`
Een veelgebruikt element voor SVG graphics in HTML 5 is het`g` element, oftwel group element. Op zicht doet dat element niet veel, vergelijkbaar met het `div` element. We kunnen het gebruiken om zone SVG elementen te groeperen:

    <svg width=700 height=350>
        <g>
          <circle cx=250 cy=50 r=50>
          </circle>
        </g>
    </svg>

Deze toevoeging zal niets veranderen aan je website. Maar nu kunnen we attributen toevoegen aan het groepselement zodat alle elementen binnen die groep beïnvloed worden. Zo kunnen we de hele groep 200 pixels naar rechts bewegen:

    <svg width=700 height=350>
        <g transform="translate(200, 0)">
          <circle cx=250 cy=50 r=50>
          </circle>
        </g>
    </svg>
    
Denk eraan dat ons 'canvas' maar 700 pixels breed is. Als je daar buitentreed zal je cirkel niet, of maar gedeeltelijk, zichtbaar zijn.

## 3. Smiley

Tijd om eens een smiley-face zonder glimlach te maken. We maken een SVG canvas van 960x500. Vervolgens tekenen we een cirkel op (480, 250), in het midden dus. We maken weliswaar gebruik een groep om de cirkel op die plaats te zetten, niet van de eigenschappen `cx` en `cy`. Ten slotte maken we de cirkel geel. Als straal pakken we 245 zodat we wat marge hebben. Voorlopig ziet er dat dan uit als volgt:

    <svg width=960 height=500>
        <g transform="translate(480, 250)">
          <circle r=245 fill=yellow>
          </circle>
        </g>
    </svg>

<svg width=300 height=100>
    <g transform="translate(150, 50)">
        <circle r=45 fill=yellow>
        </circle>
    </g>
</svg>

We kunnen nog een 'stroke' toevoegen aan deze cirkel met behulp van de eigenschappen `stroke` en `stroke-width`:

    <svg width=960 height=500>
        <g transform="translate(480, 250)">
          <circle r=245 fill=yellow stroke=black stroke-width=10>
          </circle>
        </g>
    </svg>

<svg width=300 height=100>
    <g transform="translate(150, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
    </g>
</svg>

Voordat je verder leest, probeer nu eerst eens zelf om oogjes toe te voegen. Blijf binnen hetzelfde groepselement (`g`) werken. De oplossing vind je hieronder.

    <svg width=960 height=500>
        <g transform="translate(480, 250)">
          <circle r=245 fill=yellow stroke=black stroke-width=10>
          </circle>
          <circle cx=-130 cy=-100 r=40 fill=black />
          <circle cx=130 cy=-100 r=40 fill=black />
        </g>
    </svg>

<svg width=300 height=100>
    <g transform="translate(150, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
          <circle cx=-20 cy=-10 r=8 fill=black />
          <circle cx=20 cy=-10 r=8 fill=black />
    </g>
</svg>

Let op, bovenstaande smiley is html die rechtstreeks in dit document staat en dus op een veel kleinere afmeting is gemaakt, het is dus mogelijk dat het voorbeeld hier niet exact overeenkomt met jouw oplossing.

Probeer nu eens de smiley 3x te herhalen:

<svg width=300 height=100>
    <g transform="translate(50, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
          <circle cx=-20 cy=-10 r=8 fill=black />
          <circle cx=20 cy=-10 r=8 fill=black />
    </g>
    <g transform="translate(150, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
          <circle cx=-20 cy=-10 r=8 fill=black />
          <circle cx=20 cy=-10 r=8 fill=black />
    </g>
    <g transform="translate(250, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
          <circle cx=-20 cy=-10 r=8 fill=black />
          <circle cx=20 cy=-10 r=8 fill=black />
    </g>
</svg>

Hoe doe je dat? Als je het goed hebt, dan heb je de smiley 3x gekopieerd en enkel de x-coördinaat aangepast. Maar wat is de smiley in dit geval? Dat is het `g` element, en de x- en y-coördinaat van de smiley staat in de transform eigenschap van die groep. Dit is de code van bovenstaand voorbeeld (met kleinere afmetingen):

    <svg width=300 height=100>
        <g transform="translate(50, 50)">
            <circle r=45 fill=yellow stroke=black stroke-width=3>
            </circle>
            <circle cx=-20 cy=-10 r=8 fill=black />
            <circle cx=20 cy=-10 r=8 fill=black />
        </g>
        <g transform="translate(150, 50)">
            <circle r=45 fill=yellow stroke=black stroke-width=3>
            </circle>
            <circle cx=-20 cy=-10 r=8 fill=black />
            <circle cx=20 cy=-10 r=8 fill=black />
        </g>
        <g transform="translate(250, 50)">
            <circle r=45 fill=yellow stroke=black stroke-width=3>
            </circle>
            <circle cx=-20 cy=-10 r=8 fill=black />
            <circle cx=20 cy=-10 r=8 fill=black />
        </g>
    </svg>

En wat als ik nu een kleine smiley will, een middelgrote en een grote? Probeer zelf eens te spelen met de waardes om dat voor elkaar te krijgen.

<svg width=300 height=100>
    <g transform="translate(50, 50)">
        <circle r=20 fill=yellow stroke=black stroke-width=1>
        </circle>
          <circle cx=-8 cy=-5 r=4 fill=black />
          <circle cx=8 cy=-5 r=4 fill=black />
    </g>
    <g transform="translate(150, 50)">
        <circle r=35 fill=yellow stroke=black stroke-width=2>
        </circle>
          <circle cx=-15 cy=-8 r=6 fill=black />
          <circle cx=15 cy=-8 r=6 fill=black />
    </g>
    <g transform="translate(250, 50)">
        <circle r=45 fill=yellow stroke=black stroke-width=3>
        </circle>
          <circle cx=-20 cy=-10 r=8 fill=black />
          <circle cx=20 cy=-10 r=8 fill=black />
    </g>
</svg>

Je zal al snel merken dat dat minder praktisch is. Waarom zijn we dit aan het doen? Omdat we graag SVG elementen willen aanpassen op basis van data die we inlezen. Denk bijvoorbeeld aan een bar-chart waarbij we rechthoeken hoger of korter maken op basis van data. In datavisualisatie werken we dus vaak met dezelfde SVG elemenen die we willen aanpassen met kleine waardes. We hebben een framework dat ons daarbij kan helpen: Vue.

## 4. Vue Componenten

Met Vue kunnen we eigen html-componenten definiëren. Dat betekent dat we eigenlijk willen dat onze html er ruwweg als volgt uiziet:

    <svg>
        <face> ... </face>
        <face> ... </face>
        <face> ... </face>
    </svg>

idealiter kunnen we dan ook nog eigenschappen meegeven om de gezichtjes onderling aanpasbaar te maken:

    <svg>
        <face cx=150 cy=0 r=20> ... </face>
        ...

### Face.vue
Maak in de map components een nieuw bestandje aan: `Face.vue`. Als je kijkt naar App.vue, dan kan je zien dat zo een Vue component bestaat uit 3 onderdelen:

- template
- script
- style

Er zijn er nog meer dan dat en we hebben ze niet allemaal nodig. Onder template staat de html voor de component. We kunnen daar dus de html copy-pasten van onze smiley (vergeet die wel niet tussen de `template` tags te steken):

    <template>
        <g transform="translate(480, 250)">
            <circle r=245 fill=yellow stroke=black stroke-width=10>
            </circle>
            <circle cx=-130 cy=-100 r=40 fill=black />
            <circle cx=130 cy=-100 r=40 fill=black />
        </g>
    </template>

Daaronder zetten we nog het volgende:

    <script>
    export default {
    name: 'Face',
    components: {
        
    }
    }
    </script>

Wat doet dit nu eigenlijk? De `script` tags herken je wellicht van javascript en dit is de eigenlijke Vue-code die de html tussen de `template` tags staat blootstelt aan de rest van het project en hangt daar een naampje aan: `Face`.

Alles wat we nu gedaan hebben heeft geen enkele invloed op onze webpagina. Dat komt omdat alle html code voor onze pagina nog steeds in `App.vue` staat. Tijd om onze gloednieuwe `Face` component te gebruiken. 

Toen we ons project voor het eerst opstartten gebruikte `App.vue` al een andere component: `HelloWorld.vue`. Dat zag er toen zo uit:

    <script>
    import HelloWorld from './components/HelloWorld.vue'

    export default {
    name: 'App',
    components: {
        HelloWorld
    }
    }
    </script>

We zien dat er dus 2 zaken nodig zijn om de `Face` component te kunnen gebruiken. Het eerste is een `import` statement, het andere is het het gebruik van de component declareren in de `components` lijst. We passen dus `App.vue` aan:

    <script>
    import Face from './components/Face.vue'
    export default {

    name: 'App',
    components: {
        Face
    }
    }
    </script>

Dat zijn al veel aanpassingen zonder te testen... Dus laat ons nog 1 kleine aanpassing maken zodat we de impact kunnen zien, en het is de beste aanpassing: we gaan de html in App.vue aanpassen zodat daar ons nieuwe Face element gebruikt wordt:

    <template>
    <div id="app">
        <svg width=960 height=500>
            <Face />  
        </svg>
    </div>
    </template>

Run opnieuw je project en je zal zien dat je nog steeds een smiley hebt. Misschien is dat niet zo spannend voor de doorsnee gebruiker, maar wij weten beter. De html maakt nu gebruik van een html element dat we zelf gedefinieerd hebben.

## 5. Componenten & Data

We zijn al een stap verder! We hebben nu een eigen html element. Helaas kunnen we nog niet veel met dit element. De volgende code zal bijvoorbeeld drie gezichtjes tekenen, maar telkens op dezelfde plaats waardoor we maar 1 gezichtje zien:

    <template>
    <div id="app">
        <svg width=960 height=500>
            <Face />  
            <Face />  
            <Face />  
        </svg>
    </div>
    </template>

### Coordinaten
We zouden dus graag data doorgeven aan ons html element: de x-coordinaat van het gezicht. Het eerste wat we moeten doen is de code van onze Face component uitbreiden zodat die ook `props`, oftewel properties, blootstelt:

    <script>
    export default {
    name: 'Face',
    props: {
        'cx': Number,
        'cy': Number
    },
    components: {
        
    }
    }
    </script>

Dit zorgt ervoor dat ons html element 2 eigenschappen heeft (van het type Number, of getal) waar we een getal aan kunnen toewijzen. We kunnen daar waarde aan toewijzen op dezelfde manier als andere html eigenschappen. We passen dus `App.vue` aan als volgt:

    <template>
    <div id="app">
        <svg width=960 height=500>
            <Face cx=480 cy=250 />  
        </svg>
    </div>
    </template>

Helaas werkt dit niet. Vue heeft wat hulp nodig en moet weten welke eigenschappen doorgegeven moeten worden aan componenten. We moeten daarvoor een Vue directive toevoegen, dat is dus wat extra boekhouding. Gelukkig is dat heel simpel om te doen: we marken de eigenschappen met `v-bind:`.

    <template>
    <div id="app">
        <svg width=960 height=500>
        <Face v-bind:cx=480 v-bind:cy=250 />  
        </svg>
    </div>
    </template>

Wat doet dit nu? Helemaal niets. We gebruik die properties namelijk nergens binnen ons Face element. Laat ons dat even aanpassen. De x- en y-coordinaat staat op dit moment binnen onze transform eigenschap:

    <g transform="translate(480, 250)">

Helaas is het niet voldoende om dit te doen:

    <g transform="translate(cx, cy)">

De waarde van transform is namelijk een string: `'translate(480, 250)'`. Als we daar rechtstreeks cx en cy inzetten worden die geinterpreteerd als tekst. We gaan daarom de tekst opdelen. In javascript kan je tet 'optellen' om die aan elkaar te hangen. Dus `'translate(480, 250)'` is hetzelfde als `'translate(' + '480, 250)'` of `'translate(' + 480 + ', ' + 250 + ')'`. Om de interpretatie als tekst te mijden schrijven we dus: `'translate(' + cx + ', ' + cy + ')'`:

    <template>
        <g v-bind:transform="'translate(' + cx + ', ' + cy + ')'">
            <circle r=245 fill=yellow stroke=black stroke-width=10>
            </circle>
            <circle cx=-130 cy=-100 r=40 fill=black />
            <circle cx=130 cy=-100 r=40 fill=black />
        </g>
    </template>

Merk op dat hier ook de `v-bind:` directive gebruikt moet worden.

### Andere properties

Tijd om een versnelling hoger te schakelen. We gaan nog extra properties toevoegen aan Face:

    <script>
    export default {
    name: 'Face',
    props: {
        'cx': Number,
        'cy': Number,
        'r': Number,
        'strokeWidth': Number,
        'eyeOffsetX': Number,
        'eyeOffsetY': Number,
        'eyeRadius': Number
    },
    components: {
        
    }
    }
    </script>

We gebruiken die properties ook op de gepaste manier in de html van onze Face-component:

    <template>
        <g v-bind:transform="'translate(' + cx + ', ' + cy + ')'">
            <circle v-bind:r=r fill=yellow stroke=black v-bind:stroke-width=strokeWidth>
            </circle>
            <circle v-bind:cx=-eyeOffsetX v-bind:cy=-eyeOffsetY v-bind:r=eyeRadius fill=black />
            <circle v-bind:cx=eyeOffsetX v-bind:cy=-eyeOffsetY v-bind:r=eyeRadius fill=black />
        </g>
    </template>

Ten slotte geven we de correcte waardes door vanaf `App.vue`:

    <template>
    <div id="app">
        <svg width=960 height=500>
        <Face 
            v-bind:cx=100 
            v-bind:cy=75 
            v-bind:r=50 
            v-bind:eye-offset-x=20 
            v-bind:eye-offset-y=10 
            v-bind:eye-radius=10
            v-bind:stroke-width=5 
        />  
        </svg>
    </div>
    </template>

We kunnen nu gemakkelijker bijvoorbeeld 3 gezichten naast elkaar zetten:

    <template>
    <div id="app">
        <svg width=960 height=500>
        <Face 
            v-bind:cx=100 
            v-bind:cy=75 
            v-bind:r=50 
            v-bind:eye-offset-x=20 
            v-bind:eye-offset-y=10 
            v-bind:eye-radius=10
            v-bind:stroke-width=5 
        />  
        <Face 
            v-bind:cx=250 
            v-bind:cy=75 
            v-bind:r=50 
            v-bind:eye-offset-x=20 
            v-bind:eye-offset-y=10 
            v-bind:eye-radius=10
            v-bind:stroke-width=5 
        />  
        <Face 
            v-bind:cx=400 
            v-bind:cy=75 
            v-bind:r=50 
            v-bind:eye-offset-x=20 
            v-bind:eye-offset-y=10 
            v-bind:eye-radius=10
            v-bind:stroke-width=5 
        />  
        </svg>
    </div>
    </template>

Er zijn een aantal zaken waar je hier voor moet oppassen:
- De namen van de props schrijven we in camel case, dus bijvoorbeel `eyeOffsetX`. Als we de property effectief binden (in `App.vue`), dan gebruiken we de notatie met liggende streepjes (kebab-case): `eye-offset-x`. Dat is wat vervelend en iets waar je voor moet uitkijken.
- Je kan een property binnen je component gebruiken als volgt: `cx=-eyeOffsetX` maar je kan die eveneens tussen " zetten: `cx="-eyeOffsetX"`. Nu komt de verwarring: we hadden toch net moeite met die " bij het gebruik van transform. Dat komt omdat transform een string verwacht en vue ook net graag die " voor expressies. Zo kan je bijvoorbeeld schrijven: `cx="50 + eyeOffsetX/2"`. Dat conflicteert soms, zoals het geval is bij de transform, en dan moeten we andere oplossingen zoeken zoals de string opdelen.

## 6. Uitdaging
Probeer hier nu een paar stapjes in verder te gaan. 

- Begin eens met het toevoegen van een `Eye` component die dan gebruikt wordt door de `Face` component die we gemaakt hebben.
- Maak ook de `fill` property een property van `Face`.
- Maak een rij van gezichtjes, allemaal met een andere afmeting en kleur. Bijvoorbeeld:


