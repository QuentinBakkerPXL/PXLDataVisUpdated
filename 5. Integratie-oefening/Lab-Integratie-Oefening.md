# Lab - Integratie-Oefening

In dit lab gaan we al onze harde werk samenzetten. We gaan stap voor stap door een grotere oefening. Let op, er zit ook wat nieuwe leerstof verstopt in deze oefening.

## 1. Gegevens
We hebben 2 csv files: population.csv en fertility_rate.csv. Best dat je die al eens bekijkt. Je kan een csv zelfs opendoen met Excel. Dan kan je eens kijken welke kolommen/data er zijn.

### Klaarzetten van het project
Maak een nieuw Vue project met Vuetify en zet beide csv files in je `public` folder.

Voeg d3 toe aan je project. Verwijder ook de standaardcomponent zoals we nu al een paar keer gedaan hebben. Run je app al eens met `npm run dev` om zeker te zijn dat alles nog goed werkt. 

### Data inlezen
Het is een goed idee om het uitlezen meteen te testen. 

In onze App.vue gaan we onze data eerst eens proberen in te lezen. We doen dat in onze `onMounted()` functie. We lezen zowel de population.csv in alsook de fertility_rate.csv. Voorlopig printen we het resultaat gewoon af. Een goede tip is om alijd zo weinig mogelijk toe te voegen vooraleer je test. Als je app niet meer werkt weet je dan dat de oorzaak ligt in wat je net toegevoegd hebt. Hoe minder je toegevoegd hebt, hoe gemakkelijker de fout is om op te sporen.

Zorg ervoor dat je de csv files kan uitlezen uit je console.

### Data filtering
Population bevat wereldpopulatie per land en fertility_rate bevat het gemiddeld aantal kinderen per vrouw per land. We willen de population data toevoegen aan de data van fertility_rate. Bovendien zijn we enkel geinteresseerd in de volgende landen:

    Belgium
    China
    Turkey
    Germany
    Finland
    Niger

Tijd voor het moeilijkste gedeelte: de filtering. Om die netjes te kunnen aanpassen steken we de landen waarin we geïnteresseerd zijn in een array:

    const countries = [
        "Belgium",
        "China",
        "Turkiye",
        "Germany",
        "Finland",
        "Niger"
    ];

We proberen al eens onze fertData te filteren in `mounted()`: 

    onMounted(async () => {
        ...

        fertData = fertData.filter(r => countries.includes(r.Country));
        popData = popData.filter(r=> countries.includes(r['Country Name']));

        ...
    })

We gebruiken de `includes` methode om te weten of een een bepaald land in de onze array `countries` zit. Zo zal `countries.includes("Belgium")` bijvoorbeeld `true` zijn, maar `countries.includes("Sam")` zal helaas `false` zijn. Let er ook op dat in dit geval `popData` en `fertData` (de namen die ik gegeven heb aan de lijsten die ik ingelezen heb van de csv files) gedeclareerd moeten zijn met `let` en niet met `const`. Dat doen we omdat we de array overschrijven. En dat mag niet niet met een constante variabele. `let` is dus flexibeler, maar het is veiliger om zoveel mogelijk met `const` te werken, dan weet je zeker dat een waarde niet per ongeluk kan veranderen. 
  
We krijgen hier 6, correcte resultaten. Let hier op de `r['Country Name']` in de plaats van `r.Country`. De reden is dat in de population csv de kolom `Country Name` heet. Dus we zouden eigenlijk dit willen doen: `r.Country Name`. Maar je mag geen spaties gebruiken in de naam van een eigenschap. Dit werkt helaas ook niet: `r."Country Name"`. Dit is daarom een uitzondering. Als je een eigenschap wilt oproepen waar een spatie in zit moet je de arraynotatie `[]` gebruiken: `r['Country Name']`. Dit is een goed moment om te zeggen dat je doorgaans zelf geen spaties zal toevoegen aan de naam van objecteigenschappen. We zitten hier nu mee omdat de csv file zo is opgebouwd. 

