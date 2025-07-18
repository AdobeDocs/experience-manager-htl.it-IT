---
title: Specifica HTL
description: Per informazioni dettagliate sulla sintassi, consulta la sezione Specifica HTL.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 62%

---


# Specifica HTL {#htl-specification}

HTML Template Language (HTL) è il sistema di modelli lato server preferito e consigliato per HTML.

## Livelli HTL {#layers}

È possibile definire HTL in AEM in base a un numero di livelli.

1. **[Specifiche HTL](https://github.com/adobe/htl-spec)**: HTL è una specifica open-source, indipendente dalla piattaforma, che chiunque può implementare. Le sue specifiche sono mantenute nel suo archivio GitHub.
1. **[Motore di script HTL Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - Il progetto `Sling` ha creato l&#39;implementazione di riferimento di HTL, utilizzata da AEM. Il progetto `Sling` mantiene la relativa documentazione.
1. **[Estensioni AEM](aem-extensions.md)** - AEM si basa sul motore di script HTL `Sling` per offrire agli sviluppatori funzionalità convenienti specifiche per AEM. Queste estensioni sono documentate come parte di questo set di documentazione.

Segui i link riportati qui sopra per consultare la documentazione dedicata per tutti i livelli di HTL utilizzati da AEM.
