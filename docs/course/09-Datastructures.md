# Lab 00
Datavisualisaties starten vanuit rauwe data die we inlezen vanuit een file of een WebAPI of een andere bron. Voordat we die data kunnen gebruiken moeten we gestructureerde samenvattingen maken van die data. Als we bijvoorbeeld kijken naar de Netflix data en we willen een antwoord geven op de volgende vraag:

Hoeveel TV series worden er gelanceerd per jaar en hoeveel films per jaar?

Om een antwoord te kunnen geven op die vraag moeten we de rauwe date eerst filteren, groeperen, ... structureren. Het is die gestructureerde data die de input zal vormen voor Chart.js en D3.js. In dit lab gaan we de fundamenten van datastructuren binnen javascript nog eens herhalen. Het gebruik van arrays, sets en maps, het gebruik van methodes om te filteren en te sorteren.

## Arrays
Een array is een simpele collectie van data. Dat kan een reeks zijn van primitieve data:

    const values = [10, 20, 30];

Of een array van objecten:

    const shows = [
      { title: "Stranger Things", type: "TV Show", year: 2016 },
      { title: "Roma", type: "Movie", year: 2018 }
    ];

Enkele essentiele methodes van arrays zijn `map`, `filter`, `reduce` en `sort`. 

### Map
Met de `map` functie transformeer je elk element in een array naar een nieuw element. Je kan objecten transformeren naar primitieven, je kan values filteren uit elk datapunt, je kan velden toevoegen aan elk object, etc. Gebruik `map` als je een aanpassing wilt maken aan elk element in de array.


    const values = [10, 20, 30];
    const newValues = values.map(x => x + 5);

Dit voorbeeld neemt elk element uit de array values, x, en telt er 5 bij op. `map` genereert een nieuwe array, dus de oorspronkelijke array blijft ongewijzigd. Denk eraan dat de functie die als input dient voor `map` een shorthand is voor:

    (x) -> {
        return x + 5;
    }

We kunnen dus bijvoorbeeld converteren naar objecten op de volgende manier:

    const newValues = values.map((x) => {
        return {
            age: x
        }
    });

Denk eraan dat je ook een feitelijk functie kan schrijven en die meegeven, maar dan moet je wel een goede naam verzinnen voor de functie:

    function remapValue(x) {
        return x + 5;
    }

En je gebruikt die dan als volgt:

    const newValues = values.map(remapValues);

Map is handig als we nieuwe, berekende velden willen toevoegen (bijvoorbeeld de leeftijd van een persoon op basis van de leeftijd), veldjes willen uitfilteren (we zijn bijvoorbeeld niet geinteresseerd in de geboortedatum) of een volledige conversie willen van het datatype (bijvoorbeeld objecten converteren naar een getal, of een object naar een ander object).

✅ Gebruik `map` om van de array `shows` een array te maken die alleen uit de titels van de films bestaat.
✅ We zijn ons aan het voorbereiden op een datavisualisatie die de totale kijktijd vergelijkt van series en films. We krijgen de volgende data:

    const titles = [
        { title: "City of Code", type: "Movie", duration: 110 },
        { title: "Data Detectives", type: "TV Show", duration: 3 },
        { title: "The Last Algorithm", type: "Movie", duration: 95 },
        { title: "AI Explained", type: "TV Show", duration: 2 }
    ];

Voor films interpreteren we `duration` als de duur in minuten, voor series is de duur het aantal seizoenen van die serie. We gaan er van uit dat elke serie een seizoen heeft dat bestaat uit 10 afleveringen. Elke aflevering duurt 45 minuten. Transformeer deze data met behulp van `map` naar iets bruikbaars. De output is van de volgende vorm:

    [
        { title: "City of Code", type: "Movie", estimatedMinutes: 110 },
        { title: "Data Detectives", type: "TV Show", estimatedMinutes: 1350 },
        { title: "The Last Algorithm", type: "Movie", estimatedMinutes: 95 },
        { title: "AI Explained", type: "TV Show", estimatedMinutes: 900 }
    ]

