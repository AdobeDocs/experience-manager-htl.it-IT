---
title: Guida introduttiva ad HTL
description: Scopri HTL, il sistema di modelli lato server preferito e consigliato per HTML in AEM e i principali concetti della lingua e dei suoi costrutti fondamentali.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 56%

---


# Guida introduttiva ad HTL {#getting-started-with-htl}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML in Adobe Experience Manager. Come in tutti i sistemi di modelli lato server di HTML, un file HTL definisce l’output inviato al browser specificando lo stesso HTML, una logica di presentazione di base e variabili da valutare in fase di esecuzione.

Questo documento fornisce una panoramica dello scopo di HTL e un&#39;introduzione ai concetti e costrutti fondamentali della lingua.

>[!TIP]
>
>Questo documento presenta lo scopo di HTL e una panoramica della sua struttura e dei suoi concetti fondamentali. Se hai domande sulla sintassi specifica, consulta la [Specifica HTL.](specification.md)

## Livelli HTL {#layers}

HTL utilizzato in AEM può essere definito da un numero di livelli.

1. **[Specifiche HTL](specification.md)** - HTL è una specifica open-source, indipendente dalla piattaforma, che chiunque può implementare.
1. **[Motore di scripting HTL Sling](specification.md)** - Il progetto Sling ha creato l’implementazione di riferimento di HTL, utilizzato da AEM.
1. **[Estensioni AEM](specification.md)** - AEM si basa sul motore di script HTL Sling per offrire agli sviluppatori funzionalità convenienti specifiche per AEM.

Questa documentazione HTL si concentra sull’utilizzo di HTL per sviluppare soluzioni AEM. Come tale, tocca tutti e tre i livelli, collegando le risorse esterne come necessario.

## Concetti fondamentali di HTL {#fundamental-concepts-of-htl}

HTML Template Language utilizza un linguaggio di espressione per inserire parti di contenuto nel markup rappresentato e attributi di dati HTML5 per definire istruzioni su blocchi di markup (come condizioni o iterazioni). Man mano che HTL viene compilato nei servlet Java, le espressioni e gli attributi dei dati HTL vengono valutati interamente lato server e nulla rimane visibile nell’HTML risultante.