### Data filtering - vervolg
We hebben nu 12 datapunten, eentje voor elk land en voor elke file. We willen die 2 samenzetten in 1 array. We gaan enkel de population uit 2021 voor elk land toevoegen aan de data van `fertData`. Dat doen we zo:

    for (let i = 0; i < fertData.length; i++) 
    {
      const pop = popData.find(r => r['Country Name'] == fertData[i].Country);
      fertData[i].population = +pop['2021'];
    }

Het is goed om dit nog even stapsgewijs te herhalen. We beginnen wat eenvoudiger:

    for (let i = 0; i < fertData.length; i++) 
    {
      console.log(fertData[i]);
    }

Deze `for-lus` overloopt elk datapunt uit `fertData` een voor een. Als je nu naar je log kijkt zal je dat ook duidelijk kunnen zien. We kunnen een eigenschap toevoegen aan object in javascript door daar naar te verwijzen alsof het als bestaat en daar een waarde aan toe te kennen (javascript is een vieze taal...):

    for (let i = 0; i < fertData.length; i++) 
    {
        fertData[i].population = 40;
        console.log(fertData[i]);
    }

Kijk nu maar eens naar dezelfde log, en je zal zien dat elk object uit de `fertData` array een eigenschap `population` heeft bijgekregen en die staat voor elk object op 40. We moeten nu nog de juiste population vinden. Eerst gaan we een andere deelprobleem oplossen: `fertData[i]` geeft ons het juiste object omdat `i` elke keer ophoogt in de for-lus. `popData[i]` zou ons in dit geval ook de juiste data geven in de juiste volgorde. Bijvoorbeeld, als `fertData[i]` de data over België bevat, zal `popData[i]` ook de data van België zijn. Dat komt omdat beide csv files alfabetisch zijn, dus de arrays worden op dezelfde volgorde opgesteld. Maar daar ga ik niet graag vanuit. Als de csv file verandert van volgorde zou onze code dan niet meer kloppen! Daarom ga ik de robuustere `find` methode gebruiken:

    for (let i = 0; i < fertData.length; i++) 
    {
        const pop = popData.find(r => r['Country Name'] == fertData[i].Country);
        fertData[i].population = 40;
        console.log(fertData[i]);
    }

`find` zal de array doorlopen en op zoek gaan naar een element waar het volgende voor waar is:

    r['Country Name'] == fertData[i].Country

Met andere woorden, deze `find` zal voor ons op zoek gaan naar een element uit `popData` waarvoor de `Country Name` overeenkomt met `fertData[i].Country` (Je kan dit nota bene nog robuuster maken door de namen van deze landen eerst naar lower ase om te zetten). Ten slotte moeten we nog de juiste populatie toekennen in de plaats van gewoon 40. De `+` zetten we erbij om die populatie ook meteen om te zetten naar een getal (denk eraan, elk veld dat we uitlezen van csv file wordt automatisch geïnterpreteerd als tekst):

Enkele opmerkingen:

    * Op dit moment is elke veld van een object uit `fertData` nog tekst in de plaats van een getal. Dat moeten we nog remediëren.

    * We hebben nu alles in `onMounted()` gestoken. Je zou dit werk beter opdelen door wat andere functies/methodes toe te voegen. Dat laat ik als een oefening voor jullie.

### Dataconversie
We gaan hier wat trukken moeten gebruiken om al deze data om te zetten naar getallen, want op dit moment zitten we met heel wat jaartallen die we allemaal willen omzetten naar een getal. Om dat probleem te kunnen oplossen voor al die jaartallen moeten we natuurlijk eerst weten hoe we dat oplossen voor 1 jaartal. Laat ons bijvoorbeeld eens het eerste element nemen uit `fertData`:

    let element = fertData[0];

Het jaartal 1960 verkrijgen dan normaal zo:

    element.1960

Maar we mogen geen cijfers gebruiken als eerste teken voor een objecteigenschap. We gaan hier dus dezelfde logica moeten toepassen als we gedaan hebben bij het de eigenschap `Country Name` bij `popData`: we geen de arraynotatie moeten gebruiken:

    element['1960']

Als we die eigenschap willen omzetten naar een getal doen we dat zo:

    element['1960'] = +element['1960']

