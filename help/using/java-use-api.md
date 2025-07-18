---
title: Java Use-API per HTL
description: Java Use-API per HTL consente a un file HTL di accedere a metodi di supporto in una classe Java personalizzata.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '1132'
ht-degree: 99%

---


# Java Use-API per HTL {#htl-java-use-api}

Java Use-API per HTL consente a un file HTL di accedere a metodi di supporto in una classe Java personalizzata.

## Caso d’uso {#use-case}

Java Use-API per HTL consente a un file HTL di accedere a metodi di supporto in una classe Java personalizzata tramite `data-sly-use`. Questo metodo consente di incapsulare nel codice Java tutte le logiche di business complesse, mentre il codice HTL tratta solo la produzione di markup diretto.

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

La mappa `bindings` può contenere oggetti che forniscono contesto allo script HTL attualmente eseguito che può essere utilizzato dall’oggetto Use-API per la relativa elaborazione.

## Un esempio semplice {#a-simple-example}

Questo esempio illustra l’utilizzo di Use-API.

>[!NOTE]
>
>Questo esempio è semplificato per illustrare il suo utilizzo. In un ambiente di produzione, Adobe consiglia di utilizzare i [modelli Sling](https://sling.apache.org/documentation/bundles/models.html).

Inizia con un componente HTL, chiamato `info,`, che non ha una classe use. È costituito da un singolo file, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Aggiungi del contenuto per questo componente per il rendering a `/content/my-example/`:

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando si accede a questo contenuto, viene eseguito il file HTL. All’interno del codice HTL si utilizza l’oggetto contestuale `properties` per accedere a `title` e `description` della risorsa corrente e per visualizzarli. Il file di output `/content/my-example.html` è il seguente:

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Aggiunta di una classe use {#adding-a-use-class}

Il componente `info` così com’è non ha bisogno di una classe use per eseguire la sua semplice funzione. Tuttavia, in alcuni casi è necessario eseguire operazioni che sono impossibili in HTL e quindi è necessario utilizzare una classe use. Ma tieni presente quanto segue:

>[!NOTE]
>
>È consigliabile utilizzare una classe use solo quando non è possibile eseguire operazioni unicamente in HTL.

Ad esempio, supponiamo che si desideri che il componente `info` visualizzi le proprietà `title` e `description` della risorsa, ma il tutto in minuscolo. Poiché HTL non dispone di un metodo per rendere le stringhe in minuscolo, puoi aggiungere una classe use Java e modificare `/apps/my-example/component/info/info.html` come segue:

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

Inoltre, viene creato `/apps/my-example/component/info/Info.java`.

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

Per ulteriori dettagli, consulta la [documentazione Java per `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html).

Osserviamo ora le diverse parti del codice.

### Confronto tra classe Java locale e bundle {#local-vs-bundle-java-class}

La classe use Java può essere installata in due modi:

* **Locale**: in un’installazione locale, il file Java di origine viene posizionato accanto al file HTL, nella stessa cartella dell’archivio. Il file di origine viene compilato automaticamente su richiesta. Non è richiesta alcuna fase separata di compilazione o packaging.
* **Bundle**: in un’installazione bundle, la classe Java deve essere compilata e implementata all’interno di un bundle OSGi utilizzando il meccanismo di implementazione di bundle standard di AEM (vedi la sezione [Classe Java in bundle](#bundled-java-class)).

Per sapere quale metodo utilizzare, considera questi due punti:

* Si consiglia una **classe use Java locale** quando la classe use è specifica per il componente in questione.
* Una **classe use Java bundle** è consigliata quando il codice Java implementa un servizio a cui si accede da più componenti HTL.

In questo esempio viene utilizzata un’installazione locale.

### Il pacchetto Java è il percorso dell’archivio {#java-package-is-repository-path}

Quando si utilizza un’installazione locale, il nome del pacchetto della classe use deve corrispondere alla posizione della cartella dell’archivio. I caratteri di sottolineatura nel nome del pacchetto sostituiscono eventuali trattini nel percorso.

In questo caso, `Info.java` si trova in `/apps/my-example/components/info` e quindi il pacchetto è `apps.my_example.components.info`:

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

Ci sono diversi modi per incorporare una classe Java con HTL, ma quello più semplice consiste nell’estendere la classe `WCMUsePojo`. Per questo esempio `/apps/my-example/component/info/Info.java`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inizializzazione della classe {#initializing-the-class}

Quando la classe use viene estesa da `WCMUsePojo`, l’inizializzazione viene eseguita escludendo il metodo `activate`, in questo caso in `/apps/my-example/component/info/Info.java`

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

In genere, il metodo [activate](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) viene utilizzato per pre-calcolare e memorizzare (in variabili membro) i valori necessari nel codice HTL, in base al contesto corrente (ad esempio la richiesta e la risorsa correnti).

La classe `WCMUsePojo` consente di accedere allo stesso set di oggetti contestuali disponibili all’interno di un file HTL (consulta il documento [Oggetti globali](global-objects.md)).

In una classe che estende `WCMUsePojo`, è possibile accedere agli oggetti contestuali utilizzando i relativi nomi:

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

In alternativa, è possibile accedere direttamente agli oggetti contestuali di uso comune utilizzando il metodo di convenienza appropriato elencato in questa tabella.

| Oggetto | Metodo di convenienza |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### Metodi getter {#getter-methods}

Una volta inizializzata la classe use, viene eseguito il file HTL. In questa fase, solitamente HTL richiama lo stato di diverse variabili membro della classe use e li renderizza per la presentazione.

Per fornire l’accesso a questi valori dall’interno del file HTL, è necessario definire metodi getter personalizzati nella classe use in base alla seguente convenzione di denominazione:

* Un metodo del modulo `getXyz` espone all’interno del file HTL una proprietà dell’oggetto denominata `xyz`.

Nel seguente file di esempio `/apps/my-example/component/info/Info.java`, i metodi `getTitle` e `getDescription` rendono accessibili le proprietà oggetto `title` e `description` all’interno del contesto del file HTL.

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

### Attributo `data-sly-use` {#data-sly-use-attribute}

L’attributo `data-sly-use` viene utilizzato per inizializzare la classe use all’interno del codice HTL. Nell’esempio, l’attributo `data-sly-use` dichiara che è utilizzata la classe `Info`. È possibile utilizzare solo il nome locale della classe perché si sta utilizzando un’installazione locale (avendo posizionato il file Java di origine nella stessa cartella del file HTL). Se si utilizzasse un’installazione bundle, si dovrebbe specificare il nome completo della classe.

Nota l’utilizzo in questo esempio `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificatore locale {#local-identifier}

L’identificatore `info` (dopo il punto in `data-sly-use.info`) viene utilizzato all’interno del file HTL per identificare la classe. L’ambito di questo identificatore è globale all’interno del file, dopo che è stato dichiarato. Non è limitato all’elemento che contiene l’istruzione `data-sly-use`.

Nota l’utilizzo in questo esempio `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ottenere le proprietà {#getting-properties}

L’identificatore `info` viene quindi utilizzato per accedere alle proprietà oggetto `title` e `description` esposte tramite i metodi getter `Info.getTitle` e `Info.getDescription`.

Nota l’utilizzo in questo esempio `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Output {#output}

Ora, quando si accede a `/content/my-example.html`, viene restituito il seguente file `/content/my-example.html`.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Questo esempio è stato semplificato per illustrarne l’utilizzo. In un ambiente di produzione, Adobe consiglia di utilizzare i [modelli Sling](https://sling.apache.org/documentation/bundles/models.html).

## Oltre le nozioni di base {#beyond-the-basics}

In questa sezione sono presentate alcune ulteriori funzioni che vanno oltre il semplice esempio già descritto.

* Trasferimento dei parametri a una classe use
* Classe use Java in bundle

### Trasferimento dei parametri {#passing-parameters}

I parametri possono essere trasferiti a una classe use al momento dell’inizializzazione.

Per informazioni dettagliate, vedere la `Sling` [documentazione del motore di script HTL](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects).

### Classe Java in bundle {#bundled-java-class}

Con una classe use in bundle, la classe deve essere compilata, inserita in un pacchetto e implementata in AEM utilizzando il meccanismo di implementazione standard di bundle OSGi. A differenza di un’installazione locale, la dichiarazione di pacchetto della classe use deve essere denominata normalmente come nell’esempio `/apps/my-example/component/info/Info.java`.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

L’istruzione `data-sly-use` deve fare riferimento al nome completo della classe, anziché al solo nome locale della classe, come nell’esempio `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