>[!TIP]
>
>Per eseguire la maggior parte degli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione live denominato [Read Eval Print Loop](https://github.com/adobe/aem-htl-repl).

### Blocchi ed espressioni {#blocks-and-expressions}

Di seguito un primo esempio, che potrebbe essere contenuto così com’è in un file `template.html`:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Si possono distinguere due diversi tipi di sintassi:

* **Istruzioni a blocchi** - Per visualizzare il `<h1>` un elemento `data-sly-test` Viene utilizzato l’attributo dati di HTML5. HTL fornisce più attributi di questo tipo, che consentono di associare il comportamento a qualsiasi elemento di HTML, e tutti hanno il prefisso `data-sly`.
* **Lingua delle espressioni** - Le espressioni HTL sono delimitate dal `${` e `}` caratteri. In fase di esecuzione, queste espressioni vengono valutate e il loro valore viene inserito nel flusso HTML in uscita.

Consulta la sezione [Specifica HTL](specification.md) per informazioni dettagliate su entrambe le sintassi.

### L’elemento SLY {#the-sly-element}

Un concetto centrale di HTL consiste nell’offrire la possibilità di riutilizzare gli elementi HTML esistenti per definire le istruzioni di blocco, evitando la necessità di inserire delimitatori aggiuntivi per definire dove inizia e termina l’istruzione. Questa annotazione discreta del markup per trasformare un HTML statico in un modello dinamico funzionante offre il vantaggio di non interrompere la validità del codice HTML e quindi di visualizzarlo comunque correttamente, anche come file statico.

Tuttavia, a volte potrebbe non esserci un elemento esistente nella posizione esatta in cui deve essere inserita un’istruzione di blocco. Per tali casi, è possibile inserire un `sly` elemento che verrà rimosso automaticamente dall’output, durante l’esecuzione delle istruzioni di blocco allegate e la visualizzazione del relativo contenuto di conseguenza.

Il seguente esempio...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...genererà un output simile al seguente HTML, ma solo se sono definite entrambe le proprietà `jcr:title` e `jcr:description` e se nessuna delle due è vuota:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa da tenere a mente è solo usare il `sly` quando nessun elemento esistente poteva essere annotato con l’istruzione block . Questo perché `sly` gli elementi scoraggiano il valore offerto dal linguaggio per non alterare il HTML statico quando lo rende dinamico.

Ad esempio, se l’esempio precedente fosse stato racchiuso in un `div` , quindi l&#39;aggiunta `sly` elemento potrebbe essere abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

e `div` L’elemento potrebbe essere stato annotato con la condizione :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Commenti HTL {#htl-comments}

L’esempio seguente mostra un commento HTL sulla prima riga e un commento HTML sulla seconda riga.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

I commenti HTL sono commenti HTML con una sintassi di tipo JavaScript aggiuntiva. L’intero commento HTL e qualsiasi cosa al suo interno verrà completamente ignorato dal processore e rimosso dall’output.

Tuttavia, il contenuto dei commenti HTML standard verrà trasmesso e verranno valutate le espressioni all’interno del commento.

I commenti HTML non possono contenere commenti HTL e viceversa.

### Contesti speciali {#special-contexts}

Per poter utilizzare al meglio HTL, è importante comprendere bene le conseguenze del fatto che sia basato sulla sintassi HTML.

Fai riferimento alla [Sezione Contesto visualizzato](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) della specifica HTL per ulteriori dettagli.

### Nomi di elementi e di attributi {#element-and-attribute-names}

Le espressioni possono essere posizionate solo nel testo o nei valori di attributo di HTML, ma non nei nomi di elementi o di attributi, altrimenti non sarebbero più valide per HTML. Per impostare dinamicamente i nomi degli elementi, è possibile utilizzare l’istruzione `data-sly-element` sugli elementi desiderati; per impostare dinamicamente i nomi degli attributi, anche impostando più attributi contemporaneamente, è possibile utilizzare l’istruzione `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contesti senza istruzioni di blocco {#contexts-without-block-statements}

Poiché HTL utilizza gli attributi di dati per definire le istruzioni di blocco, non è possibile definirle all’interno dei seguenti contesti, in cui è possibile utilizzare solo le seguenti espressioni:

* commenti HTML
* elementi script
* elementi di stile

Il motivo è che il contenuto di questi contesti è testo e non HTML e gli elementi HTML contenuti sarebbero considerati come dati di carattere semplici. Pertanto, senza veri e propri elementi HTML, non possono essere eseguiti anche gli attributi `data-sly`.

Potrebbe sembrare una restrizione significativa, tuttavia è desiderata, in quanto HTML Template Language non dovrebbe essere abusato per generare un output che non è HTML. La sezione [Use-API per l’accesso alla logica](#use-api-for-accessing-logic) di seguito illustra come richiamare una logica aggiuntiva dal modello, che può essere utilizzata se necessaria per preparare output complessi per questi contesti. Ad esempio, un modo semplice per inviare dati dal back-end a uno script front-end consiste nell’impostare la logica del componente in modo da generare una stringa JSON, che può quindi essere posizionata in un attributo di dati con una semplice espressione HTL.

L’esempio seguente illustra il comportamento di commenti HTML, ma in elementi di script o di stile si osserva lo stesso comportamento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

genererà un output simile al seguente HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contesti espliciti richiesti {#explicit-contexts-required}

Come spiegato nella sezione [Escape automatico in base al contesto](#automatic-context-aware-escaping) di seguito, uno degli obiettivi di HTL è quello di ridurre i rischi di introduzione di vulnerabilità cross-site scripting (XSS) applicando automaticamente l’escape in base al contesto a tutte le espressioni. Anche se HTL può rilevare automaticamente il contesto delle espressioni posizionate all’interno del markup HTML, non analizza la sintassi di JavaScript o CSS in linea e quindi si basa sullo sviluppatore per specificare esplicitamente il contesto esatto da applicare a tali espressioni.

Poiché la mancata applicazione dei risultati di escape corretti genera vulnerabilità XSS, HTL rimuove l’output di tutte le espressioni che si trovano in contesti di script e di stile quando il contesto non è stato dichiarato.

Di seguito un esempio di come impostare il contesto per le espressioni posizionate all’interno di script e stili:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Per ulteriori dettagli su come controllare l’escape, consulta la sezione [Contesto di visualizzazione della lingua delle espressioni](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) sezione delle specifiche HTL.

## Funzionalità generali di HTL {#general-capabilities-of-htl}

Questa sezione descrive rapidamente le funzioni generali di HTML Template Language.

### Use-API per l’accesso alla logica {#use-api-for-accessing-logic}

Java Use-API per HTML Template Language (HTL) consente a un file HTL di accedere a metodi helper in una classe Java personalizzata tramite `data-sly-use`. Questo consente di incapsulare nel codice Java tutte le logiche di business complesse, mentre il codice HTL tratta solo la produzione di markup diretto.

Vedere il documento [API di utilizzo Java HTL](java-use-api.md) per ulteriori dettagli.

### Escape automatico in base al contesto {#automatic-context-aware-escaping}

Esaminiamo il seguente esempio:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Nella maggior parte dei linguaggi basati su modelli, questo esempio potrebbe potenzialmente creare una vulnerabilità cross-site scripting (XSS), perché, anche quando tutte le variabili hanno automaticamente un escape HTML, l’attributo `href` deve essere ancora specifico per l’escape URL. Questa omissione è uno degli errori più comuni, perché può essere facilmente dimenticata, ed è difficile individuare in modo automatizzato.

Per facilitare questa fase, HTML Template Language evita automaticamente ogni variabile in base al contesto in cui viene posizionata. Questo è possibile grazie al fatto che HTL comprende la sintassi di HTML.

Prendiamo in esame il seguente file `logic.js`:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

L’esempio iniziale si tradurrà quindi nel seguente output:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Notate come i due attributi sono stati escape in modo diverso, perché HTL sa che `href` e `src` Gli attributi devono essere preceduti dal carattere di escape per il contesto URI. Inoltre, se l’URI iniziasse con `javascript:`, l’attributo sarebbe stato rimosso completamente, a meno che il contesto non sia stato esplicitamente modificato diversamente.

Per ulteriori dettagli su come controllare l’escape, consulta la sezione [Contesto di visualizzazione della lingua delle espressioni](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) sezione delle specifiche HTL.

### Rimozione automatica di attributi vuoti {#automatic-removal-of-empty-attributes}

Esaminiamo il seguente esempio:

```xml
<p class="${properties.class}">some text</p>
```

Se il valore della proprietà `class` è vuoto, HTML Template Language rimuoverà automaticamente l’intero attributo `class` dall’output.

Anche in questo caso, questo è possibile, perché HTL comprende la sintassi HTML e può quindi mostrare gli attributi con i valori dinamici in modo condizionale solo se il loro valore non è vuoto. Questo è molto comodo in quanto evita di aggiungere un blocco di condizione intorno agli attributi, il che avrebbe reso il markup non valido e illeggibile.

Inoltre, è importante il tipo di variabile posizionata nell’espressione:

* **Stringa:**
   * **not empty:** imposta la stringa come valore di attributo.
   * **empty:** rimuove completamente l’attributo.

* **Numero:** imposta il valore come valore di attributo.

* **Booleano:**
   * **true:** visualizza l’attributo senza valore (come attributo HTML booleano)
   * **false:** rimuove completamente l’attributo.

Ecco un esempio di come un&#39;espressione booleana consentirebbe il controllo di un attributo booleano di HTML:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Per l’impostazione degli attributi, anche l’istruzione `data-sly-attribute` potrebbe risultare utile.

## Pattern comuni con HTL {#common-patterns-with-htl}

Questa sezione presenta alcuni scenari comuni e illustra come risolverli al meglio con HTML Template Language.

### Caricamento librerie client {#loading-client-libraries}

In HTL, le librerie client vengono caricate tramite un modello helper fornito da AEM, a cui è possibile accedere tramite `data-sly-use`. In questo file sono disponibili tre modelli, che possono essere richiamati tramite `data-sly-call`:

* **`css`**: carica solo i file CSS delle librerie client di riferimento.
* **`js`**: carica solo i file JavaScript delle librerie client di riferimento.
* **`all`**: carica tutti i file delle librerie client di riferimento (sia CSS che JavaScript).

Ogni modello helper richiede un’opzione `categories` per fare riferimento alle librerie client desiderate. Tale opzione può essere una matrice di valori stringa o una stringa contenente un elenco di valori separati da virgole.

Di seguito sono riportati due brevi esempi.

#### Caricamento completo di più librerie client contemporaneamente {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Riferimento a una libreria client in diverse sezioni di una pagina {#referencing-a-client-library-in-different-sections-of-a-page}

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

In questo esempio, nel caso in cui HTML `head` e `body` gli elementi vengono inseriti in file diversi, `clientlib.html` quindi il modello deve essere caricato in ogni file che lo richiede.

La sezione sul modello e le istruzioni di chiamata nella sezione [Specifica HTL](specification.md) fornisce ulteriori dettagli su come funzionano la dichiarazione e la chiamata di tali modelli.

### Trasmissione di dati al client {#passing-data-to-the-client}

Il modo migliore e più elegante per trasmettere i dati al cliente in generale, ma ancora di più con HTL, è quello di utilizzare `data` attributi.

L’esempio seguente mostra come la logica (che potrebbe anche essere scritta in Java) può essere utilizzata per serializzare comodamente a JSON l’oggetto da passare al client, che può quindi essere facilmente inserito in un `data` attributo:

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

Da lì, è facile immaginare come un JavaScript lato client possa accedere a tale attributo e analizzare nuovamente il JSON. Questo, ad esempio, sarebbe il JavaScript corrispondente da inserire in una libreria client:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilizzo dei modelli lato client {#working-with-client-side-templates}

Un caso speciale, in cui la tecnica spiegata nella sezione [Limitazioni di sollevamento di contesti speciali](#lifting-limitations-of-special-contexts) può essere legittimamente utilizzata, è quello di scrivere modelli lato client (come ad esempio Handlebars) che si trovano all’interno di elementi `scrip`. Il motivo per cui questa tecnica può essere utilizzata in questo caso è che l’elemento `script` non contiene quindi JavaScript, come ipotizzato, ma altri elementi HTML. Di seguito un esempio di come potrebbe funzionare:

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

Come mostrato in precedenza, il markup che verrà incluso nel `script` L&#39;elemento può contenere istruzioni di blocco HTL e le espressioni non devono fornire contesti espliciti, perché il contenuto del modello Handlebars è stato isolato nel proprio file. Inoltre, questo esempio mostra come l’HTL eseguito lato server (come nell’elemento `h2`) può essere combinato con un linguaggio basato su modello eseguito lato client, come Handlebars (mostrato sull’elemento `h3`).

Una tecnica più moderna, tuttavia, sarebbe quella di utilizzare l’elemento HTML `template`, in quanto il vantaggio sarebbe che non richiede di isolare il contenuto dei modelli in file separati.

### Limitazioni di sollevamento di contesti speciali {#lifting-limitations-of-special-contexts}

Nei casi speciali in cui è necessario bypassare le restrizioni dei contesti di script, stili e commenti, è possibile isolarne il contenuto in un file HTL separato. Tutto ciò che si trova nel proprio file verrà interpretato da HTL come un normale frammento HTML, dimenticando il contesto di limitazione da cui potrebbe essere stato incluso.

Per un esempio, consulta la sezione [Utilizzo dei modelli lato client](#working-with-client-side-templates) più avanti.

>[!CAUTION]
>
>Questa tecnica può introdurre vulnerabilità cross-site scripting (XSS) e gli aspetti di sicurezza dovrebbero essere attentamente studiati se necessario. Solitamente esistono modi migliori per implementare la stessa cosa che affidarsi a questa pratica.