Oftwel in 1 keer:

    fertData[0]['1960'] = +fertData[0]['1960']

als we dat voor elk object uit onze array willen doen, moeten we dat in een `for-lus` doen:

    for (let i = 0; i < fertData.length; i++) 
    {
      fertData[i]['1960'] = +popfertData[i]['1960'];
    }

We moeten dat voor alle andere jaartallen ook doen. Dat zou er zo uitzien:

    for (let i = 0; i < fertData.length; i++) 
    {
      fertData[i]['1960'] = +popfertData[i]['1960'];
      fertData[i]['1961'] = +popfertData[i]['1961'];
      fertData[i]['1962'] = +popfertData[i]['1962'];
      ...
    }

Dat is veel werk. Dat willen we niet doen. Kunnen we dat niet sneller doen? Laat ons eens een stap terug nemen en kijken of we dat voor elkaar krijgen voor ons eerste element 0:

    fertData[0]['1960'] = +popfertData[0]['1960'];
    fertData[0]['1961'] = +popfertData[0]['1961'];
    fertData[0]['1962'] = +popfertData[0]['1962'];
    ...

De jaartallen lopen beginnen bij 1960 en lopen door tot en met 2020. We kunnen getallen omzetten naar tekst in javascript. Dus als we een `for-lus` kunnen maken die loopt van 1960 tot en met 2020, kunnen we dat gebruiken in onze arraynotatie. Deze `for-lus` loopt van 1960 tot en met 2020 (de `console.log` is ter illustratie zodat je het eens kan testen):

    for(let i = 1960; i <= 2020; i++)
    {
      console.log(i);
    }

Dit genereerd de getallen 1960..2020, maar we willen tekst. Dat kan gemakkelijk in javascript, want als je een getal bij tekst optelt, krijg je opnieuw tekst. Bijvoorbeeld:

    "Sam" + 2 = "Sam2"

Dus als we een getal optellen bij een leeg stukje tekst, maken we tekst:

    "" + 2 = "2"

We proberen dat eens in onze `for-lus`:

    for(let i = 1960; i <= 2020; i++)
    {
      console.log("" + i);
    }

Nu moeten we dat samenzetten, voor element 0 ziet er dat zo uit:

    for(let i = 1960; i <= 2020; i++)
    {
      fertData[0]['' + i] = +fertData[0]['' + i];
    }

Als we dat willen combineren met onze vorige `for-lus`, om dit voor elke object in `fertData` te kunnen doen, moeten we opletten dat we niet dezelfde lusvariabele `i` 2x gebruiken. Dus ik verander de naam van de lusvariabele in de 2e `for-lus` naar `j`:

    for(let i = 0; i < fertData.length; i++)
    {
        for(let j = 1960; j <= 2020; j++)
        {
            fertData[i]['' + j] = +fertData[i]['' + j];
        }
    }

We hebben in onze `mounted()` echter al een `for-lus` waar we lopen door onze `fertData`:

    for (let i = 0; i < fertData.length; i++) 
    {
      const pop = popData.find(r => r['Country Name'] == fertData[i].Country);
      fertData[i].population = +pop['2021'];
    }

We kunnen de 2 combineren:

    for (let i = 0; i < fertData.length; i++) 
    {
      const pop = popData.find(r => r['Country Name'] == fertData[i].Country);
      fertData[i].population = +pop['2021'];

      for(let j = 1960; j <= 2020; j++)
      {
        fertData[i]['' + j] = +fertData[i]['' + j];
      }
    }

Zo! Nu hebben we cleane data om mee te werken! Let er ook op dat je deze berekeningen netjes opslaat in een componentsvariabele! Zoniet is ons harde werk voor niets.

## 2. Visualisatie

Onze data staat klaar, daar komen we zo dadelijk op terug. We gaan eerst wat html toevoegen om onze visualisatie te kunnen maken. We gaan een cirkel per land maken. De straal van de cirkel zal afhankelijk zijn van de bevolking. We voegen ook een as toe waar we de fertiliteit op aanduiden: dat zal een as zijn die van 0 naar 6 loopt. Ten slotte willen we een slider zodat we door de tijd heen kunnen kijken. Die slider biedt waarden aan tussen 1960 en 2020.

