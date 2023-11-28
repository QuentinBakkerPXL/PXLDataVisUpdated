# D3 Data Processing

In het vorige lab hebben we gewerkt met statische data maar we zouden graag werken met data die we krijgen vanuit een (.csv) file of zelfs van een API. Gelukkig kan een javascript library, D3, ons daarbij helpen. Zet je strak in je stoel, dit is het moeilijkste en droogste gedeelte van dit vak.

## 1. D3

Maar Sam, we hebben het al moeilijk met Vue, moeten we nu echt weer iets nieuws toevoegen? 
Luister, File IO is niet van de poes. D3.js is een javascript library die ons kan helpen om gemakkelijker data te koppelen aan visuele elementen, maar heeft ook methodes om gemakkelijk csv files te verwerken. Er wordt wel eens gezegd dat D3 een grafische library is om grafieken te tekenen maar je zal snel merken dat d3 veel meer te bieden heeft dan dat. Sterker nog, als je op zoek bent naar een library om grafieken te tekenen raad ik aan dat je chart.js gebruikt. Een van de krachtigste tools die d3 aanbiedt is selecties: het selecteren van DOM-elementen uit onze html om dan eigenschappen van dat element te koppelen aan data, vergelijksbaar met SQL voor databases. Wij gaan D3 echter enkel en alleen gebruiken om onze .csv files in te lezen.

### D3 installeren

Ale, nog een laatste keer:
We starten met een nieuw vue project. 

    npm create vue@latest
    
Noem het project data-processing. Dan volgen onze standaardcommando's om pakketten te installeren en het project op te starten:

    cd data-processing
    npm install
    npm run dev

Vervolgens installeren we het d3 pakket voor ons project met behulp van node package manager:

    npm install d3

Controleer opnieuw even of je project start:

    npm run dev

## 2. CSV
CSV files, of `'comma seperated value'` zijn eigenlijk tekstbestanden die data bevatten. Elke regel in een csv bestand kan je vergelijken met de rij in een excel bestand. We duiden de kolomscheidingen aan met een `,`. Vandaar spreken we van `comma seperated`. Als we bijvoorbeeld de volgende excel data hebben:

    POSTCODE    CITY
    3680        Neeroeteren
    3620        Lanaken
    ...

Als een .csv file ziet er dat zo uit:

    POSTCODE,CITY
    3680,Neeroeteren
    3620,Lanaken
    ...

Geen magie dus.

### Eerste CSV inlezen
We werken in dit lab pas op het einde visueel, we zijn vooral geïnteresseerd in het verwerken van data. We schrijven onze code in `onMounted()` bij onze `App.vue`. Je kan opnieuw de voorgekauwde elementen verwijderen (zie vorige labs). Ter herinnering, voor onze `onMounted()` ziet dat er zo uit:

    <script setup>
    import { onMounted } from 'vue';

    onMounted(() => {
        console.log('hello')
    })

    </script>

Vooreerst hebben we wat gemakkelijke data nodig om mee te werken. Bij dit Lab vind je de csv file: `harry_potter.csv`. Dit is een eenvoudige csv file om mee te oefenen. Kopieer dit bestand naar de `public` folder van je project.

Het inlezen van deze data is gelukkig poepsimpel. D3 voorziet een methode `csv` om csv data in te laden. Die methode is echter async. Dat betekent dat het even duurt en het programma op de achtergrond zijn ding doet en dan pas later een resultaat terug geeft. Denk eraan, wat voor ons als meteen overkomt, kan voor een computer een oneindigheid zijn. Er zijn heel veel van dit soort async oproepen in front-end development, dus je kan daar beter aan wennen. Die async oproepen dienen ook vooral om het programma niet te blokkeren. We willen niet dat onze website blokkeert terwijl de data wordt ingeladen. 
Het inlezen van de data is een logische kandidaat voor een async methode omdat data files natuurlijk heel erg groot zijn en we willen niet dat het inlezen van de data de applicate volledig opeist en blokkeert. D3 noemt dit `promises` of beloftes. Vergelijk het met een ticket. Je bestelt bij D3 data verwerking en als die gereed is kan je je ticket inruilen voor het resultaat. De code voor het inladen van de csv file ziet er zo uit:

    <script setup>
    import { onMounted } from 'vue';
    import * as d3 from "d3"

    onMounted(async () => {
    const data = await d3.csv('harry_potter.csv');
    console.log(data);
    })

    </script>

