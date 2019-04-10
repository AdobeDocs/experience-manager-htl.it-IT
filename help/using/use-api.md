---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: Sono disponibili due API per HTL - Java Use-API e Javascript Use-API
seo-description: Sono disponibili due API per Adobe HTL - Java Use-API e Javascript
  Use-API
uuid: ab 44 aa 5 c-ce 7 e -40 b 9-97 fb-e 86 c 6 a 28405 c
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 89004426-eb 59-4 b 63-913 f -51 bf 98662773
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL Use-API {#htl-use-api}

La tabella seguente fornisce una panoramica dei professionisti e dei cons di ciascuna API.

|  | **Java Use-API** | **Javascript Use-API** |
|--- |--- |--- |
| **Professionisti** | <ul><li>più veloce</li><li>può essere verificato con un debugger</li><li>facile da utilizzare</li></ul> | <ul><li>può essere modificato dagli sviluppatori front-end</li><li>si trova all'interno del componente, mantenendo la logica di visualizzazione di un componente vicino al modello corrispondente</li></ul> |
| **Cons** | <ul><li>non può essere modificato da sviluppatori front-end</li></ul> | <ul><li>lente</li><li>nessun debugger (ancora)</li><li>harder to unit-test</li></ul> |


Per i componenti della pagina, si consiglia di utilizzare un modello misto, in cui la logica del modello si trova in Java, fornendo API chiare che sono agnostiche a qualsiasi cosa si verifichi nella vista (ovvero all'interno dei componenti). AEM viene fornito con modelli predefiniti come la Pagina o l'API Risorse che dovrebbero essere in grado di soddisfare la maggior parte dei casi.

All view logic that is specific to a component should be collocate in that component as javascript, because it belongs to that component.
