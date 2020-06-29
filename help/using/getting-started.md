---
title: Guida introduttiva ad HTL
description: HTL supportato da AEM sostituisce JSP come sistema di modelli lato server preferito e consigliato per HTML in AEM.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2490'
ht-degree: 0%

---


# Guida introduttiva ad HTL {#getting-started-with-htl}

HTML Template Language (HTL) supportato da  Adobe Experience Manager (AEM) è il sistema di modelli lato server preferito e consigliato per HTML in AEM. Sostituisce JSP (JavaServer Pages) come utilizzato nelle versioni precedenti di AEM.

>[!NOTE]
>
>Per eseguire la maggior parte degli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione live denominato [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) .

## HTL su JSP {#htl-over-jsp}

È consigliabile che i nuovi progetti AEM usino il linguaggio HTML Template Language, in quanto offre più vantaggi rispetto a JSP. Per i progetti esistenti, tuttavia, una migrazione ha senso solo se si stima che sarà meno impegno rispetto al mantenimento degli JSP esistenti per i prossimi anni.

Ma passare a HTL non è necessariamente una scelta completa o nulla, perché i componenti scritti in HTL sono compatibili con i componenti scritti in JSP o ESP. Ciò significa che i progetti esistenti possono utilizzare senza problemi HTL per i nuovi componenti, mantenendo allo stesso tempo JSP per i componenti esistenti.

Anche all’interno dello stesso componente, i file HTL possono essere utilizzati insieme a JSP ed ESP. L&#39;esempio seguente mostra sulla **riga 1** come includere un file HTL da un file JSP e sulla **riga 2** come includere un file JSP da un file HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Domande frequenti{#frequently-asked-questions}

Prima di iniziare a usare il linguaggio HTML Template Language, iniziamo con le risposte preliminari ad alcune domande relative all&#39;argomento JSP vs HTL.

**HTL ha qualche limite che JSP non ha?** - HTL non ha davvero limiti rispetto a JSP nel senso che ciò che può essere fatto con JSP dovrebbe anche essere raggiungibile con HTL. Tuttavia, HTL è per progettazione più severo rispetto a JSP in diversi aspetti, e ciò che può essere ottenuto in un singolo file JSP, potrebbe dover essere separato in una classe Java o un file JavaScript per essere raggiungibile in HTL. Ma questo è generalmente desiderato per garantire una buona separazione delle preoccupazioni tra la logica e il markup.

