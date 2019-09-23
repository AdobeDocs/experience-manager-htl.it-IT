---
title: Istruzioni blocco HTL
seo-title: Istruzioni blocco HTL
description: Le istruzioni di blocco HTML Template Language (HTL) sono attributi di dati personalizzati aggiunti direttamente al codice HTML esistente.
seo-description: 'Le istruzioni di blocco HTML Template Language (HTL) sono attributi di dati personalizzati aggiunti direttamente al codice HTML esistente. '
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: Utente
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: afc29cbad83caeb549097da3fc33fd9147f1157a

---


# Istruzioni blocco HTL {#htl-block-statements}

Le istruzioni di blocco HTML Template Language (HTL) sono `data` attributi personalizzati aggiunti direttamente al codice HTML esistente. Questo consente di inserire facilmente un'annotazione discreta di un prototipo di pagina HTML statica, convertendola in un modello dinamico funzionante senza interrompere la validità del codice HTML.

## elemento Sly {#sly-element}

L'elemento **** &lt;sly&gt; non viene visualizzato nel codice HTML risultante e può essere utilizzato invece del metodo data-sly-unwrapping. L'obiettivo dell'elemento &lt;sly&gt; è di rendere più ovvio che l'elemento non è generato. Se desiderate, potete comunque utilizzare la funzione di estrazione rapida dei dati.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Come con lo sminuzzamento automatico dei dati, cercate di ridurre al minimo l'uso di questo.

## use {#use}

**`data-sly-use`**: Inizializza un oggetto helper (definito in JavaScript o Java) ed lo espone attraverso una variabile.

Inizializzare un oggetto JavaScript, dove il file di origine si trova nella stessa directory del modello. Il nome file deve essere utilizzato:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inizializzare una classe Java, in cui il file di origine si trova nella stessa directory del modello. È necessario utilizzare il nome di classe, non il nome del file:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inizializzare una classe Java, in cui tale classe viene installata come parte di un bundle OSGi. È necessario utilizzare il nome completo della classe:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

I parametri possono essere passati all'inizializzazione utilizzando *le opzioni*. In genere questa funzione deve essere utilizzata solo dal codice HTL che si trova all’interno di un `data-sly-template` blocco:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inizializzate un altro modello HTL che può essere chiamato utilizzando **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Per ulteriori informazioni sull'API Use-API, vedi:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## staccare {#unwrap}

**`data-sly-unwrap`**: Rimuove l'elemento host dalla marcatura generata mantenendo il contenuto. Questo consente di escludere gli elementi che sono richiesti come parte della logica di presentazione HTL ma che non sono desiderati nell'output effettivo.

Tuttavia, tale affermazione dovrebbe essere utilizzata con cautela. In generale è meglio mantenere la marcatura HTL il più vicino possibile alla marcatura di output prevista. In altre parole, quando si aggiungono istruzioni blocco HTL, provare il più possibile ad aggiungere semplicemente annotazioni all'HTML esistente, senza introdurre nuovi elementi.

Ad esempio, questo

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produrrà

```xml
<p>Hello World</p>
```

considerando che,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

produrrà

```xml
Hello World
```

È inoltre possibile estrarre un elemento in modo condizionale:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## Testo {#text}

**`data-sly-text`**: Sostituisce il contenuto del relativo elemento host con il testo specificato.

Ad esempio, questo

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Entrambi visualizzeranno il valore di **`jcr:description`** come testo paragrafo. Il vantaggio del secondo metodo è che consente l'annotazione non intrusiva di HTML mantenendo il contenuto segnaposto statico nella finestra di progettazione originale.

## attribute {#attribute}

**data-sly-attribute**: Aggiunge attributi all'elemento host.

Ad esempio, questo

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Entrambi impostano l' `title` attributo sul valore di **`jcr:title`**. Il vantaggio del secondo metodo è che consente l'annotazione non intrusiva di HTML mantenendo il contenuto segnaposto statico nella finestra di progettazione originale.

