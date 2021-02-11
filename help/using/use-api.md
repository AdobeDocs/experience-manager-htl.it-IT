---
title: API di utilizzo HTL
description: Sono disponibili due API per HTL - Java Use-API e Javascript Use-API
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 8%

---


# API di utilizzo HTL {#htl-use-api}

HTL incoraggia la separazione delle preoccupazioni, non consentendo logica di business di mischiarsi con il markup. La logica aziendale può essere implementata tramite l&#39;API Use.

La tabella seguente fornisce una panoramica dei vantaggi e degli svantaggi di ciascuna API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Vantaggi** | <ul><li>Più veloce</li><li>Può essere ispezionato con un debugger</li><li>Facile da testare</li></ul> | <ul><li>Può essere modificato da sviluppatori front-end</li><li>Si trova all’interno del componente, mantenendo la logica di visualizzazione di un componente vicina al modello corrispondente</li></ul> |
| **Svantaggi** | <ul><li>Non può essere modificato da sviluppatori front-end</li></ul> | <ul><li>Lentezza</li><li>Nessun debugger (ancora)</li><li>Prova più rapida</li></ul> |

Per i componenti della pagina, si consiglia di utilizzare un modello misto, in cui tutte le logiche del modello si trovano in Java, fornendo API chiare che siano agnostiche a qualsiasi cosa accada nella vista (cioè all&#39;interno dei componenti). AEM vengono forniti ottimi modelli predefiniti come Pagina o l&#39;API delle risorse, che dovrebbero essere in grado di coprire la maggior parte dei casi.

Tutte le logiche di visualizzazione specifiche per un componente devono essere inserite all’interno di tale componente come JavaScript, perché appartengono a tale componente.
