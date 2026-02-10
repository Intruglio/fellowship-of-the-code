---
name: debug-triage
description: Analizza bug o errore, identifica root cause e propone fix sistematico
---

# Comando: Debug Triage

## Scopo
Analizza un bug, errore o comportamento inaspettato in modo sistematico, identifica la root cause e propone soluzione con step di verifica.

## Utilizzo
```bash
/debug-triage $ARGUMENTS
```

**Esempi:**
- `/debug-triage white screen of death dopo aggiornamento plugin`
- `/debug-triage carrello WooCommerce non aggiorna quantità`
- `/debug-triage errore: Call to undefined function wp_get_current_user()`
- `/debug-triage pagina checkout carica infinitamente`
- `/debug-triage immagini non caricano dopo migrazione`

## Workflow

### 1. Raccolta Informazioni
Analizza `$ARGUMENTS` ed estrai:
- **Sintomo**: cosa non funziona
- **Contesto**: dove si verifica (frontend, admin, specifiche pagine)
- **Quando**: dopo quale azione/modifica
- **Frequenza**: sempre, intermittente, solo per certi utenti

Se informazioni incomplete, chiedi dettagli specifici:
```markdown
Per diagnosticare meglio, ho bisogno di:
- [ ] Quale versione WordPress/WooCommerce?
- [ ] Errori nel browser console? (F12 → Console)
- [ ] Errori nel debug.log? (attiva WP_DEBUG)
- [ ] Quando è iniziato? (dopo aggiornamento, modifica codice, etc.)
- [ ] Quali plugin attivi?
- [ ] Theme custom o standard?
```

### 2. Ipotesi Iniziali
Basandoti su sintomo, formula 3-5 ipotesi possibili:
```markdown
## Possibili Cause

### Ipotesi 1: Conflitto Plugin ⭐ (più probabile)
Sintomo tipico quando due plugin manipolano la stessa funzionalità.
Probabilità: 70%

### Ipotesi 2: Memory Limit
WSOD spesso causato da PHP memory exhausted.
Probabilità: 20%

### Ipotesi 3: Permessi File
Problemi upload potrebbero essere permessi directory.
Probabilità: 10%
```

### 3. Step Diagnostici
Crea checklist sistematica per isolare problema:

```markdown
## Diagnostica Step-by-Step

### Step 1: Attiva Debug Mode
Aggiungi in `wp-config.php` prima di "/* That's all, stop editing! */":
```php
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
define('SCRIPT_DEBUG', true);
```

Risultato atteso: file `wp-content/debug.log` creato con errori.

**Esegui e riporta**: cosa appare nel debug.log quando riproduci il bug?

---

### Step 2: Controlla Browser Console
1. Apri pagina con problema
2. Premi F12 (DevTools)
3. Vai a tab "Console"
4. Riproduci bug

**Cosa cercare:**
- Errori JavaScript (rosso)
- Failed AJAX calls (tab Network)
- 404 su risorse (JS, CSS non trovati)

**Esegui e riporta**: screenshot console errors.

---

### Step 3: Disattiva Plugin (Metodo Binario)
**Se hai accesso admin:**
1. Disattiva tutti i plugin
2. Verifica se bug scompare
3. Se sì, riattiva metà plugin
4. Se bug riappare, causa è in quella metà
5. Ripeti fino a isolare plugin specifico

**Se NO accesso admin (WSOD):**
Via FTP/SSH rinomina cartelle:
```bash
mv wp-content/plugins wp-content/plugins-disabled
```
Se sito torna funzionante, riattiva plugin uno alla volta.

**Esegui e riporta**: quale plugin causa il problema?

---

### Step 4: Testa con Theme Default
1. Attiva theme Twenty Twenty-Four (o altro default)
2. Verifica se problema persiste

**Se scompare**: causa è nel theme custom.
**Se persiste**: problema è in plugin o core.

**Esegui e riporta**: risultato test theme.

---

### Step 5: Controlla Error Log Server
Se hai accesso server, controlla:
- `/var/log/apache2/error.log` (Apache)
- `/var/log/nginx/error.log` (Nginx)
- PHP error log (path varia)

**Cosa cercare:**
- Fatal errors PHP
- Memory exhausted
- Timeout errors

**Esegui e riporta**: errori server-side rilevanti.

---

### Step 6: Verifica Query Database
Installa plugin "Query Monitor":
1. Attiva Query Monitor
2. Riproduci bug
3. Guarda barra top → Query Monitor

**Cosa cercare:**
- Query molto lente (> 1s)
- Errori MySQL
- N+1 query problems

**Esegui e riporta**: query problematiche.
```

### 4. Analisi Root Cause
Basandoti su risultati diagnostica, identifica causa:

```markdown
## Root Cause Identificata

### Causa: Conflitto tra Plugin X e Plugin Y

**Spiegazione Tecnica:**
Plugin X registra script 'custom-slider' con handle duplicato
rispetto a Plugin Y. Quando entrambi chiamano `wp_enqueue_script()`
con stesso handle, secondo sovrascrive primo causando missing
dependencies e errore JS.

**Evidenza:**
- Debug.log mostra: "Warning: wp_enqueue_script was called incorrectly"
- Console browser: "Uncaught ReferenceError: jQuery is not defined"
- Bug scompare disattivando uno dei due plugin

**Perché Succede:**
WordPress non permette registrare due script con handle identico.
Plugin devono usare prefissi unici per evitare conflitti.
```

### 5. Proposta Fix

