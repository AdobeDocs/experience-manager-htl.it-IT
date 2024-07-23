---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.it-IT
index: y
recommendations: noDisplay
source-git-commit: 22f62868df0fcfc558e5d62434dde843a9f3ca83
workflow-type: tm+mt
source-wordcount: '81'
ht-degree: 40%

---


# Metadati per uso interno

Il sistema di authoring GitHub definisce i metadati in modo gerarchico, con livelli di precedenza crescenti come mostrato di seguito:

1. metadata.md
1. Sommario
1. Articolo

I metadati definiti nel file metadata.md si applicano all’intero archivio, ma possono essere ignorati a livello di sommario e di articolo. Eventuali esclusioni dei metadati devono essere eseguite al livello più basso possibile.

I metadati nell&#39;archivio `experience-manager-core-components.en` sono il minimo richiesto.

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