Gli attributi vengono risolti da sinistra a destra, con l'istanza più a destra di un attributo (letterale o definito tramite **`data-sly-attribute`**) che ha la precedenza su qualsiasi istanza dello stesso attributo (definito letteralmente o tramite **`data-sly-attribute`**) definita a sinistra.

Si noti che un attributo ( **`literal`** o impostato tramite **`data-sly-attribute`**) il cui valore *restituisce* la stringa vuota verrà rimosso nella marcatura finale. L'unica eccezione a questa regola è che verrà mantenuto un attributo *letterale* impostato su una stringa vuota *letterale* . Esempio,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce,

```xml
<div></div>
```

ma

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce,

```xml
<div class=""></div>
```

Per impostare più attributi, passare un oggetto mappa contenente coppie chiave-valore corrispondenti agli attributi e ai relativi valori. Ad esempio, supponendo

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Allora,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produce,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## elemento {#element}

**`data-sly-element`**: Sostituisce il nome dell'elemento dell'elemento host.

Esempio,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Sostituisce l’ **`h1`** con il valore di **`titleLevel`**.

Per motivi di sicurezza, `data-sly-element` accetta solo i seguenti nomi di elementi:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Per impostare altri elementi, la protezione XSS deve essere disattivata ( `@context='unsafe'`).

## test {#test}

**`data-sly-test`** : Rimuove in modo condizionale l'elemento host ed è contenuto. Un valore di `false` rimozione dell'elemento; un valore di `true` mantiene l'elemento.

Ad esempio, il rendering dell' `p` elemento e del relativo contenuto sarà eseguito solo se `isShown` è `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

Il risultato di un test può essere assegnato a una variabile che può essere utilizzata successivamente. Questa funzione è in genere utilizzata per costruire la logica "if else", in quanto non esiste un'istruzione else esplicita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Una volta impostata, la variabile ha ambito globale all’interno del file HTL.

Di seguito sono riportati alcuni esempi di confronto di valori:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## repeat {#repeat}

Con la ripetizione semplice dei dati è possibile *ripetere* più volte un elemento in base all'elenco specificato.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Funziona allo stesso modo dell'elenco di tipo data-side, ma non è necessario un elemento contenitore.

L'esempio seguente mostra che è anche possibile fare riferimento all' *elemento* per gli attributi:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## elenco {#list}

**`data-sly-list`**: Ripete il contenuto dell'elemento host per ogni proprietà enumerabile nell'oggetto fornito.

Di seguito è riportato un ciclo semplice:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Nell'ambito dell'elenco sono disponibili le seguenti variabili predefinite:

**`item`**: L'elemento corrente nell'iterazione.

**`itemList`**: Oggetto con le seguenti proprietà:

**`index`**: contatore basato su zero ( `0..length-1`).

**`count`**: un contatore ( `1..length`).

`first`: `true` se l'elemento corrente è il primo elemento.

**`middle`**: `true` se l'elemento corrente non è né il primo né l'ultimo elemento.

**`last`**: `true` se l'elemento corrente è l'ultimo elemento.

**`odd`**: `true` se `index` è strano.

**`even`**: `true` se `index` è pari.

La definizione di un identificatore nell' `data-sly-list` istruzione consente di rinominare le **`itemList`** variabili e `item` le variabili. **`item`** diventerà *** `<variable>`**** e **`itemList`** diventerà **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

È inoltre possibile accedere alle proprietà in modo dinamico:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## riferimento {#resource}

**`data-sly-resource`**: Include il risultato del rendering della risorsa indicata attraverso la risoluzione e il processo di rendering della sling.

Una semplice risorsa include:

```xml
<article data-sly-resource="path/to/resource"></article>
```

Le opzioni consentono una serie di varianti aggiuntive:

Manipolazione del percorso della risorsa:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Aggiungere o sostituire un selettore:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Aggiungete, sostituite o rimuovete più selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Aggiungere un selettore a quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Rimuovete alcuni selettori da quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Rimuovete tutti i selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Sostituisce il tipo di risorsa:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Cambia la modalità WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Per impostazione predefinita, i tag di decorazione AEM sono disattivati, l’opzione decorationTagName consente di riportarli indietro e il parametro cssClassName aggiunge le classi a tale elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM offre una logica chiara e semplice che controlla i tag di decorazione che racchiudono gli elementi inclusi. Per informazioni dettagliate, consultate [Decoration Tag](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) (Tagdecorazione) nella documentazione sui componenti in via di sviluppo.

