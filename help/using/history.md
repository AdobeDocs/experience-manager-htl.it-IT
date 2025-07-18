---
title: Storia di HTL
description: Per gli utenti di lunga data di AEM, questo documento fornisce informazioni di base su HTL, su come esso sostituisce JSP e sul cambiamento di nome da Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 96%

---


# Storia di HTL {#history-of-htl}

Per gli utenti di lunga data di AEM, questo documento fornisce informazioni di base su HTL, su come esso sostituisce JSP e sul cambiamento di nome da Sightly.

## Precedentemente noto come Sightly {#sightly}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML in Adobe Experience Manager. Sostituisce JSP (JavaServer Pages), utilizzato nelle versioni precedenti di AEM.

## HTL su JSP {#htl-over-jsp}

Adobe consiglia di utilizzare HTML Template Language per i nuovi progetti AEM, perché offre molteplici vantaggi rispetto a JSP. Per i progetti esistenti, tuttavia, una migrazione ha senso solo se si stima che sarà meno impegnativa rispetto al mantenimento degli JSP esistenti per i prossimi anni.

Il passaggio ad HTL non è necessariamente una scelta “tutto o niente”, dal momento che i componenti scritti in HTL sono compatibili con i componenti scritti in JSP o ESP. Questo approccio implica che i progetti esistenti possono utilizzare HTL per i nuovi componenti senza alcun problema, mantenendo JSP per i componenti esistenti.

Anche all’interno dello stesso componente, i file HTL possono essere utilizzati insieme a JSP ed ESP. L’esempio seguente mostra alla **riga 1** come includere un file HTL da un file JSP e alla **riga 2** come un file JSP può essere incluso da un file HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Domande frequenti {#frequently-asked-questions}

Gli sviluppatori AEM esperti che hanno poca esperienza con HTL, spesso pongono le seguenti domande:

### HTL ha dei limiti che JSP non ha? {#limitations}

HTL non ha limiti rispetto a JSP, nel senso che ciò che può essere fatto con JSP dovrebbe essere realizzabile anche con HTL. Tuttavia, in base alla progettazione, HTL è più rigido di JSP in diversi aspetti. Ciò che può essere ottenuto in un singolo file JSP potrebbe dover essere separato in una classe Java o in un file JavaScript per essere realizzabile in HTL. Ma questo approccio è generalmente desiderato al fine di garantire una buona separazione delle questioni di logica e markup.

### HTL supporta le librerie di tag JSP? {#tag-libraries}

No. Tuttavia, come mostrato nella sezione [Caricamento librerie client](getting-started.md#loading-client-libraries) del documento Guida introduttiva, le istruzioni di modello e chiamata offrono un pattern simile.

### È possibile estendere le funzioni HTL su un progetto AEM? {#extended}

No. HTL dispone di potenti meccanismi di estensione per il riutilizzo della logica ([Use-API](#use-api-for-accessing-logic)) e di markup (istruzioni di modello e chiamata), che possono essere utilizzati per modulare il codice dei progetti.

### Quali sono i principali vantaggi di HTL rispetto a JSP? {#benefits}

I principali vantaggi sono la sicurezza e l’efficienza del progetto, descritte nei dettagli nella [Panoramica.](overview.md).

### Le pagine JavaServer (JSP) diventeranno obsolete? {#go-away}

No. Non è prevista la disattivazione di JSP.

## Come si chiama? {#what-is-in-a-name}

In AEM 6.0 e 6.1, HTL era indicato come **Sightly**. Adobe l’ha rinominato **HTML Template Language** o **HTL** per chiarire a cosa serve la specifica e per allinearla alle linee guida per le denominazioni di Adobe in generale. Questa modifica al nome è stata applicata a partire da agosto 2016 e si applica ad AEM versione 6.0 e successive.

>[!NOTE]
>
>Questa modifica al nome non influisce sul codice o sull’API, pertanto la compatibilità non è interessata.

<!-- LINK IS 404
For more information, watch [this announcement video](https://helpx.adobe.com/experience-manager/how-to/announce-htl.html). -->

Per ulteriori informazioni su HTL, consulta [Guida introduttiva ad HTML Template Language (HTL)](overview.md).