Start je app, open je console, en normaal gezien krijg je dan de data uit de file te zien in je console (Het kan zijn dat je een promise krijgt, klap die wat open en ga op zoek naar het resultaat). Dat is goed nieuws want nu zit die data dus in een simpele array van objecten, zoals we gedaan hebben op het einde van het vorige lab. Daarmee is de cyclus compleet! We lezen data in van een CSV file naar een array van objecten. Die array van objecten kunnen we dan visualiseren. Laat ons die code eens bekijken en wat uitleggen. De code die deze magie veroorzaakt is dit:

    const data = await d3.csv('harry_potter.csv');
    console.log(data);

We moeten het javascript keyword `await` gebruiken omdat de uitvoer van de `csv` methode async is. Het is niet zo dat een async methode alleen maar opgeroepen kan worden met het `await` keyword. Typisch geef je het startschot van een async functie en lever je ook meteen een methode aan die uitgevoerd moet worden zodra het resultaat van de async methode ter beschikking is. Een alternatief is dat je het `await` keyword gebruikt. Dan zeg je in feite dat de uitvoer van de code mag blijven hangen op deze regel tot het resultaat beschikbaar is. Dat is niet hetzelfde als je programma blokkeren. Als deze code wordt uitgevoerd, wordt de code uitgevoerd tot aan het `await` keyword. Dan zal je compuer stoppen met het uitvoeren van de code en andere taken gaan uitvoeren. Na een tijdje keert de computer terug om te kijken of het resultaat er al is. Zoja, dan zal de uitvoer weer hervatten na het `await` keyword. Vergelijk het met pasta koken zonder timer. Je zet de pot op het vuur en keert regelmatig terug om te kijken of de pasta al gaar. Pas als de pasta gaar is, zal je de rest van het gerecht afwerken. Zonder `await` keyword ben je de pasta aan het koken met een timer. Je snijdt ondertussen de groentjes en als de timer afgaat keer je terug naar de pasta.

Het resultaat vangen we op in de variabele `data`. Ten slotte printen we het resultaat af. <b>Belangrijk:</b> Dankzij het `await` keyword kan de `onMounted()` functie niet in 1 keer afgewerkt worden door de computer. Er zit namelijk een natuurlijk wachtpunt/stoppunt in de functie: onze `await`. Javascript moet dit weten, zodat de `onMounted()` functie ook correct opgeroepen kan worden en behandeld worden door Vue. Daarom staat er nu een nieuw keyword bij onze `onMounted()` functie: `async`. Elke keer als je async functions gebruikt in mounted (of eender welke js functie) moet je dat aangeven door de methode te markeren als `async`.

Vergeet ook niet deze regel:

    import * as d3 from "d3"

...Anders kan je d3 niet gebruiken in je script. Het import statement zorgt ervoor dat d3 ingeladen wordt vanuit de pakketten.

### Typeconversie
We kunnen aan de slag met de data die we ingelezen hebben van de csv, maar zoals je al gemerkt hebt in de les, is de data die we krijgen typisch niet 'clean'. Dat komt omdat die data vaak door mensen wordt ingegeven en misschien staan de datums bijvoorbeeld niet allemaal in hetzelfde format of is een categorie soms met een hoofdletter geschreven en soms zonder hoofdletter. Daarom proberen we bij het visualiseren van de data altijd de data eerst op te schonen. 

Bij dit lab zit ook een andere csv file: `movies.csv`. Elke film in die dataset heeft een categorie, bijvoorbeeld actie, komedie, etc. We willen graag een visualisatie maken van de opbrengsten per categorie. Verdienen actiefilms bijvoorbeeld doorsnee meer geld dan komedies?

We laden eerst csv file in (Zet movies.csv dus ook in je `public` folder):

    const data = await d3.csv('movies.csv');

Deze data is verre van perfect. Zo zien we bijvoorbeeld dat sommige budgetten ingegeven zijn als tekst (strings) en niet als getallen (om dat te weten te komen moet je het bestand natuurlijk eens opendoen). De genres van de films zijn ook een json-object in tekstvorm. Als je niet goed weet wat ik daarmee bedoel, json is een manier om een javascript object textueel neer te schrijven, en is net als .csv eigenlijk niet zo speciaal.

