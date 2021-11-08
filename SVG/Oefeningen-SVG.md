# Oefeningen SVG

Hieronder vind je enkele oefeningen over SVG elementen in html. Je kan elke oefeningen in pure html maken maar we raden aan dat je de oefeningen in Vue maakt, zoals geleerd in de les. We gebruiken niet alleen de cirkel die gebruikt werd als voorbeeld uit de les. Voor meer informatie over het gebruik van andere SVG elementen zoals de rechthoek, zie: https://www.w3schools.com/graphics/svg_rect.asp.

## 1. Bar chart
Voor deze oefening maak je de volgende simpele bar chart:

<svg width = 800 height=110>
    <rect width=20 height=100 x=0 fill=orange />
    <rect width=20 height=30 x=30 y=70 fill=orange />
    <rect width=20 height=60 x=60 y=40 fill=tomato />
    <rect width=20 height=80 x=90 y=20 fill=orange />
</svg>

Maak gebruik van het `rect` element, zie: https://www.w3schools.com/graphics/svg_rect.asp. Maak de bar chart eerst rechtstreeks in `App.vue`. Maak dan een `Bar` component en bouw de bar chart op met 4 van die `Bar` componenten. Zorg dat je voor elke bar de hoogte en de x-positie kan instellen.

## 2. Polyline
Met het SVG-element `polyline` kan je lijndiagrammes tekenen. Probeer met behulp van https://www.w3schools.com/graphics/svg_polyline.asp uit te zoeken hoe je onderstaande vorm kan namaken in html:

<svg width = 800 height=130>
    <polyline points="1,105,50,30,100,75,120,50,180,105,0,105" style="fill:none; stroke:tomato; stroke-width:3" />
</svg>

## 3. Labels
SVG ondersteunt ook labels met behulp van SVG text (https://www.w3schools.com/graphics/svg_text.asp). Herneem oefening 1 en voeg labels toe elke bar:

<svg width = 800 height=150>
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

Breid je `Bar` component uit zodat je ook een label kan meegeven als property.