**HTL supporta le librerie di tag JSP?** - No, ma come mostrato nella sezione [Caricamento delle librerie](getting-started.md#loading-client-libraries) client, le istruzioni di [modello e di chiamata](block-statements.md#template-call) offrono un pattern simile.

**È possibile estendere le funzioni HTL su un progetto AEM?** - No, non possono. HTL dispone di potenti meccanismi di estensione per il riutilizzo della logica ( [Use-API](getting-started.md#use-api-for-accessing-logic) ) e della marcatura (le istruzioni di [modello e di chiamata](block-statements.md#template-call) ), che possono essere utilizzati per modulare il codice dei progetti.

**Quali sono i principali vantaggi di HTL rispetto a JSP?** - Sicurezza ed efficienza del progetto sono i principali vantaggi, che sono descritti in dettaglio nella [Panoramica](overview.md).

**Alla fine JSP andrà via?** - Alla data corrente, non ci sono piani in questa direzione.

## Concetti fondamentali di HTL {#fundamental-concepts-of-htl}

Il linguaggio HTML Template Language utilizza un linguaggio di espressione per inserire parti di contenuto nella marcatura rappresentata, e gli attributi di dati HTML5 per definire istruzioni su blocchi di markup (come condizioni o iterazioni). Man mano che HTL viene compilato nei servlet Java, le espressioni e gli attributi dei dati HTL vengono valutati completamente sul lato server e non rimane nulla visibile nell’HTML risultante.

### Blocchi ed espressioni {#blocks-and-expressions}

Di seguito è riportato un primo esempio, che potrebbe essere contenuto come in un **`template.html`** file:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

È possibile distinguere due diversi tipi di sintassi:

* **[Istruzioni](block-statements.md)**blocco - Per visualizzare l&#39;elemento **&lt;h1>**in modo condizionale, viene utilizzato un attributo di dati[`data-sly-test`](block-statements.md#test)HTML5. HTL fornisce più attributi di questo tipo, che consentono di associare il comportamento a qualsiasi elemento HTML, e tutti hanno il prefisso`data-sly`.

* **[Lingua](expression-language.md)**espressione - le espressioni HTL sono delimitate da caratteri`${`e`}`. In fase di esecuzione, queste espressioni vengono valutate e il relativo valore viene immesso nel flusso HTML in uscita.

Le due pagine collegate sopra forniscono l&#39;elenco dettagliato delle funzioni disponibili per la sintassi.

### Elemento SLY {#the-sly-element}

Un concetto centrale di HTL consiste nell&#39;offrire la possibilità di riutilizzare gli elementi HTML esistenti per definire le istruzioni dei blocchi, evitando di dover inserire delimitatori aggiuntivi per definire dove inizia e finisce l&#39;istruzione. Questa annotazione discrezionale della marcatura per trasformare un HTML statico in un modello dinamico funzionante offre il vantaggio di non interrompere la validità del codice HTML, e quindi di visualizzare comunque correttamente, anche come file statici.

Tuttavia, a volte potrebbe non esistere un elemento esistente nella posizione esatta in cui deve essere inserita un&#39;istruzione block. Per tali casi, è possibile inserire un elemento SLY speciale che verrà rimosso automaticamente dall&#39;output, mentre si eseguono le istruzioni di blocco allegate e ne viene visualizzato il contenuto di conseguenza.

Esempio:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

restituirà un risultato simile al seguente HTML, ma solo se sono definite entrambe le proprietà, una **`jcr:title`** e una **`jcr:description`** proprietà e se nessuna delle due è vuota:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa da tenere presente è utilizzare l&#39;elemento SLY solo quando nessun elemento esistente poteva essere annotato con l&#39;istruzione block, perché gli elementi SLY dissuadono il valore offerto dal linguaggio per non modificare l&#39;HTML statico quando lo rende dinamico.

Ad esempio, se l’esempio precedente era già stato incluso in un elemento DIV, l’elemento SLY aggiunto sarebbe abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

e l&#39;elemento DIV potrebbe essere stato annotato con la condizione:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

>[!NOTE]
>
>L’elemento SLY è stato introdotto con AEM 6.1 o HTL 1.1.
>
>Prima di tale data, era necessario utilizzare l&#39; [`data-sly-unwrap`](block-statements.md) attributo.

### Commenti HTL {#htl-comments}

L’esempio seguente mostra sulla **riga 1** un commento HTL e sulla **riga 2** un commento HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

I commenti HTL sono commenti HTML con una sintassi JavaScript aggiuntiva. L&#39;intero commento HTL e tutto ciò che è all&#39;interno saranno completamente ignorati dal processore e rimossi dall&#39;output.

Tuttavia, il contenuto dei commenti HTML standard verrà trasmesso e le espressioni all&#39;interno del commento verranno valutate.

I commenti HTML non possono contenere commenti HTL e viceversa.

### Contesti speciali {#special-contexts}

Per poter utilizzare al meglio HTL, è importante comprendere bene le conseguenze che ne derivano in base alla sintassi HTML.

### Nomi di elementi e attributi {#element-and-attribute-names}

Le espressioni possono essere inserite solo nei valori di testo o attributi HTML, ma non nei nomi di elementi o attributi, oppure non sarebbero più HTML validi. Per impostare i nomi degli elementi in modo dinamico, è possibile utilizzare l&#39; [`data-sly-element`](block-statements.md#element) istruzione sugli elementi desiderati e per impostare in modo dinamico i nomi degli attributi, anche impostando più attributi contemporaneamente, è possibile utilizzare l&#39; [`data-sly-attribute`](block-statements.md#attribute) istruzione.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contesti Senza Istruzioni Blocca {#contexts-without-block-statements}

Poiché HTL utilizza gli attributi di dati per definire le istruzioni di blocco, non è possibile definire tali istruzioni di blocco all&#39;interno dei seguenti contesti, e solo le espressioni possono essere utilizzate in questo caso:

* Commenti HTML
* Elementi script
* Elementi stile

Il motivo è che il contenuto di questi contesti è testo e non HTML, e gli elementi HTML contenuti sarebbero considerati come dati di carattere semplici. Pertanto, senza elementi HTML reali, non è possibile eseguire **`data-sly`** attributi.

Questo può sembrare una grande restrizione, ma è una restrizione desiderata, perché il linguaggio HTML Template Language non dovrebbe essere abusato per generare un output che non è HTML. La sezione [Use-API per l&#39;accesso alla logica](getting-started.md#use-api-for-accessing-logic) di seguito illustra come è possibile invocare logica aggiuntiva dal modello, che può essere utilizzata se è necessaria per preparare output complessi per questi contesti. Ad esempio, un modo semplice per inviare i dati dal back-end a uno script front-end consiste nel fare in modo che la logica del componente generi una stringa JSON, che può quindi essere inserita in un attributo di dati con una semplice espressione HTL.

L&#39;esempio seguente illustra il comportamento dei commenti HTML, ma negli elementi di script o di stile viene osservato lo stesso comportamento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

restituirà un output simile al seguente HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contesti espliciti richiesti {#explicit-contexts-required}

Come spiegato nella sezione [Automatic Context-Aware Escaping](getting-started.md#automatic-context-aware-escaping) (Sfuggimento automatico in base al contesto), un obiettivo di HTL è quello di ridurre i rischi legati all&#39;introduzione di vulnerabilità di script (XSS) tra siti applicando automaticamente la escape in base al contesto a tutte le espressioni. HTL è in grado di rilevare automaticamente il contesto delle espressioni inserite all&#39;interno di tag HTML, ma non analizza la sintassi di JavaScript o CSS in linea e pertanto si affida allo sviluppatore per specificare esplicitamente quale contesto esatto debba essere applicato a tali espressioni.

Poiché l&#39;applicazione della escape corretta genera vulnerabilità XSS, HTL rimuove l&#39;output di tutte le espressioni che si trovano nei contesti di script e di stile quando il contesto non è stato dichiarato.

Di seguito è riportato un esempio di come impostare il contesto per le espressioni inserite all&#39;interno di script e stili:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Per ulteriori dettagli su come controllare la escape, vedere la sezione Contesto [di visualizzazione della lingua di](expression-language.md#display-context) espressione.

### Limitazioni di sollevamento dei contesti speciali {#lifting-limitations-of-special-contexts}

Nei casi speciali in cui è necessario bypassare le restrizioni dei contesti script, stile e commento, è possibile isolare il contenuto in un file HTL separato. Tutto ciò che si trova nel proprio file verrà interpretato da HTL come un normale frammento HTML, dimenticando il contesto di limitazione dal quale potrebbe essere stato incluso.

Per un esempio, vedere la sezione [Utilizzo dei modelli](getting-started.md#working-with-client-side-templates) lato client più in basso.

>[!CAUTION]
>
>Questa tecnica può introdurre vulnerabilità di scripting cross-site (XSS) e gli aspetti di sicurezza dovrebbero essere attentamente studiati se questo viene utilizzato. Di solito ci sono modi migliori per attuare la stessa cosa piuttosto che affidarsi a questa pratica.

## Funzionalità generali di HTL {#general-capabilities-of-htl}

Questa sezione descrive rapidamente le funzioni generali del linguaggio HTML Template Language.

### Use-API per l&#39;accesso alla logica {#use-api-for-accessing-logic}

Considerare l&#39;esempio seguente:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

E il seguente file JavaScript eseguito sul lato `logic.js` server, posizionato accanto ad esso:

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Poiché il linguaggio HTML Template Language non consente di mixare il codice all&#39;interno della marcatura, offre invece il meccanismo di estensione Use-API per eseguire facilmente il codice dal modello.

L&#39;esempio precedente utilizza JavaScript eseguito sul lato server per ridurre il titolo a 10 caratteri, ma avrebbe anche potuto utilizzare codice Java per fare lo stesso fornendo un nome di classe Java completo. In genere, la logica di business dovrebbe essere integrata in Java, ma quando il componente necessita di alcune modifiche specifiche della vista rispetto a quanto fornito dall&#39;API Java, può essere utile utilizzare JavaScript eseguito sul lato server per farlo.

Ulteriori informazioni in merito nelle sezioni seguenti:

* La sezione della [`data-sly-use` dichiarazione](block-statements.md#use) spiega tutto ciò che è possibile fare con tale affermazione.
* La pagina [](use-api.md) Use-API fornisce alcune informazioni utili per scegliere se scrivere la logica in Java o in JavaScript.
* E per informazioni dettagliate su come scrivere la logica, dovrebbero essere di aiuto le pagine [JavaScript Use-API](use-api-javascript.md) e [Java Use-API](use-api-java.md) .

### Scorrimento automatico in base al contesto {#automatic-context-aware-escaping}

Considerare l&#39;esempio seguente:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Nella maggior parte dei linguaggi dei modelli, questo esempio potrebbe creare una vulnerabilità di scripting tra siti (XSS), perché anche quando tutte le variabili sono sottoposte a escape HTML automatico, l&#39; `href` attributo deve ancora essere dotato di una specifica funzione di escape URL. Questa omissione è uno degli errori più comuni, perché può essere facilmente dimenticata, ed è difficile individuare in modo automatizzato.

Per facilitare questa fase, il linguaggio HTML Template Language (Linguaggio modello HTML) evita automaticamente ogni variabile in base al contesto in cui viene inserita. Questo è possibile grazie al fatto che HTL comprende la sintassi di HTML.

Presupponendo il seguente `logic.js` file:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

L&#39;esempio iniziale darà come risultato il seguente risultato:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Notate come i due attributi sono stati preceduti da una diversa escape, perché HTL sa che `href` e gli `src` attributi devono essere preceduti da un escape per il contesto URI. Inoltre, se l’URI iniziasse con **`javascript:`**, l’attributo sarebbe stato rimosso completamente, a meno che il contesto non fosse stato esplicitamente modificato in altro modo.

Per ulteriori dettagli su come controllare la escape, vedere la sezione Contesto [di visualizzazione della lingua di](expression-language.md#display-context) espressione.

### Rimozione automatica di attributi vuoti {#automatic-removal-of-empty-attributes}

Considerare l&#39;esempio seguente:

```xml
<p class="${properties.class}">some text</p>
```

Se il valore della `class` proprietà risulta vuoto, il linguaggio HTML Template Language rimuove automaticamente l&#39;intero `class` attributo dall&#39;output.

Anche in questo caso, ciò è possibile, perché HTL comprende la sintassi HTML e può quindi mostrare gli attributi con valori dinamici solo se il loro valore non è vuoto. Ciò è estremamente conveniente in quanto evita di aggiungere un blocco di condizione intorno agli attributi, il che avrebbe reso la marcatura non valida e illeggibile.

Inoltre, il tipo di variabile inserita nell&#39;espressione conta:

* **Stringa:**
   * **not empty:** Imposta la stringa come valore attributo.
   * **empty:** Rimuove completamente l&#39;attributo.

* **Numero:** Imposta il valore come valore attributo.

* **Booleano:**
   * **true:** Visualizza l&#39;attributo senza valore (come attributo HTML booleano)
   * **false:** Rimuove completamente l&#39;attributo.

Di seguito è riportato un esempio di come un&#39;espressione booleana consentirebbe di controllare un attributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Anche per l&#39;impostazione degli attributi, l&#39; [`data-sly-attribute`](block-statements.md#attribute) istruzione potrebbe essere utile.

## Pattern comuni con HTL {#common-patterns-with-htl}

In questa sezione vengono presentati alcuni scenari comuni e viene illustrato come risolvere al meglio tali problemi con il linguaggio HTML Template Language.

### Caricamento delle librerie client {#loading-client-libraries}

In HTL, le librerie client vengono caricate tramite un modello helper fornito da AEM, accessibile tramite [`data-sly-use`](block-statements.md#use). In questo file sono disponibili tre modelli, che possono essere richiamati tramite [`data-sly-call`](block-statements.md#template-call):

* **`css`** - Carica solo i file CSS delle librerie client di riferimento.
* **`js`** - Carica solo i file JavaScript delle librerie client di riferimento.
* **`all`** - Carica tutti i file delle librerie client di riferimento (sia CSS che JavaScript).

Ogni modello di supporto prevede un&#39; **`categories`** opzione per fare riferimento alle librerie client desiderate. Tale opzione può essere una matrice di valori stringa o una stringa contenente un elenco di valori separati da virgola.

Di seguito sono riportati due brevi esempi:

### Caricamento completo di più librerie client {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Riferimento a una libreria client in diverse sezioni di una pagina {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

Nel secondo esempio precedente, nel caso in cui gli elementi HTML **`head`** e **`body`** gli elementi siano inseriti in file diversi, il **`clientlib.html`** modello dovrà quindi essere caricato in ciascun file che ne ha bisogno.

La sezione relativa alle istruzioni di [modello e di chiamata](block-statements.md#template-call) fornisce ulteriori dettagli sul funzionamento di tali modelli.

### Trasmissione di dati al client {#passing-data-to-the-client}

Il modo migliore e più elegante per trasmettere i dati al client in generale, ma ancora di più con HTL, è utilizzare gli attributi di dati.

L&#39;esempio seguente mostra come la logica (che potrebbe anche essere scritta in Java) possa essere utilizzata per serializzare in modo molto conveniente a JSON l&#39;oggetto da passare al client, che può quindi essere facilmente collocato in un attributo di dati:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

Da qui, è facile immaginare come un JavaScript lato client possa accedere a tale attributo e analizzare nuovamente il JSON. Si tratta, ad esempio, del codice JavaScript corrispondente da inserire in una libreria client:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilizzo dei modelli lato client {#working-with-client-side-templates}

Un caso speciale, in cui la tecnica spiegata nella sezione Limitazioni [di sollevamento dei contesti](getting-started.md#lifting-limitations-of-special-contexts) speciali può essere utilizzata legittimamente, è quello di scrivere modelli lato client (come ad esempio Handlebars) che si trovano all&#39;interno di elementi **script** . Il motivo per cui questa tecnica può essere utilizzata in questo caso è perché l&#39;elemento **script** non contiene quindi JavaScript come ipotizzato, ma ulteriori elementi HTML. Ecco un esempio di come potrebbe funzionare:

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Come mostrato sopra, la marcatura che verrà inclusa nell&#39; **`script`** elemento può contenere istruzioni di blocco HTL e le espressioni non devono fornire contesti espliciti, perché il contenuto del modello Handlebars è stato isolato nel proprio file. Inoltre, questo esempio mostra come l&#39;HTL eseguito sul lato server (come nell&#39; **`h2`** elemento) possa essere combinato con un linguaggio modello eseguito sul lato client, come Handlebars (mostrato sull&#39; **`h3`** elemento).

Una tecnica più moderna, tuttavia, sarebbe utilizzare l&#39; **`template`** elemento HTML, in quanto il vantaggio sarebbe che non è necessario isolare il contenuto dei modelli in file separati.

**Articolo successivo:**

* [Lingua](expression-language.md) espressione - per apprendere in dettaglio cosa si può fare all&#39;interno delle espressioni HTL.
* [Blocca istruzioni](block-statements.md) - per scoprire tutte le istruzioni di blocco disponibili in HTL e come utilizzarle.
