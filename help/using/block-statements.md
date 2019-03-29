---
title: Istruzioni blocco HTL
seo-title: Istruzioni blocco HTL
description: Le istruzioni block HTL (HTML Template Language) sono attributi di dati
  personalizzati aggiunti direttamente all'HTML esistente.
seo-description: Le istruzioni block HTL (HTML Template Language) sono attributi di
  dati personalizzati aggiunti direttamente all'HTML esistente.
uuid: 0624 fb 6 e -6989-431 b-aabc -1138325393 f 1
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 58 aa 6 ea 8-1 d 45-4 f 6 f-a 77 e -4819 f 593 a 19 d
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Istruzioni blocco HTL {#htl-block-statements}

Le istruzioni block HTL (HTML Template Language) sono attributi personalizzati `data` aggiunti direttamente all'HTML esistente. Questo consente un'annotazione semplice e non intrusiva di una pagina HTML statica prototipo, convertendola in un modello dinamico funzionante senza interrompere la validità del codice HTML.

## elemento sly {#sly-element}

& **amp; lt; sly & amp; gt; non** viene visualizzato nell'HTML risultante e può essere utilizzato al posto del wrapping data-sly. L'obiettivo della & amp; lt; sly & amp; gt; per rendere più ovvio che l'elemento non viene aggiornato. Se si desidera, è possibile continuare a utilizzare i dati.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Come accade con i dati, cercate di ridurre al minimo l'utilizzo di questa funzione.

## use {#use}

**`data-sly-use`**: Inizializza un oggetto helper (definito in javascript o Java) e lo espone attraverso una variabile.

Inizializzate un oggetto javascript in cui il file di origine si trova nella stessa directory del modello. È necessario utilizzare il nome file:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inizializzate una classe Java, in cui il file di origine si trova nella stessa directory del modello. È necessario utilizzare il nome di classe, non il nome del file:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inizializzate una classe Java, in cui questa classe viene installata come parte di un bundle osgi. È necessario utilizzare il nome completo della classe:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

I parametri possono essere passati all'inizializzazione utilizzando *le opzioni*. In genere questa funzione deve essere utilizzata solo dal codice HTL stesso all'interno di `data-sly-template` un blocco:

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
>* [Javascript Use-API](use-api-javascript.md)
>



## scorwrap {#unwrap}

**`data-sly-unwrap`**: Rimuove l'elemento host dal tag generato conservandone il contenuto. Questo consente l'esclusione degli elementi richiesti come parte della logica di presentazione HTL, ma non è auspicabile nell'output effettivo.

Tuttavia, questa istruzione deve essere utilizzata con cautela. In generale, è consigliabile mantenere il marcatore HTL il più vicino possibile alla marcatura di output desiderata. In altre parole, quando aggiungete istruzioni di blocco HTL, provate il più possibile a annotarvi l'HTML esistente, senza introdurre nuovi elementi.

Ad esempio,

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produrrà

```xml
<p>Hello World</p>
```

Questo,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

produrrà

```xml
Hello World
```

È inoltre possibile rimuovere in modo condizionato un elemento:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## Testo {#text}

**`data-sly-text`**: Sostituisce il contenuto del relativo elemento host con il testo specificato.

Ad esempio,

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Entrambi visualizzeranno il valore del **`jcr:description`** testo di paragrafo. Il secondo metodo è costituito dal secondo metodo che consente l'annotazione non intrusiva dell'HTML, mantenendo il contenuto segnaposto statico dal designer originale.

## attribute {#attribute}

**data-sly-attribute**: Aggiunge gli attributi all'elemento host.

Ad esempio,

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Entrambi imposteranno l' `title` attributo sul valore di **`jcr:title`**. Il secondo metodo è costituito dal secondo metodo che consente l'annotazione non intrusiva dell'HTML, mantenendo il contenuto segnaposto statico dal designer originale.

Gli attributi vengono risolti da sinistra a destra, con l'istanza più a destra di un attributo (letterale o definito tramite **`data-sly-attribute`**) che ha precedenza rispetto a qualsiasi istanza dello stesso attributo (definita letteralmente o tramite) **`data-sly-attribute`**definita a sinistra.

Si noti che un attributo (o **`literal`** impostato tramite) **`data-sly-attribute`**il cui valore *restituisce* la stringa vuota verrà rimosso nella marcatura finale. L'unica eccezione a questa regola è che viene *mantenuto un* attributo letterale impostato su una stringa vuota *vuota* . Esempio,

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

Per impostare più attributi, passate un oggetto mappa contenente coppie chiave-valore corrispondenti agli attributi e ai relativi valori. Ad esempio, supponendo,

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

Sostituisce il **`h1`** valore di **`titleLevel`**.

Per motivi di sicurezza `data-sly-element` , accetta solo i seguenti nomi di elementi:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Per impostare altri elementi, la protezione XSS deve essere disattivata ( `@context='unsafe'`).

## test {#test}

**`data-sly-test`:** Rimuove in modo condizionato l'elemento host e il contenuto. Un valore di `false` rimuove l'elemento; un valore che `true` mantiene l'elemento.

Ad esempio, l' `p` elemento e il relativo contenuto verranno sottoposti a rendering solo `isShown``true`se:

```xml
<p data-sly-test="${isShown}">text</p>
```

Il risultato di un test può essere assegnato a una variabile che può essere utilizzata in un secondo momento. In genere viene utilizzato per creare logica "se altro", poiché non è presente un'istruzione else esplicita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Una volta impostata, la variabile ha ambito globale all'interno del file HTL.

Seguono alcuni esempi relativi al confronto di valori:

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

Con data-sly, è possibile *ripetere* un elemento più volte in base all'elenco specificato.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Questo funziona allo stesso modo di data-sly, ma non è necessario un elemento contenitore.

L'esempio seguente mostra che è possibile fare riferimento all' *elemento* per gli attributi:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## elenco {#list}

**`data-sly-list`**: Ripete il contenuto dell'elemento host per ogni proprietà enumerabile nell'oggetto fornito.

Segue un ciclo semplice:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Le seguenti variabili predefinite sono disponibili nell'ambito dell'elenco:

**`item`**: L'elemento corrente nell'iterazione.

**`itemList`**: Oggetto contenente le proprietà seguenti:

**`index`**: contatore basato su zero ( `0..length-1`).

**`count`**: contatore basato su uno ( `1..length`).

`first`: `true` se l'elemento corrente è il primo elemento.

**`middle`**: `true` se l'elemento corrente non è né il primo né l'ultimo elemento.

**`last`**: `true` se l'elemento corrente è l'ultimo elemento.

**`odd`**: `true` if `index` is odd.

**`even`**: `true` if `index` is even.

La definizione di un identificatore nell' `data-sly-list` istruzione consente di rinominare le **`itemList`**`item` variabili e le variabili. **`item`** diventerà*** `<variable>`*** e **`itemList`** diventerà **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Potete inoltre accedere dinamicamente alle proprietà:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## riferimento {#resource}

**`data-sly-resource`**: Include il risultato del rendering della risorsa indicata tramite la risoluzione sling e il processo di rendering.

Una risorsa semplice include:

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

Aggiungere (o sostituire) un selettore:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Aggiungere, sostituire o rimuovere più selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Aggiungere un selettore a quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Rimuovere alcuni selettori da quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Rimuovere tutti i selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Sostituisce il tipo di risorsa della risorsa:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Modifica la modalità WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Per impostazione predefinita, i tag di decorazione AEM sono disattivati, l'opzione decorationtagname consente di riportarli e il nome cssclassname per aggiungere classi a tale elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM offre un controllo logico semplice e semplice sui tag decoration che racchiudono gli elementi inclusi. Per informazioni dettagliate consultate [Tag decoration (Tag decoration)](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/decoration-tag) nella documentazione sullo sviluppo dei componenti.

## include {#include}

**`data-sly-include`**: Sostituisce il contenuto dell'elemento host con quello generato dal file di modello HTML indicato (HTL, JSP, ESP ecc.) quando viene elaborata dal motore dei modelli corrispondente. Il contesto di rendering del file *incluso non* includerà il contesto HTL corrente (quello *del file incluso*); Di conseguenza, per l'inclusione di file HTL, la versione corrente **`data-sly-use`** deve essere ripetuta nel file incluso (in questo caso, è preferibile utilizzare **`data-sly-template`** e `data-sly-call`)

Una semplice inclusione:

```xml
<section data-sly-include="path/to/template.html"></section>
```

I JSP possono essere inclusi nello stesso modo:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Le opzioni consentono di manipolare il percorso del file:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

È inoltre possibile modificare la modalità WCM:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## modello e chiamata {#template-call}

`data-sly-template`: Definisce un modello. L'elemento host e il relativo contenuto non vengono generati da HTL

`data-sly-call`: Chiama un modello definito con un modello-sly. Il contenuto del modello chiamato (facoltativamente parametrizzato) sostituisce il contenuto dell'elemento host della chiamata.

Definire un modello statico e chiamarlo:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Definire un modello dinamico, quindi chiamarlo con parametri:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

È possibile inizializzare i modelli presenti in un altro file `data-sly-use`. In questo caso `data-sly-use` , è `data-sly-call` possibile inserire anche lo stesso elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

È supportata la ricorsività dei modelli:

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

## i 18 n e gli oggetti di lingua {#i-n-and-locale-objects}

Se si utilizzano i 18 n e HTL, è ora possibile trasmettere anche gli oggetti internazionali personalizzati.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Manipolazione URL {#url-manipulation}

È disponibile un nuovo set di manipolazioni URL.

Consultate i seguenti esempi sull'utilizzo:

Aggiunge l'estensione html a un percorso.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Aggiunge l'estensione html e un selettore a un percorso.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Aggiunge un'estensione html e un frammento (# value) a un percorso.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Funzioni HTL supportate in AEM 6.3 {#htl-features-supported-in-aem}

Le seguenti nuove funzioni HTL sono supportate in Adobe Experience Manager (AEM) 6.3:

### Formato numerico/data {#number-date-formatting}

AEM 6.3 supporta la formattazione nativa di numeri e date, senza scrivere codice personalizzato. Questo supporta anche il fuso orario e le impostazioni internazionali.

Gli esempi seguenti mostrano che il formato è specificato per primo, quindi il valore che richiede la formattazione:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Per informazioni complete sul formato che potete utilizzare, fate riferimento [alle specifiche HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### utilizzo di dati con risorse {#data-sly-use-with-resources}

Questo consente di ottenere risorse direttamente in HTL con un uso diverso e non richiede la scrittura del codice per ottenere la risorsa.

Esempio:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Attributi richiesti {#request-attributes}

Nella *risorsa data-sly* e *data-sly è ora* possibile trasmettere *requestattributes* per utilizzarli nel destinatario HTL ricevente.

Questo consente di passare correttamente i parametri in script o componenti.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Codice Java della classe Settings, la mappa viene utilizzata per trasmettere i requestattributes:

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

Ad esempio, tramite un modello Sling, puoi consumare il valore dei requestattributes specificati.

In questo esempio, *il layout* viene inserito tramite la Mappa dalla classe Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Correzione per @ extension {#fix-for-extension}

L'estensione @ funziona in tutti gli scenari di AEM 6.3, prima di avere un risultato come *www.adobe.com.ht ml* e controlla se aggiungere o meno l'estensione.

```xml
${ link @ extension = 'html' }
```
