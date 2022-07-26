---
title: JavaScript Use-API per HTL
description: Scopri in che modo l’API di utilizzo JavaScript per HTML Template Language (HTL) abilita un file HTL per accedere al codice helper scritto in JavaScript.
exl-id: e98bfbd5-fa64-48c7-bd14-477d4c5e1788
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: tm+mt
source-wordcount: '326'
ht-degree: 93%

---

# JavaScript Use-API per HTL {#htl-javascript-use-api}

JavaScript Use-API per HTML Template Language (HTL) consente a un file HTL di accedere a codice helper scritto in JavaScript. Questo consente di incapsulare nel codice JavaScript tutte le logiche di business complesse, mentre il codice HTL tratta solo la produzione di markup diretto.

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

## Un esempio semplice {#a-simple-example}

Definiamo un componente, `info`, che si trova in

`/apps/my-example/components/info`

Contiene due file:

* **`info.js`**: un file JavaScript che definisce la classe use.
* **`info.html`**: un file HTL che definisce il componente `info`. Questo codice utilizzerà le funzionalità di `info.js` tramite use-API.

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

Creiamo anche un nodo di contenuto che utilizza il componente `info` in

`/content/my-example`, con le proprietà:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Di seguito è illustrata la struttura di archivio risultante:

### Struttura di archivio {#repository-structure}

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

La logica corrispondente può essere scritta utilizzando il seguente JavaScript lato server, che si trova in un file `component.js` accanto al modello:

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

Questo cerca di estrarre il `title` da diversi file di origine e riduce la descrizione a 50 caratteri.

## Dipendenze {#dependencies}

Immaginiamo di avere una classe di utilità già dotata di funzioni intelligenti, come la logica predefinita per il titolo di navigazione o la riduzione di una stringa a una determinata lunghezza:

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

Il pattern di dipendenza può essere utilizzato anche per estendere la logica di un altro componente (che in genere corrisponde a `sling:resourceSuperType` del componente corrente).

Immagina che il componente principale fornisca già il `title` e si desideri aggiungere anche una `description`:

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

Nel caso di istruzioni `data-sly-template` che possono essere indipendenti dai componenti, può essere utile trasmettere parametri all’use-API associata.

Quindi nel nostro componente chiamiamo un modello che si trova in un file diverso:

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

La logica corrispondente può essere scritta utilizzando il seguente JavaScript lato server, che si trova in un file `template.js` accanto al file di modello:

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

I parametri trasmessi vengono impostati sulla parola chiave `this`.
