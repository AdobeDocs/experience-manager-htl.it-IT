---
title: Panoramica di HTL
description: Scopri come AEM supporta HTL (HTML Template Language) per fornire un’infrastruttura web di livello enterprise produttiva che migliora la sicurezza. Questa infrastruttura consente agli sviluppatori HTML senza conoscenze Java di partecipare in modo migliore ai progetti AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '677'
ht-degree: 100%

---


# Panoramica {#overview}

>[!TIP]
>
>**Hai preso in considerazione Edge Delivery Services per AEM?**
>
>È possibile continuare a utilizzare i metodi descritti in questo documento per i progetti esistenti. Tuttavia, per i nuovi progetti, Adobe consiglia di sfruttare [Edge Delivery Services.](https://experienceleague.adobe.com/it/docs/experience-manager-cloud-service/content/edge-delivery/overview)

HTML Template Language (HTL), supportato da Adobe Experience Manager (AEM), mira a fornire un’infrastruttura web di livello enterprise produttiva che migliora la sicurezza. Consente inoltre agli sviluppatori HTML senza conoscenze Java di partecipare in modo migliore ai progetti AEM.

[Introdotto in AEM 6.0](history.md), HTML Template Language è il sistema di modelli lato server preferito e consigliato per HTML in AEM. HTML Template Language supporta gli sviluppatori web nella creazione di siti web aziendali affidabili, aumentando la sicurezza e l’efficienza dello sviluppo.

## Maggiore sicurezza {#increased-security}

HTL (HTML Template Language) migliora la sicurezza del sito applicando automaticamente gli escape adeguati in base al contesto a tutte le variabili di output, rendendolo più sicuro rispetto alla maggior parte degli altri sistemi di modelli. Questo approccio è possibile in quanto HTL comprende la sintassi HTML e utilizza tale conoscenza per modificare l’escape necessario per le espressioni, in base alla loro posizione nell’ordine di markup. Questo metodo si traduce, ad esempio, in espressioni posizionate in attributi `href` o `src` con un escape diverso rispetto a quelle posizionate in altri attributi o altrove.

Sebbene sia possibile ottenere lo stesso risultato con i linguaggi basati su modelli come JSP, con questi ultimi lo sviluppatore deve accertarsi manualmente che l’escape corretto venga applicato a ogni variabile. Poiché una sola omissione o un singolo errore nell’escape applicato potrebbe causare una vulnerabilità cross-site scripting (XSS), Adobe ha deciso di automatizzare questa attività con HTL. Se necessario, gli sviluppatori possono comunque specificare un escape diverso sulle espressioni. Con HTL, tuttavia, è molto più probabile che il comportamento predefinito corrisponda a quello desiderato, riducendo la probabilità di errori.

## Sviluppo semplificato {#simplified-development}

HTML Template Language è facile da imparare e presenta funzioni appositamente limitate per garantirne la semplicità. Dispone inoltre di potenti meccanismi per strutturare la logica di markup e chiamata, applicando costantemente una rigida separazione delle questioni di markup e logica. HTL è HTML5 standard, che utilizza espressioni e attributi di dati per annotare il markup con un comportamento dinamico. Questo approccio mantiene la validità e la leggibilità del markup. La valutazione delle espressioni e degli attributi dei dati viene eseguita interamente sul lato server e non sarà visibile sul client, dove è possibile utilizzare qualsiasi infrastruttura JavaScript senza creare interferenze.

Queste funzionalità consentono agli sviluppatori HTML senza conoscenze Java di modificare i modelli HTL, integrarsi nel team di sviluppo e semplificare la collaborazione con sviluppatori Java full stack. Viceversa, HTL consente agli sviluppatori Java di concentrarsi sul codice back-end senza preoccuparsi dell’HTML.

## Costi ridotti {#reduced-costs}

Una maggiore sicurezza, uno sviluppo semplificato e una migliore collaborazione in team si traducono, per i progetti AEM, in maggiore efficienza, tempi di realizzazione più rapidi (TTM) e una riduzione del costo totale di proprietà (TCO).

La reimplementazione del sito Adobe.com con HTML Template Language ha dimostrato che i costi e la durata del progetto sono ridotti fino a circa il 25%.

![Aumento dell’efficienza e riduzione dei costi](assets/chlimage_1.png)

Il diagramma precedente mostra i potenziali miglioramenti seguenti in termini di efficienza resi possibili da HTL:

* **HTML/CSS/JS:** gli sviluppatori HTML possono modificare direttamente i modelli HTL, consentendo l’implementazione diretta dei progetti front-end sui componenti AEM, eliminando la necessità di un’implementazione separata. Questo approccio riduce le ripetute iterazioni degli sviluppatori Java full-stack.
* **JSP/HTL:** poiché il linguaggio HTL non richiede alcuna conoscenza Java ed è di facile scrittura, qualsiasi sviluppatore con esperienza HTML è in grado di modificare i modelli.
* **Java:** grazie alla semplicità d’uso dell’API di utilizzo fornita da HTL, l’interfaccia con la logica di business diventa più chiara, andando anche a beneficio dello sviluppo Java in generale.

## Video introduttivo {#video}

Il seguente video proveniente da una [sessione AEM Gems](https://experienceleague.adobe.com/it/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl), fornisce una panoramica dello scopo di HTL e degli esempi di implementazione.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Nota che il video fa riferimento a HTL dal [suo nome precedente, Sightly](history.md).

## Passaggi successivi {#next-steps}

Ora che conosci gli obiettivi e i vantaggi di HTL, puoi iniziare a utilizzare il linguaggio. Consulta [Guida introduttiva a HTML Template Language](getting-started.md).
