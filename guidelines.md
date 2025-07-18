---
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '714'
ht-degree: 96%

---
# Linee guida per contribuire alla documentazione di Adobe Experience Manager

## Filosofia della documentazione

Gli utenti di Adobe Experience Manager lavorano in ambienti altamente competitivi per creare esperienze digitali che li distinguano dalla concorrenza. Pertanto, quando Adobe offre strumenti avanzati in AEM, questi sono integrati da una documentazione precisa e chiara. Consente alla clientela di utilizzare immediatamente il proprio investimento AEM e massimizzarne il ritorno.

Per la documentazione di AEM, l’obiettivo è renderla disponibile agli utenti della soluzione il prima possibile. Viene, quindi, data priorità a una documentazione accurata e fruibile, sottoposta ad aggiornamento e miglioramento continuo.

## Contributi alla documentazione

Al fine di migliorare continuamente la documentazione di AEM, apprezziamo il contributo dell’intera comunità di utenti AEM. I miglioramenti apportati alla documentazione, che derivino da richieste pull o da problemi, possono essere correzioni, chiarimenti, ampliamenti ed esempi aggiuntivi.

## Standard della documentazione

Pur accogliendo con favore i contributi alla documentazione, qualsiasi contributo alla documentazione di AEM, sotto forma di richiesta pull o segnalazione di un problema, deve essere conforme agli standard in merito a contributi e documentazione di Adobe.

I contributi che non soddisfano tali standard possono essere rifiutati.

### I casi d’uso standard sono documentati in Adobe.

La documentazione di AEM riguarda i casi d’uso standard. I casi di utilizzo che esulano dall’ambito di installazione e utilizzo standard del prodotto non rientrano nella documentazione di AEM.

### In genere Adobe non documenta i bug o le relative soluzioni.

La documentazione di AEM riguarda i casi d’uso standard. Per questo motivo, i bug, gli effetti causati dai bug e le soluzioni per ovviare ai bug non sono documentati.

Fanno eccezione le note sulla versione, in cui possono essere elencati i problemi noti con possibili soluzioni approvate dal team di gestione del prodotto.

### I contributi alla documentazione non devono essere usati per rispondere a domande tecniche.

È gradita come contributo qualsiasi idea che punti al miglioramento della documentazione di AEM. Tuttavia, commenti, problemi e richieste pull sono intesi solo come *contributi*. Non hanno l’obiettivo di rispondere a domande su come usare AEM, su come implementare un progetto AEM o risolvere problemi tecnici.

Puoi porre qualsiasi domanda relativa all’utilizzo di AEM o agli errori tecnici. Utilizza il normale processo di supporto tramite il [Portale di supporto Experience Cloud Enterprise](https://experienceleague.adobe.com/it?support-solution=General#support) o le discussioni nella [community di Experience Manager](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/ct-p/adobe-experience-manager-community?lang=it).

***I contributi alla documentazione di AEM non sostituiscono l’Assistenza clienti di Adobe***. Pertanto, verranno rifiutati tutti i contributi che richiedono risposte a domande correlate al supporto.

### I contributi devono fare riferimento in modo chiaro alle pagine della documentazione interessate.

Se apri una segnalazione per suggerire miglioramenti alla documentazione, devi includere i collegamenti alle pagine interessate. Se apri una segnalazione di un problema utilizzando il collegamento **Modifica questa pagina** di una pagina della documentazione, il problema verrà creato automaticamente con un collegamento alla pagina in questione.

Ciò non si applica alle richieste pull, che per loro natura fanno riferimento alle pagine interessate.

## Linee guida per la documentazione

Adobe chiede che qualsiasi contributo alla documentazione segua determinate linee guida di stile.

L’adesione a queste linee guida semplifica la revisione del contributo proposto e ne velocizza quindi l’integrazione nella documentazione di Adobe.

### Lingua e stile

#### Lingua

* La documentazione di AEM è creata e mantenuta in inglese americano.
* Usa frasi quanto più semplici possibile.
* Usa un linguaggio chiaro e conciso.

Ricorda che la documentazione di AEM viene letta in tutto il mondo da persone che potrebbero non essere di madrelingua inglese o non parlare inglese correntemente. Evita le espressioni colloquiali e usa un linguaggio chiaro e semplice.

#### Segui il manuale di stile di Microsoft®

[Il manuale di stile di Microsoft®](https://learn.microsoft.com/it-it/style-guide/welcome/) è una guida di stile per la documentazione disponibile gratuitamente, specifica per la documentazione di software. La documentazione di AEM segue questa guida quando possibile.

### Formattazione

| Elemento | Stile |
|---|---|
| Elemento o opzione dell’interfaccia utente | **grassetto** |
| Nome file, percorso, input utente, valori di parametri | `monospaced` |
| Codice, riga di comando | ```Code Block``` |

### Schermate

Le schermate vanno utilizzate in modo non eccessivo e solo quando una descrizione testuale è insufficiente.

Nelle schermate non devono essere utilizzati marcatori o altre annotazioni (come riquadri rossi, frecce o testo). In questo modo le schermate possono essere riutilizzate o replicate più facilmente nelle versioni localizzate della documentazione.

### Riferimenti a specifiche versioni

È meglio evitare, quando possibile, riferimenti diretti a una versione specifica nel contenuto della documentazione. Questa raccomandazione rende la documentazione più flessibile con la possibilità di estenderla anche a versioni future.

### Utilizzo di Day, AEM, CQ, CRX

In un articolo, fai sempre riferimento al prodotto con il nome completo, scrivi **Adobe Experience Manager** la prima volta che lo utilizzi. In seguito, si può far riferimento ad esso con **AEM**.

Day, Day Software, CQ e CRX non devono essere utilizzati a meno che non sia inevitabile, ad esempio nei nomi di classi o in riferimenti specifici a versioni storiche di AEM.
