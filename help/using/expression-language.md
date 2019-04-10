---
title: Linguaggio espressioni HTL
seo-title: Linguaggio espressioni HTL
description: Il linguaggio HTML Template Language utilizza un linguaggio di espressioni
  per accedere alle strutture di dati che forniscono gli elementi dinamici dell'output
  HTML.
seo-description: Il linguaggio HTML Template Language utilizza un linguaggio di espressioni
  per accedere alle strutture di dati che forniscono gli elementi dinamici dell'output
  HTML.
uuid: 38 b 4 a 259-03 b 5-4847-91 c 6-e 20377600070
contentOwner: Utente
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: riferimento
discoiquuid: 9 ba 37 ca 0-f 318-48 b 0-a 791-a 944 a 72502 ed
mwpw-migration-script-version: 2017-10-12 T 21 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# Linguaggio espressioni HTL {#htl-expression-language}

Il linguaggio HTML Template Language utilizza un linguaggio di espressioni per accedere alle strutture di dati che forniscono gli elementi dinamici dell'output HTML. Queste espressioni sono delimitate da caratteri `${` e `}`. Per evitare il formato HTML non valido, le espressioni possono essere utilizzate solo nei valori degli attributi, nel contenuto dell'elemento o nei commenti.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Le espressioni possono essere precedute da un carattere di escape da parte di un **`\`** carattere, ad esempio **`\${test}`** renderizzare **`${test}`**.

>[!NOTE]
>
>Per provare gli esempi forniti in questa pagina, è possibile utilizzare un ambiente di esecuzione dal vivo denominato [Leggi ciclo](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) di stampa eval.

La sintassi delle espressioni include [variabili](#variables), [letterali](#literals), [operatori](#operators) e [opzioni](#options):

## Variabili {#variables}

Le variabili sono contenitori che memorizzano valori o oggetti di dati. I nomi delle variabili sono denominati identificatori.

Senza dover specificare nulla, HTL fornisce accesso a tutti gli oggetti che erano disponibili in JSP dopo l'inclusione `global.jsp`. La [pagina Oggetti](global-objects.md) globali fornisce l'elenco di tutti gli oggetti a cui è stato concesso l'accesso da parte di HTL.

### Accesso proprietà {#property-access}

Esistono due modi per accedere alle proprietà delle variabili, con notazione del punto, o con una notazione della parentesi:

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

Per la maggior parte dei casi, la notazione più semplice del punto deve essere preferibile e la notazione parentesi deve essere utilizzata per accedere alle proprietà contenenti caratteri identificatore non validi o per accedere in modo dinamico alle proprietà. I due capitoli seguenti forniscono dettagli su questi due casi.

Le proprietà a cui accedete possono essere funzioni, tuttavia il corso degli argomenti non è supportato, pertanto solo funzioni che non prevedono l'accesso agli argomenti, come i getter. Si tratta di una limitazione desiderata, che consente di ridurre la quantità di logica incorporata nelle espressioni. Se necessario, l' [`data-sly-use`](block-statements.md#use) istruzione può essere utilizzata per trasmettere parametri alla logica.

Nell'esempio riportato qui sopra, inoltre, è possibile utilizzare funzioni getter Java come `getTitle()`, ad esempio, accessibili senza l'anteprima e **`get`**ridurre il caso del carattere successivo.

### Caratteri di rientro validi {#valid-indentifier-characters}

I nomi delle variabili, denominati identificatori, sono conformi a determinate regole. Devono iniziare con una lettera (**`A`**-**`Z`** e **`a`**-)**`z`**, oppure un carattere di sottolineatura (**`_`**) e i caratteri successivi possono essere cifre (**`0`**-**`9`**) o due punti (**`:`**). Lettere Unicode, ad esempio **`å`** e **`ü`** non possono essere utilizzate in identificatori.

Dato che i caratteri due punti (**:**) sono comuni nei nomi delle proprietà AEM, è comodo che si tratti di un carattere di identificatore valido:

`${properties.jcr:title}`

La notazione delle parentesi quadre può essere utilizzata per accedere alle proprietà contenenti caratteri identificatori non validi, come il carattere spazio nell'esempio seguente:

`${properties['my property']}`

### Accesso ai membri in modo dinamico {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Gestione permissiva dei valori null {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Letterali {#literals}

Un letterale è una notazione che rappresenta un valore fisso.

### Booleano {#boolean}

Booleano rappresenta un'entità logica e può avere due valori: **`true`**e **`false`**.

`${true} ${false}`

### Numeri {#numbers}

Esiste un solo tipo di numero: numeri interi positivi. Mentre altri formati numerici, come a virgola mobile, sono supportati nelle variabili, ma non possono essere espressi come letterali.

`${42}`

### Stringhe {#strings}

Rappresentano dati testuali e possono essere singoli o doppi:

`${'foo'} ${"bar"}`

Oltre ai caratteri standard, possono essere utilizzati caratteri speciali:

* **`\\`** Carattere backslash
* **`\'`** Virgolette singole (o apostrofo)
* **`\"`** Virgolette doppie
* **`\t`** Tabulazione
* **`\n`** Nuova riga
* **`\r`** Ritorno a capo
* **`\f`** Feed modulo
* **`\b`** Backspace
* `\uXXXX` Il carattere Unicode specificato dalle quattro cifre esadecimali XXXX.\
   Alcune utili sequenze di escape Unicode sono:

   * **\ u 0022** for **"**
   * **\ u 0027** for **'**

