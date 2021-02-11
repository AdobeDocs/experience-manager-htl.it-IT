---
title: Istruzioni di blocco HTL
description: Le istruzioni di blocco HTML Template Language (HTL) sono attributi di dati personalizzati aggiunti direttamente al codice HTML esistente.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '1555'
ht-degree: 1%

---


# Istruzioni di blocco HTL {#htl-block-statements}

Le istruzioni di blocco HTML Template Language (HTL) sono attributi `data` personalizzati aggiunti direttamente all&#39;HTML esistente. Questo consente di inserire facilmente un&#39;annotazione discreta di un prototipo di pagina HTML statica, convertendola in un modello dinamico funzionante senza interrompere la validità del codice HTML.

## Panoramica del blocco {#overview}

I plug-in per blocchi HTL sono definiti dagli attributi `data-sly-*` impostati sugli elementi HTML. Gli elementi possono avere un tag di chiusura o chiudersi autonomamente. Gli attributi possono avere valori (che possono essere stringhe statiche o espressioni), oppure essere semplicemente attributi booleani (senza un valore).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Tutti gli attributi `data-sly-*` valutati vengono rimossi dalla marcatura generata.

### Identificatori {#identifiers}

Un&#39;istruzione block può anche essere seguita da un identificatore:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

L&#39;identificatore può essere utilizzato dall&#39;istruzione block in vari modi. Di seguito sono riportati alcuni esempi:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Gli identificatori di primo livello non fanno distinzione tra maiuscole e minuscole (perché possono essere impostati tramite attributi HTML che non fanno distinzione tra maiuscole e minuscole), ma tutte le relative proprietà sono sensibili alla distinzione tra maiuscole e minuscole.

## Istruzioni blocco disponibili {#available-block-statements}

Sono disponibili diverse istruzioni di blocco. Quando viene utilizzato sullo stesso elemento, il seguente elenco di priorità definisce il modo in cui vengono valutate le istruzioni di blocco:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`,  `data-sly-include`,  `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Quando due istruzioni di blocco hanno la stessa priorità, l&#39;ordine di valutazione è da sinistra a destra.

### use {#use}

`data-sly-use` inizializza un oggetto helper (definito in JavaScript o Java) ed lo espone attraverso una variabile.

Inizializzare un oggetto JavaScript, dove il file di origine si trova nella stessa directory del modello. Il nome file deve essere utilizzato:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inizializzare una classe Java, in cui il file di origine si trova nella stessa directory del modello. È necessario utilizzare il nome della classe, non il nome del file:

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inizializzare una classe Java, in cui tale classe viene installata come parte di un bundle OSGi. È necessario utilizzare il nome completo della classe:

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

I parametri possono essere passati all&#39;inizializzazione utilizzando le opzioni. In genere questa funzione deve essere utilizzata solo dal codice HTL che si trova all&#39;interno di un blocco `data-sly-template`:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inizializzare un altro modello HTL che può essere chiamato utilizzando `data-sly-call`:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Per ulteriori informazioni sull&#39;API Use-API, vedi:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### utilizzo semplice dei dati con risorse {#data-sly-use-with-resources}

Questo consente di ottenere risorse direttamente in HTL con `data-sly-use` e non richiede la scrittura del codice per ottenerlo.