### Slider
We starten met die slider. Dankzij Vuetify hebben we nu toegang tot de component `v-slider` (https://vuetifyjs.com/en/components/sliders/). 
Laat ons die slider even toevoegen:

    ...
        <v-main>
        <div>  
            <v-slider
            label="Jaartal"
            thumb-label="always"
            min="1960"
            max="2020"
            step="1"
            ></v-slider>
        </div>
        </v-main>
    ...

### De basis
Tijd om onze html wat te verfijnen:

    ...
        <v-main>
        <div id="container">
            <svg width="750" height="500">
                <rect x="20" y="450" width="710" height="1" fill="black" />
                <g v-for="n in 9" :key="n">
                    <g :transform="`translate(${20 + (n-1) * 710/8}, 440)`">
                    <rect x="0" y="0" width="1" height="10" fill="black" />
                    <text x="-5" y="-10" fill="black"> {{n - 1}}</text>
                    </g>
                </g>
            </svg>
            <div id="slider">
                <v-slider
                label="Jaartal"
                thumb-label="always"
                min="1960"
                max="2020"
                step="1"
                ></v-slider>
            </div>
            
        </div>
        </v-main>
    ...

We hebben daarvoor ook nog volgende elementen toegevoegd aan onze `style`: 
   
    #container {
        width: 750px;
    }

    svg {
        margin: 50px;
    }

    #slider {
        margin: 50px;
        box-sizing: content-box;
        width: 750px; 
    }

Misschien goed dat we nog even op het tekenen van de as inzoomen:

    <rect x="20" y="450" width="710" height="1" fill="black" />
    <g v-for="n in 9" :key="n">
        <g :transform="`translate(${20 + (n-1) * 710/8}, 440)`">
        <rect x="0" y="0" width="1" height="10" fill="black" />
        <text x="-5" y="-10" fill="black"> {{n - 1}}</text>
        </g>
    </g>

We gebruiken het `rect` element om lijnen te tekenen. De bovenste `rect` tekent onze as. De `v-for` gaat onze labeltjes toevoegen. Er worden in totaal 9 labeltjes toegevoegd. Per label verschilt de text en de x-coordinaat. Die wordt als volgt berekend:

    20 + (n-1) * 710/8

Waarbij n dus loopt van 1..9. Wat mij hier nu nog aan stoort is dat we zoveel getalletjes aan het gebruiken zijn die eigenlijk afhankelijk zijn van andere getalletjes. Bijvoorbeelde de marge tussen de labels: `710/8` is gebasseerd op de totale lengte van de as (`710`) en het aantal labels dat we nodig hebben -1. Die zaken steek je het liefst in componentvariabele zodat je daar achteraf mee kunt spelen. Ik raad dus ook aan dat je dat doet. We zullen bijvoorbeeld de breedte van de as al eens toevoegen als variabele aan onze (App) component:

We weten ondertussen dat we dat doen door gebruik te maken van `data()`:

    ...
    
    <script setup>
    import { onMounted } from 'vue';
    import * as d3 from "d3";

    const axisWidth = 710;
    ...

We passen dan de html voor het tekenen van onze as aan:

    <rect x="20" y="450" :width=axisWidth height="1" fill="black" />
    <g v-for="n in 9">
        <g :transform="`translate(${20 + (n-1) * axisWidth/8}, 440)`">
        <rect x="0" y="0" width="1" height="10" fill="black" />
        <text x="-5" y="-10" fill="black"> {{n - 1}}</text>
        </g>
    </g>

We hebben een canvas om op te tekenen en we hebben een slider om mee te euh... sliden? De volgende stap is dat we data effectief gaan tekenen. We doen dat eerst voor 1 specifiek jaartal. Daarna breiden we uit zodat we ook luisteren naar de slider.

### 1960
Voor we kunnen starten met onze data te tekenen moeten we die ook effectief opslaan. We hebben al dat harde datatransformatiewerk al achter de rug maar we doen er nog niks mee. We voegen nog wat componentsvariabelen toe:
    
    let data = ref( []);
    let populationScale = null;