✅ Pas de titels nog aan van bovenstaande data, zodat de titel tussen haakjes het type bevat. Bv: "City of Code (Movie)".

### Filter
Filter gebruik je om 'rijen' uit je data te filteren, om elementen ut een array te halen. Dankzij filter zal je array kleiner worden of hetzelfde blijven. Filter neemt als input een functie die voor elk elementje uitgevoerd gaat worden. Als die functie voor een elementje `true` teruggeeft, dan zal dat elementje behouden blijven. Geeft die functie `false`, dan zal het elementje verwijderd worden uit de array.

    const newValues = values.filter(x => x > 15);

In dit voorbeeld overlopen we elke x uit `values`. Voor elk elementje, x, kijken we of dat elementje groter is dan 15, zoja, dan geeft die functie `true` terug en wordt het elementje dus behouden.
Wat gebeurt in de volgende situaties volgens jou?

    const newValues = values.filter(x => true);

    const newValues = shows.filter(x => x.type == 'Movie');

    const newValues = shows.filter(x => x);

    const newValues = shows.filter(x => x > 15);

Analyseer zeker de laatste 2 voorbeelden! Probeer ze eens uit en console log het resultaat eens. 

✅ Gebruikt de volgende data:

    const titles = [
        { title: "City of Code", type: "Movie", releaseYear: 2020, rating: "PG-13", country: "United States", duration: 110 },
        { title: "Data Detectives", type: "TV Show", releaseYear: 2018, rating: "TV-MA", country: "United Kingdom", duration: 3 },
        { title: "The Last Algorithm", type: "Movie", releaseYear: 2022, rating: "R", country: "Canada", duration: 95 },
        { title: "AI Explained", type: "TV Show", releaseYear: 2021, rating: "TV-PG", country: "United States", duration: 2 },
        { title: "Parallel Worlds", type: "Movie", releaseYear: 2019, rating: "PG", country: "France", duration: 130 },
        { title: "Neural Nights", type: "Movie", releaseYear: 2021, rating: "PG-13", country: "United States", duration: 105 },
        { title: "Quantum Dreams", type: "TV Show", releaseYear: 2020, rating: "TV-MA", country: "Germany", duration: 4 }
    ];

Gebruik filter om data over te houden die voldoet aan de volgende criteria:
- Uitgebracht in of na 2020
- Als het film is, een film die langer duurt dan 100 minuten, als het een serie is, een serie met minstens 3 seizoenen
- Geproduceerd in de Verenigde Staten of het Verenigd Koninkrijk
- Als rating PG-13, TV-PG of TV-MA heeft.

✅ Maak gebruik van een herbruikbare functie voor bovenstaande oefening, dus geen lambda functie of anonieme functie.

### Reduce
De reduce functie neemt een array en reduceert die array tot een enkele waarde. Goede voorbeelden van reduce zijn het berekenen van de som van elk element in een array, het gemiddelde, etc.
De reduce functie neemt tevens een functie als input, weliswaar met meer dan 1 parameter. Om die functie goed te snappen gaan we die even apart zetten. Beschouw de volgende array:

    const v = [1,2,3,4];

En de volgende functie:

    const agg(accumulator, currentValue) {
        return accumulator + currentValue;
    }

Dit is een som functie.

    agg(3, 4);

geeft bijvoorbeeld 7 als resultaat. We gebruiken deze functie als input voor reduce:

    v.reduce(agg);

Wat gebeurt er nu achter de schermen? Reduce zal deze functie aanroepen voor elk element in de array `v`, net zoals bij `map` of `filter` gebeurt. De eerste parameter van deze functie, handig accumulator genoemd, zal dienen als doorgeefluik en het resultaat van elke aanroep doorgeven aan de volgende aanroep. We maken dat even concreet, voor elke lus van reduce. De `agg` functie wordt 1 keer opgeroepen voor elke element in de rij met uitzondering van het laatste element, het zal zo meteen wel duidelijk worden waarom:

