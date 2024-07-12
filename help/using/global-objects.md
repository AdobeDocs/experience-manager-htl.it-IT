---
title: Oggetti globali di HTL
description: Scopri gli oggetti enumerabili, gli oggetti basati su Java e gli oggetti basati su JavaScript in HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '166'
ht-degree: 100%

---


# Oggetti globali di HTL {#htl-global-objects}

Senza dover specificare nulla, HTL fornisce l’accesso a molti oggetti utili allo sviluppatore. Questi oggetti sono in aggiunta a quelli che possono essere introdotti tramite [Use-API.](java-use-api.md)

>[!NOTE]
>
>Per gli sviluppatori che hanno familiarità con lo sviluppo JSP in AEM, HTL fornisce l’accesso a tutti gli oggetti che erano comunemente disponibili in JSP dopo aver incluso `global.jsp`.

## Oggetti elencabili {#enumerable-objects}

Questi oggetti consentono di accedere facilmente a informazioni di uso comune. Il loro contenuto è accessibile mediante notazione col punto e può essere iterato tramite `data-sly-list` o `data-sly-repeat`.

| Nome della variabile | Descrizione | Supportato da |
|--- |--- |--- |
| `properties` | Elenco delle proprietà della risorsa corrente | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Elenco delle proprietà della pagina corrente | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Elenco delle proprietà di pagina ereditate della pagina corrente | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Oggetti supportati da Java {#java-backed-objects}

Ciascuno dei seguenti oggetti viene supportato dal relativo oggetto Java.

| Nome della variabile | Descrizione |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Oggetti supportati da JavaScript {#javascript-backed-objects}

È possibile supportare la logica HTL con JavaScript. Tuttavia, il metodo preferito o consigliato è quello di utilizzare [modelli Sling.](https://sling.apache.org/documentation/bundles/models.html)