`populationScale` zal de schaalfunctie zijn die we door d3 laten berekenen in onze `onMounted()` functie, want population loopt al snel tegen de miljoenen.:

    ...
    
    const populationExtents = d3.extent(fertData, d => d.population);
    populationScale = d3.scaleLinear()
        .domain([0, populationExtents[1]])
        .range([0, 150]);

    data.value = fertData;

    ...
    
We slaan hier ook`fertData` op in onze componentvariabele: `data` (vergeet de `value` niet). Denk eraan, `populationScale` variabele waar we een functie in steken! Dat is wat bizar. Normaal is een variabele enkel data. Een functie is berekening.

We breiden onze html uit:

    <svg>
    ...
    <g v-for="(d, index) in data">
        <g :transform="`translate(${index * 50}, 100)`">
            <circle :r="populationScale(d.population)" />
        </g>
    </g>
    </svg>
    ...

Dit hebben wel al enkele keren gezien. We overlopen elk element uit onze lijst. Per element verschilt de x-coordinaat en de straal. De straal laten we berekenen met behulp van die `populationScale` functie die we hebben laten maken door d3. 

Ik heb nog wat styling toegevoegd voor de circles:

    circle {
        fill: orange;
        opacity: 0.5;
    }

We zien al snel dat er 1 hele grote cirkel is (China), die zou nog eens problematisch groot kunnen worden, want sommige andere cirkels zijn in verhouding gevaarlijk klein. We gaan daarom eerst een paar kleine aanpassingen maken. De population gebruiken als straal geeft eigenlijk een verkeerd beeld. Een cirkel met straal 20 oogt namelijk 4x zo groot als een cirkel met straal 10. Wat je eigenlijk wilt is dat de oppervlakte van de cirkel representatief is voor het cijfer, niet de straal. Zo zou de cirkel die 10 voorstelt 2x moeten passen in de cirkel die 20 voorstelt. We passen daarom even onze schaalfunctie aan naar:

    populationScale = d3.scaleSqrt()
      .domain([0, populationExtents[1]])
      .range([0, 150]);

`scaleSqrt` gaat ons een schaalfunctie geven die wel correct cirkelweergaves gaat maken. Daardoor worden onze andere cirkels ook beter zichtbaar.

We gaan nu nog de x-coordinaat laten afhangen van de fertiliteitscijfers van 1960. De x-coordinaat kan als volgt bepaald worden:

    d['1960']/8 * axisWidth

Dat geeft ons de volgende html:

    ...
    <g v-for="(d, index) in this.data" :key="index">
        <g :transform="`translate(${d['1960']/8 * axisWidth}, 200)`">
            <circle :r="this.populationScale(d.population)" />
            <text y="-50" fill="black"> {{d['1960']}} </text>
        </g>
    </g>
    ...

Let op dat ik hier ook een `text` elementje heb toegevoegd. Die heb ik gewoon even ter controle toegevoegd zodat ik zeker ben dat de berekening van de x-coordinaat klopt. Dat kan je weglaten. Ik heb ook de y-coordinaat gezet van 100 naar 200 om wat meer ruimte te maken.

### Slidervalue

We hebben nu statisch de data van 1960 gebruikt. We breiden nu uit zodat we luisteren naar de slider. Daarvoor gaan we 2-way binding gebruiken. We voegen eerst opnieuw een componentvariabele toe die onze slider waarde gaat bevatten:

    let sliderValue = ref(1989);

(We zetten hier de initiele waarde van sliderValue op 1989, het beste jaar).
Dan gaan we tegen de slider vertellen dat de waarde altijd weggeschreven moet worden naar `sliderValue`. Dat doen we met `v-model`:

    ...
    <v-slider
          label="Jaartal"
          thumb-label="always"
          min="1960"
          max="2020"
          step="1"
          v-model="sliderValue"
    ></v-slider>
    ...

Als je nu je pagina refreshed zal je zien dat je slider ook automatisch start op 1989. Dat is een goed teken want dat betekent dat de slider gestart is van de waarde die we in `sliderValue` gestoken hebben. Als de gebruiker de slider verandert zal ook de waarde van `sliderValue` veranderen. Vica versa, als wij de waarde van `sliderValue` veranderen zal ook de slider veranderen. 