- 1e lus:
accumulator = 1, currentValue = 2
Bij de eerste aanroep is accumulator gelijk aan het eerste element en currentValue gelijk aan het tweede element. het result van de aanroep is 3, die wordt doorgegeven aan...
- 2e lus:
accumulator = 3, currentValue = 3
Bij de tweede aanroep is accumulator gelijk aan het resultaat van de vorige aanroep (3) en currentValue krijgt de waarde van het volgende element in de array: 3. Het resultaat is 6. 
- 3e lus:
accumulator = 6, currentValue = 4
Bij de derde aanroep is accumulator gelijk aan het resultaat van de vorige aanroep (6) en currentValue krijgt de waarde van het volgende element in de array: 4. Het resultaat is 10.

We hebben nu elk element gehad en het uiteindelijke resultaat is 10. Deze reducer berekent dus de som van de elementen in de array. Reduce is niet zo simpel om te lezen dus hieronder staan  nog wat voorbeelden.
Let op dat we in deze voorbeeld de functies apart gezet hebben en niet gebruik hebben gemaakt van anonieme functies/lambda expressies.


    const numbers = [175, 50, 25];

    const result = numbers.reduce(myFunc);

    function myFunc(total, num) {
      return total - num;
    }

In dit voorbeeld vertrekken we vanuit het eerste element 175 en trekken er dan elk element vanaf: 175 - 50 = 125 - 25 = 100.
In het volgende voorbeeld berekenen we opnieuw het totaal, maar we ronden elk getal af. Let op dat er hier een extra argument wordt meegegeven aan de `reduce` functie. Die extra parameter (0), bepaalt de startwaarde van accumulator.

    const numbers = [15.5, 2.3, 1.1, 4.7];
    const result = numbers.reduce(getSum, 0);

    function getSum(total, num) {
      return total + Math.round(num);
    }

✅ We zijn ons aan het voorbereiden op een datavisualisatie die de data weergeeft per jaar. We krijgen de volgende data:

    const titles = [
        { title: "City of Code", type: "Movie", duration: 110 },
        { title: "Data Detectives", type: "TV Show", duration: 3 },
        { title: "The Last Algorithm", type: "Movie", duration: 95 },
        { title: "AI Explained", type: "TV Show", duration: 2 }
    ];

Voor films interpreteren we `duration` als de duur in minuten, voor series is de duur het aantal seizoenen van die serie. Gebruik `reduce` om data te verzamelen over elk jaar. De output moet de volgende vorm hebben:

    {
        2018: { totalTitles: 1, movies: 0, tvShows: 1, totalMinutes: 1350 },
        2019: { totalTitles: 1, movies: 1, tvShows: 0, totalMinutes: 130 },
        2020: { totalTitles: 2, movies: 1, tvShows: 1, totalMinutes: 1910 },
        2021: { totalTitles: 2, movies: 1, tvShows: 1, totalMinutes: 1005 },
        2022: { totalTitles: 1, movies: 1, tvShows: 0, totalMinutes: 95 }
    }

### Sort
Sort sorteert. Dat lijkt me evident. Maar de syntax van sort kan wat verwarrend zijn. Net als `reduce` verwacht `sort` een functie met 2 inputparameters. Die 2 parameters worden ingevuld door 2 elementjes uit de lijst en moeten duidelijk maken hoe we kunnen bepalen welk element voor het andere moet komen in de sortering. Voor getallen is een volgorde evident: 4 is groter dan 3. Maar moeten we 'Kat' sorteren voor of na 'Hond'? Als we films sorteren, op basis van wat doen we dat dan? Productiebudget? Jaartal van de release? Hoe zit het dan met 2 films die in hetzelfde jaar uitgekomen zijn?

de functie die `sort` verwacht ziet er zo uit:

    function compare(a, b) {
        return a - b;
    }

Deze functie moet een getal teruggeven, als dat getal...

- 0 is dan moeten `a` en `b` beschouwd worden als gelijk wat betreft sortering
- minder dan 0 is, dan zou `a` VOOR `b` moeten komen
- meer is dan 0, dan zou `a` NA `b` moeten komen

