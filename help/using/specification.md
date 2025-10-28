---
title: Specifica HTL
description: Per informazioni dettagliate sulla sintassi, fai riferimento alla specifica HTL.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
index: false
source-git-commit: 391c5279f0021dbedaffb0c63e67e037d6c782e1
workflow-type: tm+mt
source-wordcount: '135'
ht-degree: 85%

---


# Specifica HTL {#htl-specification}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML.

## Livelli HTL {#layers}

È possibile definire HTL in AEM in base a un numero di livelli.

1. **[Specifiche HTL](https://github.com/adobe/htl-spec)**: HTL è una specifica open-source, indipendente dalla piattaforma, che chiunque può implementare. Le sue specifiche sono mantenute nel suo archivio GitHub.
1. **[Motore di script HTL Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)**: il progetto `Sling` ha creato l’implementazione di riferimento di HTL, utilizzata da AEM. Il progetto `Sling` mantiene la propria documentazione.
1. **[Estensioni AEM](https://experienceleague.adobe.com/it/docs/experience-manager-htl/content/aem-extensions)** - AEM si basa sul motore di script HTL `Sling` per offrire agli sviluppatori funzionalità convenienti specifiche per AEM. Queste estensioni sono documentate come parte di questo set di documentazione.

Segui i link riportati qui sopra per consultare la documentazione dedicata per tutti i livelli di HTL utilizzati da AEM.
