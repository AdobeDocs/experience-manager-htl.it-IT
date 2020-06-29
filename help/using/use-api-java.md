---
title: API di utilizzo Java HTL
description: 'Java Use-API per HTML Template Language (HTL) abilita un file HTL per accedere a metodi helper in una classe Java personalizzata. '
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 1%

---


# API di utilizzo Java HTL {#htl-java-use-api}

The HTML Template Language (HTL) Java Use-API enables an HTL file to access helper methods in a custom Java class through `data-sly-use`. Questo consente di incorporare nel codice Java tutte le logiche aziendali complesse, mentre il codice HTL si occupa solo della produzione di markup diretti.

Un oggetto Java Use-API può essere un semplice POJO, istanziato da una particolare implementazione tramite il costruttore predefinito di POJO.

I POJO Use-API possono inoltre esporre un metodo pubblico, denominato init, con la seguente firma:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

La `bindings` mappa può contenere oggetti che forniscono contesto allo script HTL attualmente eseguito che l&#39;oggetto Use-API può utilizzare per la relativa elaborazione.

## Esempio semplice {#a-simple-example}

Inizieremo con un componente HTL che non dispone di una classe use. È costituito da un unico file, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Per eseguire il rendering di questo componente è inoltre necessario aggiungere del contenuto in `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando si accede a questo contenuto, viene eseguito il file HTL. All&#39;interno del codice HTL utilizziamo l&#39;oggetto contestuale `properties` per accedere alla risorsa corrente `title` `description` e visualizzarla. L&#39;HTML di output sarà:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Aggiunta di una classe d&#39;uso {#adding-a-use-class}

Il componente **info** così com&#39;è non ha bisogno di una classe use per eseguire la sua funzione (molto semplice). Ci sono tuttavia dei casi in cui è necessario fare cose che non possono essere fatte in HTL e quindi è necessario un use-class. Tenete presente quanto segue:

>[!NOTE]
>
>Utilizzare una classe use solo quando non è possibile eseguire operazioni in HTL.

Ad esempio, se desiderate che il `info` componente visualizzi le `title` e `description` le proprietà della risorsa, il tutto in lettere minuscole. Poiché HTL non dispone di un metodo per le stringhe di tipo minuscolo, sarà necessario utilizzare la classe. A tal fine, è possibile aggiungere una classe d&#39;uso Java e modificare le `info.html` seguenti impostazioni:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

Nelle sezioni che seguono passiamo attraverso le diverse parti del codice.

### Classe Java locale e Bundle {#local-vs-bundle-java-class}

Java use-class può essere installato in due modi: **locale** o **bundle**. In questo esempio viene utilizzata un&#39;installazione locale.

In un’installazione locale, il file di origine Java viene posizionato accanto al file HTL, nella stessa cartella del repository. L&#39;origine viene compilata automaticamente su richiesta. Non è richiesta alcuna fase di compilazione o di imballaggio separata.

In un’installazione bundle, la classe Java deve essere compilata e distribuita all’interno di un bundle OSGi utilizzando il meccanismo di distribuzione standard del bundle AEM (consultate [Bundled Java Class](#bundled-java-class)).

>[!NOTE]
>
>È consigliabile utilizzare una classe **Java** locale quando la classe use è specifica per il componente in questione.
>
>È consigliabile creare un **bundle Java use-class** quando il codice Java implementa un servizio a cui si accede da più componenti HTL.

### Il pacchetto Java è il percorso dell&#39;archivio {#java-package-is-repository-path}

Quando viene utilizzata un&#39;installazione locale, il nome del pacchetto della classe use deve corrispondere a quello del percorso della cartella del repository, con i trattini nel percorso sostituiti da caratteri di sottolineatura nel nome del pacchetto.

In questo caso `Info.java` si trova in `/apps/my-example/components/info` modo che il pacchetto sia `apps.my_example.components.info`:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>L’utilizzo di trattini nei nomi degli elementi del repository è una pratica consigliata nello sviluppo di AEM. Tuttavia, i trattini non sono consentiti nei nomi dei pacchetti Java. Per questo motivo, **tutti i trattini nel percorso dell&#39;archivio devono essere convertiti in caratteri di sottolineatura nel nome** del pacchetto.

### Estensione `WCMUsePojo` {#extending-wcmusepojo}

Mentre esistono diversi modi per incorporare una classe Java con HTL (vedere Alternative a `WCMUsePojo`), il più semplice è quello di estendere la `WCMUsePojo` classe:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inizializzazione della classe {#initializing-the-class}

Quando use-class viene esteso da `WCMUsePojo`, l&#39;inizializzazione viene eseguita ignorando il `activate` metodo:

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Contesto {#context}

In genere, il metodo [activate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) viene utilizzato per calcolare e memorizzare (nelle variabili membro) i valori necessari nel codice HTL, in base al contesto corrente (ad esempio la richiesta e la risorsa correnti).

La `WCMUsePojo` classe fornisce l&#39;accesso allo stesso set di oggetti contestuali disponibile all&#39;interno di un file HTL (vedere Oggetti [](global-objects.md)globali).

In una classe che si estende, `WCMUsePojo`è possibile accedere agli oggetti contestuali per nome utilizzando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

In alternativa, è possibile accedere direttamente agli oggetti contestuali di uso comune tramite il metodo **** comodità appropriato:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Progettazione](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Stile](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Risorsa](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Metodi Getter {#getter-methods}

Una volta inizializzata la classe use, il file HTL viene eseguito. In questa fase HTL esegue in genere il pulling dello stato di varie variabili membro della classe use ed eseguirà il rendering per la presentazione.

Per consentire l’accesso a questi valori dall’interno del file HTL, è necessario definire metodi getter personalizzati nella classe use in base alla seguente convenzione di denominazione:

* Un metodo del modulo `getXyz` espone nel file HTL una proprietà oggetto denominata `xyz`.

Nell&#39;esempio seguente, i metodi `getTitle` e `getDescription` i risultati ottenuti rendono le proprietà dell&#39;oggetto `title` e `description` diventano accessibili nel contesto del file HTL:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### attributo di utilizzo semplice dei dati {#data-sly-use-attribute}

L’ `data-sly-use` attributo viene utilizzato per inizializzare la classe use all’interno del codice HTL. Nel nostro esempio, l&#39; `data-sly-use` attributo dichiara che si desidera utilizzare la classe `Info`. Possiamo usare solo il nome locale della classe perché stiamo utilizzando un&#39;installazione locale (dopo aver inserito il file di origine Java si trova nella stessa cartella del file HTL). Se utilizzassimo un&#39;installazione bundle, dovremmo specificare il nome completo della classe.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificatore locale {#local-identifier}

L’identificatore `info` (dopo il punto in `data-sly-use.info`) viene utilizzato nel file HTL per identificare la classe. L&#39;ambito di questo identificatore è globale all&#39;interno del file, dopo che è stato dichiarato. Non è limitato all&#39;elemento che contiene l&#39; `data-sly-use` istruzione.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ottenimento delle proprietà {#getting-properties}

L&#39;identificatore `info` viene quindi utilizzato per accedere alle proprietà dell&#39;oggetto `title` e `description` che sono state esposte tramite i metodi getter `Info.getTitle` e `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Output {#output}

Ora, quando accediamo, `/content/my-example.html` restituirà il seguente HTML:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Oltre Le Nozioni Di Base {#beyond-the-basics}

In questa sezione presenteremo alcune ulteriori funzioni che vanno oltre il semplice esempio precedente:

* Trasmissione di parametri a una classe use.
* Classe di utilizzo Java in bundle.
* Alternative a `WCMUsePojo`

### Trasmissione parametri {#passing-parameters}

I parametri possono essere passati a una classe use al momento dell&#39;inizializzazione. Per esempio, potremmo fare qualcosa del genere:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Qui stiamo passando un parametro chiamato `text`. use-class quindi aggiorna la stringa recuperata e visualizza il risultato con `info.upperCaseText`. Di seguito è riportata la classe d&#39;uso corretta:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

Il parametro è accessibile tramite il `WCMUsePojo` metodo [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Nel nostro caso, la dichiarazione:

`get("text", String.class)`

La stringa viene quindi invertita ed esposta tramite il metodo:

`getReverseText()`

### Passa solo parametri da un modello basato su dati {#only-pass-parameters-from-data-sly-template}

Sebbene l&#39;esempio di cui sopra sia tecnicamente corretto, in realtà non ha molto senso trasmettere un valore da HTL per inizializzare una classe use, quando il valore in questione è disponibile nel contesto di esecuzione del codice HTL (o, in modo banale, il valore è statico, come sopra).

Il motivo è che la classe use avrà sempre accesso allo stesso contesto di esecuzione del codice HTL. Questo fornisce un punto di partenza per l&#39;importazione delle best practice:

>[!NOTE]
>
>Il passaggio di un parametro a una classe use deve essere eseguito solo quando la classe use viene utilizzata in un `data-sly-template` file che viene chiamato da un altro file HTL con parametri che devono essere trasmessi.

Ad esempio, creiamo un `data-sly-template` file separato con il nostro esempio esistente. Chiameremo il nuovo file `extra.html`. Contiene un `data-sly-template` blocco denominato `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

Il modello `extra`, prende un singolo parametro, `text`. Inizializza quindi Java use-class `ExtraHelper` con il nome locale `extraHelper` e gli passa il valore del parametro template `text` come parametro use-class `text`.

Il corpo del modello ottiene la proprietà `extraHelper.reversedText` (che, sotto il cofano, chiama effettivamente `ExtraHelper.getReversedText()`) e visualizza quel valore.

Inoltre adattiamo le nostre esistenti `info.html` per utilizzare questo nuovo modello:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Il file `info.html` ora contiene due `data-sly-use` istruzioni, quella originale che importa la classe d&#39;uso `Info` Java e una nuova che importa il file modello con il nome locale `extra`.

Si noti che avremmo potuto inserire il blocco modello all&#39;interno del `info.html` file per evitare il secondo `data-sly-use`, ma un file modello separato è più comune e riutilizzabile.

La `Info` classe viene utilizzata come precedente, chiamando i relativi metodi getter `getLowerCaseTitle()` e `getLowerCaseDescription()` attraverso le proprietà HTL corrispondenti `info.lowerCaseTitle` e `info.lowerCaseDescription`.

Quindi eseguiamo una `data-sly-call` operazione sul modello `extra` e trasmettiamo il valore `properties.description` come parametro `text`.

Java use-class `Info.java` viene modificato per gestire il nuovo parametro di testo:

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

Il `text` parametro viene recuperato con `get("text", String.class)`, il valore viene invertito e reso disponibile come oggetto HTL `reversedText` attraverso il getter `getReversedText()`.

### Classe Java Bundle {#bundled-java-class}

Con una classe d’uso bundle, la classe deve essere compilata, inclusa in pacchetti e distribuita in AEM tramite il meccanismo di distribuzione standard OSGi. A differenza di un&#39;installazione locale, la dichiarazione **del** pacchetto use-class deve essere denominata normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

e, l&#39; `data-sly-use` istruzione deve fare riferimento al nome di classe completo, anziché al solo nome della classe locale:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternative a `WCMUsePojo` {#alternatives-to-wcmusepojo}

Il modo più comune per creare una classe d&#39;uso Java è quello di estendere `WCMUsePojo`. Esistono tuttavia diverse altre opzioni. Per capire queste varianti aiuta a capire come funziona l&#39; `data-sly-use` istruzione HTL sotto la cappa.

Si supponga di disporre della seguente `data-sly-use` istruzione:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

Il sistema elabora l&#39;istruzione come segue:

(1)

* Se esiste un file locale `UseClass.java` nella stessa directory del file HTL, provate a compilarlo e caricarlo. In caso di esito positivo, passare a (2).
* In caso contrario, interpretate `UseClass` come nome di classe completo e provate a caricarlo dall’ambiente OSGi. In caso di esito positivo, passare a (2).
* In caso contrario, interpretate `UseClass` come percorso di un file HTL o JavaScript e caricate il file. Se il risultato è positivo (4).

(2)

* Prova ad adattare la corrente `Resource` a `UseClass`. In caso di esito positivo, passare a (3).
* In caso contrario, provare ad adattare la corrente `Request` a `UseClass`. In caso di esito positivo, passare a (3).
* In caso contrario, provare a creare un&#39;istanza `UseClass` con un costruttore a argomenti zero. In caso di esito positivo, passare a (3).

(3)

* All&#39;interno di HTL, vincolate l&#39;oggetto appena adattato o creato al nome `localName`.
* Se `UseClass` implementa [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) allora chiama il `init` metodo, passando il contesto di esecuzione corrente (nel modulo di un `javax.scripting.Bindings` oggetto).

(4)

* Se `UseClass` si tratta di un percorso a un file HTL contenente un file `data-sly-template`, preparate il modello.
* In caso contrario, se `UseClass` si tratta di un percorso a una classe use JavaScript, preparare la classe use (vedere [JavaScript Use-API](use-api-javascript.md)).

Alcuni punti significativi sulla descrizione di cui sopra:

* Qualsiasi classe adattabile da `Resource`, adattabile da `Request`o con un costruttore a argomento zero può essere una classe use. La classe non deve estendere `WCMUsePojo` o implementare `Use`.
* Tuttavia, se la classe use *non* viene implementata `Use`, il relativo `init` metodo verrà chiamato automaticamente con il contesto corrente, consentendo di inserire il codice di inizializzazione che dipende da tale contesto.
* Una classe d&#39;uso che si estende `WCMUsePojo` è solo un caso speciale di implementazione `Use`. Fornisce i metodi di contesto di convenienza e il suo `activate` metodo viene chiamato automaticamente da `Use.init`.

### Implementazione diretta dell&#39;interfaccia {#directly-implement-interface-use}

Anche se il modo più comune per creare una classe d&#39;uso è quello di estendere `WCMUsePojo`, è anche possibile implementare direttamente l&#39; [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) interfaccia stessa.

L&#39; `Use` interfaccia definisce un solo metodo:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

Il `init` metodo viene chiamato all&#39;inizializzazione della classe con un `Bindings` oggetto che contiene tutti gli oggetti contestuali e tutti i parametri passati alla classe use.

Tutte le funzionalità aggiuntive (come l&#39;equivalente di `WCMUsePojo.getProperties()`) devono essere implementate esplicitamente utilizzando l&#39; [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) oggetto. Ad esempio:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

Il caso principale per implementare l&#39; `Use` interfaccia in sé invece di estenderla `WCMUsePojo` è quando si desidera utilizzare una sottoclasse di una classe già esistente come classe use.

### Adattabile dalla risorsa {#adaptable-from-resource}

Un&#39;altra opzione consiste nell&#39;utilizzare una classe helper adattabile da `org.apache.sling.api.resource.Resource`.

Supponiamo che sia necessario scrivere uno script HTL che visualizzi il tipo mimetico di una risorsa DAM. In questo caso si sa che quando viene chiamato lo script HTL, lo script sarà all&#39;interno del contesto di un `Resource` che racchiude un JCR `Node` con un tipo di nodo `dam:Asset`.

È noto che un `dam:Asset` nodo ha una struttura come questa:

### Struttura archivio {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Qui viene mostrata la risorsa (un&#39;immagine JPEG) fornita con un&#39;installazione predefinita di AEM come parte del progetto di esempio geometrixx. La risorsa viene chiamata `jane_doe.jpg` e il suo mimetismo è `image/jpeg`.

Per accedere alla risorsa dall&#39;interno di HTL, potete dichiarare [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) come classe nell&#39; `data-sly-use` istruzione e quindi utilizzare un metodo get per `Asset` recuperare le informazioni desiderate. Ad esempio:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

L&#39; `data-sly-use` istruzione indirizza HTL per adattare la corrente `Resource` a un `Asset` e gli dà il nome locale `asset`. Quindi, chiama il `getMimeType` metodo di `Asset` utilizzo della abbreviazione getter HTL: `asset.mimeType`.

### Adattabile da richiesta {#adaptable-from-request}

È inoltre possibile utilizzare come classe use qualsiasi classe adattabile da [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

Come nel caso precedente di un adattatore use-class da `Resource`, nell&#39;istruzione [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) `data-sly-use` è possibile specificare un adattatore use-class da. Al momento dell&#39;esecuzione, la richiesta corrente verrà adattata alla classe specificata e l&#39;oggetto risultante sarà reso disponibile all&#39;interno di HTL.
