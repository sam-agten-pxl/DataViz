# Lab D3 Transitions

Tijd voor mijn favoriete feature van D3: animaties. Animaties maken het verkennen van data niet alleen een ervaring, maar ze kunnen ook helpen bij de interpretatie van de data. 

## Overgang tussen data
Animatie komt in het spel als we een overgang willen maken tussen twee representaties of twee datasets. Ondertussen kan je al wat met D3 dus hakken we de details wat weg. 

We hebben al gewerkt met data die we uitlezen vanuit een bestand en ook al met statische data. We kunnen ook werken met willekeurige data:

    <script>

    export default {
    name: 'App',
    components: {
    },
    methods: {
        randomizedData : function()
        {
        let data = [];

        for(let i = 0; i < 5; i++)
        {
            data.push(Math.random() * 800);
        }

        return data;
        }
    }
    }
    </script>

We hebben hier een methode toegevoegd `randomizedData` die een rij teruggeeft van willekeurige getallen tussen 0 en 800. We kunnen die `randomizedData` dus gebruiken in onze join. Join deze "data" om 5 cirkels te tekenen. Je gebruikt de data om de x positie te bepalen. Bijvoorbeeld:


<svg width="800" height="100"><circle r="30" cy="50" cx="151.69701211646415" style="fill: tomato;"></circle><circle r="30" cy="50" cx="663.964250644939" style="fill: tomato;"></circle><circle r="30" cy="50" cx="103.76184202131427" style="fill: tomato;"></circle><circle r="30" cy="50" cx="658.5592418888091" style="fill: tomato;"></circle><circle r="30" cy="50" cx="407.46111345111336" style="fill: tomato;"></circle></svg>

Probeer dit eerst zelf! De D3 code die ik gebruikt heb vind je hieronder.

    drawCircles : function()
    {
      d3.select("svg")
        .selectAll("circle")
        .data(this.randomizedData())
        .join("circle")
        .attr("r", 50)
        .attr("cy", 50)
        .style("fill", "tomato")
        .attr("cx", function(d) {
            return d;
        })
    }

Elke keer als je nu de pagina refreshed wordt de randomized data opnieuw berekend en worden de cirkels dus op een nieuwe x-coordinaat getekend. We willen niet elke keer refreshen. Daarom voegen we even een button toe aan onze visualisatie. Als je daar op klikt zal de data opnieuw berekend worden. 
Daarvoor voegen we eerst een javascript methode toe aan onze component die opgeroepen zal worden als er op de knop geklikt wordt:

    clickButton : function() 
    {
      this.drawCircles();
    }

We voegen ook nog een button toe aan onze html:

    <button v-on:click="clickButton" >Update</button>

Er wordt hier een nieuwe vue directive gebruikt: `v-on`. Daarmee kunnen we het click event op de button binden aan een methode binnen onze component, in dit geval de `clickButton` component.

Als je nu klikt op de button zullen de cirkels een nieuwe positie krijgen. Voor de volledigheid is hieronder nog eens de volledige code van de component:

    <template>
    <div id="app">
        <svg width=800 height=300>
        <circle />
        </svg>
        <button v-on:click="clickButton" >Update</button>
    </div>
    </template>

    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    methods: {
        randomizedData : function()
        {
        let data = [];

        for(let i = 0; i < 5; i++)
        {
            data.push(Math.random() * 800);
                console.log(data[i]);
        }

        return data;
        },
        drawCircles : function()
        {
        d3.select("svg")
        .selectAll("circle")
        .data(this.randomizedData())
        .join("circle")
        .attr("r", 50)
        .attr("cy", 50)
        .style("fill", "tomato")
        .style("opacity", .7)
        .attr("cx", function(d) {
            return d;
        })
        },
        clickButton : function() 
        {
        this.drawCircles();
        }
    },
    mounted () {
        this.drawCircles();
    }
    }
    </script>

## Eerste transitie

Een transitie toevoegen in D3 is eigenlijk heel simpel. We passen onze `drawCircles` methode aan als volgt:

    d3.select("svg")
      .selectAll("circle")
      .data(this.randomizedData())
      .join("circle")
      .attr("r", 50)
      .attr("cy", 50)
      .style("fill", "tomato")
      .style("opacity", .7)
      .transition()
      .attr("cx", function(d) {
        return d;
      })

