---
title: Estensioni AEM
description: AEM offre estensioni della specifica HTL da AEM per comodità in qualità di sviluppatore.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# Estensioni AEM {#aem-extensions}

Simile al [Estensioni Apache Sling della specifica HTL,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM offre alcune opzioni di espressione aggiuntive che facilitano l’utilizzo dei concetti di AEM direttamente negli script HTL.

## i18n {#i18n}

Lo stesso [tre opzioni aggiuntive](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) come in Apache Sling può essere utilizzato insieme a `i18n`:

* `locale`
* `hint`
* `basename`

Tuttavia, in AEM [supporto all&#39;internazionalizzazione](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) per HTL viene implementato con l’aiuto dell’API dal `com.day.cq.i18n` pacchetto.

## data-sly-include {#data-sly-include}

In AEM, `data-sly-include` può richiedere un `wcmmode` che controllano [Modalità WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) per lo script incluso. I valori consentiti sono i nomi delle costanti enum disponibili.

## data-sly-resource {#data-sly-resource}

Oltre ai percorsi e `Resources`, `data-sly-resource` l’elemento blocco può anche funzionare con [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) o [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Con entrambi gli approcci, la `resourceName` È necessario specificare la proprietà String . Il relativo valore viene utilizzato per creare un [Risorsa sintetica](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) che verranno inclusi nel contesto di rendering. Il resto delle proprietà dal `Record` o `Map` è stato trasmesso a `data-sly-resource` viene utilizzato come normale `Resource` proprietà. Se la `sling:resourceType` manca la proprietà in questa mappa. Il tipo di risorsa sarà considerato il valore della `resourceType` [opzione espressione](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) o il tipo di risorsa della risorsa corrente che gestisce il rendering.

Considerate le seguenti proprietà mappa/record disponibili nell&#39;ambito dello script come `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

E data la marcatura seguente:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

È previsto il seguente output:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
