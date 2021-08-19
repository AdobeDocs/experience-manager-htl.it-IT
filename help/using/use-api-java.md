---
title: Java Use-API per HTL
description: Java Use-API per HTML Template Language (HTL) consente a un file HTL di accedere a metodi helper in una classe Java personalizzata.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2558'
ht-degree: 100%

---

# Java Use-API per HTL {#htl-java-use-api}

Java Use-API per HTML Template Language (HTL) consente a un file HTL di accedere a metodi helper in una classe Java personalizzata tramite `data-sly-use`. Questo consente di incapsulare nel codice Java tutte le logiche di business complesse, mentre il codice HTL tratta solo la produzione di markup diretto.

Un oggetto Java Use-API può essere un semplice POJO, istanziato in base a una particolare implementazione tramite il costruttore predefinito di POJO.

I POJO Use-API possono inoltre esporre un metodo pubblico, denominato init, con la seguente firma:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

La mappa `bindings` può contenere oggetti che forniscono contesto allo script HTL attualmente eseguito che l’oggetto Use-API può utilizzare per la relativa elaborazione.

## Un esempio semplice {#a-simple-example}

Inizieremo con un componente HTL che non ha una classe use. È costituito da un singolo file, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Aggiungiamo anche del contenuto per questo componente per il rendering a `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando si accede a questo contenuto, viene eseguito il file HTL. All’interno del codice HTL utilizziamo l’oggetto contestuale `properties` per accedere a `title` e `description` della risorsa corrente e per visualizzarli. L’HTML di output sarà:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Aggiunta di una classe use {#adding-a-use-class}

Il componente **info** così com’è non ha bisogno di una classe use per eseguire la sua funzione (molto semplice). Tuttavia, in alcuni casi è necessario eseguire operazioni che sono impossibili in HTL e quindi è necessario utilizzare una classe use. Ma tieni presente quanto segue:

>[!NOTE]
>
>È consigliabile utilizzare una classe use solo quando non è possibile eseguire operazioni unicamente in HTL.

Ad esempio, supponiamo che si desideri che il componente `info` visualizzi le proprietà `title` e `description` della risorsa, ma il tutto in minuscolo. Poiché HTL non dispone di un metodo per rendere le stringhe in minuscolo, sarà necessaria una classe use. Per farlo, si aggiunge una classe use Java e si modifica `info.html` come segue:

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

Nelle sezioni seguenti esaminiamo le diverse parti del codice.

### Confronto tra classe Java locale e bundle {#local-vs-bundle-java-class}

La classe use Java può essere installata in due modi: **locale** o **bundle**. In questo esempio viene utilizzata un’installazione locale.

In un’installazione locale, il file Java di origine viene posizionato accanto al file HTL, nella stessa cartella dell’archivio. Il file di origine viene compilato automaticamente su richiesta. Non è richiesta alcuna fase separata di compilazione o packaging.

In un’installazione bundle, la classe Java deve essere compilata e implementata all’interno di un bundle OSGi utilizzando il meccanismo di implementazione di bundle standard AEM (vedi [Classe Java in bundle](#bundled-java-class)).

>[!NOTE]
>
>Si consiglia una **classe use Java locale** quando la classe use è specifica per il componente in questione.
>
>Una **classe use Java bundle** è consigliata quando il codice Java implementa un servizio a cui si accede da più componenti HTL.

### Il pacchetto Java è il percorso dell’archivio {#java-package-is-repository-path}

Quando si utilizza un’installazione locale, il nome del pacchetto della classe use deve corrispondere a quello della posizione della cartella dell’archivio, con eventuali trattini nel percorso sostituiti da trattini bassi nel nome del pacchetto.

In questo caso, `Info.java` si trova in `/apps/my-example/components/info` e quindi il pacchetto è `apps.my_example.components.info`:

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
>L’utilizzo dei trattini nei nomi di elementi dell’archivio è una pratica consigliata nello sviluppo AEM. Tuttavia, i trattini non sono consentiti nei nomi dei pacchetti Java. Per questo motivo, **tutti i trattini nel percorso dell’archivio devono essere convertiti in trattini bassi nel nome del pacchetto**.

### Estensione `WCMUsePojo` {#extending-wcmusepojo}

Mentre ci sono diversi modi per incorporare una classe Java con HTL (vedi Alternative a `WCMUsePojo`), il più semplice è quello di estendere la classe `WCMUsePojo`:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inizializzazione della classe {#initializing-the-class}

Quando la classe use viene estesa da `WCMUsePojo`, l’inizializzazione viene eseguita aggirando il metodo `activate`:

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

In genere, il metodo [activate](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) viene utilizzato per pre-calcolare e memorizzare (in variabili membro) i valori necessari nel codice HTL, in base al contesto corrente (ad esempio la richiesta e la risorsa correnti).

La classe `WCMUsePojo` consente di accedere allo stesso insieme di oggetti contestuali disponibili all’interno di un file HTL (vedi [Oggetti globali](global-objects.md)).

In una classe che estende `WCMUsePojo`, è possibile accedere agli oggetti contestuali per nome utilizzando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

In alternativa, è possibile accedere direttamente agli oggetti contestuali comunemente utilizzati con il **metodo di convenienza** appropriato:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Page](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Page](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Style](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Component](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Resource](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Metodi getter {#getter-methods}

Una volta inizializzata la classe use, viene eseguito il file HTL. In questa fase, solitamente HTL richiama lo stato di varie variabili membro della classe use e le restituisce per la presentazione.

Per fornire l’accesso a questi valori dall’interno del file HTL, è necessario definire metodi getter personalizzati nella classe use in base alla seguente convenzione di denominazione:

* Un metodo del modulo `getXyz` esporrà all’interno del file HTL una proprietà dell’oggetto denominata `xyz`.

Nell’esempio seguente, i metodi `getTitle` e `getDescription` rendono accessibili le proprietà dell’oggetto `title` e `description` all’interno del contesto del file HTL:

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

### attributo data-sly-use {#data-sly-use-attribute}

L’attributo `data-sly-use` viene utilizzato per inizializzare la classe use all’interno del codice HTL. Nel nostro esempio, l’attributo `data-sly-use` dichiara la volontà di utilizzare la classe `Info`. È possibile utilizzare solo il nome locale della classe perché si sta utilizzando un’installazione locale (avendo posizionato il file Java di origine nella stessa cartella del file HTL). Se utilizzassimo un’installazione bundle, dovremmo specificare il nome completo della classe.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificatore locale {#local-identifier}

L’identificatore `info` (dopo il punto in `data-sly-use.info`) viene utilizzato all’interno del file HTL per identificare la classe. L’ambito di questo identificatore è globale all’interno del file, dopo che è stato dichiarato. Non è limitato all’elemento che contiene l’istruzione `data-sly-use`.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ottenere le proprietà {#getting-properties}

L’identificatore `info` viene quindi utilizzato per accedere alle proprietà dell’oggetto `title` e `description` esposte tramite i metodi getter `Info.getTitle` e `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Output {#output}

Ora, quando accediamo a `/content/my-example.html`, restituirà il seguente HTML:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Oltre le nozioni di base {#beyond-the-basics}

In questa sezione presenteremo alcune ulteriori funzioni che vanno oltre il semplice esempio precedente:

* Trasferimento dei parametri a una classe use.
* Classe use Java in bundle.
* Alternative a `WCMUsePojo`

### Trasmissione dei parametri {#passing-parameters}

I parametri possono essere trasmessi a una classe use al momento dell’inizializzazione. Ad esempio, potremmo fare una cosa simile:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Stiamo trasmettendo un parametro chiamato `text`. La classe use quindi rende in maiuscolo la stringa recuperata e visualizza il risultato con `info.upperCaseText`. Di seguito è riportata la classe use corretta:

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

Il parametro è accessibile tramite il `WCMUsePojo` metodo [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Nel nostro caso, l’informazione:

`get("text", String.class)`

La stringa viene quindi invertita ed esposta tramite il metodo:

`getReverseText()`

### Trasmettere parametri solo da data-sly-template {#only-pass-parameters-from-data-sly-template}

Anche se l’esempio precedente è tecnicamente corretto, non ha molto senso trasmettere un valore da HTL per inizializzare una classe use, quando il valore in questione è disponibile nel contesto di esecuzione del codice HTL (o, in modo banale, se il valore è statico, come sopra).

Il motivo è che la classe use avrà sempre accesso allo stesso contesto di esecuzione del codice HTL. Viene quindi presentato un punto di partenza per le best practice relative alle importazioni:

>[!NOTE]
>
>La trasmissione di un parametro a una classe use deve essere eseguita solo quando la classe use viene utilizzata in un file `data-sly-template` che viene chiamato da un altro file HTL con parametri che devono essere trasmessi.

Ad esempio, creiamo un file `data-sly-template` separato accanto al nostro esempio esistente. Il nuovo file verrà chiamato `extra.html`. Contiene un blocco `data-sly-template` denominato `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

Il modello `extra` contiene un singolo parametro, `text`. Inizializza quindi la classe use Java `ExtraHelper` con il nome locale `extraHelper` e le trasmette il valore del parametro di modello `text` come parametro della classe use `text`.

Il corpo del modello riceve la proprietà `extraHelper.reversedText` (che, dal punto di vista tecnico, chiama effettivamente `ExtraHelper.getReversedText()`) e visualizza tale valore.

Inoltre, adattiamo il nostro `info.html` esistente per utilizzare questo nuovo modello:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Il file `info.html` contiene ora due istruzioni `data-sly-use`, quella originale che importa la classe use Java `Info` e una nuova che importa il file di modello con il nome locale `extra`.

Avremmo potuto posizionare il blocco del modello all’interno del file `info.html` per evitare il secondo `data-sly-use`, ma un file di modello separato è più comune e più riutilizzabile.

La classe `Info` viene utilizzata come prima, chiamando i relativi metodi getter `getLowerCaseTitle()` e `getLowerCaseDescription()` attraverso le relative proprietà HTL `info.lowerCaseTitle` e `info.lowerCaseDescription` corrispondenti.

Quindi eseguiamo un `data-sly-call` sul modello `extra` e trasmettiamo il valore `properties.description` come parametro `text`.

La classe use Java `Info.java` viene modificata per gestire il nuovo parametro di testo:

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

Il parametro `text` viene recuperato con `get("text", String.class)`, il valore viene invertito e reso disponibile come oggetto HTL `reversedText` attraverso il getter `getReversedText()`.

### Classe Java in bundle {#bundled-java-class}

Con una classe use in bundle, la classe deve essere compilata, inserita in un pacchetto e implementata in AEM utilizzando il meccanismo di implementazione standard di bundle OSGi. A differenza di un’installazione locale, la **dichiarazione di pacchetto** della classe use deve essere denominata normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

e l’istruzione `data-sly-use` deve fare riferimento al nome completo della classe, anziché al solo nome locale della classe:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternative a `WCMUsePojo` {#alternatives-to-wcmusepojo}

Il modo più comune per creare una classe use Java è quello di estendere `WCMUsePojo`. Tuttavia, esistono diverse altre opzioni. Per comprendere queste varianti, è utile comprendere come funziona l’istruzione HTL `data-sly-use` dal punto di vista tecnico.

Supponiamo di avere la seguente istruzione `data-sly-use`:

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

Il sistema elabora l’istruzione come segue:

(1)

* Se esiste un file locale `UseClass.java` nella stessa directory del file HTL, prova a compilare e caricare tale classe. In caso di esito positivo, vai a (2).
* In caso contrario, interpreta `UseClass` come nome di classe completo e prova a caricarlo dall’ambiente OSGi. In caso di esito positivo, vai a (2).
* In caso contrario, interpreta `UseClass` come percorso di un file HTL o JavaScript e carica quel file. In caso di esito positivo, vai a (4).

(2)

* Prova ad adattare l’attuale `Resource` a `UseClass`. In caso di esito positivo, vai a (3).
* In caso contrario, prova ad adattare l’attuale `Request` a `UseClass`. In caso di esito positivo, vai a (3).
* In caso contrario, prova a creare un’istanza `UseClass` con un costruttore di argomento zero. In caso di esito positivo, vai a (3).

(3)

* All’interno di HTL, associa l’oggetto appena adattato o creato al nome `localName`.
* Se `UseClass` implementa [`io.sightly.java.api.Use`](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html), chiama il metodo `init`, trasmettendo il contesto di esecuzione corrente (sotto forma di oggetto `javax.scripting.Bindings`).

(4)

* Se `UseClass` è un percorso per un file HTL contenente un `data-sly-template`, prepara il modello.
* In caso contrario, se `UseClass` è un percorso per una classe use JavaScript, prepara la classe use (vedi [JavaScript Use-API](use-api-javascript.md)).

Alcuni punti significativi sulla descrizione precedente:

* Qualsiasi classe adattabile da `Resource`, adattabile da `Request` o con un costruttore di argomento zero può essere una classe use. La classe non deve estendere `WCMUsePojo` o implementare `Use`.
* Tuttavia, se la classe use *implementa* `Use`, il relativo metodo `init` verrà automaticamente chiamato con il contesto corrente, consentendo di inserire il codice di inizializzazione che dipende da tale contesto.
* Una classe use che estende `WCMUsePojo` è solo un caso speciale di implementazione di `Use`. Fornisce i metodi di contesto di convenienza e il relativo metodo `activate` viene chiamato automaticamente da `Use.init`.

### Implementazione diretta dell’interfaccia use {#directly-implement-interface-use}

Anche se il modo più comune per creare una classe use è quello di estendere `WCMUsePojo`, è anche possibile implementare direttamente l’interfaccia [`io.sightly.java.api.Use`](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html).

L’interfaccia `Use` definisce un solo metodo:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/it/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

Il metodo `init` verrà chiamato al momento dell’inizializzazione della classe con un oggetto `Bindings` che contiene tutti gli oggetti contestuali ed eventuali parametri trasmessi nella classe use.

Tutte le funzionalità aggiuntive (come l’equivalente di `WCMUsePojo.getProperties()`) devono essere implementate esplicitamente utilizzando l’oggetto [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html). Esempio:

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

L’esempio principale per implementare autonomamente l’interfaccia `Use` invece di estendere `WCMUsePojo` è il caso in cui si desidera utilizzare una sottoclasse di una classe già esistente come classe use.

### Adattabile da risorsa {#adaptable-from-resource}

Un’altra opzione consiste nell’utilizzare una classe helper adattabile da `org.apache.sling.api.resource.Resource`.

Supponiamo che si debba scrivere uno script HTL che mostri il tipo MIME di una risorsa DAM. In questo caso, si sa che quando viene chiamato lo script HTL, questo si troverà nel contesto di una `Resource` che racchiude un `Node` JCR con tipo di nodo `dam:Asset`.

Si sa che un nodo `dam:Asset` ha una struttura come questa:

### Struttura di archivio {#repository-structure}

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

Qui mostriamo la risorsa (un’immagine JPEG) fornita con un’installazione predefinita di AEM come parte del progetto di esempio Geometrixx. La risorsa è denominata `jane_doe.jpg` e il suo tipo MIME è `image/jpeg`.

Per accedere alla risorsa da HTL, si può dichiarare [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) come classe nell’istruzione `data-sly-use` e quindi utilizzare un metodo GET di `Asset` per recuperare le informazioni desiderate. Esempio:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

L’istruzione `data-sly-use` indirizza HTL per adattare la `Resource` corrente a un `Asset` e le assegna il nome locale `asset`. Chiama quindi il metodo `getMimeType` di `Asset` utilizzando la scorciatoia di getter HTL: `asset.mimeType`.

### Adattabile da richiesta {#adaptable-from-request}

È inoltre possibile utilizzare come classe use qualsiasi classe adattabile da [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

Come nel caso precedente di una classe use adattabile da `Resource`, nell’istruzione `data-sly-use` è possibile specificare una classe use adattabile da [`SlingHttpServletRequest`](https://helpx.adobe.com/it/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html). Al momento dell’esecuzione, la richiesta corrente verrà adattata alla classe specificata e l’oggetto risultante sarà reso disponibile in HTL.
