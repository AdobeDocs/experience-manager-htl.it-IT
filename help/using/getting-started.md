---
title: Guida introduttiva ad HTL
description: Scopri HTL, il sistema di modelli lato server preferito e consigliato per HTML in AEM, e i principali concetti del linguaggio e dei suoi costrutti fondamentali.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '2045'
ht-degree: 100%

---


# Guida introduttiva ad HTL {#getting-started-with-htl}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML in Adobe Experience Manager. Come in tutti i sistemi di modelli HTML lato server, un file HTL definisce l’output inviato al browser specificando l’HTML stesso, alcune logiche di presentazione di base e variabili da valutare in fase di esecuzione.

Questo documento fornisce una panoramica dello scopo di HTL e un’introduzione ai concetti e costrutti fondamentali del linguaggio.

>[!TIP]
>
>Questo documento presenta lo scopo di HTL e una panoramica della sua struttura e dei suoi concetti fondamentali. Se hai domande sulla sintassi specifica, consulta la [specifica HTL](specification.md).

## Livelli HTL {#layers}

In AEM, un certo numero di livelli definiscono HTL.

1. **[Specifica HTL](specification.md)**: HTL è una specifica open-source, indipendente dalla piattaforma, che chiunque può implementare.
1. **[`Sling`Motore di script HTL](specification.md)**: il progetto `Sling` ha creato l’implementazione di riferimento di HTL, utilizzata da AEM.
1. **[Estensioni AEM](specification.md)**: AEM si basa sul motore di script HTL `Sling` per offrire agli sviluppatori funzioni adeguate specifiche per AEM.

Questa documentazione si concentra sull’utilizzo di HTL per sviluppare soluzioni AEM. Come tale, tocca tutti e tre i livelli, collegando le risorse esterne in base alle esigenze.

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

* **Istruzioni di blocco**: se desideri visualizzare l’elemento `<h1>` in modo condizionale, utilizza un attributo `data-sly-test` di dati HTML5. HTL fornisce più attributi di questo tipo, che consentono di associare il comportamento a qualsiasi elemento HTML e hanno tutti il prefisso `data-sly`.
* **Linguaggio di espressione**: i caratteri `${` e `}` delimitano le espressioni HTL. In fase di esecuzione, queste espressioni vengono valutate e il loro valore viene inserito nel flusso HTML in uscita.

Per informazioni dettagliate su entrambe le sintassi, consulta la sezione [Specifica HTL](specification.md).

### L’elemento SLY {#the-sly-element}

Un concetto centrale di HTL consiste nell’offrire la possibilità di riutilizzare gli elementi HTML esistenti per definire le istruzioni di blocco. Questo riutilizzo evita la necessità di inserire delimitatori aggiuntivi per definire l’inizio e la fine dell’istruzione. L’annotazione del markup trasforma in modo discreto l’HTML statico in un modello dinamico senza interrompere la validità dell’HTML, garantendo una visualizzazione corretta anche come file statici.

Tuttavia, a volte potrebbe non esserci un elemento esistente nella posizione esatta in cui deve essere inserita un’istruzione di blocco. In questi casi, è possibile inserire un elemento `sly` speciale. Questo elemento viene rimosso automaticamente dall’output durante l’esecuzione delle istruzioni di blocco associate e la conseguente visualizzazione del relativo contenuto.

Il seguente esempio:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

Genera un output simile all’HTML seguente, ma solo se sono definite entrambe le proprietà `jcr:title` e `jcr:description` e se nessuna delle due è vuota:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

tuttavia, è importante ricorda di usare l’elemento `sly` solo se nessun elemento esistente può essere annotato con l’istruzione di blocco. Questo accade perché gli elementi `sly` impediscono che il valore offerto dal linguaggio possa alterare l’HTML statico quando lo si rende dinamico.

Ad esempio, se l’esempio precedente fosse stato racchiuso all’interno di un elemento `div`, l’elemento `sly` aggiunto risulterebbe abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

E l’elemento `div` avrebbe potuto essere annotato con la condizione:

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

I commenti HTL sono commenti HTML con una sintassi di tipo JavaScript aggiuntiva. Il processore ignora completamente l’intero commento HTL e qualsiasi cosa al suo interno, rimuovendolo dall’output.

Tuttavia, il contenuto dei commenti HTML standard viene trasmesso e sono valutate le espressioni all’interno del commento.

I commenti HTML non possono contenere commenti HTL e viceversa.

### Contesti speciali {#special-contexts}

Per poter utilizzare al meglio HTL, è importante comprendere bene le conseguenze del fatto che sia basato sulla sintassi HTML.

Per ulteriori dettagli, consulta la [sezione Display Context](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) della specifica HTL.

