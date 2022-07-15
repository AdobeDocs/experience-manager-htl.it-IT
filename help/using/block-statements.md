---
title: Cosa sono le istruzioni del blocco HTL?
description: Scopri cosa sono le istruzioni del blocco HTL o HTMLTemplate Language (HTL). Le istruzioni del blocco sono attributi di dati personalizzati aggiunti direttamente all’HTML esistente.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 79d299766da07dae001708b396b05c73cd70d4cc
workflow-type: ht
source-wordcount: '1563'
ht-degree: 100%

---

# Istruzioni di blocco HTL {#htl-block-statements}

Le istruzioni di blocco HTML Template Language (HTL) sono attributi `data` personalizzati aggiunti direttamente all’HTML esistente. Questo consente di inserire annotazioni semplici e discrete in una pagina HTML statica di un prototipo, convertendola in un modello dinamico funzionante senza interrompere la validità del codice HTML.

## Panoramica di blocco {#overview}

I plug-in per blocchi HTL sono definiti dagli attributi `data-sly-*` impostati sugli elementi HTML. Gli elementi possono avere un tag di chiusura o chiusura automatica. Gli attributi possono avere valori (che possono essere stringhe o espressioni statiche) o semplicemente attributi booleani (senza un valore).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Tutti gli attributi `data-sly-*` valutati vengono rimossi dal markup generato.

### Identificatori {#identifiers}

Un’istruzione di blocco può anche essere seguita da un identificatore:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

L’identificatore può essere utilizzato dall’istruzione di blocco in vari modi. Di seguito sono riportati alcuni esempi:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Gli identificatori di primo livello non prevedono la distinzione tra maiuscole e minuscole (perché possono essere impostati tramite attributi HTML, che prevedono tale distinzione), ma tutte le loro proprietà sì.

## Istruzioni di blocco disponibili {#available-block-statements}

Sono disponibili diverse istruzioni di blocco. Quando utilizzato sullo stesso elemento, il seguente elenco di priorità definisce il modo in cui vengono valutate le istruzioni di blocco:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Quando due istruzioni di blocco hanno la stessa priorità, il loro ordine di valutazione è da sinistra a destra.

### use {#use}

`data-sly-use` inizializza un oggetto helper (definito in JavaScript o Java) e lo espone tramite una variabile.

Inizializza un oggetto JavaScript, in cui il file di origine si trova nella stessa directory del modello. Deve essere utilizzato il nome del file:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inizializza una classe Java, in cui il file di origine si trova nella stessa directory del modello. È necessario utilizzare il nome della classe, non il nome del file:

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inizializza una classe Java, in cui tale classe è installata come parte di un bundle OSGi. Deve essere utilizzato il nome della classe completo:

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

I parametri possono essere trasmessi all’inizializzazione utilizzando le opzioni. In genere questa funzione deve essere utilizzata solo dal codice HTL stesso che si trova all’interno di un blocco `data-sly-template`:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inizializza un altro modello HTL che può quindi essere chiamato utilizzando `data-sly-call`:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Per ulteriori informazioni su Use-API, consulta:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### data-sly-use con risorse {#data-sly-use-with-resources}

Questo consente di ottenere risorse direttamente in HTL con `data-sly-use` e non richiede la scrittura di codice per ottenerlo.

