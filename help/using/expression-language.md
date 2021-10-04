---
title: Linguaggio di espressione HTL
description: HTML Template Language utilizza un linguaggio di espressione per accedere alle strutture di dati che forniscono gli elementi dinamici dell’output HTML.
exl-id: 57e3961b-8c84-4d56-a049-597c7b277448
source-git-commit: 89b9e89254f341e74f1a5a7b99735d2e69c8a91e
workflow-type: tm+mt
source-wordcount: '1852'
ht-degree: 99%

---

# Linguaggio di espressione HTL {#htl-expression-language}

HTML Template Language utilizza un linguaggio di espressione per accedere alle strutture di dati che forniscono gli elementi dinamici dell’output HTML. Queste espressioni sono delimitate dai caratteri `${` e `}`. Per evitare HTML con formato errato, le espressioni possono essere utilizzate solo nei valori di attributo, nel contenuto di un elemento o in commenti.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Le espressioni possono avere escape anteponendo un carattere `\`, ad esempio `\${test}` restituirà `${test}`.

>[!NOTE]
>
>Per provare gli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione live denominato [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl).

La sintassi delle espressioni include [variabili](#variables), [letterali](#literals), [operatori](#operators) e [opzioni](#options):

## Variabili {#variables}

Le variabili sono contenitori che memorizzano valori di dati o oggetti. I nomi delle variabili sono denominati identificatori.

Senza dover specificare nulla, HTL fornisce l’accesso a tutti gli oggetti comunemente disponibili in JSP dopo aver incluso `global.jsp`. La pagina [Oggetti globali](global-objects.md) fornisce l’elenco di tutti gli oggetti a cui è stato fornito l’accesso da HTL.

### Accesso alle proprietà {#property-access}

Esistono due modi per accedere alle proprietà delle variabili, con una notazione del punto o con una notazione della parentesi:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

La notazione del punto è più semplice e deve essere preferita per la maggior parte dei casi; la notazione della parentesi deve essere utilizzata per accedere a proprietà che contengono caratteri di identificazione non validi o per accedere a proprietà in modo dinamico. I due capitoli seguenti illustreranno in dettaglio questi due casi.

Le proprietà a cui si accede possono essere funzioni, ma gli argomenti di trasmissione non sono supportati, quindi è possibile accedere solo alle funzioni che non prevedono argomenti, come i getter. Si tratta di una limitazione voluta, che ha lo scopo di ridurre la quantità di logica incorporata nelle espressioni. Se necessario, l’istruzione [`data-sly-use`](block-statements.md#use) può essere utilizzata per trasmettere parametri alla logica.

Inoltre, nell’esempio precedente, è mostrato come accedere a funzioni getter Java, come `getTitle()`, senza anteporre il `get` e rendendo minuscolo il carattere che segue.

### Caratteri di identificazione validi {#valid-identifier-characters}

I nomi delle variabili, denominati identificatori, sono conformi a determinate regole. Devono iniziare con una lettera (`A`-`Z` e `a`-`z`) o un trattino basso (`_`) e i caratteri successivi possono anche essere cifre (`0`-`9`) o due punti (`:`). Gli identificatori non possono utilizzare lettere Unicode come `å` e `ü`.

Dato che il carattere due punti (`:`) è comune nei nomi AEM di proprietà, è opportuno sottolineare che si tratta di un carattere di identificazione valido:

`${properties.jcr:title}`

La notazione della parentesi può essere utilizzata per accedere a proprietà che contengono caratteri di identificazione non validi, come il carattere spazio nell’esempio seguente:

`${properties['my property']}`

### Accesso dinamico a membri {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Gestione permissiva di valori Null {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Letterali {#literals}

Un letterale è una notazione che rappresenta un valore fisso.

### Booleano {#boolean}

Il booleano rappresenta un’entità logica e può avere due valori: `true` e `false`.

`${true} ${false}`

### Numeri {#numbers}

Esiste un solo tipo di numero: numeri interi positivi. Gli altri formati di numeri, come il numero in virgola mobile, sono supportati nelle variabili, ma non possono essere espressi come letterali.

`${42}`

### Stringhe {#strings}

Le stringhe rappresentano dati testuali e possono essere tra virgolette singole o doppie:

`${'foo'} ${"bar"}`

Oltre ai caratteri comuni, è possibile utilizzare i seguenti caratteri speciali:

* `\\` Carattere barra rovesciata
* `\'` Virgoletta singola (o apostrofo)
* `\"` Virgolette doppie
* `\t` Tabulazione
* `\n` Nuova riga
* `\r` Ritorno a capo
* `\f` Avanzamento modulo
* `\b` Backspace
* `\uXXXX` Il carattere Unicode specificato dalle quattro cifre esadecimali XXXX.\
   Alcune utili sequenze di escape Unicode sono:

   * `\u0022` per `"`
   * `\u0027` per `'`

