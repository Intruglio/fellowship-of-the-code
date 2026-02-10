---
name: explain-like-im-new
description: Spiega concetti WordPress/WooCommerce in modo semplice e graduale, adatto a principianti
---

# Comando: Explain Like I'm New

## Scopo
Spiega concetti WordPress, WooCommerce o codice esistente in modo chiaro, graduale e accessibile anche a sviluppatori junior o principianti.

## Utilizzo
```bash
/explain-like-im-new $ARGUMENTS
```

**Esempi:**
- `/explain-like-im-new cos'è il hook system di WordPress`
- `/explain-like-im-new come funziona il carrello WooCommerce`
- `/explain-like-im-new spiega questo codice: [snippet]`
- `/explain-like-im-new differenza tra action e filter`
- `/explain-like-im-new template hierarchy WordPress`

## Workflow

### 1. Identifica Argomento
Analizza `$ARGUMENTS` per determinare:
- È un concetto generale? (es: hook system)
- È codice specifico da spiegare?
- È un confronto? (es: action vs filter)
- È un processo? (es: checkout flow)

### 2. Valuta Livello
Assumi livello principiante a meno che specificato:
- Conoscenza HTML/CSS/JS base
- Poca esperienza PHP
- Familiarità limitata con WordPress
- Nessuna esperienza WooCommerce

### 3. Struttura Spiegazione

#### A. Concetto in Parole Semplici
Inizia con definizione comprensibile in 1-2 frasi:
```markdown
## Cos'è [Concetto]?

Gli hook di WordPress sono come "ganci" nel codice dove puoi
"appendere" le tue funzioni custom per modificare o aggiungere
funzionalità senza toccare i file core.
```

#### B. Analogia del Mondo Reale
Usa paragone familiare:
```markdown
## Come un'Analogia

Immagina WordPress come una catena di montaggio in una fabbrica.
Gli hook sono stazioni lungo la catena dove puoi:
- Aggiungere un componente (action)
- Modificare un componente esistente (filter)

Non devi ricostruire l'intera fabbrica, solo intervenire nei
punti prestabiliti.
```

#### C. Come Funziona (Tecnico Semplice)
Spiega meccanica con terminologia accessibile:
```markdown
## Come Funziona

WordPress esegue codice in sequenza. In punti specifici,
"chiama" gli hook dicendo "esegui tutte le funzioni registrate qui".

1. WordPress arriva al punto X nel codice
2. Controlla se ci sono funzioni "agganciate" a quel punto
3. Esegue le funzioni in ordine di priorità
4. Continua con il proprio codice
```

#### D. Esempio Pratico Commentato
Codice funzionante con spiegazioni riga per riga:
```markdown
## Esempio Pratico

Aggiungiamo testo alla fine di ogni post:

```php
// 1. Definiamo la nostra funzione custom
function aggiungi_testo_fine_post($content) {
    // $content contiene il testo del post originale

    // 2. Aggiungiamo il nostro testo
    $testo_extra = '<p>Grazie per aver letto!</p>';

    // 3. Restituiamo contenuto originale + nostro testo
    return $content . $testo_extra;
}

// 4. "Agganciamo" la nostra funzione al filtro 'the_content'
// Questo filtro viene eseguito quando WordPress mostra il contenuto di un post
add_filter('the_content', 'aggiungi_testo_fine_post');

// Priorità 10 (default): esegue dopo altre funzioni con priorità < 10
// Parametri 1 (default): la nostra funzione accetta 1 parametro ($content)
```

**Cosa succede:**
1. Quando qualcuno visualizza un post
2. WordPress esegue `the_content` filter
3. Chiama la nostra funzione `aggiungi_testo_fine_post`
4. Nostra funzione riceve il contenuto originale
5. Aggiunge il testo extra
6. Restituisce il contenuto modificato
7. WordPress mostra il risultato finale
```

