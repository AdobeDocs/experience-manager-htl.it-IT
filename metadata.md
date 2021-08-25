---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://git.corp.adobe.com/AdobeDocs/experience-manager-htl.it-IT
index: y
source-git-commit: 5b88f6255534ef5af0958681c80303ab3da112b5
workflow-type: ht
source-wordcount: '107'
ht-degree: 100%

---


# Metadati per uso interno

I metadati nel sistema di authoring GitHub sono gerarchici, in base ai seguenti livelli crescenti di precedenza.

1. metadata.md
1. Sommario
1. Articolo

I metadati definiti nel file metadata.md si applicano all’intero archivio, ma possono essere ignorati a livello di sommario e articolo. Eventuali esclusioni dei metadati devono essere eseguite al livello più basso possibile.

I metadati nell’archivio experience-manager-core-components.en rappresentano il minimo richiesto.

metadata.md

* `product`
* `git-repo`
* `index: y`

Non più utilizzati:

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

Sommario

* `sub-product`
* `user-guide-title`

Articolo

* `title`
* `description`
* `index: n` (solo per le versioni precedenti dei componenti)

Ulteriori informazioni sui metadati sono disponibili nella [guida all’authoring interno.](https://docs.adobe.com/help/it/collaborative-doc-instructions/collaboration-guide/markdown/metadata.html#solution-metadata)
