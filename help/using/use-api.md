---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: Sono disponibili due API per HTL - Java Use-API e Javascript Use-API
seo-description: Sono disponibili due API per Adobe HTL - Java Use-API e Javascript Use-API
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: Utente
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTL Use-API {#htl-use-api}

La tabella seguente fornisce una panoramica dei pro e dei contro di ciascuna API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Pro** | <ul><li>più veloce</li><li>può essere controllato con un debugger</li><li>test di unità</li></ul> | <ul><li>può essere modificato da sviluppatori front-end</li><li>si trova all’interno del componente, mantenendo la logica di visualizzazione di un componente vicina al modello corrispondente</li></ul> |
| **Cons** | <ul><li>non può essere modificato da sviluppatori front-end</li></ul> | <ul><li>lento</li><li>nessun debugger (ancora)</li><li>più difficile da testare</li></ul> |


Per i componenti della pagina, si consiglia di utilizzare un modello misto, in cui tutte le logiche del modello si trovano in Java, fornendo API chiare che siano agnostiche a qualsiasi cosa accada nella vista (cioè all'interno dei componenti). AEM viene fornito con modelli predefiniti fantastici come Pagina o API delle risorse che dovrebbero essere in grado di coprire la maggior parte dei casi.

Tutte le logiche di visualizzazione specifiche per un componente devono essere inserite all’interno di tale componente come JavaScript, perché appartengono a tale componente.