Voordat we kunnen beginnen aan een visualisatie moeten we dus wat types omzetten naar bruikbare types. In D3 kunnen we aan de csv methode een functie meegeven die voor elke rij opgeroepen wordt voordat die in het eindresultaat komt. Daarmee kunnen we ook filteren op de data die we nodig hebben. We gaan daarvoor al eens een functie toevoegen aan onze component. We hebben nog al functies gebruikt, zoals `onMounted()` maar dat is een ingebouwde functie. Nu gaan we een volledig eigen creatie toevoegen. Spannend!

    <script setup>
    import { onMounted } from 'vue';
    import * as d3 from "d3"

    onMounted(async () => {
    const data = await d3.csv('movies.csv', typeConversion);
    console.log(data);
    })

    function typeConversion()
    {
    }

    </script>


### Nog even wat opfrissing over Javascript functies
Tijd voor  wat theorie over javascript functies/methodes (ik gebruik deze 2 termen als synoniemen)! Ik heb de functie `typeConversion` genoemd. Op dit moment doet die functie helemaal niks en wordt die ook nergens in de code gebruikt. 

Een functie is een miniprogrammatje. Dat betekent dat een functie input krijgt, berekeningen maakt en output teruggeeft. We gaan deze functie zo schrijven dat ze elke rij in het csv bestand gaat verwerken. De input voor ons programma is een rij uit ons bestand, de output is een javascript object dat de waardes bevat die wij interessant vinden. Als we naar de .csv file kijken zien we welke kolommen er allemaal zijn. Voor onze visualisatie is het voldoende om een object te hebben dat per film het genre bijhoudt, het budget, en de revenue. Zoiets dus:

    {
        genre: "...",
        budget: ...,
        revenue: ...
    }

We moeten ook input krijgen, dat gaat uiteindelijk de rij zijn van ons csv bestand. Input voor een funtie noemen we ook wel de parameters, die staan altijd tussen de haakjes. Als je dit moeilijk vindt, hier is een voorbeeld van een functie die we zonet gebruikt hebben:

    const data = await d3.csv('movies.csv');

Je leest dat best van rechts naar links. De functie is `csv`, de input voor die functie is `movies.csv` en de output, die steken wij in een variabele genaamd `data`. Wat daar precies inzit, wel dat hebben we net al bekeken in onze `console.log`. Onze `typeconversion` functie gaat dus ruwweg deze vorm moeten krijgen:

    function typeConversion(row)
    {
        return {
            genre: ...,
            budget: ...,
            revenue: ...
        }
    }

De `...` gaan we nog invullen. `row` is hier de naam die we aan onze input geven.  Dat mag eender wat zijn, maar je kiest best iets beschrijvends, of volg de richtlijnen van het bedrijf. Javascript is een typeloze taal, we zeggen nergens wat `row` precies moet zijn (een getal? een javascript object? een stuk tekst?). Andere programmeertalen zijn strenger (en beter), die verwachten dat je voor een parameter ook zegt wat die input mag zijn (bijvoorbeeld een integer of een string), dan aanvaardt die functie geen input die daar niet aan voldoet. Dat is typisch (ha!) veel beter, maar javascript is het wilde westen en helaas de ruggegraat van een groot deel van het surfbare internet. Als we iets geven aan onze functie waar die niet mee overweg kan zal dat gewoonweg fouten geven tijdens de uitvoer van het programma. Tof.

Genoeg achtergrond, we gaan nu terug naar onze visualisatie.

###  Typeconversie, vervolg
Laat ons meteen eens kijken naar hoe dat er uitziet:

    onMounted(async () => {
        const data = await d3.csv('movies.csv', typeConversion);
        console.log(data);
    })

    function typeConversion(row)
    {
        return {
            genre: row.genre,
            budget: row.budget,
            revenue: row.revenue
        } 
    }

