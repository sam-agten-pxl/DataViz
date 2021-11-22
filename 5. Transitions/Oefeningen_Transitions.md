# Oefeningen D3 Transitions

## Oefening 1
Je leest opnieuw de data in van harry potter (`harry_potter.csv`, zie lab Data Processing). Je geeft de `revenue` weer van elke film met behulp van een cirkel. De grootte van de straal komt overeen met de revenue.

## Oefening 2
Breid oefening 1 uit. Voeg twee knoppen toe: "Revenue" & "Budget". Als je op Revenue klikt geven de cirkels de revenue weer van elke film, als je op budget klikt stelt de straal van elke cirkel het relatieve budget voor van de film. Zorg ervoor dat de straal geanimeerd overgaat. Voor Revenue gebruik je `tomato` als vulkleur met een `opacity` van 0.7. Voor Budget gebruik je `orange` als vulkleur met een `opacity` van 0.7.

## Oefening 3
Breid oefening 2 uit. In de plaats van alle films te tonen, tonen we bij Revenue nu enkel de films met een revenue die groter is `1000000000` en bij Budget tonen we enkel films een een budget groter dan `120000000`. Elementen die verdwijnen vallen naar beneden (`cy=500`).