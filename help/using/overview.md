---
title: Panoramica di AEM HTL
seo-title: Panoramica della documentazione tecnica di AEM HTL.
description: L’obiettivo di HTL supportato da AEM è quello di offrire un framework Web a livello aziendale altamente produttivo che aumenta la sicurezza e consente agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.
seo-description: Questo documento illustra i principi e lo scopo del linguaggio HTL (HTML Template Language) supportato da Adobe Experience Manager. HTL è un framework Web a livello aziendale altamente produttivo che aumenta la sicurezza e consente agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.
uuid: 8f486325-0a1b-4186-a998-96fc0034c44a
contentOwner: Utente
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: introduzione
content-type: riferimento
discoiquuid: 8f779e08-94c7-43bc-a6e5-d81a9f818c5c
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
skyline: verifica della replica
translation-type: tm+mt
source-git-commit: 0aa1e905fd6d24f7031dceb0a8a89b56da198719

---


# Panoramica {#overview}

Lo scopo di HTML Template Language (HTL), supportato da Adobe Experience Manager (AEM), è quello di offrire un framework Web a livello aziendale altamente produttivo che aumenta la sicurezza e consente agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.

Il linguaggio HTML Template Language è stato introdotto con AEM 6.0 e sostituisce JSP (JavaServer Pages) come sistema di modelli lato server preferito e consigliato per HTML. Per gli sviluppatori Web che devono creare siti Web aziendali affidabili, il linguaggio HTML Template Language contribuisce a migliorare la sicurezza e l'efficienza dello sviluppo.

## Maggiore sicurezza {#increased-security}

Il linguaggio HTML Template Language aumenta la sicurezza dei siti che lo utilizzano nella loro implementazione, rispetto a JSP e alla maggior parte degli altri sistemi di modelli, perché HTL è in grado di applicare automaticamente la corretta escape in base al contesto a tutte le variabili che vengono inviate al livello della presentazione. HTL rende ciò possibile perché comprende la sintassi HTML e utilizza tale conoscenza per regolare la escape richiesta per le espressioni, in base alla loro posizione nella marcatura. This will for instance result in expressions placed in  or  attributes to be escaped differently from expressions placed in other attributes, or elsewhere.`href``src`

Anche se lo stesso risultato può essere ottenuto con linguaggi del modello come JSP, lo sviluppatore deve assicurarsi manualmente che la corretta escape sia applicata a ogni variabile. Poiché un'unica omissione o errore nell'escape applicata è potenzialmente sufficiente per causare una vulnerabilità di scripting tra siti (XSS), abbiamo deciso di automatizzare questa attività con HTL. Se necessario, gli sviluppatori possono comunque specificare una diversa escape sulle espressioni, ma con HTL il comportamento predefinito è molto più probabile che corrisponda al comportamento desiderato, riducendo la probabilità di errori.

## Simplified Development {#simplified-development}

The HTML Template Language is easy to learn and its features are purposely limited to ensure that it stays simple and straight-forward. It also has powerful mechanisms for structuring the markup and invoking logic, while always enforcing strict separation of concerns between markup and logic. HTL itself is standard HTML5 as it uses expressions and data attributes to annotate the markup with the desired dynamic behavior, meaning that it doesn't break the validity of the markup and keeps it readable. Note that the evaluation of the expressions and data attributes is done entirely server-side and won't be visible on the client-side, where any desired JavaScript framework can be used without interfering.

These capabilities allow HTML developers without Java knowledge and with little product-specific knowledge to be able to edit HTL templates, allowing them to be part of the development team, and streamlining the collaboration with the full-stack Java developers. And vice versa this allows Java developers to focus on the back-end code without worrying about HTML.

## Reduced Costs {#reduced-costs}

Increased security, simplified development and improved team collaboration, translates for AEM projects in reduced effort, faster time to market (TTM), and lower total cost of ownership (TCO).

Concretely, from what has been observed when re-implementing the Adobe.com site with the HTML Template Language is that the cost and duration of the project could be reduced by about 25%.

![](assets/chlimage_1.png)

The diagram above shows following improvements in efficiency potentially made possible by HTL:

* **HTML / CSS / JS:** Because the HTML developers are able to directly edit HTL templates, the front-end designs don't have to be implemented separately from the AEM project anymore, but can be implemented directly on the actual AEM components. This reduces painful iterations with the full-stack Java developers.
* **JSP / HTL:** Since HTL itself doesn't require any Java knowledge and is straight-forward to write, any developer with HTML expertise is empowered to edit the templates.
* **** Java: Grazie all'utilizzo chiaro e semplice di Use-API fornito da HTL, l'interfaccia con la logica di business viene chiarita, che va anche a beneficio dello sviluppo Java in generale.

**Ulteriori informazioni:**

* [Guida introduttiva alla lingua modello HTML](getting-started.md)

