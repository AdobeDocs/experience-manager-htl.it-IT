---
title: HTL javascript Use-API
seo-title: HTL javascript Use-API
description: The HTML Template Langugae - HTL - javascript Use-API consente a un file
  HTL di accedere al codice helper scritto in javascript.
seo-description: The HTML Template Langugae - HTL - javascript Use-API consente a
  un file HTL di accedere al codice helper scritto in javascript.
uuid: 7 ab 34 b 10-30 ac -44 d 6-926 b -0234 f 52 e 5541
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 18871 af 8-e 44 b -4 eec-a 483-fcc 765 dae 58 f
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL javascript Use-API {#htl-javascript-use-api}

Il linguaggio HTL (HTML Template Langugae (HTL) javascript Use-API consente a un file HTL di accedere al codice helper scritto in javascript. In questo modo, tutte le complessità di business complesse possono essere racchiuse nel codice javascript, mentre il codice HTL occupa solo la produzione di marcatura diretta.

## Esempio semplice {#a-simple-example}

Definiamo un componente, `info`che si trova in

**`/apps/my-example/components/info`**

Contiene due file:

* **`info.js`**: un file javascript che definisce la classe use.
* `info.html`: un file HTL che definisce il componente `info`. Questo codice utilizza la funzionalità di `info.js` tramite l'API use.

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

Viene inoltre creato un nodo di contenuto che utilizza il **`info`** componente in

**`/content/my-example`**, con le proprietà:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Ecco la struttura dell'archivio risultante:

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

Prendete in considerazione il modello di componente seguente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La logica corrispondente può essere scritta utilizzando javascript ***lato server*** , ubicato in un `component.js` file accanto al modello:

```
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

Questo tenta di acquisire le `title` origini da diverse sorgenti e ritaglia la descrizione a 50 caratteri.

## Dipendenze {#dependencies}

Immaginiamo di disporre di una classe di utilità già dotata di funzionalità intelligenti, come la logica predefinita per il titolo di navigazione o di tagliare una stringa con una certa lunghezza:

```
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

Il pattern di dipendenza può essere utilizzato anche per estendere la logica di un altro componente (che in genere corrisponde al `sling:resourceSuperType` componente corrente).

Immagina che il componente principale fornisca già l' `title`e che desideriamo aggiungere **`description`** anche:

```
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

Nel caso di **`data-sly-template`** istruzioni che possono essere indipendenti dai componenti, può essere utile trasmettere parametri all'API associata associata.

Quindi, nel nostro componente, invochiamo un modello che si trova in un altro file:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Si tratta del modello in `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La logica corrispondente può essere scritta utilizzando javascript ***lato server*** , ubicato in un `template.js` file accanto al file modello:

```
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

I parametri passati sono impostati sulla `this` parola chiave.