#### A. Fix Immediato (Workaround)
Soluzione rapida per ripristinare funzionalità:
```markdown
## 🚑 Fix Immediato

Disattiva Plugin X temporaneamente:
1. Via admin: Plugin → Disattiva "Plugin X"
2. Via FTP: rinomina `wp-content/plugins/plugin-x` in `plugin-x-disabled`

**Impatto:** funzionalità di Plugin X non disponibile.
**Durata:** temporaneo fino a fix definitivo.
```

#### B. Fix Definitivo
Soluzione permanente con codice:
```markdown
## 🔧 Fix Definitivo

### Opzione 1: Deregister Script Conflittuale
Aggiungi in `functions.php` del child theme:

```php
/**
 * Fix conflitto handle 'custom-slider' tra Plugin X e Y
 * Deregistra script di Plugin X e re-registra con handle unico
 */
add_action('wp_enqueue_scripts', 'fix_slider_conflict', 100);

function fix_slider_conflict() {
    // Priorità 100 per eseguire DOPO entrambi i plugin

    // Deregistra script Plugin X
    wp_deregister_script('custom-slider');

    // Re-registra con handle unico
    wp_enqueue_script(
        'pluginx-custom-slider', // Handle unico con prefisso
        plugins_url('js/slider.js', PLUGINX_FILE),
        array('jquery'),
        '1.0.0',
        true
    );
}
```

**Pros:** ✅ Risolve definitivamente, mantiene entrambi plugin attivi
**Cons:** ⚠️ Richiede update ad ogni aggiornamento Plugin X

### Opzione 2: Contatta Autore Plugin
Riporta bug agli sviluppatori di Plugin X:
- Link support forum plugin
- Descrizione conflitto
- Suggerisci rename handle a 'pluginx_custom_slider'

**Pros:** ✅ Fix upstream, beneficia tutti gli utenti
**Cons:** ⚠️ Tempi lunghi, dipende da terzi
```

### 6. Piano di Test
Verifica che fix funzioni:
```markdown
## Testing

### Pre-Fix Checklist
- [ ] Bug riproducibile consistentemente
- [ ] Backup database e files

### Test Fix
1. Applica fix (aggiungi codice in functions.php)
2. Refresh cache (Ctrl+F5 o plugin cache)
3. Testa pagina con bug
4. Verifica funzionalità Plugin X ancora funzionanti
5. Verifica funzionalità Plugin Y ancora funzionanti
6. Test cross-browser (Chrome, Firefox, Safari)
7. Test mobile

### Regression Testing
- [ ] Homepage carica correttamente
- [ ] Admin dashboard accessibile
- [ ] Login/Logout funziona
- [ ] Checkout WooCommerce (se e-commerce)
- [ ] Forms inviano dati

### Post-Fix
- [ ] Disattiva WP_DEBUG in produzione
- [ ] Documenta fix in note interne
- [ ] Monitora error log per 48h
```

### 7. Prevenzione Futura
Suggerimenti per evitare bug simili:
```markdown
## Prevenzione

### Immediate Actions
1. **Update Policy**: testa aggiornamenti in staging prima di produzione
2. **Backup Automatici**: schedule backup giornalieri
3. **Monitoring**: setup alert per errori PHP critici

### Long-Term
1. **Plugin Audit**: rimuovi plugin non essenziali (meno conflitti)
2. **Code Review**: verifica handle unici in custom code
3. **Staging Environment**: test modifiche prima di deploy
4. **Error Logging**: mantieni WP_DEBUG_LOG attivo (con DISPLAY false)
```

## Categorie Bug Comuni

### WSOD (White Screen of Death)
**Cause:**
- PHP Fatal Error (syntax error, function undefined)
- Memory limit exceeded
- Plugin conflict
- .htaccess corrotto

**Diagnostica:**
1. Controlla debug.log
2. Aumenta memory: `define('WP_MEMORY_LIMIT', '256M');`
3. Rinomina plugins folder
4. Rinomina .htaccess

---

### AJAX Not Working
**Cause:**
- URL admin-ajax.php errato
- Nonce non valido/scaduto
- Action hook non registrato
- JavaScript error blocca execution

**Diagnostica:**
1. Console browser per JS errors
2. Network tab per response AJAX (0 o -1 = errore)
3. Verifica wp_ajax_{action} hook registrato
4. Verifica nonce generation/verification match

---

### 404 su Assets (CSS/JS)
**Cause:**
- Permalink non flushed dopo cambio rewrite rules
- Hard-coded path errati
- .htaccess permessi errati
- CDN/cache issue

**Diagnostica:**
1. Vai Settings → Permalinks → Save (flush rewrite rules)
2. Verifica path con View Source
3. Controlla permessi .htaccess (644)
4. Purge CDN cache

---

### Query Lente
**Cause:**
- N+1 query problem
- Missing database indexes
- Inefficient WP_Query args
- Large dataset senza pagination

**Diagnostica:**
1. Query Monitor plugin
2. Identifica query > 1 secondo
3. Check for loops con get_posts() inside
4. Optimize con JOIN invece di multiple queries

## Output
Documento strutturato con:
1. **Sintesi bug**: descrizione chiara problema
2. **Ipotesi cause**: ranked per probabilità
3. **Step diagnostici**: checklist eseguibile
4. **Root cause**: spiegazione tecnica
5. **Fix proposto**: workaround + soluzione definitiva
6. **Test plan**: verifica fix funzionante
7. **Prevenzione**: evitare bug simili futuro

## Agente Consigliato
Questo comando utilizza l'agente:
- `debugger` per analisi sistematica e troubleshooting