Sorteren op basis van jaar zou dus als volgt kunnen:

    shows.sort((a,b) => {
        return a.year - b.year;
    })

Sorteert dit voorbeeld van hoog naar laag? Of van laag naar hoog?

### Oefeningen
Werk voor de oefeningen met onderstaande dataset:


    const titles = [
      {
        id: 1,
        title: "City of Code",
        type: "Movie",
        releaseYear: 2020,
        rating: "PG-13",
        country: "United States",
        duration: 110
      },
      {
        id: 2,
        title: "Data Detectives",
        type: "TV Show",
        releaseYear: 2018,
        rating: "TV-MA",
        country: "United Kingdom",
        duration: 3 // seasons
      },
      {
        id: 3,
        title: "The Last Algorithm",
        type: "Movie",
        releaseYear: 2022,
        rating: "R",
        country: "Canada",
        duration: 95
      },
      {
        id: 4,
        title: "AI Explained",
        type: "TV Show",
        releaseYear: 2021,
        rating: "TV-PG",
        country: "United States",
        duration: 2
      },
      {
        id: 5,
        title: "Parallel Worlds",
            type: "Movie",
            releaseYear: 2019,
            rating: "PG",
            country: "France",
            duration: 130
          },
          {
            id: 6,
            title: "Neural Nights",
            type: "Movie",
            releaseYear: 2021,
            rating: "PG-13",
            country: "United States",
            duration: 105
          },
          {
            id: 7,
            title: "Quantum Dreams",
            type: "TV Show",
            releaseYear: 2020,
            rating: "TV-MA",
            country: "Germany",
            duration: 4
          },
          {
            id: 8,
            title: "The Visualizers",
            type: "Movie",
            releaseYear: 2023,
            rating: "PG",
            country: "Japan",
            duration: 98
          }
    ];

Genereer de volgende structuren:

✅ Genereer een array die enkel de `releaseYear` bevat.

✅ Filter op shows die enkel na 2015 zijn uitgebracht.

✅ Sorter op `releaseYear`.

✅ Tel het aantal titels per `rating`.


## Maps
Ah...maps. Maps zijn een geliefde datastructuur en eentje die D3 vaak terug zal geven als resultaat. Een map is een datastructuur die werkt op basis van key-value pairs. Je zal mogelijks ook de termen HashMap of Dictionary tegenkomen in je carriere, ook dat zijn datastructuren die volgens datzelfde key-value pair principe werken. Maar wat is dat nu precies?

Een traditionele array gebruikt een `index` om een element aan te spreken:

    const values = [1,3,5,7];

In dit voorbeeld verwijzen we naar het element met de waarde '3' door middel van de index '1':

    values[1];

We bepalen niet rechtstreeks de index van een element uit de array, dat wordt bepaald door de plaats die het element heeft in de array. In dit geval is de index de 'key' en het element de 'vvalue'. Bij een key-value datastructuur bepalen we zelf per element wat de key moet zijn. We bekijken een voorbeeld:

    const map = new Map();
    map.set("Movie", 300);
    map.set("TV Show", 120);

Onze map start als een lege datastructuur. Daarna voegen we twee elementjes toe: 300 en 120. Bij het toevoegen van een element geven we steeds een 'key' mee. Dit is de waarde die je moet gebruiken om het element aan te spreken:

    map["Movie"];       //Dit geeft 300 als resultaat
    map["TV Show"];     //Dit geeft 120 als resultaat

We kunnen een map steeds converteren naar een array, maar dan moeten we specifieren wat we willen doen met de key en wat we willen doen met de value:
    
    const arr = Array.from(map, ([key, value]) => ({
      category: key,
      count: value
    }));

in dit geval zal `arr` er als volgt uitzien:

    [
        {
            category: Movie,
            count: 300
        },
        {
            category: TV Show,
            count: 120
        }
    ]