Vlak voordat we de `cx` instellen van onze cirkel hebben we een `transition()` oproep toegevoegd. Dat signaleert aan D3 dat alle `style` en `attr` oproepen die volgen (in ons voorbeeld enkel `cx`) geanimeerd moeten worden. Op dit moment wordt een default animatie toegepast op `cx`. Test dit eens uit en druk een paar keer op de knop. De cirkels zullen nu van plaats tot plaats animeren. Om dat te demonstreren zullen we nu een meerdere eigenschappen animeren. We gaan onze data wat complexer maken en gebruiken een object in de plaats van een getal:

    randomizedData : function()
    {
      let data = [];

      for(let i=0; i<5; i++) {
        data.push({
          x: Math.random() * 800,
          r: Math.random() * 40,
          fill: d3.rgb(Math.random() * 255, Math.random() * 255, Math.random() * 255)
        });
      }

      return data;
    }

Elk datapunt heeft nu dus 3 attributen: `x`, `r` en `fill`. We gaan die gebruiken voor onze cirkels en we stellen die eigenschappen in na een `transition()` call:

    d3.select("svg")
      .selectAll("circle")
      .data(this.randomizedData())
      .join("circle")
      .style("opacity", .7)
      .attr("cy", 50)
      .transition()
      .attr("cx", function(d) {
        return d.x;
      })
      .attr("r", function(d) {
        return d.r;
      })
      .style("fill", function(d) {
        return d.fill;
      })

<svg width="800" height="100"><circle cy="50" style="opacity: 0.7; fill: rgb(188, 88, 34);" cx="390.5505469765155" r="23.79031935434177"></circle><circle cy="50" style="opacity: 0.7; fill: rgb(240, 166, 7);" cx="176.80637913598912" r="23.35033207014563"></circle><circle cy="50" style="opacity: 0.7; fill: rgb(73, 144, 86);" cx="454.3362801013206" r="17.466416239407245"></circle><circle cy="50" style="opacity: 0.7; fill: rgb(40, 45, 8);" cx="382.7605947936952" r="15.435411002050206"></circle><circle cy="50" style="opacity: 0.7; fill: rgb(61, 216, 107);" cx="345.82007364621603" r="22.1240468909821"></circle></svg>

De animatie die we nu krijgen is de standaardanimatie van D3. We kunnen verdere controle uitoefenen over die animatie door de `duration` (hoe lang de animatie duurt) en de `delay` (hoe lang de animatie wacht eerdat die afspeelt) in te stellen. Beide eigenschappen kunnen we instellen met een methode op onze transition en beide verwacht een tijdsduur in miliseconden. Bijvoorbeeld:

    ...
    .transition()
    .delay(100)
    .duration(2000)
    ...

Nu zal de animatie 100ms wachten voordat hij start. De animatie zal ook 2000ms (2 seconden) duren.

We kunnen deze delay ook verschillend maken per element! Probeer onderstaande maar eens uit:

    ...
    .transition()
    .delay(function(d, i){
        return i * 75;
    })
    ...

Nu krijgt het de eerste cirkel een delay van 0ms, de tweede cirkel een delay van 75ms, de derde cirkel een delay van 150ms, etc. Hierdoor animeren de cirkels een voor een. Dat is een voorbeeld van animatie die kan helpen om de visualistie nuttiger te maken, we kunnen namelijk zien welk datapunt transformeert waardoor we de verandering kunnen koppelen aan de informatie. Neig!

## Easing

In een ander, fantastisch vak, hebben jullie al gezien dat aan een animatie niet alleen een timing gekoppeld is (die kunnen we instellen met `duration`) maar ook een easing. D3 geeft ons ook controle over de easing van de animatie. D3 gebruikt daarvoor een aantal ingebouwde easing functies zoals `d3.easeCircleIn`, `d3.easeBounceOut`, etc. Voor een volledige lijst van de easing functies zie: https://github.com/d3/d3-ease.

De easing toevoegen is heel erg simpel:

    ...
    .transition()
    .ease(d3.easeCircleIn)
    ...

Je kan ook je eigen tween functies definieren maar dat laten we hier buiten beschouwing.

## Animation Chaining

D3 geeft ons ook de optie om de transities te chainen, net zoals we methodes kunnen chainen. Als je meerdere `transition()` oproepen hebt in je D3 code dan worden die in volgorde afgehandeld en begint de volgende transitie pas als de voorgaande afgelopen is. We kunnen bijvoorbeeld de cirkels eerst laten veranderen van positie en daarna de straal aanpassen:

    d3.select("svg")
      .selectAll("circle")
      .data(this.randomizedData())
      .join("circle")
      .style("opacity", .7)
      .style("fill","tomato")
      .attr("cy", 50)
      .transition()
      .delay(function(d, i){
        return i * 75;
      })
      .attr("cx", function(d) {
        return d.x;
      })
      .transition()
      .ease(d3.easeBounce)
      .attr("r", function(d) {
        return d.r;
      })

Waanzin!

## Enter, Exit, Update

