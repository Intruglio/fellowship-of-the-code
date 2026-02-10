---
name: plan
description: Analizza requisito e crea piano implementazione dettagliato per feature o fix
---

# Comando: Plan

## Scopo
Analizza un requisito (feature, bug fix, refactor) e produce un piano di implementazione dettagliato con step, file coinvolti e considerazioni.

## Utilizzo
```bash
/plan $ARGUMENTS
```

**Esempi:**
- `/plan aggiungi campo custom al checkout WooCommerce`
- `/plan fix bug carrello non aggiorna quantità`
- `/plan refactor sistema di cache transient`
- `/plan integra payment gateway Stripe`

## Workflow

### 1. Analisi Requisito
- Leggi e comprendi la richiesta in `$ARGUMENTS`
- Identifica tipo di task (feature, bug, refactor, integration)
- Identifica ambito (plugin, theme, WooCommerce, core WP)

### 2. Ricerca Codebase
- Cerca file rilevanti nel repository
- Identifica pattern esistenti da seguire
- Controlla dipendenze (plugin, funzioni WP/WC)
- Verifica convenzioni di naming e struttura

### 3. Progettazione Soluzione
- Identifica approccio ottimale ("The WordPress Way")
- Lista hook e filtri WordPress/WooCommerce da usare
- Identifica file da creare/modificare
- Considera edge cases e error handling
- Pianifica validazione e sicurezza
- Considera impact su performance

### 4. Piano Implementazione
Produci piano strutturato con:

#### A. Overview
- Descrizione soluzione in 2-3 frasi
- Approccio tecnico scelto
- Razionale decisioni architetturali

#### B. File da Modificare/Creare
Lista completa con percorsi:
```markdown
1. `functions.php` - aggiungere hook per campo custom
2. `templates/checkout/custom-field.php` - template campo
3. `includes/class-checkout-handler.php` - logica validazione
```

#### C. Step Implementazione
Lista numerata dettagliata:
```markdown
1. Creare template campo in `templates/checkout/custom-field.php`
   - Input textarea con ID `delivery_instructions`
   - Label "Istruzioni di consegna"
   - Placeholder descrittivo

2. Hook campo nel checkout (`functions.php`)
   - Usare `woocommerce_after_order_notes` action
   - Chiamare `woocommerce_form_field()` helper

3. Validare input durante checkout
   - Hook `woocommerce_checkout_process`
   - Sanitizzare con `sanitize_textarea_field()`
   - Max 500 caratteri
   - Error con `wc_add_notice()` se validation fail

4. Salvare dato nell'ordine
   - Hook `woocommerce_checkout_update_order_meta`
   - Salvare in order meta con prefisso `_delivery_instructions`

5. Mostrare in admin ordine
   - Hook `woocommerce_admin_order_data_after_billing_address`
   - Display read-only del valore
```

#### D. Considerazioni Sicurezza
```markdown
- ✅ Input sanitization: `sanitize_textarea_field()`
- ✅ Output escaping: `esc_html()` quando display
- ✅ Nonce: gestito da WooCommerce checkout form
- ✅ Capability: solo admin vede campo in ordine
- ✅ Validazione: max length 500 caratteri
```

#### E. Considerazioni Performance
```markdown
- Campo salvato come order meta (nessun impact query)
- Nessuna query DB aggiuntiva
- Load solo su pagina checkout (conditional enqueue se necessario)
```

#### F. Test Plan
```markdown
1. Test aggiunta prodotto e checkout con campo compilato
2. Test checkout con campo vuoto (opzionale)
3. Test validazione max 500 caratteri
4. Test caratteri speciali e HTML (deve essere sanitizzato)
5. Test visualizzazione in admin ordine
6. Test email ordine (se campo deve apparire)
7. Test compatibilità con altri plugin checkout
```

#### G. Rischi e Mitigazioni
```markdown
- **Rischio**: Conflitto con altri plugin che modificano checkout
  **Mitigazione**: Usare priorità hook bassa (15+)

- **Rischio**: Campo non salvato se errore durante checkout
  **Mitigazione**: Error logging e fallback graceful
```

#### H. Rollback Plan
```markdown
Se feature causa problemi:
1. Rimuovere hook da functions.php
2. Eliminare template file
3. Dati esistenti in order meta rimangono (no data loss)
```

## Output
Produce documento markdown strutturato con tutte le sezioni sopra.

## Note
- **Non implementare codice**, solo pianificare
- Identificare eventuali informazioni mancanti e richiederle
- Suggerire alternative se esistono approcci multipli
- Evidenziare complessità e time estimation indicativa (semplice/medio/complesso)
- Linkare documentazione WordPress/WooCommerce rilevante

## Agente Consigliato
Questo comando può beneficiare dell'expertise di:
- `wp-woo-expert` per architettura e hook
- `reviewer` per considerazioni qualità e sicurezza
- `qa-tester` per test plan