### Nomi di elementi e di attributi {#element-and-attribute-names}

Le espressioni possono essere inserite solo nei valori di testo o di attributo HTML, ma non all’interno di nomi di elementi o di attributi, altrimenti non sarebbe più un HTML valido. Per impostare dinamicamente i nomi degli elementi, è possibile utilizzare l’istruzione `data-sly-element` sugli elementi desiderati e per impostare dinamicamente i nomi degli attributi, anche su più attributi contemporaneamente, è possibile utilizzare l’istruzione `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contesti senza istruzioni di blocco {#contexts-without-block-statements}

Poiché HTL utilizza gli attributi di dati per definire le istruzioni di blocco, non è possibile definire tali istruzioni all’interno dei seguenti contesti e solo le espressioni possono essere utilizzate in questi casi:

* commenti HTML
* elementi script
* elementi di stile

Il motivo è che il contenuto di questi contesti è testo e non HTML e gli elementi HTML contenuti sarebbero considerati come dati di carattere semplici. Pertanto, senza veri e propri elementi HTML, non possono essere eseguiti neanche gli attributi `data-sly`.

Questo approccio può sembrare una restrizione significativa. Tuttavia, è preferibile perché HTML Template Language deve generare solo un output HTML valido. La sezione [Use-API per l’accesso alla logica](#use-api-for-accessing-logic) di seguito illustra come richiamare una logica aggiuntiva dal modello, che può essere utilizzata se necessaria per preparare output complessi per questi contesti. Per inviare dati dal back-end a uno script front-end, genera una stringa JSON con la logica del componente e inseriscila in un attributo di dati utilizzando una semplice espressione HTL.

L’esempio seguente illustra il comportamento di commenti HTML, ma in elementi di script o di stile si osserva lo stesso comportamento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

Genera un output simile all’HTML seguente:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contesti espliciti richiesti {#explicit-contexts-required}

Come spiegato nella sezione [Escape automatico in base al contesto](#automatic-context-aware-escaping) di seguito, uno degli obiettivi di HTL è quello di ridurre i rischi di introduzione di vulnerabilità cross-site scripting (XSS) applicando automaticamente l’escape in base al contesto a tutte le espressioni. HTL rileva il contesto delle espressioni nel markup HTML, ma non analizza JavaScript o CSS in linea, pertanto gli sviluppatori devono specificare il contesto esatto per tali espressioni.

Poiché la mancata applicazione dei risultati di escape corretti genera vulnerabilità XSS, HTL rimuove l’output di tutte le espressioni che si trovano in contesti di script e di stile quando il contesto non è stato dichiarato.

Di seguito un esempio di come impostare il contesto per le espressioni posizionate all’interno di script e stili:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Per ulteriori dettagli su come controllare l’escape, consulta la sezione [Expression Language Display Context](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) delle specifiche HTL.

## Funzionalità generali di HTL {#general-capabilities-of-htl}

Questa sezione descrive rapidamente le funzioni generali di HTML Template Language.

### Use-API per l’accesso alla logica {#use-api-for-accessing-logic}

Java Use-API per HTML Template Language (HTL) consente a un file HTL di accedere a metodi helper in una classe Java personalizzata tramite `data-sly-use`. Questo processo consente di incapsulare nel codice Java tutte le logiche di business complesse, mentre il codice HTL tratta solo la produzione di markup diretto.

Per ulteriori dettagli, consulta il documento [Java Use-API per HTL](java-use-api.md).

### Escape automatico in base al contesto {#automatic-context-aware-escaping}

Prendi in considerazione l’esempio seguente:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Nella maggior parte dei linguaggi basati su modelli, questo esempio potrebbe potenzialmente creare una vulnerabilità cross-site scripting (XSS), perché, anche quando tutte le variabili hanno automaticamente un escape HTML, l’attributo `href` deve essere ancora specifico per l’escape URL. Questa omissione è uno degli errori più comuni, perché può essere facilmente dimenticata ed è difficile da individuare in modo automatico.

Per facilitare questa fase, HTML Template Language evita automaticamente ogni variabile in base al contesto in cui viene posizionata. Questo approccio è possibile grazie al fatto che HTL comprende la sintassi dell’HTML.

Presupponendo il seguente file `logic.js`:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

L’esempio iniziale restituisce il seguente output:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Nota come i due attributi hanno escape diversi, perché HTL riconosce che gli attributi `href` e `src` devono avere un escape per il contesto URI. Inoltre, se l’URI iniziasse con `javascript:`, l’attributo sarebbe stato rimosso completamente, a meno che il contesto non fosse stato esplicitamente cambiato.

Per ulteriori dettagli su come controllare l’escape, consulta la sezione [Expression Language Display Contex](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) nellle specifiche HTL.

### Rimozione automatica di attributi vuoti {#automatic-removal-of-empty-attributes}

Prendi in considerazione l’esempio seguente:

```xml
<p class="${properties.class}">some text</p>
```

Se il valore della proprietà `class` è vuoto, HTML Template Language rimuoverà automaticamente l’intero attributo `class` dall’output.

Di nuovo, questo processo è possibile perché HTL comprende la sintassi HTML e può quindi mostrare gli attributi con valori dinamici in modo condizionale, solo se il loro valore non è vuoto. La ragione è particolarmente utile, in quanto evita la necessità di aggiungere un blocco di condizione intorno agli attributi, che avrebbe reso il markup non valido e illeggibile.

Inoltre, è importante il tipo di variabile posizionata nell’espressione:

* **Stringa:**
   * **non vuota:** imposta la stringa come valore di attributo.
   * **vuota:** rimuove completamente l’attributo.

* **Numero:** imposta il valore come valore di attributo.

* **Booleano:**
   * **true:** visualizza l’attributo senza valore (come attributo HTML booleano)
   * **false:** rimuove completamente l’attributo.

Di seguito un esempio di come un’espressione booleana può consentire di controllare un attributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Per l’impostazione degli attributi, anche l’istruzione `data-sly-attribute` potrebbe risultare utile.

## Pattern comuni con HTL {#common-patterns-with-htl}

Questa sezione presenta alcuni scenari comuni. Spiega come risolvere al meglio questi scenari con HTML Template Language.

### Caricamento librerie client {#loading-client-libraries}

In HTL, le librerie client vengono caricate tramite un modello helper fornito da AEM, a cui è possibile accedere tramite `data-sly-use`. In questo file sono disponibili tre modelli, che possono essere richiamati tramite `data-sly-call`:

* **`css`**: carica solo i file CSS delle librerie client di riferimento.
* **`js`**: carica solo i file JavaScript delle librerie client di riferimento.
* **`all`**: carica tutti i file delle librerie client di riferimento (sia CSS che JavaScript).

Ogni modello helper richiede un’opzione `categories` per fare riferimento alle librerie client desiderate. Tale opzione può essere un array di valori stringa o una stringa contenente un elenco di valori separati da virgola.

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

In questo esempio, se gli elementi HTML `head` e `body` sono posizionati in file diversi, il modello `clientlib.html` deve essere caricato in ogni file che lo richiede.

La sezione sulle istruzioni di modello e chiamata nella [Specifica HTL](specification.md) fornisce ulteriori dettagli sul funzionamento della dichiarazione e della chiamata di tali modelli.

### Trasmissione di dati al client {#passing-data-to-the-client}

Il modo migliore e più elegante per trasmettere i dati al client in generale, ma ancora di più con HTL, è quello di utilizzare gli attributi `data`.

L’esempio seguente illustra come serializzare un oggetto in JSON (possibile anche in Java) per passare al client. Può quindi essere facilmente posizionato in un attributo `data`:

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

Da lì, è facile immaginare come un JavaScript lato client possa accedere a tale attributo e analizzare nuovamente il JSON. Questo approccio sarebbe il JavaScript corrispondente da posizionare in una libreria client, ad esempio:

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

Il markup dell’elemento `script` può includere istruzioni di blocco HTL senza contesti espliciti, in quanto il contenuto del modello Handlebars è isolato nel proprio file. Inoltre, questo esempio mostra come l’HTL eseguito lato server (come nell’elemento `h2`) può essere combinato con un Template Language eseguito lato client, come Handlebars (mostrato sull’elemento `h3`).

Una tecnica più moderna, tuttavia, sarebbe quella di utilizzare l’elemento HTML `template`, in quanto il vantaggio sarebbe che non richiede di isolare il contenuto dei modelli in file separati.

### Limitazioni di sollevamento di contesti speciali {#lifting-limitations-of-special-contexts}

Nei casi speciali in cui è necessario aggirare le restrizioni dei contesti di script, di stile e di commento, è possibile isolarne il contenuto in un file HTL separato. HTL interpreta tutto ciò che si trova nel proprio file come un frammento HTML standard, ignorando qualsiasi contesto limitativo da dove era incluso.

Per un esempio, consulta la sezione [Utilizzo dei modelli lato client](#working-with-client-side-templates) più avanti.

>[!CAUTION]
>
>Questa tecnica può introdurre vulnerabilità cross-site scripting (XSS). Gli aspetti relativi alla sicurezza dovrebbero essere attentamente studiati se si utilizza tale approccio. In genere esistono modi migliori per implementare la stessa cosa rispetto ad affidarsi a questa pratica.
