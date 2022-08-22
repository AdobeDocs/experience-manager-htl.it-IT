---
title: Storia di HTL
description: Per gli utenti di lunga data di AEM, questo documento fornisce lo sfondo su HTL, come sostituisce JSP e il cambiamento di nome da Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 53%

---


# Storia di HTL {#history-of-htl}

Per gli utenti di lunga data di AEM, questo documento fornisce lo sfondo su HTL, come sostituisce JSP e il cambiamento di nome da Sightly.

## Precedentemente noto come Sightly {#sightly}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML in Adobe Experience Manager. Sostituisce JSP (JavaServer Pages), utilizzato nelle versioni precedenti di AEM.

## HTL su JSP {#htl-over-jsp}

Per i nuovi progetti AEM, si consiglia di utilizzare HTML Template Language, in quanto offre numerosi vantaggi rispetto a JSP. Per i progetti esistenti, tuttavia, una migrazione ha senso solo se si stima che sarà meno impegnativa rispetto al mantenimento degli JSP esistenti per i prossimi anni.

Tuttavia, passare ad HTL non è necessariamente una scelta “tutto o niente”, dal momento che i componenti scritti in HTL sono compatibili con i componenti scritti in JSP o ESP. Ciò significa che i progetti esistenti possono utilizzare senza problemi HTL per i nuovi componenti, mantenendo allo stesso tempo JSP per i componenti esistenti.

Anche all’interno dello stesso componente, i file HTL possono essere utilizzati insieme a JSP ed ESP. L’esempio seguente mostra alla **riga 1** come includere un file HTL da un file JSP e alla **riga 2** come un file JSP può essere incluso da un file HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Domande frequenti  {#frequently-asked-questions}

Queste sono alcune domande che vengono comunemente poste dagli sviluppatori esperti AEM nuovi di HTL.

### HTL ha dei limiti che JSP non ha? {#limitations}

HTL non ha assolutamente limiti rispetto a JSP, nel senso che ciò che può essere fatto con JSP dovrebbe essere praticabile anche con HTL. Tuttavia, HTL è di progettazione più rigoroso rispetto a JSP per diversi aspetti. Ciò che può essere ottenuto in un singolo file JSP, potrebbe essere necessario essere separato in una classe Java o in un file JavaScript per essere raggiungibile in HTL. Ma ciò è generalmente desiderato al fine di garantire una buona separazione delle questioni di logica e markup.

### HTL supporta le librerie di tag JSP? {#tag-libraries}

No, ma come mostrato nella [Caricamento delle librerie client](getting-started.md#loading-client-libraries) Nella sezione del documento Guida introduttiva, le istruzioni modello e chiamata offrono un pattern simile.

### È possibile estendere le funzioni HTL su un progetto AEM? {#extended}

No, non è possibile. HTL dispone di potenti meccanismi di estensione per il riutilizzo della logica (il [Use-API](#use-api-for-accessing-logic)) e di markup (modello e istruzioni di chiamata), che può essere utilizzato per modularizzare il codice dei progetti.

### Quali sono i principali vantaggi di HTL rispetto a JSP? {#benefits}

I principali vantaggi sono la sicurezza e l’efficienza del progetto, descritte in dettaglio nella [Panoramica.](overview.md)

### JSP diventerà obsoleto? {#go-away}

Non ci sono piani in questo senso.

## Come si chiama? {#what-is-in-a-name}

Nelle AEM 6.0 e 6.1, HTL è stato indicato come **Sightly**. Adobe rinominato in **Lingua del modello di HTML** o **HTL** chiarire a cosa serve la specifica e allinearsi alle linee guida generali per la denominazione dell’Adobe in generale. Questa modifica al nome è stata applicata a partire da agosto 2016 e si applica ad AEM versione 6.0 e successive.

>[!NOTE]
>
>Questa modifica al nome non influisce sul codice o sull’API, pertanto la compatibilità non è interessata. Per maggiori informazioni, [fai riferimento a questo video di annuncio.](https://helpx.adobe.com/it/experience-manager/how-to/announce-htl.html)

Per saperne di più su HTL e un ottimo posto per iniziare è il nostro ufficiale [Guida introduttiva alla lingua dei modelli di HTML (HTL).](overview.md)
