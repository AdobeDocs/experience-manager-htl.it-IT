---
title: Use-Api per HTL
description: Sono disponibili due API per HTL - Java Use-API e Javascript Use-API
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---

# Use-Api per HTL {#htl-use-api}

HTL incoraggia la separazione delle questioni non consentendo alla logica di business di mescolarsi con il markup. La logica di business può essere implementata tramite use-API.

La tabella seguente offre una panoramica dei vantaggi e degli svantaggi di ogni API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Vantaggi** | <ul><li>Più veloce</li><li>Può essere esaminata con un debugger</li><li>Unit testing semplice</li></ul> | <ul><li>Può essere modificata da sviluppatori front-end</li><li>Si trova all’interno del componente, mantenendo la logica di visualizzazione di un componente vicina al modello corrispondente</li></ul> |
| **Svantaggi** | <ul><li>Non può essere modificata da sviluppatori front-end</li></ul> | <ul><li>Più lenta</li><li>Nessun debugger (al momento)</li><li>Unit testing più complicato</li></ul> |

Per i componenti di pagina, si consiglia di utilizzare un modello misto, in cui la logica del modello si trova in Java, fornendo API chiare e indipendenti da qualsiasi cosa accada nella visualizza (cioè all’interno dei componenti). AEM viene fornito con eccellenti modelli predefiniti come Pagina o API di risorsa che dovrebbero essere in grado di coprire la maggior parte dei casi.

Tutta la logica di visualizzazione specifiche per un componente deve essere posizionata all’interno di tale componente come JavaScript, poiché appartiene a tale componente.