## include {#include}

**`data-sly-include`**: Sostituisce il contenuto dell'elemento host con la marcatura generata dal file modello HTML indicato (HTL, JSP, ESP, ecc.). quando viene elaborato dal motore modello corrispondente. Il contesto di rendering del file ** incluso non includerà il contesto HTL corrente (quello del file ** incluso); Di conseguenza, per l'inclusione di file HTL, l'attuale **`data-sly-use`** dovrebbe essere ripetuto nel file incluso (In tal caso è generalmente meglio utilizzare **`data-sly-template`** e `data-sly-call`)

Un semplice esempio:

```xml
<section data-sly-include="path/to/template.html"></section>
```

I JSP possono essere inclusi allo stesso modo:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Le opzioni consentono di modificare il percorso del file:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

È inoltre possibile modificare la modalità WCM:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## template &amp; call {#template-call}

`data-sly-template`: Definisce un modello. L’elemento host e il relativo contenuto non vengono inviati da HTL

`data-sly-call`: Chiama un modello definito con un modello basato su dati. Il contenuto del modello denominato (eventualmente con parametri) sostituisce il contenuto dell’elemento host della chiamata.

Definire un modello statico e richiamarlo:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Definire un modello dinamico e richiamarlo con i parametri:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

I modelli che si trovano in un file diverso possono essere inizializzati con `data-sly-use`. Si noti che in questo caso `data-sly-use` e `data-sly-call` potrebbe anche essere posizionato sullo stesso elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

È supportata la ricorsione del modello:

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## Oggetti i18n e internazionali {#i-n-and-locale-objects}

Quando si utilizzano i18n e HTL, ora è possibile trasmettere anche oggetti delle impostazioni internazionali personalizzati.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Modifica URL {#url-manipulation}

È disponibile un nuovo set di manipolazioni URL.

Consultate i seguenti esempi di utilizzo:

Aggiunge l’estensione html a un percorso.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Aggiunge l’estensione html e un selettore a un percorso.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Aggiunge l’estensione html e un frammento (#value) a un percorso.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Funzioni HTL supportate in AEM 6.3 {#htl-features-supported-in-aem}

Le seguenti nuove funzioni HTL sono supportate in Adobe Experience Manager (AEM) 6.3:

### Formattazione numero/data {#number-date-formatting}

AEM 6.3 supporta la formattazione nativa di numeri e date, senza scrivere codice personalizzato. Supporta inoltre il fuso orario e le impostazioni internazionali.

Gli esempi seguenti mostrano che il formato è specificato per primo, quindi il valore da formattare:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Per informazioni complete sul formato che potete utilizzare, consultate la specifica [](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)HTL.

### utilizzo semplice dei dati con le risorse {#data-sly-use-with-resources}

Questo consente di ottenere risorse direttamente in HTL con un utilizzo semplice dei dati e non richiede la scrittura di codice per ottenere la risorsa.

Esempio:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Request-Attributes {#request-attributes}

Nella *sintassi con* dati e nella risorsa *con* dati è ora possibile trasmettere *requestAttributes* per utilizzarli nello script HTL ricevente.

Questo consente di trasmettere correttamente i parametri agli script o ai componenti.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Codice Java della classe Settings, la Mappa viene utilizzata per trasmettere in requestAttributes:

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

Ad esempio, tramite un modello Sling, è possibile utilizzare il valore di requestAttributes specificato.

In questo esempio, il *layout* viene inserito tramite la Mappa dalla classe Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Correzione per @extension {#fix-for-extension}

L'estensione @extension funziona in tutti gli scenari di AEM 6.3, prima di poter ottenere un risultato come *www.adobe.com.html* e controlla anche se aggiungere o meno l'estensione.

```xml
${ link @ extension = 'html' }
```
