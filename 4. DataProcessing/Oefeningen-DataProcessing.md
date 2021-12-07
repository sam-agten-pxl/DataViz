# Oefeningen: Data Processing

De oefeningen voor data processing zijn niet visueel en volgen steeds hetzelfde stramien:
- Lees de csv file in
- Voer transformaties uit op de data
- Filter de data
- Print het resultaat in de console om je oplossing te controleren. 

Je krijgt per oefening steeds enkele indicaties om je oplossing te controleren.

## Oefening 1

Transformeer de data uit `movies.csv` zodat je enkel de kolommen `title`, `vote_average`, `vote_count` en `homepage` opneemt. Filter de data zodat je enkel overblijft met de film die minstens 1000 stemmen hebben gekregen en die een homepage hebben. Je zou dan moeten overblijven met <b>490</b> films.

## Oefening 2

Nu maken we het wat complexer. We zijn geinteresseerd in de kolommen `production_countries`, `title` en `release_date`. Al deze waardes zijn strings als we ze uitlezen van de csv. We gaan dus eerst wat dataconversie moeten toepassen.

Als we kijken naar de waardes in de csv zien we dat `release_date` een datum is dus die willen we omzetten naar een javascript datum object zodat we daar in onze code mee kunnen rekenen.
Je kan string objecten omzetten naar effectieve datum objecten met behulp van `d3.timeParse`. Die functie verwacht dat je formaat aanlevert van de datum zoals die in de string staat. De string heeft namelijk een bepaalde datumnotatie zoals dag-maand-jaar. Het volgende voorbeeld converteer de datum 31 december 2000 van een string naar een datum object:

    const date = d3.timeParse('%Y-%m-%d')('2000-12-31')

Als je meer uitleg wilt over de notatie van het formaat (`'%Y-%m-%d'`) kan je best eens een kijkje nemen naar de documentatie: https://github.com/d3/d3-time-format

`production_countries` is eigenlijk een JSON object. Strings die in JSON formaat staan, zoals de `production_countries` kan je omvormen naar javascript objecten met behulp van `JSON.Parse`. Bijvoorbeeld:

    const obj = JSON.parse('{"name":"John", "age":30, "city":"New York"}');

Lees opnieuw de data in van `movies.csv` en behoud enkel de kolommen `title`, `production_countries` en `release_date`. Zorg ervoor dat `production_countries` en `release_date` converteer naar de juiste datatypes zoals hierboven beschreven.

Filter daarna de data zodat je enkel de films behoudt die onder andere gemaakt zijn in Duitsland (maw: Duitsland staat tussen de production countries), na het jaar 2005. Je zou dan moeten overblijven met <b>785</b> films.

Als je wat moeite hebt met de filtering zijn hier enkele tips:
- Met de methode `getFullYear()` krijg je van een javascript date object het jaartal (4 cijfers)
- Het object in `production_countries` is een array met daarin objecte die bestaan uit 2 velden: `iso_3166_1` en `name`. Als de `iso_3166_1` code gelijk is aan "DE", is Duitsland een productieland.

## Oefening 3

Voor deze oefening laad je de data `abonnees_genk.csv` in. We zijn enkel geinteresseerd in de kolom `Postcode`. Lees alle data in behoudt enkel die kolom. Converteer `Postcode` ook naar een getal. Filter de data zodat je enkel overblijft met geldige postcodes. Een postcode is geldig indien die bestaat uit 4 cijfers. Op dit punt zou je moeten overblijven met <b>12 145</b> datapunten/abonnees.

Gebruik nu de `rollup` functie om te tellen hoeveel abonnees er zijn per postcode. In de plaats van `sum` kan je hier `count` gebruiken om de hoeveelheid abonnees per groep te tellen. Je zou nu een array moeten hebben die <b>292</b> elementen bevat. Elk element in die rij is een een postcode en een hoeveelheid abonnees.

Sorteer ten slotte deze rij van elementen van het meeste abonnees naar het minste abonnees. Ter controle: controleer het vierde element in je rij (index 3!). Dat zou postcode <b>3680</b> moeten zijn met <b>473</b> abonnees.