Esempio:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Vedere anche la sezione [Percorso non sempre obbligatorio.](#path-not-required)

### estrarre {#unwrap}

`data-sly-unwrap` rimuove l&#39;elemento host dalla marcatura generata mantenendo il contenuto. Questo consente di escludere gli elementi che sono richiesti come parte della logica di presentazione HTL ma che non sono desiderati nell&#39;output effettivo.

Tuttavia, tale affermazione dovrebbe essere utilizzata con cautela. In generale è meglio mantenere la marcatura HTL il più vicino possibile alla marcatura di output prevista. In altre parole, quando si aggiungono istruzioni blocco HTL, provare il più possibile ad aggiungere semplicemente annotazioni all&#39;HTML esistente, senza introdurre nuovi elementi.

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

### imposta {#set}

`data-sly-set` definisce un nuovo identificatore con un valore predefinito.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### Testo {#text}

`data-sly-text` sostituisce il contenuto del relativo elemento host con il testo specificato.

Ad esempio, questo

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Entrambi visualizzeranno il valore di `jcr:description` come testo di paragrafo. Il vantaggio del secondo metodo è che consente l&#39;annotazione non intrusiva di HTML mantenendo il contenuto segnaposto statico nella finestra di progettazione originale.

### attribute {#attribute}

`data-sly-attribute` aggiunge attributi all&#39;elemento host.

Ad esempio, questo

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Entrambi imposteranno l&#39;attributo `title` sul valore di `jcr:title`. Il vantaggio del secondo metodo è che consente l&#39;annotazione non intrusiva di HTML mantenendo il contenuto segnaposto statico nella finestra di progettazione originale.

Gli attributi vengono risolti da sinistra a destra, con l&#39;istanza più a destra di un attributo (letterale o definito tramite `data-sly-attribute`) che ha la precedenza su qualsiasi istanza dello stesso attributo (definito letteralmente o tramite `data-sly-attribute`) definita a sinistra.

Tenere presente che un attributo (`literal` o impostato tramite `data-sly-attribute`) il cui valore restituisce una stringa vuota verrà rimosso nella marcatura finale. L&#39;unica eccezione a questa regola è che verrà mantenuto un attributo letterale impostato su una stringa vuota letterale. Esempio,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce,

```xml
<div></div>
```

ma,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce,

```xml
<div class=""></div>
```

Per impostare più attributi, passare un oggetto mappa contenente coppie chiave-valore corrispondenti agli attributi e ai relativi valori. Ad esempio, supponendo che

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

### elemento {#element}

`data-sly-element` sostituisce il nome dell&#39;elemento dell&#39;elemento host.

Esempio,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Sostituisce l&#39;elemento `h1` con il valore di `titleLevel`.

Per motivi di sicurezza, `data-sly-element` accetta solo i seguenti nomi di elementi:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Per impostare altri elementi, la protezione XSS deve essere disattivata ( `@context='unsafe'`).

### test {#test}

`data-sly-test` rimuove in modo condizionale l&#39;elemento host ed è contenuto. Un valore di `false` rimuove l&#39;elemento; un valore di `true` mantiene l&#39;elemento.

Ad esempio, l&#39;elemento `p` e il relativo contenuto saranno sottoposti a rendering solo se `isShown` è `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

Il risultato di un test può essere assegnato a una variabile che può essere utilizzata successivamente. Questa funzione è in genere utilizzata per costruire la logica &quot;if else&quot;, in quanto non esiste un&#39;istruzione else esplicita:

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

### ripetizione {#repeat}

Con `data-sly-repeat` è possibile ripetere un elemento più volte in base all&#39;elenco specificato.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Funziona allo stesso modo di `data-sly-list`, ma non è necessario un elemento contenitore.

L&#39;esempio seguente mostra che è anche possibile fare riferimento a *item* per gli attributi:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### elenco {#list}

`data-sly-list` ripete il contenuto dell&#39;elemento host per ogni proprietà enumerabile nell&#39;oggetto fornito.

Di seguito è riportato un ciclo semplice:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Nell&#39;ambito dell&#39;elenco sono disponibili le seguenti variabili predefinite:

* `item`: L&#39;elemento corrente nell&#39;iterazione.
* `itemList`: Oggetto con le seguenti proprietà:
* `index`: contatore basato su zero (  `0..length-1`).
* `count`: un contatore (  `1..length`).
* `first`:  `true` se l&#39;elemento corrente è il primo elemento.
* `middle`:  `true` se l&#39;elemento corrente non è né il primo né l&#39;ultimo elemento.
* `last`:  `true` se l&#39;elemento corrente è l&#39;ultimo elemento.
* `odd`:  `true` se  `index` è strano.
* `even`:  `true` se  `index` è pari.

La definizione di un identificatore nell&#39;istruzione `data-sly-list` consente di rinominare le variabili `itemList` e `item`. `item` diventerà  `<variable>` e  `itemList` diventerà  `<variable>List`.

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

### riferimento {#resource}

`data-sly-resource` include il risultato del rendering della risorsa indicata tramite la risoluzione e il processo di rendering sling.

Una semplice risorsa include:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Percorso non sempre richiesto {#path-not-required}

Tenere presente che l&#39;utilizzo di un percorso con `data-sly-resource` non è richiesto se si dispone già di una risorsa. Se disponete già di una risorsa, potete utilizzarla direttamente.

Ad esempio, quanto segue è corretto.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Tuttavia, anche quanto segue è perfettamente accettabile.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

È consigliabile utilizzare la risorsa direttamente quando possibile, per i motivi seguenti.

* Se disponete già della risorsa, la risoluzione del problema utilizzando il percorso è un lavoro aggiuntivo e non necessario.
* Se si utilizza il percorso in cui è già presente la risorsa, è possibile che vengano generati risultati imprevisti in quanto le risorse Sling possono essere racchiuse o sintetiche e non essere fornite nel percorso specificato.

#### Opzioni {#resource-options}

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

Per impostazione predefinita, i tag di decorazione AEM sono disattivati, l&#39;opzione decorationTagName consente di riportarli e l&#39;opzione cssClassName consente di aggiungere classi a tale elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM offre una logica chiara e semplice che controlla i tag di decorazione che racchiudono gli elementi inclusi. Per informazioni dettagliate, vedere [Decoration Tag](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/decoration-tag.html) nella documentazione dei componenti in via di sviluppo.

### includi {#include}

`data-sly-include` sostituisce il contenuto dell&#39;elemento host con la marcatura generata dal file modello HTML indicato (HTL, JSP, ESP, ecc.) quando viene elaborato dal motore modello corrispondente. Il contesto di rendering del file incluso non includerà il contesto HTL corrente (quello del file incluso); Di conseguenza, per l&#39;inclusione di file HTL, l&#39;attuale `data-sly-use` dovrebbe essere ripetuta nel file incluso (In tal caso è generalmente meglio utilizzare `data-sly-template` e `data-sly-call`)

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

### Request-Attributes {#request-attributes}

In `data-sly-include` e `data-sly-resource` è possibile passare `requestAttributes` per utilizzarli nello script HTL ricevente.

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

Ad esempio, tramite un modello Sling, è possibile utilizzare il valore di `requestAttributes` specificato.

In questo esempio, il layout viene inserito tramite la Mappa dalla classe Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### template &amp; call {#template-call}

I blocchi modello possono essere utilizzati come chiamate di funzione: nella loro dichiarazione possono ottenere parametri, che possono poi essere passati quando li chiamano. Consentono inoltre la ricorsione.

`data-sly-template` definisce un modello. L’elemento host e il relativo contenuto non vengono inviati da HTL

`data-sly-call` chiama un modello definito con un modello basato su dati. Il contenuto del modello denominato (eventualmente con parametri) sostituisce il contenuto dell’elemento host della chiamata.

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

I modelli che si trovano in un file diverso possono essere inizializzati con `data-sly-use`. Tenere presente che in questo caso `data-sly-use` e `data-sly-call` possono essere inseriti anche sullo stesso elemento:

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

## sly Element {#sly-element}

Il tag HTML `<sly>` può essere utilizzato per rimuovere l&#39;elemento corrente, consentendo la visualizzazione solo degli elementi secondari. La sua funzionalità è simile all&#39;elemento blocco `data-sly-unwrap`:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Sebbene non sia un tag HTML 5 valido, il tag `<sly>` può essere visualizzato nell&#39;output finale utilizzando `data-sly-unwrap`:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

L&#39;obiettivo dell&#39;elemento `<sly>` è di rendere più ovvio che l&#39;elemento non è in uscita. Se si desidera è comunque possibile utilizzare `data-sly-unwrap`.

Come con `data-sly-unwrap`, cercate di ridurre al minimo l&#39;uso di questo.