Onze html code aanpassen zodat we de waarde van de slider gebruiken in de plaats van `'1960'` is triviaal:

    ...
     <g v-for="(d, index) in this.data" :key="index">
        <g :transform="`translate(${20 + d[sliderValue]/8 * axisWidth}, 200)`">
            <circle :r="this.populationScale(d.population)" />
            <text y="-50" fill="black"> {{d[sliderValue]}} </text>
        </g>
    </g>
    ...

We hebben nu een werkende visualisatie! Je kan de slider bewegen om door de jaren te scrollen en de fertiliteitscijfers zien. 

## 3. Afwerking
Er is echter nog heel wat werk dat we kunnen verrichten om dit te verbeteren. Zo zien we op dit moment bijvoorbeeld niet over welk land het gaat, of het effectieve cijfer. Die afwerking, daar gaan we nu naar kijken. De onderstaande stappen zijn afwerking. Deze stappen zijn zeker belangrijk voor de gebruiker maar verwar ze niet met de hoofdzaak van dit vak. We gaan hier ook snel over.

### Font en CSS
We voegen het Open Sans font toe. Open `index.html` en voeg de volgende link toe aan de header:

    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;800&display=swap" rel="stylesheet">

We breiden ook de css uit voor App.vue:

    #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    }

    body {
    font-family: 'Open Sans', sans-serif;
    background: #303952;
    }

    text {
    
    font-family: 'Open Sans', sans-serif;
    }

    svg {
    margin: 50px;
    margin-bottom: 0;
    }

    circle:hover {
    fill: #ea8685;
    opacity: 1;
    }

    #container {
    width: 750px;
    }

    #slider {
    margin: 50px;
    box-sizing: content-box;
    width: 750px; 
    }

    .selection-label {
    
    font-weight: 800;
    }

    .populationCircle {
    transition: all 200ms ease; 
    
    }

    .selection-circle {
    animation-name: scaleIn;
    animation-duration: 200ms;
    animation-timing-function: ease-in-out;
    pointer-events: none;
    }

    .selection-line {
    animation-name: scaleIn;
    animation-duration: 200ms;
    animation-timing-function: ease-in-out;
    pointer-events: none;
    }

    @keyframes scaleIn {
    0% {
        transform: scale(0)
    }
    100% {
        transform: scale(1);
    }
    }

We gaan deze css klasses zo dadelijk ook toepassen. Eerst moeten we een aantal belangrijkere aanpassingen maken.

### Selectiefunctionaliteit
We gaan bijhouden welke cirkel de gebruiker geselecteerd heeft en ook over welke cirkel de gebruiker zweeft met zijn/haar muis. We doen dat door 2 componentsvariabele toe te voegen:

    ...
    let highlightedDataPoint = ref(null);
    let selectedDataPoint = ref(null);
    ...

`highlightedDataPoint` zal bijhouden over welk datapunt op dit moment de muis zweeft. `selectedDataPoint` houdt dan weer bij welke cirkel de gebruiker heeft geselecteerd. Beide initialiseren we op `null`, want bij de aanvang zijn beide nog niet gekend. De html van de app sturen we ook bij. Die ziet er bij mij als volgt uit:

    <v-main :style="{background: '#303952'}">
      <div id="container">
            <svg width="850" height="500">
            <rect x="20" y="450" :width="axisWidth" height="1" fill="#f8a5c2" />
            <g v-for="n in 9">
                <g :transform="`translate(${20 + (n-1) * axisWidth/8}, 440)`">
                <rect x="0" y="0" width="1" height="10" fill="#f8a5c2" />
                <text x="-5" y="-10" fill="#f8a5c2"> {{n - 1}}</text>
                </g>
            </g>

            <g v-for="(d, index) in data">
                <g :transform="`translate(${20 + d[sliderValue]/8 * axisWidth}, 200)`">
                <circle class="populationCircle" :r="populationScale(d.population)" fill="#c44569"  opacity="0.75" @mousedown="selectCircle(d)"  @mouseover="highlightCircle(d)" @mouseleave="clearHighlight"/>
                <g v-if="highlightedDataPoint == d && selectedDataPoint != d">
                    <circle class="selection-circle" r="5" fill="#cf6a87" />
                    <text class="selection-label" x="10" y="-170" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
                    <rect class="selection-line" fill="#cf6a87" height="180" x="0" y="-180" width="2" />
                    <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-180" width="6" />
                </g>
                <g v-if="selectedDataPoint == d">
                    <circle class="selection-circle" r="5" fill="#cf6a87" />
                    <text class="selection-label" x="10" y="-188" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
                    <rect class="selection-line" fill="#cf6a87" height="200" x="0" y="-200" width="2" />
                    <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-200" width="6" />
                </g>
                </g>
            </g>
            </svg>
            <div id="slider">
            <v-slider :style="{color: '#f8a5c2'}"
            label="JAARTAL"
            thumb-label="always"
            :thumb-color = "'#c44569'"
            min="1960"
            max="2020"
            step="1"
            color="#c44569"
            label-color="c44569"
            v-model="sliderValue"
            ></v-slider>
            </div>
            
        </div>
          
    </v-main>

