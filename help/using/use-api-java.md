---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: Java Use-API per HTML Template Language - HTL - abilita un file HTL per
  accedere a metodi helper in una classe Java personalizzata.
seo-description: Java Use-API per HTML Template Language - HTL - abilita un file HTL
  per accedere a metodi helper in una classe Java personalizzata.
uuid: b 340 f 8 f 7-a 193-45 c 8-aa 39-5 c 6 e 2 c 0194 ea
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 126 ebc 9 d -5 f 7 b -47 a 4-aea 2-c 8840 d 34864 c
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL Java Use-API{#htl-java-use-api}

L'API Java HTML (HTML Template Language) consente a un file HTL di accedere ai metodi helper in una classe Java personalizzata. In questo modo, tutte le complessità di business complesse possono essere racchiuse nel codice Java, mentre il codice HTL occupa solo la produzione di marcatura diretta.

## Esempio semplice {#a-simple-example}

Partiremo da un componente HTL che *non* dispone di una classe use. Consiste in un singolo file, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Per effettuare il rendering su **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando si accede a questo contenuto, viene eseguito il file HTL. All'interno del codice HTL usiamo l'oggetto context****`properties`per accedere alla risorsa corrente `title` e `description` visualizzarla. L'HTML di output sarà:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Aggiunta di una classe Use {#adding-a-use-class}

Il **componente Info** non necessita di una classe use per eseguire la funzione (molto semplice). In alcuni casi, tuttavia, è necessario eseguire operazioni che non possono essere eseguite in HTL, per cui è necessario utilizzare una classe. Tenete presente quanto segue:

>[!NOTE]
>
>*Una classe use deve essere utilizzata solo quando un elemento non può essere eseguito solo in HTL.*

Ad esempio, supponiamo che il `info` componente sia in grado di visualizzare le `title` proprietà e **`description`** le proprietà della risorsa, ma tutte in minuscole. Poiché HTL non dispone di un metodo per le stringhe sottili, è necessario utilizzare una classe use. Per farlo, potete aggiungere una classe Use Java e modificarla **`info.html`** come segue:

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

Nelle sezioni seguenti abbiamo esaminato le diverse parti del codice.

### Classe locale e Bundle Java {#local-vs-bundle-java-class}

La classe Use Java può essere installata in due modi: **locale** o **bundle**. *In questo esempio viene utilizzata un'installazione locale.*

In un'installazione locale, il file sorgente Java viene posizionato accanto al file HTL, nella stessa cartella dell'archivio. L'origine viene compilata automaticamente su richiesta. Non è richiesto un passaggio separato per la compilazione o la creazione di pacchetti.

In un pacchetto di bundle, la classe Java deve essere compilata e distribuita all'interno di un bundle osgi utilizzando il meccanismo standard di distribuzione del bundle AEM (consultate [la classe](#bundled-java-class)Bundle Bundle).

>[!NOTE]
>
>Una **classe use** Java locale è consigliata quando la classe use è specifica per il componente in questione.
>
>Un **bundle Java use-class** è consigliato quando il codice Java implementa un servizio a cui si accede da più componenti HTL.

### Il pacchetto Java è un percorso archivio {#java-package-is-repository-path}

Quando viene utilizzata un'installazione locale, il nome del pacchetto della classe use deve corrispondere a quello della posizione della cartella archivio, con qualsiasi trattino nel percorso sostituito dai caratteri di sottolineatura nel nome del pacchetto.

In questo caso **`Info.java`** si trova nel **`/apps/my-example/components/info`** pacchetto **`apps.my_example.components.info`** :

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
>L'utilizzo dei trattini nei nomi degli elementi archivio è una pratica consigliata nello sviluppo AEM. Tuttavia, i trattini non sono validi all'interno dei nomi dei pacchetti Java. Per questo motivo **, tutti i trattini nel percorso archivio devono essere convertiti in caratteri di sottolineatura nel nome del pacchetto**.

### Estensione `WCMUsePojo`{#extending-wcmusepojo}

Sebbene vi siano molti modi per incorporare una classe Java con HTL (consultate Alternative a `WCMUsePojo`), il più semplice consiste nell'estendere la `WCMUsePojo` classe:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Inizializzazione della classe {#initializing-the-class}

Quando si estende la classe use, **`WCMUsePojo`**l'inizializiazione viene eseguita ignorando il **`activate`** metodo:

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

In genere, il [metodo activate](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) viene utilizzato per precalcolare e memorizzare (nelle variabili di membro) i valori necessari nel codice HTL, in base al contesto corrente (ad esempio, la richiesta corrente e la risorsa).

La `WCMUsePojo` classe permette di accedere allo stesso set di oggetti contestuali disponibili all'interno di un file HTL (vedere [Oggetti globali](global-objects.md)).

In a class that extends **`WCMUsePojo`**, context objects can be accessed *by name* using

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

In alternativa, è possibile accedere direttamente agli oggetti contestuali utilizzati comunemente mediante il metodo **di convenienza appropriato**:

|  |
|---|---|
| [Pagemanager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [Getpagemanager ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getcurrentpage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getresourcepage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getpageproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [Getdesigner ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Progettazione](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [Getcurrentdesign ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Stile](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [Getcurrentstyle ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [Getcomponent ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getinheritedproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Risorsa](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [Getresource ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [Resourceresolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [Getresourceresolver ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [Slinghttpservletrequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [Getrequest ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [Slinghttpservletresponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [Getresponse ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [Slingscripthelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [Getslingscripthelper ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Metodi getter {#getter-methods}

Una volta inizializzata la classe use, il file HTL viene eseguito. Durante questo passaggio HTL, in genere si estrae nello stato di varie variabili membro della classe use e le eseguirà il rendering per la presentazione.

Per fornire l'accesso a questi valori dall'interno del file HTL, è necessario definire metodi getter personalizzati nella classe use **in base alla convenzione di denominazione seguente**:

* Un metodo del modulo **`getXyz`** espone all'interno del file HTL una proprietà oggetto denominata ***xyz***.

Ad esempio, nell'esempio seguente, i metodi **`getTitle`** e **`getDescription`** determinano le proprietà dell'oggetto **`title`** e **`description`** diventano accessibili nel contesto del file HTL:

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

### attributo data-sly {#data-sly-use-attribute}

L' **`data-sly-use`** attributo viene utilizzato per inizializzare la classe use all'interno del codice HTL. Nell'esempio, l' `data-sly-use` attributo dichiara che desideriamo utilizzare la classe **`Info`**. Possiamo utilizzare solo il nome locale della classe perché stiamo utilizzando un'installazione locale (l'inserimento del file di origine Java si trova nella stessa cartella del file HTL). Se utilizziamo un'installazione bundle, sarebbe necessario specificare il nome di classe completo (consultate [Installazione del bundle di utilizzo)](#LocalvsBundleJavaClass).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificatore locale {#local-identifier}

L'identificatore "**`info`**(dopo il punto in **`data-sly-use.info`**) viene usato nel file HTL per identificare la classe. L'ambito di questo identificatore è globale all'interno del file, dopo che è stato dichiarato. Non è limitato all'elemento che contiene l' `data-sly-use` istruzione.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ottenimento delle proprietà {#getting-properties}

L'identificatore `info` viene quindi utilizzato per accedere alle proprietà **`title`** dell'oggetto e **`description`** che sono state esposte tramite i metodi `Info.getTitle` getter e **`Info.getDescription`**.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Output {#output}

Ora, quando accederemo **`/content/my-example.html`** , verrà restituito il seguente codice HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Oltre le nozioni di base {#beyond-the-basics}

In questa sezione inizieremo altre funzioni che vanno oltre l'esempio semplice:

* Passare parametri a una classe use.
* Inc Java use-class.
* Alternative a `WCMUsePojo`

### Superamento dei parametri {#passing-parameters}

I parametri possono essere passati a una classe use dopo l'inizializzazione. Ad esempio, possiamo fare qualcosa di simile al seguente:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Qui trasferiamo un parametro chiamato **`text`**. La classe use quindi sostituisce la stringa con cui recuperiamo e visualizza il risultato con `info.upperCaseText`. Esempio di utilizzo della classe:

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

L'accesso al parametro viene eseguito tramite il `WCMUsePojo` metodo

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Nel nostro caso, l'istruzione

`get("text", String.class)`

La stringa viene quindi invertita e esposta tramite il metodo

**`getReverseText()`**

### Trasmettere solo i parametri da un modello-sly {#only-pass-parameters-from-data-sly-template}

Anche se l'esempio di cui sopra è tecnicamente corretto, non è effettivamente sensato passare un valore da HTL per inizializzare una classe use, quando il valore in questione è disponibile nel contesto di esecuzione del codice HTL (o, in modo trivioco, come valore statico, come sopra).

Ciò si verifica perché la classe use avrà sempre accesso allo stesso contesto di esecuzione del codice HTL. In questo modo viene visualizzato un punto di importazione delle best practice:

>[!NOTE]
>
>Il passaggio di un parametro a una classe use deve essere eseguito solo quando l'utilizzo della **classe use viene utilizzato in un** file di tipo dati-sly, chiamato da un altro file HTL con parametri che devono essere passati.

Ad esempio, creiamo un file separato `data-sly-template` con il nostro esempio esistente. Il nuovo file `extra.html`verrà chiamato. Contiene un **`data-sly-template`** blocco chiamato **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

Il modello **`extra`**accetta un singolo parametro **`text`**. Quindi inizializza la classe use `ExtraHelper` con il nome locale **`extraHelper`** e la trasmette al valore del parametro template **`text`** come parametro **`text`**use-class.

Il corpo del modello ottiene la proprietà `extraHelper.reversedText` (che, sotto il cofano, effettivamente chiama `ExtraHelper.getReversedText()`) e lo visualizza.

Abbiamo anche adattato il nostro esistente **`info.html`** per usare questo nuovo modello:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Il file `info.html` contiene ora due **`data-sly-use`** istruzioni, quella originale che importa **`Info`** la classe use Java e una nuova che importa il file modello sotto il nome `extra`locale.

Potrebbe essere stato inserito il blocco di modelli all'interno del **`info.html`** file per evitare la seconda **`data-sly-use`**, ma un file modello separato è più comune e più riutilizzabile.

La **`Info`** classe viene utilizzata come prima, chiamando i metodi **`getLowerCaseTitle()`** getter e `getLowerCaseDescription()` le relative proprietà `info.lowerCaseTitle` HTL e **`info.lowerCaseDescription`**.

Quindi viene eseguito un **`data-sly-call`** modello **`extra`** e viene trasmesso il valore `properties.description` come parametro **`text`**.

La classe use Java `Info.java` viene modificata in modo da gestire il nuovo parametro di testo:

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

Il **`text`** parametro viene recuperato con **`get("text", String.class)`**, il valore viene invertito e reso disponibile come oggetto HTL `reversedText` attraverso il getter `getReversedText()`.

### Classe Java Bundle {#bundled-java-class}

Con un bundle use the class deve essere compilata, compressa e distribuita in AEM utilizzando il meccanismo standard di distribuzione del bundle osgi. Contrariamente a un'installazione locale, la dichiarazione **del pacchetto di classe use** deve essere denominata in genere:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

e, l' `data-sly-use` istruzione deve fare riferimento al *nome di classe completo*, anziché solo al nome della classe locale:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternative a `WCMUsePojo`{#alternatives-to-wcmusepojo}

Il modo più comune di creare una classe Use-class è estendere `WCMUsePojo`. Tuttavia, esistono diverse altre opzioni. Per comprendere queste varianti è utile comprendere in che modo `data-sly-use` l'istruzione HTL funziona sotto il cofano.

Si supponga di disporre della `data-sly-use` seguente istruzione:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

Il sistema elabora l'istruzione come segue:

(1)

* Se esiste un file locale *useclass. java* nella stessa directory del file HTL, provate a compilare e caricare tale classe. In caso di esito positivo (2).

* Altrimenti, interpretate *useclass* come nome di classe completo e tentate di caricarlo dall'ambiente osgi. In caso di esito positivo (2).
* Altrimenti, interpretate *useclass* come percorso di un file HTL o javascript e caricate il file. If successfully goto (4).

(2)

* Provate a adattare la corrente **`Resource`** in *`UseClass.`* if success, passate a (3).

* Otherwise, try to adapt the current **`Request`** to *`UseClass`*. In caso di esito positivo, passate a (3).

* In caso contrario, tentate di creare un'istanza *`UseClass`* con una costruttore di argomenti zero. In caso di esito positivo, passate a (3).

(3)

* In HTL, associare al nome il nuovo oggetto adattato o creato *`localName`*.
* Se *`UseClass`* implementa [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) , quindi chiama il `init` metodo, passando il contesto di esecuzione corrente (sotto forma `javax.scripting.Bindings` di oggetto).

(4)

* Se *`UseClass`* è un percorso a un file HTL contenente un `data-sly-template`elemento, preparate il modello.

* In caso contrario, se *`UseClass`* è un percorso a un'aula javascript, preparate la classe use (consultate [javascript Use-API](use-api-javascript.md)).

Alcuni punti significativi sulla descrizione precedente:

* Qualsiasi classe adattabile `Resource`, adattabile o `Request`con costruttore di argomenti zero può essere una classe use. La classe non deve estendere estensioni `WCMUsePojo` o implementazione `Use`.

* Tuttavia, se la classe use ** implementa `Use`, il relativo **`init`** metodo verrà chiamato automaticamente con il contesto corrente, consentendo di inserire codice di inizializzazione che dipenda da tale contesto.

* Una classe utilizzata che si estende `WCMUsePojo` è solo una caso speciale di implementazione **`Use`**. It provides the convenience context methods and its **`activate`** method is automatically called from `Use.init`.

### Implementazione diretta dell'interfaccia {#directly-implement-interface-use}

Mentre si estende `WCMUsePojo`il metodo più comune per creare una classe use, è anche possibile implementare direttamente l' `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interfaccia stessa.

`Use` L'interfaccia definisce un solo metodo:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

Il **`init`** metodo verrà chiamato per l'inizializzazione della classe con un **`Bindings`** oggetto che contiene tutti gli oggetti contestuali e tutti i parametri passati alla classe use.

Tutte le funzionalità aggiuntive (ad esempio l'equivalente di `WCMUsePojo.getProperties()`) devono essere applicate esplicitamente utilizzando l' ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` oggetto. Esempio:

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

Il caso principale per implementare l' `Use` interfaccia stessa invece di estendere `WCMUsePojo` è quando desiderate utilizzare una sottoclasse di una classe già esistente come classe use.

### Adattabilità da risorse {#adaptable-from-resource}

Un'altra opzione consiste nell'utilizzare una classe helper adattabile **`org.apache.sling.api.resource.Resource`**.

Supponiamo che dobbiate scrivere uno script HTL che mostra il mimetio di una risorsa DAM. In questo caso si noti che quando lo script HTL viene chiamato, si troverà nel contesto di un **`Resource`** file JCR **`Node`** con nodetipo **`dam:Asset`**.

Sai che un **`dam:Asset`** nodo ha una struttura come questa:

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

Qui viene mostrata la risorsa (un'immagine JPEG) fornita con un'installazione predefinita di AEM come parte del progetto di esempio geometrixx. La risorsa viene chiamata **`jane_doe.jpg`** e il suo mimetype **`image/jpeg`**è.

Per accedere alla risorsa dall'interno di HTL, potete dichiararla ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` come classe nell' **`data-sly-use`** istruzione: e quindi utilizzate un metodo get per **`Asset`** recuperare le informazioni desiderate. Esempio:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

`data-sly-use` L'istruzione invia HTL per adattare la corrente **`Resource`** a un **`Asset`** oggetto e assegnarvi il nome **`asset`**locale. Quindi, chiama il **`getMimeType`** metodo di `Asset` utilizzo della abbreviazione getter HTL: `asset.mimeType`.

### Adattabilità da richiesta {#adaptable-from-request}

È anche possibile utilizzare come classe alternativa qualsiasi classe adattabile da **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Come illustrato sopra in una tabella adattabile di tipo use-class, `Resource`[`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) nell' `data-sly-use` istruzione è possibile specificare una classe adattabile da. All'esecuzione la richiesta corrente verrà adattata alla classe specificata e l'oggetto risultante sarà reso disponibile in HTL.
