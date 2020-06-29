---
title: API di utilizzo JavaScript HTL
description: HTML Template Language - HTL - JavaScript Use-API consente a un file HTL di accedere al codice helper scritto in JavaScript.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 2%

---


# API di utilizzo JavaScript HTL {#htl-javascript-use-api}

L&#39;API Use-API JavaScript (HTL, HTML Template Language) JavaScript consente a un file HTL di accedere al codice helper scritto in JavaScript. Questo consente di incorporare nel codice JavaScript tutte le complesse logiche aziendali, mentre il codice HTL si occupa solo della produzione di markup diretti.

Vengono utilizzate le seguenti convenzioni.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Esempio semplice {#a-simple-example}

Definiamo un componente, `info`, che si trova in

`/apps/my-example/components/info`

Contiene due file:

* **`info.js`**: un file JavaScript che definisce la classe use.
* **`info.html`**: un file HTL che definisce il componente `info`. Questo codice utilizzerà la funzionalità di `info.js` tramite use-API.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

Inoltre, viene creato un nodo di contenuto che utilizza il `info` componente in

`/content/my-example`, con proprietà:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Di seguito è riportata la struttura del repository risultante:

### Struttura archivio {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Considera il seguente modello di componente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La logica corrispondente può essere scritta utilizzando il seguente JavaScript lato server, situato in un `component.js` file accanto al modello:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

Questo cerca di prendere la descrizione `title` da origini diverse e ritagliarla a 50 caratteri.

## Dipendenze {#dependencies}

Immaginiamo di avere una classe di utilità già dotata di funzioni intelligenti, come la logica di default per il titolo di navigazione o che taglia bene una stringa a una certa lunghezza:

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## Estensione {#extending}

Il pattern di dipendenza può essere utilizzato anche per estendere la logica di un altro componente (che in genere corrisponde a quella `sling:resourceSuperType` del componente corrente).

Immaginate che il componente principale fornisca già l&#39; `title`, e vogliamo aggiungere `description` anche:

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## Trasmissione di parametri a un modello {#passing-parameters-to-a-template}

Nel caso di `data-sly-template` istruzioni che possono essere indipendenti dai componenti, può essere utile trasmettere parametri all&#39;API Use-API associata.

Nel nostro componente chiamiamo un modello che si trova in un file diverso:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Quindi questo è il modello che si trova in `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La logica corrispondente può essere scritta utilizzando il seguente JavaScript lato server, situato in un `template.js` file accanto al file modello:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

I parametri passati vengono impostati sulla `this` parola chiave.