Er zijn 2 grote wijzigingen. We hebben onze `typeConversion` methode afgewerkt, maar belangrijker, die wordt nu ook gebruikt. We hebben onze functie namelijk als extra input meegegeven aan de `csv` functie van D3. Dat zorgt ervoor dat D3 de .csv file regel per regel gaat inlezen en die voedt elke regel als input aan onze `typeConversion` methode (dat is geen standaard javascript of Vue, of dergelijke, dat zijn zaken die ik leer door de documentatie van d3 te lezen, specifieker: de documentatie van de `csv` methode). Die `typeConversion` methode gaat dan de regel "cleanen" door een rij te vertalen in een javascript object zoals wij dat willen. Dat gepoetste object gaat D3 toevoegen aan een `array` en als alle regels overlopen zijn krijg je dat resultaat terug. In Pseudocode doet `csv` dus het volgende:

    1. Laat de csv file in van het geheugen
    2. Maak een nieuwe lege array "resultaat"
    3. Voor elke regel in het bestand, doe:
        3.1 Deel de regel op volgens , en steek dat in een javascript object
        3.2 Geef dat javascript object aan de functie die we als 2e input gekregen hebben. Als we geen 2e input hebben, negeer dan deze stap
        3.3 Steek het resulterende object in onze array "resultaat"
    4. Geef "resultaat" als output ('return')

De functie `typeConversion` krijgt dus een volledige rij `row` binnen en geeft een nieuw object terug. Dit is een veel kleiner object dat maar bestaat uit 3 eigenschappen: genre, budget en revenue. Voor elk van deze eigenschappen kopieren we gewoon de waarde uit de rij `row` die we binnenkrijgen.  Dus op zich wordt er nu nog niks "gezuiverd" door `typeConversion`, het enige wat we doen is kolommen negeren en selecteren.

Als je dit uitvoert zal je merken dat onze data in de console nu versimpelt is, we hebben enkel nog de drie waardes per rij overgehouden. We willen echter ook typeconversie doen op die waardes en die niet zomaar kopieren. Elk stukje data dat je krijgt uit een .csv file is tekst. Daar zijn we niet zoveel mee. We kunnen niet rekenen met tekst. Door een `+` te zetten voor onze waardes probeert javascript tekst te converteren naar een getal:

    function typeConversion(row)
    {
      return {
        genre: row.genre.toLowerCase(),
        budget: +row.budget,
        revenue: +row.revenue
      }
    }

Nu zullen budget en revenue eerst omgezet worden naar een getal vooraleer we ze gebruiken. Het genre zetten we ook allemaal om naar kleine letters, zo vermijden we inconsistenties in hoofd- of kleine letters. Je kan hier heel ver in gaan en de conversie uitgebreid doen. Zo krijg je consistentie in je data en dat is heel belangrijk om daar fatsoenlijke visualisatie op te doen. We hebben het hier gehouden op een simpel voorbeeld maar dit is dus de plaats waar je je data zou opschonen. Pfoe. Dat was heel wat. Neem een slokje drinken. Pak even pauze, want we zijn nog niet klaar.

### Data filtering
Er zijn sommige films die geen genre hebben of een budget of revenue die 0 zijn. We willen die data niet mee opnemen in onze visualisatie. We gaan deze data dus ook nog filteren. We kunnen dat rechtstreeks doen nadat de data is ingeladen:

    ...
    const data = await d3.csv('movies.csv', typeConversion);
    const filteredMovies = data.filter(r => {
      return r.budget > 0 && r.genre && r.revenue > 0
    });
    console.log(filteredMovies);
    ...

We gebruiken opnieuw een ingebouwde functie, deze keer van javascript, die je kan gebruiken op een `array`: `filter`. Al die ingebouwde functies kunnen intimiderend zijn. ik verwacht dat je enkel de ingebouwde functies leert die we ook gebruiken in de labs. Als je voor het examen een andere functie nodig hebt, dan zal die gegeven zijn.
Wat doet `filter`? `filter` werkt op een een array van objecten  (we roepen hier de functie op op onze variabele `data`, niet toevallig een rij van objecten). Dan overloopt `filter` elk object in de array en filtert er de objecten uit die niet voldoen aan de voorwaarde die ook als input wordt meegegeven (dat is het `r => {...}` gedeelte). Onze voorwaarde is dit:

    r.budget > 0 && r.genre && r.revenue > 0