Per i caratteri non elencati sopra, prima di un caracter di barra viene visualizzato un errore.

Di seguito sono riportati alcuni esempi di come utilizzare la stringa di stringa:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

che darà luogo a un output successivo, perché HTL applica la escape specifica del contesto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Array {#arrays}

Un array è un insieme ordinato di valori a cui è possibile fare riferimento con un nome e un indice. I tipi di elementi possono essere misti.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

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

Questi operatori vengono generalmente utilizzati con valori booleani, come in javascript, restituiscono in realtà il valore di uno degli operandi specificati, quindi, se utilizzati con valori non booleani, restituiscono un valore non booleano.

Se un valore può essere convertito, **`true`**il valore è denominato truthy. Se un valore può essere convertito, **`false`**il valore è denominato falsy. I valori che possono essere convertiti in **`false`** sono: variabili non definite, valori null, numero zero e stringhe vuote.

#### NOT logico {#logical-not}

**`${!myVar}`** restituisce **`false`** se il relativo operando può essere convertito in `true`; in caso contrario **`true`**, restituisce.

Questo può essere utilizzato per invertire una condizione di prova, come la visualizzazione di un elemento solo se non sono presenti pagine figlie:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Operatore logico AND {#logical-and}

**`${varOne && varTwo}`** restituisce `varOne` se è false; in caso contrario restituisce **vartwo**.

Questo operatore può essere utilizzato per sottoporre a test due condizioni alla volta, come per verificare l'esistenza di due proprietà:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

L'operatore logico AND può essere utilizzato anche per visualizzare in modo condizionato gli attributi HTML, poiché HTL rimuove gli attributi con valori impostati dinamicamente che corrispondono a false o a una stringa vuota. Quindi, nell'esempio seguente, l' **`class`** attributo viene mostrato solo se **`logic.showClass`** è truthy e se **`logic.className`** esiste e non è vuoto:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Operatore logico OR {#logical-or}

**`${varOne || varTwo}`** restituisce **varone** se è trula; in caso contrario restituisce **vartwo**.

Questo operatore può essere utilizzato per eseguire il test se si applica una delle due condizioni, ad esempio la verifica dell'esistenza di almeno una proprietà:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Poiché l'operatore OR logico restituisce la prima variabile che è trula, può anche essere utile per fornire valori di fallback.

Visualizzate in modo condizionato gli attributi HTML, perché HTL rimuove gli attributi con valori impostati da espressioni che corrispondono a false o a una stringa vuota. Di conseguenza, l'esempio seguente visualizza il`properties.jcr:`titolo**** se esiste e non è vuoto. Se esiste e non **`properties.jcr:description`** è vuoto, in caso contrario verrà visualizzato il messaggio "no title or description provided":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operatore condizionale (ternario) {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** restituisce **`varOne`** se **`varCondition`** è trula; in caso contrario **`varTwo`**restituisce.

Questo operatore può essere utilizzato in genere per definire condizioni all'interno delle espressioni, come visualizzare un messaggio diverso in base allo stato della pagina:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Una nota importante, poiché i caratteri due punti sono consentiti anche in identificatori, è consigliabile separare gli operatori ternario con uno spazio vuoto per fornire chiarezza al parser:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operatori di confronto {#comparison-operators}

Gli operatori di uguaglianza e disuguaglianza supportano solo operandi di tipi identici. Quando i tipi non corrispondono, viene visualizzato un errore.

* Le stringhe sono uguali se hanno la stessa sequenza di caratteri.
* I numeri sono uguali se hanno lo stesso valore
* I booleani sono uguali se **`true`****`false`**sono entrambi o entrambi.

* Le variabili null o non definite sono uguali e uguali.

**`${varOne == varTwo}`** restituisce **`true`** se **`varOne`** e **`varTwo`** sono uguali.

**`${varOne != varTwo}`** restituisce **`true`** se **`varOne`** e **`varTwo`** non sono uguali.

Gli operatori relazionali supportano solo operandi che sono numeri. Per tutti gli altri tipi viene visualizzato un errore.

**`${varOne > varTwo}`** restituisce **`true`** se **`varOne`** è maggiore **`varTwo`**di.

**`${varOne < varTwo}`** restituisce **`true`** se **`varOne`** è minore **`varTwo`**di.

**`${varOne >= varTwo}`** restituisce **`true`** se **`varOne`** è maggiore o uguale **`varTwo`**a.

**`${varOne <= varTwo}`** restituisce **`true`** se **`varOne`** è minore o uguale **`varTwo`**a.

### Parentesi raggruppamento {#grouping-parentheses}

L'operatore di raggruppamento **`(`****`)`** controlla la precedenza della valutazione nelle espressioni.

`${varOne && (varTwo || varThree)}`

## Opzioni {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Le opzioni di espressione possono agire sull'espressione e modificarla, oppure fungere da parametri se utilizzata insieme alle istruzioni di blocco.

Tutto ciò che segue l' **`@`** è un'opzione:

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

Diverse opzioni sono separate da virgole:

```xml
${myVar @ optOne, optTwo=bar}
```

Sono inoltre possibili espressioni parametriche contenenti opzioni:

```xml
${@ optOne, optTwo=bar}
```

### Formattazione stringa {#string-formatting}

Opzione che sostituisce i segnaposto enumerati, {*n*}, con la variabile corrispondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internazionalizzazione {#internationalization}

Converte la stringa nella lingua dell' *origine corrente* (vedere di seguito) utilizzando il [dizionario corrente](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator). Se non viene trovata alcuna conversione, viene utilizzata la stringa originale.

```xml
${'Page' @ i18n}
```

L'opzione di hint può essere utilizzata per fornire un commento ai traduttori, specificando il contesto in cui viene utilizzato il testo:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

L'origine predefinita per la lingua è'resourcè, il che significa che il testo viene convertito nella stessa lingua del contenuto. Questo può essere modificato in'user ', ovvero la lingua viene ripresa dalle impostazioni internazionali del browser o dalle impostazioni internazionali dell'utente connesso:

```xml
${'Page' @ i18n, source='user'}
```

L'impostazione di un'impostazione internazionale esplicita sostituisce le impostazioni dell'origine:

```xml
${'Page' @ i18n, locale='en-US'}
```

Per incorporare variabili in una stringa convertita, è possibile utilizzare l'opzione di formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Unione array {#array-join}

Per impostazione predefinita, quando si visualizza un array come testo, HTL visualizza valori separati da virgola (senza spaziatura).

Utilizzate l'opzione di partecipazione per specificare un separatore diverso:

```xml
${['one', 'two'] @ join='; '}
```

### Visualizza contesto {#display-context}

Il contesto di visualizzazione di un'espressione HTL fa riferimento alla posizione all'interno della struttura della pagina HTML. Ad esempio, se viene visualizzata l'espressione che genera un nodo di testo una volta eseguito il rendering, si dice che si trova in **`text`** un contesto. Se viene trovato all'interno del valore di un attributo, si dice che si trova in **`attribute`** un contesto e così via.

Ad eccezione dei contesti di script (JS) e di stile (CSS), HTL rileva automaticamente il contesto delle espressioni e li escape in modo appropriato, per evitare problemi di sicurezza XSS. Nel caso di script e CSS, il comportamento contestuale desiderato deve essere impostato in modo esplicito. Inoltre, il comportamento contestuale può essere impostato in modo esplicito in qualsiasi altro caso in cui si desidera ignorare il comportamento automatico.

Qui sono disponibili tre variabili in tre contesti diversi: **`properties.link`** `uri` (context), **`properties.title`** (**`attribute`** context) e **`properties.text`**(**`text`** context). HTL ne eseguirà il escape in modo diverso in base ai requisiti di protezione dei rispettivi contesti. Non è richiesta alcuna impostazione di contesto esplicita in casi normali come:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Per una marcatura di output sicura (cioè dove l'espressione stessa valuta HTML), viene `html` utilizzato il contesto:

```xml
<div>${properties.richText @ context='html'}</div>
```

Contesto esplicito deve essere impostato per contesti di stile:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Contesto esplicito deve essere impostato per contesti di script:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

Anche la navigazione e la protezione XSS possono essere disattivate:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Impostazioni contesto {#context-settings}

| Contesto | Quando usare | Azione |
|--- |--- |--- |
| testo | Impostazione predefinita per il contenuto all'interno degli elementi | Codifica tutti i caratteri speciali HTML. |
| html | Marcatura di output sicura | Applica un filtro HTML per soddisfare le regole di criteri antisamy, rimuovendo quelli che non corrispondono alle regole. |
| attribute | Impostazione predefinita per i valori degli attributi | Codifica tutti i caratteri speciali HTML. |
| uri | Per visualizzare collegamenti e percorsi predefiniti per i valori href e src | Convalida l'URI per la scrittura come valore href o src, restituisce nulla se la convalida non riesce. |
| numero | Per visualizzare i numeri | Convalida l'URI per la raccolta di un numero intero, restituisce zero se la convalida non riesce. |
| Attributename | Default for data-sly-attribute when setting attribute names | Convalida il nome dell'attributo, restituisce nulla se la convalida non riesce. |
| Elementname | Default for data-sly element | Convalida il nome dell'elemento, restituisce nulla se la convalida non riesce. |
| Scripttoken | Per identificatori JS, numeri letterali o stringhe letterali | Convalida il token javascript, restituisce nulla se la convalida non riesce. |
| Scriptstring | All'interno delle stringhe JS | Codifica i caratteri che si scompongono dalla stringa. |
| Scriptcomment | All'interno dei commenti JS | Convalida il commento javascript, restituisce nulla se la convalida non riesce. |
| Styletoken | Per identificatori CSS, numeri, dimensioni, stringhe, colori esadecimali o funzioni. | Convalida il token CSS, restituisce nulla se la convalida non riesce. |
| Stylestring | All'interno delle stringhe CSS | Codifica i caratteri che si scompongono dalla stringa. |
| Stylecomment | All'interno dei commenti CSS | Convalida il commento CSS, restituisce nulla se la convalida non riesce. |
| non sicuro | Solo se il processo è nessuno di questi | Disattiva completamente la navigazione e la protezione XSS. |

