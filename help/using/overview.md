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
translation-type: tm+mt
source-git-commit: 1d4565a4cffa6e5d42d6a5242f7ce62203dc7c63

---


# Panoramica {#overview}

Lo scopo di HTML Template Language (HTL), supportato da Adobe Experience Manager (AEM), è quello di offrire un framework Web a livello aziendale altamente produttivo che aumenta la sicurezza e consente agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.

Il linguaggio HTML Template Language è stato introdotto con AEM 6.0 e sostituisce JSP (JavaServer Pages) come sistema di modelli lato server preferito e consigliato per HTML. Per gli sviluppatori Web che devono creare siti Web aziendali affidabili, il linguaggio HTML Template Language contribuisce a migliorare la sicurezza e l'efficienza dello sviluppo.

## Maggiore sicurezza {#increased-security}

Il linguaggio HTML Template Language aumenta la sicurezza dei siti che lo utilizzano nella loro implementazione, rispetto a JSP e alla maggior parte degli altri sistemi di modelli, perché HTL è in grado di applicare automaticamente la corretta escape in base al contesto a tutte le variabili che vengono inviate al livello della presentazione. HTL rende ciò possibile perché comprende la sintassi HTML e utilizza tale conoscenza per regolare la escape richiesta per le espressioni, in base alla loro posizione nella marcatura. Ciò si tradurrà, ad esempio, in espressioni inserite in `href` o `src` attributi con un carattere di escape diverso da espressioni posizionate in altri attributi o altrove.

Anche se lo stesso risultato può essere ottenuto con linguaggi del modello come JSP, lo sviluppatore deve assicurarsi manualmente che la corretta escape sia applicata a ogni variabile. Poiché un'unica omissione o errore nell'escape applicata è potenzialmente sufficiente per causare una vulnerabilità di scripting tra siti (XSS), abbiamo deciso di automatizzare questa attività con HTL. Se necessario, gli sviluppatori possono comunque specificare una diversa escape sulle espressioni, ma con HTL il comportamento predefinito è molto più probabile che corrisponda al comportamento desiderato, riducendo la probabilità di errori.

## Sviluppo semplificato {#simplified-development}

Il linguaggio HTML Template Language è facile da imparare e le sue funzioni sono appositamente limitate per garantire che rimanga semplice e diretto. Dispone inoltre di potenti meccanismi per strutturare la logica di markup e invoking, applicando sempre una rigida separazione delle preoccupazioni tra markup e logic. HTL stesso è HTML5 standard in quanto utilizza espressioni e attributi di dati per annotare la marcatura con il comportamento dinamico desiderato, il che significa che non interrompe la validità della marcatura e la mantiene leggibile. Tenere presente che la valutazione delle espressioni e degli attributi dei dati viene eseguita interamente sul lato server e non sarà visibile sul lato client, dove qualsiasi framework JavaScript desiderato può essere utilizzato senza interferenze.

Queste funzionalità consentono agli sviluppatori HTML senza conoscenza Java e con poca conoscenza specifica del prodotto di poter modificare i modelli HTL, consentendogli di far parte del team di sviluppo e semplificare la collaborazione con gli sviluppatori Java a tutti gli stack. E viceversa, questo consente agli sviluppatori Java di concentrarsi sul codice back-end senza preoccuparsi di HTML.

## Costi ridotti {#reduced-costs}

Maggiore sicurezza, sviluppo semplificato e collaborazione in team migliorata, traduzioni per i progetti AEM in un impegno ridotto, tempi di commercializzazione più rapidi (TTM) e riduzione del costo totale di proprietà (TCO).

In concreto, ciò che è stato osservato durante la reimplementazione del sito Adobe.com con il linguaggio HTML Template Language è che il costo e la durata del progetto potrebbero essere ridotti del 25% circa.

![](assets/chlimage_1.png)

Il diagramma precedente mostra i seguenti miglioramenti in efficienza potenzialmente resi possibili da HTL:

* **** HTML / CSS / JS: Poiché gli sviluppatori HTML sono in grado di modificare direttamente i modelli HTL, i progetti front-end non devono più essere implementati separatamente dal progetto AEM, ma possono essere implementati direttamente sui componenti AEM effettivi. Questo riduce le iterazioni dolorose con gli sviluppatori Java a pieno stack.
* **** JSP / HTL: Poiché HTL stesso non richiede alcuna conoscenza Java ed è diretto alla scrittura, qualsiasi sviluppatore con esperienza HTML ha il potere di modificare i modelli.
* **** Java: Grazie all'utilizzo chiaro e semplice di Use-API fornito da HTL, l'interfaccia con la logica di business viene chiarita, che va anche a beneficio dello sviluppo Java in generale.

**Ulteriori informazioni:**

* [Guida introduttiva alla lingua modello HTML](getting-started.md)