Er zijn hier een aantal kleine veranderingen gebeurd zoals het canvas dat breder is zodat de data van Niger ook zichtbaar blijft. Er zijn ook enkele kleuren veranderd. Ik heb ook wat gespeeld met sommige x/y waardes om alles wat netter te krijgen. Belangrijk is onderstaande code:

    <g v-for="(d, index) in this.data" :key="index">
        <g :transform="`translate(${20 + d[sliderValue]/8 * axisWidth}, 200)`">
            <circle class="populationCircle" :r="this.populationScale(d.population)" fill="#c44569"  opacity="0.75" @mousedown="selectCircle(d)"  @mouseover="highlightCircle(d)" @mouseleave="this.clearHighlight"/>
            <g v-if="this.highlightedDataPoint == d && this.selectedDataPoint != d">
                <circle class="selection-circle" r="5" fill="#cf6a87" />
                <text class="selection-label" x="10" y="-170" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
                <rect class="selection-line" fill="#cf6a87" height="180" x="0" y="-180" width="2" />
                <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-180" width="6" />
            </g>
            <g v-if="this.selectedDataPoint == d">
                <circle class="selection-circle" r="5" fill="#cf6a87" />
                <text class="selection-label" x="10" y="-188" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
                <rect class="selection-line" fill="#cf6a87" height="200" x="0" y="-200" width="2" />
                <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-200" width="6" />
            </g>
        </g>
    </g>

Vooreerst zijn er events toegevoegd. De populatiecirkels luisteren nu naar enkele events: `@mouseDown`, `@mouseOver` en `@mouseLeave`. Die zijn gekoppeld aan enkele functies die nu nog niet bestaan, we gaan die zo dadelijk toevoegen. Vervolgens gebeurt er een vertakking:

    <g :transform="`translate(${20 + d[sliderValue]/8 * axisWidth}, 200)`">
    <circle class="populationCircle" :r="populationScale(d.population)" fill="#c44569"  opacity="0.75" @mousedown="selectCircle(d)"  @mouseover="highlightCircle(d)" @mouseleave="clearHighlight"/>
    <g v-if="highlightedDataPoint == d && selectedDataPoint != d">
        <circle class="selection-circle" r="5" fill="#cf6a87" />
        <text class="selection-label" x="10" y="-170" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
        <rect class="selection-line" fill="#cf6a87" height="180" x="0" y="-180" width="2" />
        <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-180" width="6" />
    </g>
    <g v-if="selectedDataPoint == d">
        <circle class="selection-circle" r="5" fill="#cf6a87" />
        <text class="selection-label" x="10" y="-188" fill="#cf6a87"> {{d.Country.toUpperCase() + ' ' + d[sliderValue]}} </text>
        <rect class="selection-line" fill="#cf6a87" height="200" x="0" y="-200" width="2" />
        <rect class="selection-line" fill="#cf6a87" height="6" x="-2" y="-200" width="6" />
    </g>
    </g>