Per i caratteri non elencati sopra, prima di un carattere barra rovesciata viene visualizzato un errore.

Di seguito sono riportati alcuni esempi di utilizzo dell’escape di stringa:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

che determinerà il seguente output, in quanto HTL applicherà l’escape specifico per il contesto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrici {#arrays}

Una matrice è un insieme ordinato di valori a cui è possibile fare riferimento con un nome e un indice. I tipi dei suoi elementi possono essere combinati.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Le matrici sono utili per fornire un elenco di valori dal modello.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operatori {#operators}

### Operatori logici {#logical-operators}

Questi operatori vengono generalmente utilizzati con valori booleani, tuttavia, come in JavaScript, in realtà restituiscono il valore di uno degli operandi specificati; quindi, se utilizzati con valori non booleani, possono restituire un valore non booleano.

Se un valore può essere convertito in `true`, viene definito “truthy”. Se un valore può essere convertito in `false`, viene definito “falsy”. I valori che possono essere convertiti in `false` sono variabili non definite, valori Null, il numero zero e stringhe vuote.

#### NOT logico {#logical-not}

`${!myVar}` restituisce `false` se il relativo operando singolo può essere convertito in `true`; in caso contrario, restituisce `true`.

Questo può essere utilizzato, ad esempio, per invertire una condizione di test, come la visualizzazione di un elemento solo se non ci sono pagine figlie:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND logico {#logical-and}

`${varOne && varTwo}` restituisce `varOne` se è falsy; in caso contrario, restituisce `varTwo`.

Questo operatore può essere utilizzato per testare due condizioni contemporaneamente, come la verifica dell’esistenza di due proprietà:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

Anche l’operatore AND logico può essere utilizzato per visualizzare gli attributi HTML in modo condizionale, perché HTL rimuove gli attributi con valori impostati dinamicamente che restituiscono false o una stringa vuota. Nell’esempio seguente, l’attributo `class` viene visualizzato solo se `logic.showClass` è truthy e se `logic.className` esiste e non è vuoto:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR logico {#logical-or}

`${varOne || varTwo}` restituisce `varOne` se è truthy; in caso contrario, restituisce `varTwo`.

Questo operatore può essere utilizzato per testare se si applica una delle due condizioni, come verificare l’esistenza di almeno una proprietà:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Poiché l’operatore OR logico restituisce la prima variabile che è truthy, può essere opportunamente utilizzato anche per fornire valori di fallback.

Può essere utilizzato anche per visualizzare gli attributi HTML in modo condizionale perché HTL rimuove gli attributi con valori impostati da espressioni che restituiscono false o una stringa vuota. L’esempio seguente visualizzerà quindi il titolo **`properties.jcr:`** se esiste e non è vuoto, altrimenti tornerà a visualizzare **`properties.jcr:description`** se esiste e non è vuoto, altrimenti visualizzerà il messaggio “nessun titolo o descrizione fornita”:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operatore condizionale (ternario) {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` restituisce `varOne` se `varCondition` è truthy; altrimenti restituisce `varTwo`.

Questo operatore può in genere essere utilizzato per definire condizioni all’interno di espressioni, ad esempio per visualizzare un messaggio diverso in base allo stato della pagina:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Poiché gli identificatori consentono anche i caratteri due punti, è consigliabile separare gli operatori ternari con uno spazio bianco per fornire chiarezza al parser:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operatori di confronto {#comparison-operators}

Gli operatori di uguaglianza e disuguaglianza supportano solo operandi di tipi identici. Quando i tipi non corrispondono, viene visualizzato un errore.

* Le stringhe sono uguali quando hanno la stessa sequenza di caratteri.
* I numeri sono uguali quando hanno lo stesso valore
* I booleani sono uguali se entrambi sono `true` o se entrambi sono `false`.
* Le variabili Null o non definite sono uguali tra loro e l’una con l’altra.

`${varOne == varTwo}` restituisce `true` se `varOne` e `varTwo` sono uguali.

`${varOne != varTwo}` restituisce `true` se `varOne` e `varTwo` non sono uguali.

Gli operatori relazionali supportano solo operandi numerici. Per tutti gli altri tipi, viene visualizzato un errore.

`${varOne > varTwo}` restituisce `true` se `varOne` è maggiore di `varTwo`.

`${varOne < varTwo}` restituisce `true` se `varOne` è minore di `varTwo`.

`${varOne >= varTwo}` restituisce `true` se `varOne` è maggiore o uguale a `varTwo`.

`${varOne <= varTwo}` restituisce `true` se `varOne` è minore o uguale a `varTwo`.

### Raggruppamento con parentesi {#grouping-parentheses}

L’operatore di raggruppamento `()` controlla la precedenza della valutazione nelle espressioni.

`${varOne && (varTwo || varThree)}`

## Opzioni {#options}

Le opzioni di espressione possono agire sull’espressione e modificarla o fungere da parametri se utilizzate insieme alle istruzioni di blocco.

Tutto ciò che segue `@` è un’opzione:

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

Opzione che sostituisce i segnaposto elencati, {*n*}, con la variabile corrispondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## Manipolazione di URL {#url-manipulation}

È disponibile un nuovo set di manipolazioni di URL.

Vedi i seguenti esempi sul loro utilizzo:

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

Il `@extension` funziona in tutti gli scenari, verificando se aggiungere o meno l’estensione.

```xml
${ link @ extension = 'html' }
```

### Formattazione numero/data {#number-date-formatting}

HTL consente la formattazione nativa di numeri e date, senza scrivere un codice personalizzato. Supporta anche il fuso orario e la localizzazione.

Gli esempi seguenti mostrano che è specificato per primo il formato, quindi il valore da formattare:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Per informazioni complete sul formato utilizzabile, consulta l’articolo [Specifiche HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### Internazionalizzazione {#internationalization}

Traduce la stringa nella lingua del file di *origine* corrente (vedi sotto), utilizzando il [dizionario](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-translator.html) corrente. Se non viene trovata alcuna traduzione, viene utilizzata la stringa originale.

```xml
${'Page' @ i18n}
```

L’opzione Suggerimento può essere utilizzata per fornire un commento ai traduttori, specificando il contesto in cui viene utilizzato il testo:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

L’origine predefinita per la lingua è `resource`, il che significa che il testo viene tradotto nella stessa lingua del contenuto. Ciò può essere modificato in `user`, il che significa che la lingua viene presa dalle impostazioni locali del browser o dalle impostazioni locali dell’utente connesso:

```xml
${'Page' @ i18n, source='user'}
```

Fornendo la localizzazione, vengono sostituite le impostazioni di origine:

```xml
${'Page' @ i18n, locale='en-US'}
```

Per incorporare le variabili in una stringa tradotta, è possibile utilizzare l’opzione di formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Unione di matrice {#array-join}

Per impostazione predefinita, quando viene visualizzata una matrice come testo, HTL visualizza valori separati da virgola (senza spaziatura).

Utilizza l’opzione di unione per specificare un separatore diverso:

```xml
${['one', 'two'] @ join='; '}
```

### Contesto di visualizzazione {#display-context}

Il contesto di visualizzazione di un’espressione HTL si riferisce alla sua posizione all’interno della struttura della pagina HTML. Ad esempio, se l’espressione appare nella posizione in cui produrrebbe un nodo di testo una volta eseguito il rendering, si dice che si trovi in un contesto `text`. Se si trova all’interno del valore di un attributo, si dice che si trovi in un contesto `attribute` e così via.

Ad eccezione dei contesti di script (JS) e di stile (CSS), HTL rileverà automaticamente il contesto delle espressioni ed eseguirà l’escape appropriato, al fine di evitare problemi di sicurezza XSS. Nel caso di script e CSS, il comportamento contestuale desiderato deve essere impostato in modo esplicito. Inoltre, il comportamento contestuale può essere impostato esplicitamente in qualsiasi altro caso in cui si desideri superare il comportamento automatico.

Qui abbiamo tre variabili in tre contesti diversi:

* `properties.link` (contesto `uri` )
* `properties.title` (contesto `attribute`)
* `properties.text` (contesto `text`)

HTL eseguirà l’escape di ciascuna in modo diverso in base ai requisiti di sicurezza dei rispettivi contesti. Non è necessaria alcuna impostazione di contesto esplicita in casi normali come questo:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Per generare in modo sicuro il markup (ovvero, dove l’espressione stessa valuta l’HTML), viene utilizzato il contesto `html`:

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

È inoltre possibile disattivare la protezione escape e XSS:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Impostazioni di contesto {#context-settings}

| Contesto | Quando utilizzare | Effetto |
|--- |--- |--- |
| `text` | Predefinito per il contenuto all’interno di elementi | Codifica tutti i caratteri speciali HTML. |
| `html` | Per generare in modo sicuro il markup | Filtra HTML per rispettare le regole di policy AntiSamy, rimuovendo ciò che non corrisponde alle regole. |
| `attribute` | Predefinito per i valori di attributo | Codifica tutti i caratteri speciali HTML. |
| `uri` | Per visualizzare collegamenti e percorsi predefiniti per i valori di attributo href e src | Convalida l’URI per la scrittura come valore di attributo href o src, non produce output se la convalida non riesce. |
| `number` | Visualizzazione dei numeri | Convalida l’URI per contenere un numero intero, restituisce zero se la convalida non riesce. |
| `attributeName` | Predefinito per data-sly-attribute quando si impostano i nomi degli attributi | Convalida il nome dell’attributo, non produce output se la convalida non riesce. |
| `elementName` | Predefinito per data-sly-element | Convalida il nome dell’elemento, non produce output se la convalida non riesce. |
| `scriptToken` | Per identificatori JS, numeri letterali o stringhe letterali | Convalida il token JavaScript, non produce output se la convalida non riesce. |
| `scriptString` | All’interno di stringhe JS | Codifica i caratteri che fuoriescono dalla stringa. |
| `scriptComment` | All’interno di commenti JS | Convalida il commento JavaScript, non produce output se la convalida non riesce. |
| `styleToken` | Per identificatori CSS, numeri, dimensioni, stringhe, colori o funzioni esadecimali. | Convalida il token CSS, non produce output se la convalida non riesce. |
| `styleString` | All’interno di stringhe CSS | Codifica i caratteri che fuoriescono dalla stringa. |
| `styleComment` | All’interno di commenti CSS | Convalida il commento CSS, non produce output se la convalida non riesce. |
| `unsafe` | Solo se nessuno di questi funziona | Disabilita completamente la protezione escape e XSS. |