Als we nu op de update knop klikken verandert onze data, maar niet de hoeveelheid datapunten. Er zijn altijd 5 cirkels. Dit is dan ook maar een voorbeeld. Typisch wil je datasets uitwisselen en moet je visualisatie daar correct op kunnen reageren. Daarom moeten we ook een manier hebben om elementen (cirkels) toe te voegen en te verwijderen als de nieuwe dataset niet evenveel datapunten bevat. 
D3 voorziet daarvoor een relatief ingewikkeld concept door middel van `Enter`, `Exit` en `Update` methodes. Deze methodes leven in de `join` oproep en definiëren wat er moet gebeuren met elementen die toegevoegd worden, verwijderd worden of blijven staan.

    ...
    .join(
        function(enter) {
            ...
        },
        function(update) {
            ...
        },
        function(exit) {
            ...
        }
    )
    ...

In oudere versies van D3 moest je overigens altijd met deze functies werken, nu heb je dat enkel nodig voor transities. Let op, we gaan heel wat code moeten aanpassen vooraleer we opnieuw kunnen testen (dat vermijd je typisch omdat als er dan iets misgaat, kan het op verschillende plaatsen zijn misgelopen).

### Verschillende datapunten

We passen eerst onze `randomizedData` functie aan zodat er ook een willekeurig aantal datapunten wordt gegenereerd:

    randomizedData : function()
    {
      let data = [];
      let numItems = Math.ceil( Math.random() * 10);
      for(let i=0; i<numItems; i++) {
        data.push({
          x: Math.random() * 800,
          r: Math.random() * 40,
          fill: d3.rgb(Math.random() * 255, Math.random() * 255, Math.random() * 255)
        });
      }

      return data;
    }

Nu worden er maximum 10 datapunten aangemaakt.

### Enter

De enter functie krijgt een parameter binnen: `enter`. Dat stelt het element voor dat toegevoegd wordt. Dat is eigenlijk een "leeg" html element, dus geen cirkel. Om daar een cirkel van te maken moeten we `append` gebruiken:

    function(enter) {
        return enter.append('circle');
    }

We passen de rest van onze D3 code toe op deze binnenkomende elementen:

    function(enter){
        return enter.append("circle")
        .style("fill","tomato")
        .attr("cy", 50)
        .style("opacity", .7)
        .attr("cx", function(d) {
            return d.x;
            })
        .attr("r", function(d) {
            return d.r;
        })
    }

### Exit

In de exit functie krijgen we het element binnen dat verwijnt (omdat er cirkels teveel zijn). We moeten het element in die functie verwijderen. Als we dat niet doen zullen de cirkels die te veel zijn niet verdwijnen:

    function(exit){
        return exit.remove();
    }

We kunnen daar meteen een transitie aan koppelen. We kunnen de cirkels die weggaan bijvoorbeeld laten inkrimpen door hun straal op 0 te zetten:

    function(exit){
        return exit
        .transition()
        .delay(function(d, i){
            return i * 20;
        })
        .attr("r", 0)
        .remove()
    }

Merk op dat we de remove functie sparen tot het laatste.

### Update

Update laten we typisch gerust:

    function(update){
        return update;
    }

### Alles samen

Willen we zowel de bestaande elementen als de nieuwe elementen beïnvloeden, dan kan dat door de methodes achter de join te zetten zoals we gewoon zijn. We kiezen er hier voor om `enter` en `update` relatief leeg te laten en de uitgaande elementen een eigen animatie te geven:
    
    <script>
    import * as d3 from "d3"

    export default {
    name: 'App',
    components: {
    },
    methods: {
        randomizedData : function()
        {
        let data = [];
        let numItems = Math.ceil( Math.random() * 10);
        for(let i=0; i<numItems; i++) {
            data.push({
            x: Math.random() * 800,
            r: Math.random() * 40,
            fill: d3.rgb(Math.random() * 255, Math.random() * 255, Math.random() * 255)
            });
        }

        return data;
        },
        drawCircles : function()
        {
        d3.select("svg")
        .selectAll("circle")
        .data(this.randomizedData())
        .join(
            function(enter){
            return enter.append("circle");
            },
            function(update){
            return update;
            },
            function(exit){
            return exit
                .transition()
                .delay(function(d, i){
                return i * 20;
                })
                .attr("r", 0)
                .remove()
            })
        .style("fill","tomato")
        .attr("cy", 50)
        .style("opacity", .7)
        .transition()
        .attr("cx", function(d) {
            return d.x;
            })
        .attr("r", function(d) {
            return d.r;
        })
        },
        clickButton : function() 
        {
        this.drawCircles();
        }
    },
    mounted () {
        this.drawCircles();
    }
    }
    </script>