Esempio:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Vedi anche la sezione [Percorso non sempre richiesto.](#path-not-required)

### unwrap {#unwrap}

`data-sly-unwrap` rimuove l’elemento host dal markup generato mantenendo il relativo contenuto. Questo consente di escludere gli elementi necessari come parte della logica di presentazione HTL ma non desiderati nell’output effettivo.

Tuttavia, questa informazione dovrebbe essere utilizzata con cautela. In generale, è meglio mantenere il markup HTL il più vicino possibile al markup di output previsto. In altre parole, aggiungendo le istruzioni di blocco HTL, è meglio provare il più possibile ad annotare semplicemente l’HTML esistente, senza introdurre nuovi elementi.

Ad esempio, questo

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produrrà

```xml
<p>Hello World</p>
```

Mentre questo

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

### set {#set}

`data-sly-set` definisce un nuovo identificatore con un valore predefinito.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` sostituisce il contenuto del relativo elemento host con il testo specificato.

Ad esempio, questo

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Entrambi visualizzeranno il valore di `jcr:description` come testo di paragrafo. Il vantaggio del secondo metodo è che consente l’annotazione discreta di HTML mantenendo il contenuto del segnaposto statico dalla progettazione originale.

### attribute {#attribute}

`data-sly-attribute` aggiunge attributi all’elemento host.

Ad esempio, questo

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Entrambi imposteranno l’attributo `title` sul valore di `jcr:title`. Il vantaggio del secondo metodo è che consente l’annotazione discreta di HTML mantenendo il contenuto del segnaposto statico dalla progettazione originale.

Gli attributi vengono risolti da sinistra a destra, con l’istanza più a destra di un attributo (letterale o definito tramite `data-sly-attribute`) che ha la precedenza su qualsiasi istanza dello stesso attributo (definita letteralmente o tramite `data-sly-attribute`) definita alla sua sinistra.

Un attributo (`literal` o impostato tramite `data-sly-attribute`) il cui valore restituisce la stringa vuota verrà rimosso nel markup finale. L’unica eccezione a questa regola è che verrà mantenuto un attributo letterale impostato su una stringa vuota letterale. Ad esempio,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce

```xml
<div></div>
```

ma

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce

```xml
<div class=""></div>
```

Per impostare più attributi, trasmettere un oggetto mappa contenente coppie chiave-valore corrispondenti agli attributi e ai relativi valori. Ad esempio, supponendo che

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

allora

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produce

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### element {#element}

`data-sly-element` sostituisce il nome dell’elemento host.

Ad esempio,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

sostituisce l’`h1` con il valore di `titleLevel`.

Per motivi di sicurezza, `data-sly-element` accetta solo i seguenti nomi di elemento:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Per impostare altri elementi, la protezione XSS deve essere disattivata ( `@context='unsafe'`).

### test {#test}

`data-sly-test` rimuove l’elemento host e il suo contenuto in modo condizionale. Un valore di `false` rimuove l’elemento; un valore di `true` mantiene l’elemento.

Ad esempio, l’elemento `p` e il relativo contenuto saranno rappresentati solo se `isShown` è `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

Il risultato di un test può essere assegnato a una variabile da utilizzare in seguito. Questa viene solitamente utilizzata per creare la logica “if else”, in quanto non esiste un’istruzione else esplicita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

La variabile, una volta impostata, ha ambito globale all’interno del file HTL.

Di seguito sono riportati alcuni esempi su valori di confronto:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div>

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

### repeat {#repeat}

Con `data-sly-repeat` è possibile ripetere più volte un elemento in base all’elenco specificato.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Funziona allo stesso modo di `data-sly-list`, con la differenza che non è necessario un elemento contenitore.

L’esempio seguente mostra che è anche possibile fare riferimento a *item* per gli attributi:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` ripete il contenuto dell’elemento host per ogni proprietà elencabile nell’oggetto fornito.

Di seguito una sequenza semplice:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Nell’ambito dell’elenco sono disponibili le seguenti variabili predefinite:

* `item`: l’elemento corrente nell’iterazione.
* `itemList`: oggetto contenente le seguenti proprietà:
* `index`: contatore basato su zero (`0..length-1`).
* `count`: contatore basato su uno (`1..length`).
* `first`: `true` se l’elemento corrente è il primo elemento.
* `middle`: `true` se l’elemento corrente non è né il primo né l’ultimo elemento.
* `last`: `true` se l’elemento corrente è l’ultimo elemento.
* `odd`: `true` se `index` è dispari.
* `even`: `true` se `index` è pari.

La definizione di un identificatore nell’istruzione `data-sly-list` consente di rinominare le variabili `itemList` e `item`. `item` diventerà `<variable>` e `itemList` diventerà `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

È anche possibile accedere alle proprietà in modo dinamico:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### resource {#resource}

`data-sly-resource` include il risultato del rendering della risorsa indicata tramite la risoluzione Sling e il processo di rendering.

Una semplice risorsa include:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Percorso non sempre richiesto {#path-not-required}

L’utilizzo di un percorso con `data-sly-resource` non è richiesto se si dispone già della risorsa. Se si dispone già della risorsa, è possibile utilizzarla direttamente.

Ad esempio, quanto segue è corretto.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Tuttavia, anche quanto segue è perfettamente accettabile.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

È consigliabile utilizzare la risorsa direttamente quando possibile per i motivi seguenti.

* Se la risorsa è già disponibile, la nuova risoluzione utilizzando il percorso è un lavoro aggiuntivo non necessario.
* L’utilizzo del percorso quando si dispone già della risorsa può produrre risultati imprevisti, in quanto le risorse Sling possono essere racchiuse o sintetiche e non fornite nel percorso specificato.

#### Opzioni {#resource-options}

Le opzioni consentono una serie di varianti aggiuntive:

Manipolazione del percorso della risorsa:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Aggiunta (o sostituzione) di un selettore:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Aggiunta, sostituzione o rimozione di più selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Aggiunta di un selettore a quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Rimozione di alcuni selettori da quelli esistenti:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Rimozione di tutti i selettori:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Override del tipo di risorsa della risorsa:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Cambio della modalità WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Per impostazione predefinita, i tag di decorazione AEM sono disabilitati, l’opzione decorationTagName consente di riportarli indietro e il cssClassName consente di aggiungere classi a tale elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM offre una logica chiara e semplice che controlla i tag di decorazione che racchiudono gli elementi. Per informazioni dettagliate, consulta [Tag di decorazione](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html?lang=it) nella documentazione dei componenti di sviluppo.

### include {#include}

`data-sly-include` sostituisce il contenuto dell’elemento host con il markup generato dal file di modello HTML indicato (HTL, JSP, ESP, ecc.) quando viene elaborato dal motore di modello corrispondente. Il contesto di rendering del file incluso non comprenderà il contesto HTL corrente (quello del file incluso); di conseguenza, per l’inclusione di file HTL, l’attuale `data-sly-use` dovrebbe essere ripetuto nel file incluso (in questo caso, di solito è meglio utilizzare `data-sly-template` e `data-sly-call`)

Un semplice esempio di inclusione:

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

### Request-attributes {#request-attributes}

In `data-sly-include` e `data-sly-resource` è possibile trasmettere `requestAttributes` per utilizzarli nello script HTL ricevente.

Ciò consente di trasferire correttamente i parametri in script o componenti.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Codice Java della classe Settings, la Mappa viene utilizzata per trasmettere requestAttributes:

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

I blocchi modello possono essere utilizzati come chiamate di funzione: nella loro dichiarazione possono ottenere parametri, che possono poi essere trasferiti tramite chiamata. Consentono anche la ricorsione.

`data-sly-template` definisce un modello. L’elemento host e il relativo contenuto non vengono generati da HTL

`data-sly-call` chiama un modello definito con data-sly-template. Il contenuto del modello chiamato (facoltativamente parametrizzato) sostituisce il contenuto dell’elemento host della chiamata.

Definisci un modello statico e chiamalo:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Definisci un modello dinamico e chiamalo con parametri:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

I modelli che si trovano in un file diverso possono essere inizializzati con `data-sly-use`. In questo caso, `data-sly-use` e `data-sly-call` possono essere posizionati anche sullo stesso elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

È supportata la ricorsione dei modelli:

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

## Elemento sly {#sly-element}

Il tag HTML `<sly>` può essere utilizzato per rimuovere l’elemento corrente, consentendo la visualizzazione solo dei relativi elementi secondari. La sua funzionalità è simile all’elemento blocco `data-sly-unwrap`:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Anche se non è un tag HTML 5 valido, il tag `<sly>` può essere visualizzato nell’output finale utilizzando `data-sly-unwrap`:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

L’obiettivo dell’elemento `<sly>` è quello di rendere più evidente che l’elemento non è un output. Volendo, si può comunque utilizzare `data-sly-unwrap`.

Come con `data-sly-unwrap`, si prova a ridurre al minimo il suo utilizzo.
