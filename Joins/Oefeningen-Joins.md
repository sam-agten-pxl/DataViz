# Oefeningen Joins

Voor deze oefeningen gebruiken we de volgende statische data:

    cities = [
        { name: 'London', population: 8674000},
        { name: 'New York', population: 8406000},
        { name: 'Sydney', population: 4293000},
        { name: 'Paris', population: 2244000},
        { name: 'Beijing', population: 11510000}
    ];

## Oefening 1

We gaan de grootte van de deze steden weergeven met cirkels. Maak een nieuwe Vue app en zorg dat je een cirkel weergeeft voor elke cirkel. De straal van de cirkel komt overeen met de `population`. Bijvoorbeeld:

<svg width="800" height="100" class="chart"><circle cx="50" cy="50" r="34.696" style="fill: rgb(170, 170, 170);"></circle><circle cx="150" cy="50" r="33.623999999999995" style="fill: rgb(170, 170, 170);"></circle><circle cx="250" cy="50" r="17.172" style="fill: rgb(170, 170, 170);"></circle><circle cx="350" cy="50" r="8.975999999999999" style="fill: rgb(170, 170, 170);"></circle><circle cx="450" cy="50" r="46.04" style="fill: rgb(170, 170, 170);"></circle></svg>

Zorg dat je deze visualisatie ook in zijn eigen component steekt!

## Oefening 2

Voor deze oefening visualiseer je de data als bar chart (zie hieronder). Als je met de muis over een rechthoek beweegt kleurt deze oranje. De visualisatie zit in zijn eigen component.

<style>
.oef2 #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.oef2 body {
  font-family: "Helvetica Neue", Helvetica, sans-serif;
  font-size: 12px;
  color: #3333;
}

.oef2 .bars rect {
  fill: steelblue;
}

.oef2 .bars rect:hover {
  fill: orange;
}

.oef2 .labels text {
  text-anchor: end;
  fill: #fff;
}
</style>


<div class="oef2">
<svg width="800" height="300"><g transform="translate(70, 30)" class="bars"><rect height="19" width="346.96000000000004" y="0"></rect><rect height="19" width="336.24" y="20"></rect><rect height="19" width="171.72000000000003" y="40"></rect><rect height="19" width="89.76" y="60"></rect><rect height="19" width="460.40000000000003" y="80"></rect></g><g transform="translate(66, 30)" class="labels"><text y="13">London</text><text y="33">New York</text><text y="53">Sydney</text><text y="73">Paris</text><text y="93">Beijing</text></g></svg>
</div>