#### E. Variazioni Comuni
Mostra casi d'uso simili:
```markdown
## Altri Esempi Simili

### Aggiungere classe CSS al body
```php
function custom_body_class($classes) {
    $classes[] = 'mia-classe-custom';
    return $classes;
}
add_filter('body_class', 'custom_body_class');
```

### Modificare titolo post
```php
function modifica_titolo($title) {
    return strtoupper($title); // Tutto maiuscolo
}
add_filter('the_title', 'modifica_titolo');
```
```

#### F. Errori Comuni da Evitare
Evidenzia pitfall tipici:
```markdown
## ⚠️ Errori Comuni

### ❌ SBAGLIATO: Dimenticare return nel filter
```php
function modifica_prezzo($price) {
    $price = $price * 1.10;
    // Manca return! Il prezzo non verrà modificato
}
add_filter('woocommerce_product_price', 'modifica_prezzo');
```

### ✅ CORRETTO: Sempre return nei filter
```php
function modifica_prezzo($price) {
    $price = $price * 1.10;
    return $price; // ✓ Return il valore modificato
}
add_filter('woocommerce_product_price', 'modifica_prezzo');
```

### ❌ SBAGLIATO: Nome funzione generico (conflitto)
```php
function add_text($content) { // Nome troppo generico!
    return $content;
}
```

### ✅ CORRETTO: Prefisso unico
```php
function miosito_add_text($content) { // Prefisso 'miosito_'
    return $content;
}
```
```

#### G. Quando Usarlo
Guida pratica su applicazioni:
```markdown
## Quando Usare [Concetto]

✅ **Usa quando:**
- Vuoi modificare comportamento WordPress senza toccare core
- Vuoi aggiungere funzionalità a plugin/theme esistente
- Vuoi intervenire in specifici momenti del ciclo WordPress

❌ **Non usare quando:**
- Puoi ottenere lo stesso risultato con impostazioni native WP
- Esiste un plugin che fa già quello che serve
- Stai facendo modifiche troppo complesse (considera custom plugin)
```

#### H. Dove Inserire il Codice
Indicazioni pratiche:
```markdown
## Dove Mettere il Codice

### Opzione 1: functions.php del Child Theme (consigliato)
```php
// wp-content/themes/mio-child-theme/functions.php

// Il tuo codice qui
```
✅ Sopravvive agli aggiornamenti theme
❌ Si disattiva se cambi theme

### Opzione 2: Custom Plugin
Crea file: `wp-content/plugins/mio-custom-plugin/mio-custom-plugin.php`
```php
<?php
/*
Plugin Name: Mie Personalizzazioni
Description: Modifiche custom per il mio sito
Version: 1.0
*/

// Il tuo codice qui
```
✅ Indipendente dal theme
✅ Facile disattivare/riattivare
```

#### I. Prossimi Passi
Suggerisci approfondimenti:
```markdown
## Cosa Imparare Dopo

1. **Hook specifici WordPress**: `init`, `wp_head`, `save_post`
2. **Hook WooCommerce**: `woocommerce_before_cart`, `woocommerce_checkout_process`
3. **Priorità hook**: come controllare ordine di esecuzione
4. **Rimuovere hook**: `remove_action()` e `remove_filter()`

**Risorse:**
- [WordPress Plugin Handbook - Hooks](https://developer.wordpress.org/plugins/hooks/)
- [WooCommerce Hook Reference](https://woocommerce.com/documentation/plugins/woocommerce/woocommerce-codex/snippets/)
```

## Stile Comunicazione
- **Linguaggio semplice**: evita gergo tecnico quando possibile
- **Analogie**: usa paragoni concreti
- **Visual**: usa emoji, box, separatori per chiarezza
- **Progressivo**: dal semplice al complesso
- **Incoraggiante**: "non preoccuparti se non è chiaro subito"
- **Pratico**: esempi funzionanti copy-paste

## Output
Documento markdown strutturato con:
- Spiegazione stratificata (base → avanzata)
- Codice commentato abbondantemente
- Errori comuni evidenziati
- Link a risorse esterne
- Esercizi opzionali per praticare

## Agente Consigliato
Questo comando utilizza l'agente:
- `teacher` per spiegazioni pedagogiche e chiare
