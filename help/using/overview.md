---
title: Panoramica
seo-title: Panoramica
description: Lo scopo di HTL supportato da AEM è quello di offrire un framework Web
  a livello di Enterprise altamente produttivo che aumenta la sicurezza e consente
  agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.
seo-description: Lo scopo di HTML Template Language (HTL) supportato da Adobe Experience
  Manager è quello di offrire un framework Web altamente produttivo a livello di Enterprise
  che incrementa la sicurezza e consente agli sviluppatori HTML senza conoscenze Java
  di partecipare meglio ai progetti AEM.
uuid: 8 f 486325-0 a 1 b -4186-a 998-96 fc 0034 c 44 a
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: introduction
content-type: riferimento
discoiquuid: 8 f 779 e 08-94 c 7-43 bc-a 6 e 5-d 81 a 9 f 818 c 5 c
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Panoramica {#overview}

Lo scopo di HTML Template Language (HTL), supportato da Adobe Experience Manager (AEM), consiste nell'offrire un framework Web di livello Enterprise altamente produttivo che aumenta la protezione e consente agli sviluppatori HTML senza conoscenze Java di partecipare meglio ai progetti AEM.

Il linguaggio HTML Template Language è stato introdotto con AEM 6.0 e si occupa di JSP (Javaserver Pages) come sistema di modelli lato server preferito per HTML. Per gli sviluppatori Web che hanno bisogno di creare siti Web enterprise affidabili, il linguaggio HTML Template Language contribuisce a migliorare la sicurezza e l'efficienza di sviluppo.

## Maggiore sicurezza {#increased-security}

Il linguaggio HTML Template Language aumenta la protezione dei siti che lo utilizzano nella propria implementazione, rispetto a JSP e alla maggior parte dei sistemi di modelli, in quanto HTL è in grado di applicare automaticamente la escape contestuale corretta a tutte le variabili per il livello della presentazione. L'HTL rende disponibile ciò perché riconosce la sintassi HTML e utilizza tale conoscenza per regolare la escape necessaria per le espressioni, in base alla loro posizione nella marcatura. Ciò, ad esempio, determinerà la visualizzazione in sequenza di espressioni posizionate in `href` o `src` attributi in modo diverso da espressioni posizionate in altri attributi o altrove.

Sebbene sia possibile ottenere lo stesso risultato con linguaggi di tipo JSP, lo sviluppatore deve assicurarsi manualmente che la corretta escape sia applicata a ogni variabile. Poiché un'unica operazione o un errore sulla escape applicata è potenzialmente sufficiente a provocare una vulnerabilità di cross-site scripting (XSS), abbiamo deciso di automatizzare l'attività con HTL. Se necessario, gli sviluppatori possono comunque specificare una diversa fuga di espressioni, ma con HTL il comportamento predefinito è molto più probabilmente corrispondente al comportamento desiderato, riducendo la probabilità di errori.

## Sviluppo semplificato {#simplified-development}

Il linguaggio HTML Template Language (Lingua modello HTML) è facile da imparare e le sue funzionalità sono in modo corretto, per essere semplice e semplice. Dispone inoltre di sofisticati meccanismi per strutturare la marcatura e la logica di chiamata, allo stesso modo in cui si regola sempre rigorosamente la distinzione tra informazioni di marcatura e logica. HTL è uno standard HTML 5 che utilizza espressioni e attributi di dati per annotare la marcatura con il comportamento dinamico desiderato, il che significa che non interrompe la validità della marcatura e la mantiene leggibile. La valutazione delle espressioni e gli attributi dei dati sono interamente server-side e non saranno visibili sul lato client, dove qualsiasi framework javascript desiderato può essere utilizzato senza interferire.

Queste funzionalità consentono agli sviluppatori HTML senza conoscenza di Java e con scarsa conoscenza di prodotto di modificare i modelli HTL, consentendogli di far parte del team di sviluppo e di semplificare la collaborazione con gli sviluppatori Java a tutti gli effetti. E viceversa, consente agli sviluppatori Java di concentrarsi sul codice di back-end senza preoccuparsi di HTML.

## Costi ridotti {#reduced-costs}

Maggiore protezione, sviluppo semplificato e collaborazione tra team migliorata, conversione per progetti AEM in sforzi ridotti, tempo più veloce sul mercato (TTM) e riduzione del costo totale di proprietà (TCO).

In sostanza, da ciò che è stato osservato quando si riimplementa il sito Adobe.com con il linguaggio HTML Template Language, il costo e la durata del progetto potrebbero essere ridotti di circa il 25%.

![](assets/chlimage_1.png)

Il diagramma riportato qui sopra mostra i seguenti miglioramenti in termini di efficienza potenzialmente possibile da HTL:

* **HTML/CSS/JS:** Poiché gli sviluppatori HTML sono in grado di modificare direttamente i modelli HTL, le progettazioni front-end non devono più essere implementate separatamente dal progetto AEM, ma possono essere implementate direttamente sui componenti AEM. Ciò riduce le dolorazioni dolorose con gli sviluppatori Java a tutti gli effetti.
* **JSP/HTL:** Poiché HTL non richiede conoscenze Java ed è semplice da scrivere, qualsiasi sviluppatore con esperienza HTML è autorizzato a modificare i modelli.
* **Java:** Grazie alla semplice e intuitiva API Use-API fornito da HTL, l'interfaccia con la logica aziendale viene chiarita, il che andrà a vantaggio anche di sviluppo Java.

**Leggi avanti:**

* [Guida introduttiva al linguaggio HTML Template](getting-started.md)

