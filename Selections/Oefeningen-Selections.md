# Oefeningen D3 Selecties

## Oefening 1

Start met een eenvoudige Vue applicatie. Pas de html aan van `App.vue` zodat je 5 grijze cirkels toont:

<svg width="760" height="140">
    <g transform="translate(70, 70)">
        <circle r="40" style="fill:#dddddd" />
        <circle r="40" style="fill:#dddddd" cx="120" />
        <circle r="40" style="fill:#dddddd" cx="240" />
        <circle r="40" fill=#dddddd cx="360" />
        <circle r="40" fill=#dddddd cx="480" />
    </g>
</svg>

Gebruik nu D3 selections om de stijl van de cirkels te veranderen zodat:
- De straal van alle cirkels 20 wordt
- De eerste cirkel oranje wordt
- De derde cirkel een stroke krijgt van `#555555` en een stroke-width van 2

<svg width="760" height="140">
    <g transform="translate(70, 70)">
        <circle r="20" style="fill:orange" />
        <circle r="20" style="fill:#dddddd" cx="120" />
        <circle r="20" style="fill:#dddddd; stroke:#555555; stroke-width:2"  cx="240" />
        <circle r="20" fill=#dddddd cx="360" />
        <circle r="20" fill=#dddddd cx="480" />
    </g>
</svg>

## Oefening 2

Maak opnieuw een vue app. Zorg dat de html enkele zwarte rechthoeken onder elkaar toont:

<svg width="760" height="190">
    <g transform="translate(70, 20)">
        <rect width="30" height="30" y="0" />
        <rect width="30" height="30" y="40" />
        <rect width="30" height="30" y="80" />
        <rect width="30" height="30" y="120" />
    </g>
</svg>

Gebruik nu D3 om de rechthoeken trapsgewijs uit elkaar te zetten:

<svg width="760" height="190">
    <g transform="translate(70, 20)">
        <rect width="30" height="30" y="0" />
        <rect x=40 width="30" height="30" y="40" />
        <rect x=80 width="30" height="30" y="80" />
        <rect x=120 width="30" height="30" y="120" />
    </g>
</svg>

## Oefening 3

Maak een Vue-app en pas de html aan zodat we enkele cirkels hebben:

<svg width=500 height=100>
    <g transform="translate(50, 50)">
        <circle r=20 fill='orange'>
    </g>
    <g transform="translate(150, 50)">
        <circle r=20 fill='orange'>
    </g>
    <g transform="translate(250, 50)">
        <circle r=20 fill='orange'>
    </g>
</svg>

Gebruik D3 event handlers zodat de cirkel van straal verdubbelt als je er met de muis over beweegt. De kleur verandert dan tevens in `tomato`. Als je met de muis wegbeweegt herstelt de straal en de kleur.