De `v-if` statements kijken beide naar de componentsvariabele die we ook zonet hebben toegevoegd: `highlightedDataPoint` en `selectedDataPoint`. Het verschil is miniem. De lijn van de geselecteerde cirkel wordt wat hoger getekend. In principe had je dit dus ook kunnen oplossen zonder `v-if`, maar als we in de toekomst een groter verschil willen in uiterlijk is deze vertakking wel erg handig. 
We moeten nog de functies toevoegen die aangeroepen worden bij de muisevents en die ook effectief de componentsvariabele zullen invullen:

    function highlightCircle(d) {
        highlightedDataPoint.value = d;
    }
    function selectCircle(d) {
        selectedDataPoint.value = d;
    }
    function clearHighlight() {
        highlightedDataPoint.value = null;
    }

Er gebeurt hier niet veel. Als de muis beweegt over een cirkel, dan wordt `highlightCircle` opgeroepen (want die cirkel heeft `@mouseOver="highlightCircle(d)`"). Op het moment dat dat gebeurt geeft de cirkel ook `d` door aan `highlightCircle`. `d` bevat de data van de cirkel (want die gebruiken we op dat moment ook om de cirkel juist te tekenen. `d` is onze lusvariabele.). We slaan dat datapunt op, en zoals eerder gezegd gebruiken we dat in onze html om te vergelijken.

### Sorteren
Het is nog heel moeilijk om de juiste cirkels te selecteren. Dat komt omdat SVG de elementen in de volgorde tekent zoals ze in de html staan. Onze cirkels worden getekend in een `v-for`, dus ze worden getekend in de volgorde van onze array. Die staat op dit moment alfabetisch, maar dat betekent dat als een grotere cirkel een kleinere cirkel overlapt en als die grote cirkel later getekend werd dan de kleine cirkel, dan is het bijna onmogelijk om die cirkel te selecteren. Daarom gaan we onze array nog sorteren op basis van populatiegrootte. We gaan de grote cirkels eerst tekenen en de kleine cirkels steeds als laatste. We doen dat in onze `onMounted()` functie waar we onze data inlazen:

    ...
    fertData = fertData.sort((a,b) => {return b.population - a.population});
    data.value = fertData;
    ...

De `sort` functie zal onze `fertData` nog netjes sorteren vooraleer we die opslaan in onze componentsvariabele `data`. Die gesorteerde array zal de cirkels tekenen in volgorde van groot naar klein, waardoor de kleine cirkels "bovenop" de grote cirkels getekend worden en dus gemakkelijker selecteerbaar zijn.

Ten slotte nog enkele details:
* Er is wat css animatie gebruikt! Kijk gerust eens hoe die werkt.
* De css property `pointer-events` is toegevoegd aan de `selection-circle` en `selection-line`. Die zorgt ervoor dat deze svg elementen geen muis events zullen opvangen. Zet die gerust eens uit en kijk maar naar het verschil: de svg elementen worden bovenop de populatiecirkels getekend en dat leidt tot een zeer disruptieve gebruikerservaring.

Voor de volledigheid vind je hieronder nog de volledige `onMounted()` functie:
    
    onMounted(async () => {

    let popData = await d3.csv("population.csv");
    let fertData = await d3.csv("fertility.csv");

    const countries = [
        "Belgium",
        "China",
        "Turkiye",
        "Germany",
        "Finland",
        "Niger"
    ];

    fertData = fertData.filter(r => countries.includes(r.Country));
    popData = popData.filter(r=> countries.includes(r['Country Name']));

    
    for (let i = 0; i < fertData.length; i++) 
    {
        const pop = popData.find(r => r['Country Name'] == fertData[i].Country);
        fertData[i].population = +pop['2021'];

        for(let j = 1960; j <= 2020; j++)
        {
        fertData[i]['' + j] = +fertData[i]['' + j];
        }
    }

    const populationExtents = d3.extent(fertData, d => d.population);
    populationScale = d3.scaleSqrt()
        .domain([0, populationExtents[1]])
        .range([0, 150]);

    fertData = fertData.sort((a,b) => {return b.population - a.population});
    data.value = fertData;

    console.log(fertData);
    console.log(data.value);
    })