Map is een handige manier om ook groepen voor te stellen: alle data per regio, alle data per type, etc. Waarbij je in die voorbeelden de regio of het type gebruikt als key om aan de geassocieerde data te geraken. Bv:

    const map = new Map();
    map.set("North", [
    {
        sales: 100,
        region: North,
        product: TV
    },
    {
        sales: 120,
        region: North,
        product: Radio
    }]);
    map.set("South", [
    {
        sales: 80,
        region: South,
        product: TV
    },
    ]);

✅ Groepeer de array 'titles' op basis van type en steek die in een map.
✅ Converteer deze data naar een array
✅ Werk opnieuw met de volgende data:

    const titles = [
        { title: "City of Code", type: "Movie", releaseYear: 2020, duration: 110, rating: "PG-13" },
        { title: "Data Detectives", type: "TV Show", releaseYear: 2018, duration: 3, rating: "TV-MA" },
        { title: "The Last Algorithm", type: "Movie", releaseYear: 2022, duration: 95, rating: "R" },
        { title: "AI Explained", type: "TV Show", releaseYear: 2021, duration: 2, rating: "TV-PG" },
        { title: "Parallel Worlds", type: "Movie", releaseYear: 2019, duration: 130, rating: "PG" }
    ];

Maak een geneste mapstructuur van deze data met als eerste key het land en als tweede key het type. De output ziet er dus zo uit:

    Map {
        "United States" => Map {
            "Movie" => [ ... ],
            "TV Show" => [ ... ]
        },
    "United Kingdom" => Map {
            "TV Show" => [ ... ]
        }
    }

✅ Gebruik je blitse nieuwe datastructuur om gemakkelijk antwoord te kunnen geven op de volgende vragen:
- Geef alle films die in de Verenigde Staten zijn gemaakt
- Geef alle series die geproduceerd zijn in Europa
- Hoeveel titels zijn er per land?

## Sets
Sets gedragen zich als arrays met een belangrijk verschil: ze mogen geen duplicaten bevatten. Het leuke aan een set is dus dat als je elementen probeert toe te voegen die er al in zitten, dan worden ze niet opnieuw toegevoegd. Met de `has` methode kan je zelf controleren of een element reeds in de set zit.

    const arr = [1,2,3,3,4,4,4];
    const s = new Set(arr)

In bovenstaand voorbeeldje maken we een nieuwe Set en als input geven we al meteen een verzameling mee, een array die enkele getallen bevat. De set zal nu enkel de getallen 1, 2, 3 en 4 bevatten. Sets kunnen ook gecombineerd worden met elkaar om het verschil tussen 2 verzamelingen te krijgen, bijvoorbeeld, of de doorsnede. Je kan ook controleren of een Set een subset is van een andere set, enzovoort. We wijden daar wel niet heel ver over uit in deze cursus omdat Sets in dit vak niet vaak aan bod komen. Voor verdere informatie verwijs ik je graag naar de officiele JS documentatie over Sets. Sets gebruiken we voor deduplicatie, het nagaan of iets een onderdeel is van iets anders of relaties te ontdekken.

✅ Voor deze oefening ga je zelf wat opzoekingswerk moeten doen en wat meer uitzoeken over hoe je Sets kan gebruiken. Gebruik de volgende 2 datasets:

    const usCatalog = [
        { title: "City of Code", type: "Movie" },
        { title: "Data Detectives", type: "TV Show" },
        { title: "Neural Nights", type: "Movie" },
        { title: "Quantum Dreams", type: "TV Show" }
    ];

    const euCatalog = [
        { title: "City of Code", type: "Movie" },
        { title: "Parallel Worlds", type: "Movie" },
        { title: "Quantum Dreams", type: "TV Show" },
        { title: "AI Explained", type: "TV Show" }
    ];

Maak van deze arrays Sets. Gebruik deze sets om antwoord te geven op de volgende vragen:
- Alle titels die beschikbaar zijn in beide regios
- Titels die enkel beschikbaar zijn in de VS
- Titels die enkel beschikbaar zijn in de EU
- Titels die uniek zijn voor een regio
