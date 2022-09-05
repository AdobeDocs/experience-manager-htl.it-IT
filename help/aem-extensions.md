---
title: Estensioni AEM
description: AEM offre estensioni della specifica HTL per AEM, utili per gli sviluppatori.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: ht
source-wordcount: '308'
ht-degree: 100%

---


# Estensioni AEM {#aem-extensions}

Simile alle [Estensioni Apache Sling della specifica HTL](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1), AEM offre alcune opzioni di espressione aggiuntive che facilitano l’utilizzo dei concetti di AEM direttamente negli script HTL.

## i18n {#i18n}

Le stesse [tre opzioni aggiuntive](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) disponibili per Apache Sling possono essere utilizzate insieme a `i18n`:

* `locale`
* `hint`
* `basename`

Tuttavia, in AEM il [supporto all’internazionalizzazione](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html?lang=it) per HTL viene implementato con l’aiuto dell’API dal pacchetto `com.day.cq.i18n`.

## data-sly-include {#data-sly-include}

In AEM, `data-sly-include` può richiedere un’opzione `wcmmode` aggiuntiva che controlla la [Modalità WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) per lo script incluso. I valori consentiti sono i nomi delle costanti enum disponibili.

## data-sly-resource {#data-sly-resource}

Oltre ai percorsi e `Resources`, l’elemento del blocco `data-sly-resource` può anche funzionare con [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) o [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Con entrambi gli approcci, è necessario specificare la proprietà Stringa `resourceName`. Il relativo valore viene utilizzato per creare una [Risorsa sintetica](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) che verrà inclusa nel contesto di rendering. Le altre proprietà da `Record` o `Map` trasmesse a `data-sly-resource` vengono utilizzate come normali proprietà `Resource`. Se la proprietà `sling:resourceType` manca in questa mappa, come tipo di risorsa sarà considerato il valore dell’[opzione espressione](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) `resourceType` oppure il tipo della risorsa corrente che gestisce il rendering.

Considerate le seguenti proprietà mappa/record disponibili nell’ambito dello script come `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

E dato il markup seguente:

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
