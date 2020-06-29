---
title: Linguaggio delle espressioni HTL
description: Il linguaggio HTML Template Language utilizza un linguaggio di espressione per accedere alle strutture di dati che forniscono gli elementi dinamici dell'output HTML.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '1848'
ht-degree: 1%

---


# Linguaggio delle espressioni HTL {#htl-expression-language}

Il linguaggio HTML Template Language utilizza un linguaggio di espressione per accedere alle strutture di dati che forniscono gli elementi dinamici dell&#39;output HTML. Queste espressioni sono delimitate da caratteri `${` e `}`. Per evitare il formato HTML non corretto, le espressioni possono essere utilizzate solo nei valori di attributo, nel contenuto di elementi o nei commenti.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Le espressioni possono essere precedute da un `\` carattere, ad esempio `\${test}` verrà eseguito il rendering `${test}`.

>[!NOTE]
>
>Per provare gli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione live denominato [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) .

La sintassi delle espressioni include [variabili](#variables), [letterali](#literals), [operatori](#operators) e [opzioni](#options):

## Variabili {#variables}

Le variabili sono contenitori in cui sono memorizzati i valori o gli oggetti dati. I nomi delle variabili sono denominati identificatori.

Senza dover specificare nulla, HTL fornisce l&#39;accesso a tutti gli oggetti che erano comunemente disponibili in JSP dopo l&#39;inclusione `global.jsp`. La pagina Oggetti [](global-objects.md) globali contiene l&#39;elenco di tutti gli oggetti a cui è possibile accedere mediante HTL.

### Accesso proprietà {#property-access}

Esistono due modi per accedere alle proprietà delle variabili, con una notazione del punto o con una notazione parentesi quadre:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

La notazione del punto più semplice dovrebbe essere preferita per la maggior parte dei casi e la notazione tra parentesi deve essere utilizzata per accedere alle proprietà che contengono caratteri di identificazione non validi o per accedere alle proprietà in modo dinamico. I due capitoli seguenti illustreranno in dettaglio questi due casi.

Le proprietà a cui si accede possono essere funzioni, ma gli argomenti di passaggio non sono supportati, quindi è possibile accedere solo alle funzioni che non prevedono l&#39;utilizzo di argomenti, come getters. Questa è una limitazione desiderata, che ha lo scopo di ridurre la quantità di logica incorporata nelle espressioni. Se necessario, l&#39; [`data-sly-use`](block-statements.md#use) istruzione può essere utilizzata per trasmettere i parametri alla logica.

Anche nell&#39;esempio precedente è possibile accedere a funzioni Java getter come `getTitle()`, senza anteporre il carattere `get`e riducendo le maiuscole/minuscole del carattere che segue.

### Caratteri di identificatore validi {#valid-identifier-characters}

I nomi delle variabili, denominati identificatori, sono conformi a determinate regole. Devono iniziare con una lettera (`A`-`Z` e `a`-`z`), un carattere di sottolineatura (`_`) e i caratteri successivi possono anche essere cifre (`0`-`9`) o due punti (`:`). Le lettere Unicode, ad esempio `å` e `ü` non possono essere utilizzate negli identificatori.

Dato che il carattere due punti (`:`) è comune nei nomi delle proprietà di AEM, è opportuno sottolineare che si tratta di un carattere identificatore valido:

`${properties.jcr:title}`

La notazione tra parentesi può essere utilizzata per accedere a proprietà che contengono caratteri di identificazione non validi, come il carattere spazio nell&#39;esempio seguente:

`${properties['my property']}`

### Accesso dinamico ai membri {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Gestione permissiva dei valori Null {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Letterali {#literals}

Un letterale è una notazione che rappresenta un valore fisso.

### Booleano {#boolean}

Il valore booleano rappresenta un&#39;entità logica e può avere due valori: `true`e `false`.

`${true} ${false}`

### Numeri {#numbers}

Esiste un solo tipo di numero: numeri interi positivi. Mentre altri formati numerici, come il virgola mobile, sono supportati nelle variabili, ma non possono essere espressi come valori letterali.

`${42}`

### Stringhe {#strings}

Le stringhe rappresentano dati testuali e possono essere singole o doppie tra virgolette:

`${'foo'} ${"bar"}`

Oltre ai caratteri comuni, è possibile utilizzare i seguenti caratteri speciali:

* `\\` Carattere barra rovesciata
* `\'` Virgoletta singola (o apostrofo)
* `\"` Virgolette doppie
* `\t` Tab
* `\n` Nuova riga
* `\r` Ritorno a capo
* `\f` Avanzamento modulo
* `\b` Indietro
* `\uXXXX` Il carattere Unicode specificato dalle quattro cifre esadecimali XXXX.\
   Alcune utili sequenze di escape Unicode sono:

   * `\u0022` per `"`
   * `\u0027` per `'`

Per i caratteri non elencati sopra, prima di un carattere barra rovesciata viene visualizzato un errore.

Di seguito sono riportati alcuni esempi di utilizzo dell&#39;escape di stringhe:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

che darà come risultato il seguente output, perché HTL applicherà la escape specifica per il contesto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Array {#arrays}

Un array è un insieme ordinato di valori cui è possibile fare riferimento con un nome e un indice. I tipi dei suoi elementi possono essere mescolati.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Gli array sono utili per fornire un elenco di valori dal modello.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operatori {#operators}

### Operatori logici {#logical-operators}

Questi operatori vengono generalmente utilizzati con valori booleani, tuttavia, come in JavaScript, restituiscono effettivamente il valore di uno degli operandi specificati, pertanto, se utilizzati con valori non booleani, possono restituire un valore non booleano.

Se un valore può essere convertito in `true`, il valore è denominato &quot;true&quot;. Se un valore può essere convertito in `false`, il valore è denominato false. I valori ai quali è possibile convertire `false` sono variabili non definite, valori null, numero zero e stringhe vuote.

#### NOT logico {#logical-not}

`${!myVar}` restituisce `false` se il suo singolo operando può essere convertito in `true`; altrimenti restituisce `true`.

Questo può essere utilizzato ad esempio per invertire una condizione di test, come visualizzare un elemento solo se non ci sono pagine figlie:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Operatore logico AND {#logical-and}

`${varOne && varTwo}` restituisce `varOne` se è falso; altrimenti restituisce `varTwo`.

Questo operatore può essere utilizzato per testare due condizioni contemporaneamente, come per verificare l&#39;esistenza di due proprietà:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

L&#39;operatore AND logico può essere utilizzato anche per visualizzare gli attributi HTML in modo condizionale, perché HTL rimuove gli attributi con valori impostati in modo dinamico che corrispondono a false o a una stringa vuota. Nell&#39;esempio seguente, l&#39; `class` attributo è mostrato solo se `logic.showClass` è vero e se `logic.className` esiste e non è vuoto:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Operatore logico OR {#logical-or}

`${varOne || varTwo}` restituisce `varOne` se è vero; altrimenti restituisce `varTwo`.

Questo operatore può essere utilizzato per verificare se si applica una delle due condizioni, ad esempio per verificare l&#39;esistenza di almeno una proprietà:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Poiché l&#39;operatore OR logico restituisce la prima variabile che è vera, può essere utilizzato anche per fornire valori di fallback.

vengono visualizzati in modo condizionale gli attributi HTML, in quanto HTL rimuove gli attributi con valori impostati da espressioni che corrispondono a false o a una stringa vuota. L&#39;esempio seguente mostra **`properties.jcr:`** il titolo, se esiste e non è vuoto, altrimenti torna a essere visualizzato **`properties.jcr:description`** se esiste e non è vuoto, altrimenti viene visualizzato il messaggio &quot;nessun titolo o descrizione fornito&quot;:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operatore condizionale (ternario) {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` restituisce `varOne` se `varCondition` è veritiero; altrimenti restituisce `varTwo`.

Questo operatore può in genere essere utilizzato per definire le condizioni all&#39;interno delle espressioni, ad esempio per visualizzare un messaggio diverso in base allo stato della pagina:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Poiché i caratteri due punti sono consentiti anche negli identificatori, è consigliabile separare gli operatori ternari con uno spazio bianco per fornire chiarezza al parser:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operatori di confronto {#comparison-operators}

Gli operatori di uguaglianza e disuguaglianza supportano solo operandi di tipi identici. Se i tipi non corrispondono, viene visualizzato un errore.

* Le stringhe sono uguali quando hanno la stessa sequenza di caratteri.
* I numeri sono uguali quando hanno lo stesso valore
* I booleani sono uguali se entrambi sono `true` o entrambi lo sono `false`.
* Le variabili Null o undefined sono uguali a se stesse e tra loro.

`${varOne == varTwo}` restituisce `true` if `varOne` e `varTwo` sono uguali.

`${varOne != varTwo}` restituisce `true` if `varOne` e `varTwo` non sono uguali.

Gli operatori relazionali supportano solo operandi che sono numeri. Per tutti gli altri tipi, viene visualizzato un errore.

`${varOne > varTwo}` restituisce `true` se `varOne` è maggiore di `varTwo`.

`${varOne < varTwo}` restituisce `true` se `varOne` è minore di `varTwo`.

`${varOne >= varTwo}` restituisce `true` se `varOne` è maggiore o uguale a `varTwo`.

`${varOne <= varTwo}` restituisce `true` se `varOne` è minore o uguale a `varTwo`.

### Raggruppamento parentesi {#grouping-parentheses}

L&#39;operatore di raggruppamento `()` controlla la precedenza della valutazione nelle espressioni.

`${varOne && (varTwo || varThree)}`

## Opzioni {#options}

Le opzioni di espressione possono agire sull&#39;espressione e modificarla, oppure fungere da parametri se utilizzate insieme alle istruzioni di blocco.

Tutto dopo `@` è un&#39;opzione:

```xml
${myVar @ optOne}
```

Le opzioni possono avere un valore, che può essere una variabile o un letterale:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Più opzioni sono separate da virgole:

```xml
${myVar @ optOne, optTwo=bar}
```

Sono inoltre possibili espressioni parametriche contenenti solo opzioni:

```xml
${@ optOne, optTwo=bar}
```

### Formattazione stringa {#string-formatting}

Opzione che sostituisce i segnaposto enumerati, {*n*}, con la variabile corrispondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## Modifica URL {#url-manipulation}

È disponibile un nuovo set di manipolazioni URL.

Consultate i seguenti esempi di utilizzo:

Aggiunge l’estensione html a un percorso.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Aggiunge l’estensione html e un selettore a un percorso.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Aggiunge l’estensione html e un frammento (#value) a un percorso.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

Il `@extension` funzionamento avviene in tutti gli scenari, verificando se aggiungere o meno l’estensione.

```xml
${ link @ extension = 'html' }
```

### Formattazione numero/data {#number-date-formatting}

HTL consente la formattazione nativa di numeri e date, senza scrivere codice personalizzato. Supporta inoltre il fuso orario e le impostazioni internazionali.

Gli esempi seguenti mostrano che il formato è specificato per primo, quindi il valore che deve essere formattato:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Per informazioni complete sul formato che potete utilizzare, consultate la specifica [](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)HTL.

### Internazionalizzazione {#internationalization}

Traduce la stringa nella lingua dell&#39; *origine* corrente (vedere di seguito), utilizzando il [dizionario](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html)corrente. Se non viene trovata alcuna traduzione, viene utilizzata la stringa originale.

```xml
${'Page' @ i18n}
```

L&#39;opzione hint può essere utilizzata per fornire un commento ai traduttori, specificando il contesto in cui viene utilizzato il testo:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

L&#39;origine predefinita per la lingua è `resource`, ovvero il testo viene tradotto nella stessa lingua del contenuto. Questo può essere modificato in `user`, ovvero la lingua viene presa dalle impostazioni internazionali del browser o dall&#39;utente connesso:

```xml
${'Page' @ i18n, source='user'}
```

L&#39;impostazione internazionale esplicita sostituisce le impostazioni di origine:

```xml
${'Page' @ i18n, locale='en-US'}
```

Per incorporare le variabili in una stringa convertita, è possibile utilizzare l&#39;opzione di formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Join array {#array-join}

Per impostazione predefinita, quando viene visualizzata una matrice come testo, HTL visualizza valori separati da virgola (senza spaziatura).

Utilizzate l&#39;opzione join per specificare un separatore diverso:

```xml
${['one', 'two'] @ join='; '}
```

### Contesto di visualizzazione {#display-context}

Il contesto di visualizzazione di un&#39;espressione HTL si riferisce alla sua posizione all&#39;interno della struttura della pagina HTML. Ad esempio, se l&#39;espressione appare nella stessa posizione che produrrebbe un nodo di testo una volta eseguito il rendering, si dice che sia in un `text` contesto. Se si trova all&#39;interno del valore di un attributo, si dice che sia in un `attribute` contesto, e così via.

Ad eccezione dei contesti di script (JS) e di stile (CSS), HTL rileva automaticamente il contesto delle espressioni e le visualizza come escape appropriate, per evitare problemi di protezione XSS. Nel caso di script e CSS, il comportamento contestuale desiderato deve essere impostato in modo esplicito. Inoltre, il comportamento contestuale può essere impostato esplicitamente in qualsiasi altro caso in cui sia richiesta l’esclusione del comportamento automatico.

Qui abbiamo tre variabili in tre contesti diversi:

* `properties.link` ( `uri` contesto)
* `properties.title` (`attribute` contesto)
* `properties.text` (`text` contesto)

HTL si sottrarrà a ognuno di questi in modo diverso in base ai requisiti di sicurezza dei rispettivi contesti. Non è richiesta alcuna impostazione di contesto esplicita in casi normali come questo:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Per ottenere tag di output sicuri (ovvero, dove l&#39;espressione stessa valuta HTML), viene utilizzato il `html` contesto:

```xml
<div>${properties.richText @ context='html'}</div>
```

Il contesto esplicito deve essere impostato per i contesti di stile:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Il contesto esplicito deve essere impostato per i contesti di script:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

La protezione Escape e XSS può essere disattivata:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Impostazioni contesto {#context-settings}

| Contesto | Quando utilizzare | Azioni |
|--- |--- |--- |
| `text` | Impostazione predefinita per il contenuto all’interno degli elementi | Codifica tutti i caratteri speciali HTML. |
| `html` | Per una marcatura di output sicura | Filtra HTML per rispettare le regole dei criteri di anti-Samy, rimuovendo ciò che non corrisponde alle regole. |
| `attribute` | Valore predefinito per i valori degli attributi | Codifica tutti i caratteri speciali HTML. |
| `uri` | Per visualizzare collegamenti e percorsi Predefiniti per i valori degli attributi href e src | Convalida l&#39;URI per la scrittura come valore attributo href o src. Se la convalida non riesce, non viene generato alcun risultato. |
| `number` | Per visualizzare i numeri | Convalida l&#39;URI per il contenitore di un numero intero, restituisce zero se la convalida non riesce. |
| `attributeName` | Impostazione predefinita per gli attributi di tipo data-side quando si impostano i nomi degli attributi | Convalida il nome dell&#39;attributo, non genera nulla se la convalida non riesce. |
| `elementName` | Impostazione predefinita per l’elemento data-sly | Convalida il nome dell&#39;elemento, non genera nulla se la convalida non riesce. |
| `scriptToken` | Per identificatori JS, numeri letterali o stringhe letterali | Convalida il token JavaScript, non genera nulla se la convalida non riesce. |
| `scriptString` | Entro le stringhe JS | Codifica i caratteri che verrebbero eliminati dalla stringa. |
| `scriptComment` | Commenti di JS | Convalida il commento JavaScript, non genera nulla se la convalida non riesce. |
| `styleToken` | Per identificatori CSS, numeri, dimensioni, stringhe, colori esadecimali o funzioni. | Convalida il token CSS, non genera nulla se la convalida non riesce. |
| `styleString` | Entro le stringhe CSS | Codifica i caratteri che verrebbero eliminati dalla stringa. |
| `styleComment` | Commenti CSS | Convalida il commento CSS, non genera nulla se la convalida non riesce. |
| `unsafe` | Solo se nessuno di questi ha il compito | Consente di disabilitare completamente la escape e la protezione XSS. |
