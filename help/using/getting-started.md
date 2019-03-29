---
title: Guida introduttiva ad HTL
seo-title: Guida introduttiva ad HTL
description: L'HTL supportato da AEM sostituisce JSP come sistema di modelli lato
  server preferito e consigliato per HTML in AEM.
seo-description: Il linguaggio HTML Template Language (HTL) supportato da Adobe Experience
  Manager sostituisce JSP come sistema di modelli lato server consigliato per HTML
  in AEM.
uuid: 4 a 7 d 6748-8 cdf -4280-a 85 d -6 c 5319 abf 487
content-type: riferimento
topic-tags: introduction
discoiquuid: 3 bf 2 ca 75-0 d 68-489 d-bd 1 c -1 d 4 fd 730 c 61 a
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Guida introduttiva ad HTL {#getting-started-with-htl}

Il linguaggio HTL (HTML Template Language) supportato da Adobe Experience Manager (AEM) prende il posto di JSP (Javaserver Pages) come sistema di modelli lato server preferito per HTML in AEM.

>[!NOTE]
>
>Per eseguire la maggior parte degli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione live denominato [Leggi ciclo](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) di stampa eval.
>
>La community di AEM ha generato una serie [di articoli, video e seminari Web](related-community-articles.md) correlati all'uso di HTL.

## HTL over JSP {#htl-over-jsp}

È consigliabile utilizzare i nuovi progetti AEM per utilizzare il linguaggio HTML Template Language, in quanto offre più vantaggi rispetto a JSP. Tuttavia, per i progetti esistenti, una migrazione ha senso solo se si stima che non si tratta di un impegno rispetto alla manutenzione dei JSP esistenti per i prossimi anni.

Tuttavia, passare a HTL non è necessariamente un tipo di scelta completo, poiché i componenti scritti in HTL sono compatibili con i componenti scritti in JSP o ESP. Ciò significa che i progetti esistenti possono senza problemi utilizzare HTL per nuovi componenti, mantenendo JSP per i componenti esistenti.

Anche all'interno dello stesso componente, i file HTL possono essere utilizzati con JSP e esps. L'esempio seguente mostra sulla **riga 1** come includere un file HTL da un file JSP e sulla **riga 2** come un file JSP può essere incluso da un file HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Domande frequenti{#frequently-asked-questions}

Prima di iniziare a utilizzare il linguaggio HTML Template Language, iniziamo con la risposta ad alcune domande correlate all'argomento JSP rispetto a HTL.

**HTL ha alcune limitazioni che JSP non ha?**L'HTL non presenta limiti rispetto a JSP, nel senso che ciò che può essere effettuato con JSP può essere ottenuto anche con HTL. Tuttavia, HTL è un progetto più rigoroso di JSP in diversi aspetti e il risultato può essere ottenuto tutti in un unico file JSP, che potrebbe essere necessario separare in una classe Java o in un file javascript da raggiungere in HTL. In genere ciò serve a distinguere tra la logica e la marcatura.

**HTL supporta le librerie JSP tag?**No, ma come mostrato nella [sezione Caricamento librerie](getting-started.md#loading-client-libraries) client, le [istruzioni di modello e chiamata](block-statements.md#template-call) offrono un pattern simile.

**Le funzioni HTL possono essere estese su un progetto AEM?**** No, come mostrato nella sezione [Caricamento delle librerie](getting-started.md#loading-client-libraries) client, le [istruzioni di modello e chiamata](block-statements.md#template-call) offrono un pattern simile.
No, non possono. HTL offre sofisticati meccanismi di estensione per riutilizzare logica - l' [API Use-API](getting-started.md#use-api-for-accessing-logic) e la marcatura (le [istruzioni modello e chiamate](block-statements.md#template-call) ), che possono essere utilizzate per modulare il codice dei progetti.

**Quali sono i vantaggi principali di HTL su JSP?**La sicurezza e l'efficienza dei progetti sono i vantaggi principali, descritti nella [panoramica](overview.md).

**In futuro JSP scomparirà?**Alla data corrente, non sono previste piani lungo queste linee.

## Concetti fondamentali di HTL {#fundamental-concepts-of-htl}

Il linguaggio HTML Template Language utilizza un linguaggio di espressioni per inserire parti di contenuto negli attributi di rendering e di dati HTML 5 per definire istruzioni su blocchi di marcatura (come condizioni o iterazioni). Quando HTL viene compilato in servlet Java, le espressioni e gli attributi dei dati HTL sono valutati entrambi sul lato server e non rimangono visibili nel codice HTML risultante.

### Blocchi ed espressioni {#blocks-and-expressions}

Di seguito è riportato un primo esempio, che potrebbe essere contenuto in un **`template.html`** file:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

È possibile distinguere due tipi diversi di sintassi:

* **[Istruzioni
Bloccate](block-statements.md)**per visualizzare in modo condizionato il **& amp; lt; h 1 & amp; gt;** , viene utilizzato un `[data-sly-test](block-statements.md#test)` attributo di dati HTML 5. HTL fornisce più attributi, che consentono di allegare comportamenti a qualsiasi elemento HTML e tutti hanno il prefisso `data-sly`.

* **[Espressioni HTL della lingua](expression-language.md)**
espressione sono delimitate da caratteri `${` e `}`. In fase di esecuzione, queste espressioni vengono valutate e il relativo valore viene inserito nel flusso HTML in uscita.

Le due pagine collegate sopra forniscono l'elenco dettagliato di funzioni disponibili per la sintassi.

### Elemento SLY {#the-sly-element}

>[!NOTE]
>
>L'elemento SLY è stato introdotto con AEM 6.1 o HTL 1.1.
>
>Prima di tale operazione, era necessario utilizzare l' `[data-sly-unwrap](block-statements.md)` attributo.

Un concetto centrale di HTL consiste nell'offrire la possibilità di riutilizzare elementi HTML esistenti per definire istruzioni di blocco, che evitano di inserire altri delimitatori per definire l'inizio e la fine dell'istruzione. Questa annotazione non intrusiva della marcatura per trasformare un HTML statico in un modello dinamico funzionante offre il vantaggio di non interrompere la validità del codice HTML e di quindi continuare a visualizzare correttamente, anche come file statici.

Tuttavia, talvolta non è presente un elemento esistente nella posizione esatta in cui inserire un'istruzione block. Per tali casi, è possibile inserire un elemento SLY speciale che verrà rimosso automaticamente dall'output, durante l'esecuzione delle istruzioni bloccate allegate e visualizzarne il contenuto di conseguenza.

Esempio di seguito:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

restituirà un elemento simile a quello di HTML, ma solo se sono presenti entrambe, una **`jcr:title`****`jcr:decription`** e una proprietà e se nessuno di essi è vuoto:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa può fare attenzione a utilizzare solo l'elemento SLY quando non è stato possibile inserire un elemento esistente con l'istruzione block, perché gli elementi SLY descrivono il valore offerto dalla lingua per non modificare l'HTML statico quando questo viene reso dinamico.

Ad esempio, se l'esempio precedente sarebbe stato racchiuso già in un elemento DIV, l'elemento SLY aggiunto sarebbe offensivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

e l'elemento DIV poteva essere annotato con la condizione:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Commenti HTL {#htl-comments}

L'esempio seguente mostra sulla **riga 1** un commento HTL e sulla **riga 2** an HTML comment:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

I commenti HTL sono commenti HTML con sintassi javascript aggiuntiva. L'intero commento HTL e qualsiasi altro elemento all'interno verranno completamente ignorati dal processore e rimossi dall'output.

Il contenuto dei commenti HTML standard sarà comunque trasmesso e le espressioni all'interno del commento saranno valutate.

I commenti HTML non possono contenere commenti HTL e viceversa.

### Contesti speciali {#special-contexts}

Per poter utilizzare al meglio HTL, è importante comprenderne le conseguenze in base alla sintassi HTML.

### Nomi degli elementi e degli attributi {#element-and-attribute-names}

Le espressioni possono essere inserite solo in valori di testo o attributi HTML, ma non all'interno dei nomi degli elementi o nomi degli attributi, oppure non dovrebbero più essere HTML valido. Per impostare i nomi degli elementi in modo dinamico, l' [`data-sly-element`](block-statements.md#element) istruzione può essere utilizzata sugli elementi desiderati e, per impostare in modo dinamico i nomi degli attributi, anche impostando più attributi contemporaneamente, l' [`data-sly-attribute`](block-statements.md#attribute) istruzione può essere utilizzata.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contesti senza istruzioni di blocco {#contexts-without-block-statements}

Poiché HTL utilizza attributi di dati per definire istruzioni block, non è possibile definire tali istruzioni block all'interno dei seguenti contesti, e solo le espressioni possono essere utilizzate qui:

* Commenti HTML
* Elementi di script
* Elementi di stile

Il motivo sta nel fatto che il contenuto di questi contesti è testo e non HTML, e gli elementi HTML saranno considerati come dati di caratteri semplici. Pertanto, senza elementi HTML reali, non possono essere eseguiti **`data-sly`** attributi.

Questo potrebbe sembrare una grande restrizione, ma è auspicabile, perché non dovrebbe essere utilizzato per generare output HTML per generare output che non sia HTML. L' [API Use-API per l'accesso](getting-started.md#use-api-for-accessing-logic) alla sezione Logica di seguito introduce la modalità di chiamata logica dal modello, che può essere utilizzata se è necessario preparare output complesso per questi contesti. Ad esempio, un modo semplice per inviare dati dal back-end a uno script front-end consiste nell'avere la logica del componente per generare una stringa JSON, che può quindi essere inserita in un attributo di dati con una semplice espressione HTL.

L'esempio seguente illustra il funzionamento dei commenti HTML, ma negli elementi script o di stile viene visualizzato lo stesso comportamento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

restituirà un elemento come HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contesti espliciti richiesti {#explicit-contexts-required}

Come spiegato nella sezione di escape [automatico basata su contesto](getting-started.md#automatic-context-aware-escaping) di seguito, un obiettivo di HTL è quello di ridurre i rischi legati all'introduzione di vulnerabilità di cross-site scripting (XSS) applicando automaticamente la escape in base al contesto a tutte le espressioni. Mentre HTL può rilevare automaticamente il contesto di espressioni collocate all'interno della marcatura HTML, non analizza la sintassi di javascript o CSS inline e quindi si affiderà allo sviluppatore per specificare esplicitamente il contesto esatto da applicare a tali espressioni.

Poiché non si applica la corretta escape nelle vulnerabilità XSS, HTL rimuove l'output di tutte le espressioni che si trovano in contesti di script e di stile quando il contesto non è stato dichiarato.

Di seguito è riportato un esempio di impostazione del contesto per le espressioni collocate all'interno di script e stili:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Per ulteriori dettagli su come controllare la navigazione, consultate la sezione relativa [alla visualizzazione della](expression-language.md#display-context) lingua di espressione Espressione.

### Limiti di rimozione di contesti speciali {#lifting-limitations-of-special-contexts}

Nei casi speciali in cui è necessario bypassare le restrizioni relative a script, contesti e contesti di commento, è possibile isolare il contenuto in un file HTL separato. Tutto ciò che si trova nel proprio file verrà interpretato da HTL come un normale frammento HTML, che dimentica il contesto di limitazione dal quale potrebbe essere stato incluso.

Per un esempio, consultate [la sezione Utilizzo della](getting-started.md#working-with-client-side-templates) sezione Modelli lato client.

>[!CAUTION]
>
>Questa tecnica può introdurre vulnerabilità di cross-site scripting (XSS), e gli aspetti di sicurezza dovrebbero essere analizzati con attenzione se questo viene utilizzato. In genere sono disponibili modi migliori per implementare la stessa cosa rispetto a questa procedura.

## Funzionalità generali di HTL {#general-capabilities-of-htl}

Questa sezione descrive rapidamente le funzioni generali del linguaggio HTML Template Language.

### Usa API per accesso logico {#use-api-for-accessing-logic}

Considerate l'esempio seguente:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

E seguito `logic.js` dal seguente file javascript eseguito sul lato server:

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Poiché il linguaggio HTML Template Language non consente di combinare codice all'interno della marcatura, offre il meccanismo di estensione Use-API per eseguire facilmente il codice dal modello.

Nell'esempio sopra viene utilizzato javascript lato server per abbreviare il titolo a 10 caratteri, ma potrebbe anche aver utilizzato il codice Java per fare allo stesso modo fornendo un nome di classe Java completo. In genere, la logica aziendale deve essere integrata in Java, ma quando il componente richiede alcune modifiche specifiche per la vista da quelle fornite dall'API Java, può essere utile utilizzare javascript lato server per farlo.

Ulteriori informazioni sulle sezioni seguenti:

* La sezione sull'istruzione [di utilizzo dati](block-statements.md#use) spiega tutto ciò che è possibile fare con tale istruzione.
* La [pagina Use-API](use-api.md) fornisce alcune informazioni per scegliere tra la scrittura della logica in Java o in javascript.
* Per informazioni dettagliate su come scrivere la logica, è necessario utilizzare [javascript Use-API](use-api-javascript.md) e [Java Use-API](use-api-java.md) .

### Escape automatico in base al contesto {#automatic-context-aware-escaping}

Considerate l'esempio seguente:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Nella maggior parte dei linguaggi dei modelli, questo esempio crea potenzialmente una vulnerabilità di cross-site scripting (XSS), poiché anche quando tutte le variabili vengono automaticamente create in HTML, l' `href` attributo deve comunque essere specificamente basato su URL. Questa ombreggiatura è uno degli errori più comuni, in quanto può essere facilmente dimenticata ed è difficile individuarla in modo automatizzato.

Per facilitare questa fase, il linguaggio HTML Template Language (Lingua modello HTML) ignora automaticamente ogni variabile nel contesto in cui viene posizionato. Ciò è possibile se l'HTL riconosce la sintassi di HTML.

Presupponendo `logic.js` il seguente file:

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

L'esempio iniziale darà luogo a un output successivo:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Osservate come i due attributi sono riusciti in modo diverso, poiché HTL sa che il contesto `href` e `src` gli attributi devono essere soggetti a escape per il contesto URI. Inoltre, se l'URI iniziava con **`javascript:`**, l'attributo sarebbe stato rimosso completamente, a meno che il contesto non fosse stato esplicitamente cambiato in altro.

Per ulteriori dettagli su come controllare la navigazione, consultate la sezione relativa [alla visualizzazione della](expression-language.md#display-context) lingua di espressione Espressione.

### Rimozione automatica degli attributi vuoti {#automatic-removal-of-empty-attributes}

Considerate l'esempio seguente:

```xml
<p class="${properties.class}">some text</p>
```

Se il valore della `class` proprietà è vuoto, il modello HTML Template (Lingua modello HTML) rimuove automaticamente l'intero `class` attributo dall'output.

Anche in questo caso è possibile, poiché HTL riconosce la sintassi HTML e può quindi mostrare condizionalmente attributi con valori dinamici solo se il valore non è vuoto. Ciò è molto utile quando si evita di aggiungere un blocco di condizione intorno agli attributi, che avrebbe reso la marcatura non valida e non leggibile.

Inoltre, il tipo della variabile inserita nell'espressione è rilevante:

* **Stringa:**
   * **non vuoto:** Imposta la stringa come valore dell'attributo.
   * **empty:** Rimuove l'attributo.

* **Numero:** Imposta il valore come valore dell'attributo.

* **Booleano:**
   * **true:** Visualizza l'attributo senza valore (come attributo HTML booleano
   * **false:** Rimuove l'attributo.

Di seguito viene fornito un esempio di come un'espressione booleana consentirebbe di controllare un attributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Per impostare gli attributi, l' [`data-sly-attribute`](block-statements.md#attribute) istruzione potrebbe essere utile.

## Pattern comuni con HTL {#common-patterns-with-htl}

Questa sezione introduce alcuni scenari comuni e come risolverli con il linguaggio HTML Template Language.

### Caricamento delle librerie client {#loading-client-libraries}

In HTL, le librerie client vengono caricate tramite un modello helper fornito da AEM, accessibile tramite [`data-sly-use`](block-statements.md#use). In questo file sono disponibili tre modelli, che possono essere chiamati [`data-sly-call`](block-statements.md#template-call)tramite:

* **`css`** - Carica solo i file CSS delle librerie client di riferimento.
* **`js`** - Carica solo i file javascript delle librerie client di riferimento.
* **`all`** - Carica tutti i file delle librerie client di riferimento (CSS e javascript).

Ogni modello helper prevede un **`categories`** 'opzione per fare riferimento alle librerie client desiderate. Questa opzione può essere un array di valori stringa, o una stringa contenente un elenco di valori separati da virgola.

Di seguito sono riportati due esempi brevi:

### Caricamento completo di librerie client multiple contemporaneamente {#loading-multiple-client-libraries-fully-at-once}

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

Nel secondo esempio sopra, nel caso in cui l'HTML **`head`** e **`body`** gli elementi siano posizionati in file diversi, il **`clientlib.html`** modello dovrà essere caricato in ogni file che lo richiede.

La sezione sulle istruzioni [modello e chiamata](block-statements.md#template-call) fornisce ulteriori dettagli su come dichiarare e richiamare tali modelli.

### Trasferimento di dati al client {#passing-data-to-the-client}

Il modo migliore e più elegante per trasmettere dati al client in generale, ma ancora più con HTL, è utilizzare gli attributi dei dati.

L'esempio seguente mostra come la logica (che può essere scritta anche in Java) può essere utilizzata per serializzare in modo molto conveniente JSON l'oggetto da passare al client, che può quindi essere facilmente inserito in un attributo dati:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```
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

Da qui, è facile immaginare in che modo un javascript lato client può accedere a tale attributo e analizzare nuovamente il JSON. Si tratta, ad esempio, del corrispondente javascript da inserire in una libreria client:

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilizzo dei modelli lato client {#working-with-client-side-templates}

Un caso speciale, dove la tecnica descritta nella sezione Limiti [di rimozione di Contesti speciali](getting-started.md#lifting-limitations-of-special-contexts) può essere utilizzata legittimamente, consiste nel scrivere modelli lato client (come Handlebars for instance) che si trovano all'interno **di elementi di script** . Poiché questa tecnica può essere utilizzata in modo sicuro in questo caso, perché l'elemento **di script** non contiene javascript come presupposto, ma altri elementi HTML. Esempio di come funziona:

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

Come mostrato qui sopra, la marcatura che sarà inclusa nell **`script`** 'elemento può contenere istruzioni di blocco HTL e le espressioni non devono fornire contesti espliciti, in quanto il contenuto del modello Handlebars è stato isolato nel proprio file. Inoltre, questo esempio mostra il modo in cui il lato server eseguirà HTL (come sull **`h2`** 'elemento) può essere combinato con una lingua del modello eseguito lato client, come Handlebars (mostrato sull' **`h3`** elemento).

Una tecnica più moderna, invece, sarebbe utilizzare l' **`template`** elemento HTML, in quanto il vantaggio sarà che non deve isolare il contenuto dei modelli in file separati.

**Leggi avanti:**

* [Linguaggio](expression-language.md) espressione: per apprendere in dettaglio cosa può essere fatto nelle espressioni HTL.
* [Istruzioni](block-statements.md) di blocco: per scoprire tutte le istruzioni block disponibili in HTL e come utilizzarle.