`r` is hier een object uit onze array. Enkel een object waarvan het budget groter is dan 0, waarvan het genre bestaat (niet leeg is) en waarvan de revenue groter is dan 0 mag blijven. Belangrijk detail: `filter` wijzigt niet rechtstreeks de array waar we op werken (`data`), maar maakt een kopie en filtert daarop. Daarom dat we het resultaat van `filter` opvangen en in een nieuwe array steken. We hebben nu dus 2 arrays: `data` en `filteredMovies`. Het is netjes om deze filter ook in zijn eigen methode (of functie) te steken:

    function dataLoaded(rawData)
    {
        return rawData.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
    }

`dataLoaded` krijgt input `data` en voert daar de `filter` functie op uit. Deze functie zou dus enkel werken als we de functie ook daadwerkelijk  een input geven waar filter op kunnen toepassen. Als je de functie als volgt zou oproepen kan dat bijvoorbeeld niet:

    dataLoaded(5)

5 is immers een getal, en de functie `filter` bestaat niet voor een getal, enkel voor een rij van objecten. We moeten deze functie nog oproepen nadat onze data is geprepareerd:

    ...
    const filteredMovies = dataLoaded(data);
    console.log(filteredMovies);
    ...

Als je nu uitvoert zie je dat we overblijven met <b>3474</b> films, er zijn er dus heel wat verdwenen.

## 3. Datatransformatie
De data waar we nu mee werken is niet de data die we willen visualiseren. We willen deze data graag in categorieën stoppen per genre, om dan de revenue te vergelijken.
Opnieuw snelt D3 ons te hulp met de verwarrende functie: `rollup`. Als je ooit met SQL gewerkt hebt en de <i>group by</i> functionaliteit kent, gaat dit je bekend voorkomen.

de rollup methode ziet er ruwweg zo uit:

    d3.rollup(data, reducer, groupBy)

