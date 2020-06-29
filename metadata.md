---
product: Adobe Experience Manager
git-repo: https://git.corp.adobe.com/AdobeDocs/experience-manager-htl.it-IT
index: y
solution-title: Informazioni e supporto per HTL
solution-hub-url: https://docs.adobe.com/content/help/it-IT/experience-manager-cloud-service/sites/home.html
getting-started-title: Guida introduttiva allo sviluppo per AEM
getting-started-url: https://docs.adobe.com/content/help/it-IT/experience-manager-cloud-service/core-concepts/home.html
tutorials-title: Esercitazioni su AEM
tutorials-url: https://docs.adobe.com/content/help/en/experience-manager-learn/cloud-service/overview.html
translation-type: tm+mt
source-git-commit: d3426d87dce09ac34ff1aca431ff2bfad2f7134a
workflow-type: tm+mt
source-wordcount: '139'
ht-degree: 20%

---


# Metadati per uso interno

I metadati nel sistema di creazione GitHub sono gerarchici ed è definito dai seguenti livelli crescenti di precedenti.

1. metadata.md
1. ToC
1. Articolo

I metadati definiti nel file metadata.md si applicano all’intero repo, ma possono essere sostituiti a livello di AC e di articolo. L&#39;eventuale sostituzione dei metadati deve essere eseguita al livello più basso possibile.

I metadati contenuti nel repo experience-manager-core-components.en sono il minimo richiesto.

metadata.md

* `product`
* `git-repo`
* `index: y`
* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

Articolo

* `title`
* `description`
* `index: n` (solo per le versioni precedenti dei componenti)

Ulteriori informazioni sui metadati sono disponibili nella guida all’authoring [interna.](https://docs.adobe.com/help/en/collaborative-doc-instructions/collaboration-guide/markdown/metadata.html#solution-metadata)