`rollup` verwacht dus 3 soorten input: `data`, `reducer` en `groupBy`. `data` is simpelweg onze gefilterde data die we doorspelen aan D3. `groupBy` is de waarde waarop we willen groeperen. Dat is in ons geval het genre. `reducer` is wat ingewikkelder. Het zijn de waardes waarin we geïnteresseerd zijn per groep. In ons geval is dat de gecombineerde revenue van alle films in een groep. Anders gezegd: rollup pakt een reeks van data, deelt die op in groepjes op basis van de `groupBy`, en geeft dan elke groep als input aan de `reducer`. Het is de taak van de reducer om elke groep te herleiden tot 1 getal. De ouput van rollup is dus een `array` van getallen: eentje voor elke groep. 
Ik denk dat we dit best uitleggen met een voorbeeld.

 We voegen daarom een nog een functie toe:

    function prepareBarChart(d)
    {
        const dataMap = d3.rollup(
            d,
            r => d3.sum(r, x => x.revenue),
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
    }

de reducer in dit geval is:

    r => d3.sum(r, x => x.revenue)

De pijltjesnotatie is een zeer vervelend iets als je leert programmeren, want dat is in feite een andere notatie voor een functie. De pijltjesnotatie wordt heel vaak gebruikt als je een functie maar 1 keer nodig hebt, want het staat je toe om functies te maken die geen naam hebben. Daarom dat de pijltjesnotatie ook wel anonymous functions worden genoemd. 
Zo hadden we de reducer ook voluit kunnen schrijven als aparte functie:

    function ourBeautifulReducer(r)
    {
        return d3.sum(r, x => x.revenue)
    }

Onze bar chart functie zou dan worden: 

    function prepareBarChart(d)
    {
        const dataMap = d3.rollup(
            d,
            ourBeautifulReducer,
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
    }

Aangezien die pijltjesnotatie zo vaak gebruikt wordt, is het wel belangrijk dat je die kent, maar weet dus dat je die helemaal kan vervangen door gewone functies (dat hebben we bijvoorbeeld gedaan bij de functie `typeConversion`, daar had je ook zo een pijltjesnotatie kunnen gebruiken in de plaats).


Zo, nu we weten dat dit gewoon een functie is, laat ons die even ontleden. Dit is een functie die een argument (=parameter) binnenpakt: `r`. De reducer krijg per definitie een groep binnen. Dus r zou een array kunnen zijn van al onze datapunten waarvan het genre `animation` is, of `actie`, of ... Deze functie neemt die groep `r` en geeft die door aan een andere functie: `d3.sum`. Dit is een functie die een bepaald veld gaat optellen. Die functie krijgt de hele groep binnen en overloopt die per rij, zo een rij noemen we hier even `x`. Van elke rij `x` nemen we de revenue eigenschap en daar maken we de som van.

De laatste 2 regels kunnen hier ook verwarrend zijn:

    const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
      return dataArray;

Ok - ik heb gelogen. Rollup geeft geen `array` terug van getallen, maar wel een `map`. Als je eens wilt weten hoe er dat uitziet, print dan gerust eens `dataMap` af in de console. We werken echter liever met arrays om het simpel te houden en niet met een `map`. Wat een `map` precies is, is buiten de scope van dit vak. Deze laatste regels code converteren die `map` terug naar een `array` en retourneert dat resultaat.

We moeten deze functie nog oproepen. De volledige code ziet er dan zo uit:
    
    <script setup>
    import { onMounted } from 'vue';
    import * as d3 from "d3"

    onMounted(async () => {
    const data = await d3.csv('movies.csv', typeConversion);
    const filteredMovies = dataLoaded(data);
    const finishedData = prepareBarChart(filteredMovies);
    console.log(finishedData);
    })

    function typeConversion(row)
    {
        return {
            genre: row.genre.toLowerCase(),
            budget: +row.budget,
            revenue: +row.revenue
        } 
    }

    function dataLoaded(rawData)
    {
        return rawData.filter(r => {
        return r.budget > 0 && r.genre && r.revenue > 0
        });
    }

    function prepareBarChart(d)
    {
        const dataMap = d3.rollup(
            d,
            r => d3.sum(r, x => x.revenue),
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
    }

    </script>

De data waar we mee werken ziet er nu zo uit:

    0: {genre: 'Animation', revenue: 29900087791}
    1: {genre: 'Adventure', revenue: 66992153755}
    2: {genre: 'Comedy', revenue: 56864628292}
    3: {genre: 'Action', revenue: 79304756422}
    4: {genre: 'History', revenue: 1763533422}
    5: {genre: 'Drama', revenue: 51152213841}
    6: {genre: 'Crime', revenue: 10352578178}
    7: {genre: 'Fantasy', revenue: 15155568824}
    8: {genre: 'Science', revenue: 13004537408}
    9: {genre: 'Horror', revenue: 12121234371}
    10: {genre: 'Romance', revenue: 5843455308}
    11: {genre: 'Mystery', revenue: 3067920795}
    12: {genre: 'Thriller', revenue: 11693797086}
    13: {genre: 'War', revenue: 1692654029}
    14: {genre: 'Family', revenue: 7177419409}
    15: {genre: 'Music', revenue: 885786343}
    16: {genre: 'Documentary', revenue: 780123406}
    17: {genre: 'Western', revenue: 458255558}
    18: {genre: 'TV', revenue: 58252139}
    19: {genre: 'NA', revenue: 162301603}
    20: {genre: 'Foreign', revenue: 31741406}

Zoals je nu duidelijk hoort te weten en te zien, is dit een `array` van 21 javascript objecten. Elk object bestaat uit een `genre` en een `revenue`.

## 4. Bar chart
Misshien vindt je datatransformatie niet zo sexy, dus tijd om nog eens visueel te werken en alles netjes samen te brengen. We doen dat nog 1x samen. In de volgende labs gaan we hier sneller over. We starten met enkele statische bars om onze teen in het water te steken:

    <svg width=700 height=350>
        <g :transform="`translate(0, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(30, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(60, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(90, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
        <g :transform="`translate(120, 150)`">
            <rect width=20 height=150 fill=orange />
        </g>
    </svg>

(Je weet uiteraard zelf waar deze html code hoort te staan...)

Nu tekenen we statisch 5 `rect` elementen. Ik wil er evenveel als in onze data zitten. Op dit moment zit die data in `finishedData`. Maar `finishedData` bestaat alleen in de `onMounted` functie. In het vorige lab hebben we een variabele gedefinieerd buiten deze functie tussen onze `<script>` tags, waardoor de variabele toegankelijk is binnen de hele app. Helaas gaat die manier van werken niet werken. We proberen het eens: we tekenen evenveel rechthoeken als we datapunten hebben. Eerst zorgen we voor een variabele `movieData` in ons script en kennen we die toe in onze `onMounted` functie:

    <script setup>
    ...
    let movieData = [];

    onMounted(async () => {
        ...
        movieData = finishedData;
    })
    </script>

 We passen onze HTML aan voor App.vue:

    <svg width=700 height=350>
        <g v-for="(m, index) in movieData">
            <g :transform="`translate(${index * 30}, 150)`">
            <rect width=20 height=150 fill=orange />
            </g>
        </g>
    </svg>

Helaas werkt dit niet. Er worden geen rechthoeken getekend. De boosdoener is `async`. Denk eraan, de uitvoer van onze code wordt tijdelijk stilgelegd bij `await`, maar Vue zit niet stil. De rest van de webpagina wordt getekend, en op dat moment is `movieData` nog leeg. Daardoor worden er geen rechthoeken getekend. Tegen dat de `await` methode klaar is en er resultaat is van het inlezen van de file, is Vue al klaar met het tekenen/renderen van de website. We zouden tegen Vue dus eigenlijk een signaal moeten geven om onze component te hertekenen. Dat doen we het beste met een mechanisme genaamd reactivity. Hier vindt je daar meer informatie over: https://vuejs.org/guide/essentials/reactivity-fundamentals.html

Het komt erop neer dat we een variabele markeren als <i>reactief</i>. Dat wilt zeggen dat Vue een oogje in het zeil gaat houden en als de variabele verandert zal Vue op een slimme wijze de component hertekenen op de plaatsen waar deze variabele impact heeft. Dus als wij de variabele aanpassen zal Vue ook impliciet opnieuw renderen. Omgekeerd werkt dat ook. Met reactivity kunnen we een variabele koppelen aan een UI-element zoals bijvoorbeeld een tekstveld. Als de gebruiker dan de waarde verandert van dat tekstveld zal onze variabele zich ook automatisch aanpassen zonder dat wij dat manueel moeten doen. Dat concept heet ook wel 2-way binding. 2-way want een aanpassing aan de variabele zal de UI doen veranderen en een verandering aan de UI kan de variabele doen veranderen. In ons geval is eenrichtingsverkeer voldoende. 

Reactivity voegen we toe met het `ref` keyword. Dat moeten we importeren:

    import { ref } from 'vue'

We markeren `movieData` als reactief:

    let movieData = ref([])

We initializeren hier `movieData` als een lege rij, vandaar de `[]` tussen de (). Ten slotte moeten we ook het aanpassen `movieData` lichtjes herschrijven:

    movieData.value = finishedData;

Dus met andere woorden, als je een reactieve variabele wilt aanpassen, dan moeten we daar `.value` achterzetten. Dat is alles. Als alles goed gaat, heb je nu 21 rechthoeken van dezelfde grootte.

De hoogte van de rechthoeken willen we nu laten afhangen van de revenue eigenschap. Maar de revenue gaat tot in de miljarden! Zoveel pixels hebben we helaas niet. We zouden dit getal kunnen delen door enkele miljoenen of miljarden maar dat is gokwerk. We laten D3 dat voor ons berekenen. D3 kan namelijk waardes herschalen. Zo kunnen we bijvoorbeeld de getallen tussen [20, 800] herschalen naar [0, 10]. We kunnen D3 zelfs waardes laten schalen naar kleur. Zo kan je bijvoorbeeld [0, 100] laten herschalen op een kleurenas waarbij 0 blauw is en 100 rood, 50 is dan de kleur tussen blauw en rood (paars). 
Voordat we kunnen herschalen moeten we weten wat de maximum revenue en de minimum revenue in onze data is. Dat kan eenvoudig met D3:

    const extents = d3.extent(finishedData, d => d.revenue)

Het resultaat is een array van 2 getallen: het minimum en het maximum. In ons geval:

    [31741406, 79304756422]

Het maximum is dus `extents[1]`. het minimum is `extents[0]`. We zijn nu klaar om D3 onze width te laten berekenen. D3 kan voor ons een dynamische functie maken die deze berekening doet:

    const xScale = d3.scaleLinear()
      .domain([0, extents[1]])
      .range([0, 200]);

`scaleLinear` zegt dat we geïnteresseerd zijn in een linear verloop, `domain` zegt dat we vertrekken van 0 tot `extents[1]` (het maximum van onze data), en `range` zegt dat we moeten herschalen naar [0, 200]. Het resultaat is een functie(!) die een getal tussen 0 en 79304756422 zal herschalen tussen 0 en 200. We kunnen `xScale` dus bijvoorbeeld als volgt gebruiken:

    xScale(20000000)

We willen die functie graag opslaan en gebruiken om onze HTML te berekenen met Vue, dus die moet onderdeel worden van de data van onze component:

    <script setup>
    ...
    let xScale;
    ...

In `onMounted()` kennen we die functie toe:

    ...
    const finishedData = prepareBarChart(filteredMovies);
    movieData.value = finishedData;

    const extents = d3.extent(finishedData, d => d.revenue);
    xScale = d3.scaleLinear()
        .domain([0, extents[1]])
        .range([0, 200]);
    ...

Merk op dat we hier de `const` weglaten voor de `xScale`. Als je dat niet doet, interpreteert javascript dit als een nieuwe variabele (die niet bestaat buiten de `onMounted` functie), niet als de `xScale` die we op niveau van onze component hebben gedefinieerd.

We gebruiken dan die functie in onze HTML om de hoogte te berekenen:

    <svg width=700 height=350>
        <g v-for="(m, index) in movieData">
            <g :transform="`translate(${index * 30}, 150)`">
            <rect width=20 :height=xScale(m.revenue) fill=orange />
            </g>
        </g>
    </svg>

Let nogmaals op de `:` voor `height`. Als je dit bekijkt zie je dat de rechthoeken verschillen van hoogte. De y-coordinaat staat nu wel nog statisch op 150, die zou ook moeten verschillen afhankelijk van de hoogte van de rechthoek, omdat de coordinaten van de rechthoek beginnen te tellen van de bovenkant van ons canvas. Denk gerust zelf eens na over die formule, hieronder vind je de oplossing:

    <svg width=700 height=350>
    <g v-for="(m, index) in movieData">
        <g :transform="`translate(${index * 30}, ${200 - xScale(m.revenue)})`">
        <rect width=20 :height=xScale(m.revenue) fill=orange />
        </g>
    </g>
  </svg>

Hier is nog eens de volledige code:

    <script setup>
    import { onMounted } from 'vue';
    import * as d3 from "d3";
    import { ref } from 'vue';

    let movieData = ref([])
    let xScale;


    onMounted(async () => {
    const data = await d3.csv('movies.csv', typeConversion);
    const filteredMovies = data.filter(r => {
    return r.budget > 0 && r.genre && r.revenue > 0
    });
    const finishedData = prepareBarChart(filteredMovies);
    console.log(finishedData);
    movieData.value = finishedData;

    const extents = d3.extent(finishedData, d => d.revenue);
    xScale = d3.scaleLinear()
    .domain([0, extents[1]])
    .range([0, 200]);
    })

    function typeConversion(row)
    {
    return {
        genre: row.genre.toLowerCase(),
        budget: +row.budget,
        revenue: +row.revenue
    }
    }

    function dataLoaded(rawData)
    {
        return rawData.filter(r => {
            return r.budget > 0 && r.genre && r.revenue > 0
        });
    }

    function prepareBarChart(d)
    {
        const dataMap = d3.rollup(
            d,
            r => d3.sum(r, x => x.revenue),
            d => d.genre
        )

        const dataArray = Array.from(dataMap, d => ({ genre: d[0], revenue: d[1] }));
        return dataArray;
    }


    </script>

    <template>
    <svg width=700 height=350>
        <g v-for="(m, index) in movieData">
            <g :transform="`translate(${index * 30}, ${200 - xScale(m.revenue)})`">
            <rect width=20 :height=xScale(m.revenue) fill=orange />
            </g>
        </g>
    </svg>
    </template>


Deze visualisatie is nauwelijks bruikbaar want we kunnen nu niet zien welke `rect` overeenkomt met welke categorie. In de oefeningen van dit Lab ga je zelf labels proberen toe te voegen aan de rechthoeken. 

Oef... wat een brok. Maar dit schept wel een totaalbeeld van het werken met data: we importeren data uit een file, we schonen die data op, we filteren de data, we transformeren de data voor de visualisatie die we willen en gebruiken ten slotte Vue. Hoera!