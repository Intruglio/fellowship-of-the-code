---
name: review-pr
description: Code review completo di pull request con focus su qualità, sicurezza e best practice
---

# Comando: Review PR

## Scopo
Esegue code review approfondito di una pull request, verificando qualità codice, sicurezza, performance e aderenza a WordPress/WooCommerce standards.

## Utilizzo
```bash
/review-pr $ARGUMENTS
```

**Esempi:**
- `/review-pr PR #42`
- `/review-pr branch feature/custom-checkout`
- `/review-pr files: functions.php templates/checkout.php`
- `/review-pr commit abc123def`

## Workflow

### 1. Identifica Scope
Determina cosa recensire da `$ARGUMENTS`:
- Numero PR (es: #42)
- Branch specifico
- File specifici
- Commit hash
- Diff tra branch

Recupera file modificati e contenuto.

### 2. Context Review
Prima di analizzare codice, comprendi contesto:

```markdown
## Pull Request Overview

**Titolo:** Add custom delivery instructions field to checkout
**Tipo:** 🆕 Feature / 🐛 Bug Fix / 🔄 Refactor / 📝 Docs
**Files modificati:** 4
**Righe aggiunte:** +127 / Rimosse: -3

**Descrizione:**
[Estrai da PR description o commit messages]

**Issue collegato:** #38 - Customer requested delivery instructions
```

### 3. Review Strutturato

#### A. Security Review 🔴 CRITICAL
Massima priorità, blocca merge se problemi:

```markdown
## 🔐 Security Analysis

### SQL Injection
**Status:** ✅ PASS

**Verificato:**
- `functions.php:127` - Query usa `$wpdb->prepare()` ✓
- Nessuna query raw con input utente

---

### XSS (Cross-Site Scripting)
**Status:** ⚠️ WARNING

**Issue Trovati:**

#### ⚠️ Line 45: templates/checkout-field.php
```php
<input type="text" value="<?php echo $_POST['delivery_instructions']; ?>">
```

**Problema:** Output non escaped, possibile XSS injection.

**Fix Richiesto:**
```php
<input type="text" value="<?php echo esc_attr($_POST['delivery_instructions']); ?>">
```

**Severity:** HIGH - Vulnerabilità sfruttabile

---

### CSRF (Cross-Site Request Forgery)
**Status:** ✅ PASS

**Verificato:**
- Nonce verification presente in `process_checkout()`
- `wp_verify_nonce()` chiamato prima di salvare dati ✓

---

### Input Validation
**Status:** ⚠️ WARNING

**Issue Trovati:**

#### ⚠️ Line 67: includes/checkout-handler.php
```php
$instructions = sanitize_text_field($_POST['delivery_instructions']);
update_post_meta($order_id, '_delivery_instructions', $instructions);
```

**Problema:**
- Nessun limite lunghezza input (potential DB bloat)
- Nessuna validazione caratteri speciali

**Fix Suggerito:**
```php
$instructions = sanitize_textarea_field($_POST['delivery_instructions']);

// Validate max length
if (strlen($instructions) > 500) {
    wc_add_notice(__('Delivery instructions too long (max 500 characters)', 'textdomain'), 'error');
    return;
}

// Validate caratteri (opzionale, se serve limitare)
if (!preg_match('/^[\p{L}\p{N}\s\.,\-]+$/u', $instructions)) {
    wc_add_notice(__('Invalid characters in delivery instructions', 'textdomain'), 'error');
    return;
}

update_post_meta($order_id, '_delivery_instructions', $instructions);
```

---

### Capability Checks
**Status:** ✅ PASS

**Verificato:**
- Admin functions protetti con `current_user_can('manage_woocommerce')` ✓

---

### File Upload (se applicabile)
**Status:** N/A - No file upload in this PR
```

#### B. Code Quality Review 🟡

```markdown
## 💎 Code Quality

### WordPress Coding Standards
**Status:** 🟢 GOOD (minor issues)

**Verificato:**
- ✅ Indentazione consistente (tabs, non spaces)
- ✅ Naming conventions rispettate (prefisso `mystie_`)
- ✅ Docblock presenti per funzioni
- ⚠️ Mancano alcuni inline comments per logica complessa

**Issue Minori:**

#### Line 89: functions.php
```php
function mysite_save_field($order_id){
```
**Nota:** Spazio mancante prima `{` (WordPress standard richiede spazio).

**Fix:**
```php
function mysite_save_field($order_id) {
```

---

### DRY Principle (Don't Repeat Yourself)
**Status:** 🟢 GOOD

**Verificato:**
- Nessuna duplicazione codice rilevante
- Funzioni riutilizzabili estratte correttamente

---

### Function Complexity
**Status:** 🟢 GOOD

**Verificato:**
- Funzioni single-responsibility ✓
- Nessuna funzione > 50 righe
- Nesting level max 3 (accettabile)

---

### Error Handling
**Status:** ⚠️ NEEDS IMPROVEMENT

**Issue:**

#### Line 102: includes/checkout-handler.php
```php
$order = wc_get_order($order_id);
$order->update_meta_data('_delivery_instructions', $instructions);
```

**Problema:** No check se `$order` è valido.

**Fix:**
```php
$order = wc_get_order($order_id);

if (!$order) {
    error_log('Invalid order ID: ' . $order_id);
    return false;
}

$order->update_meta_data('_delivery_instructions', $instructions);
$order->save();
```

---

### Documentation
**Status:** 🟡 ADEQUATE (could be better)

**Verificato:**
- ✅ PHPDoc presente per funzioni pubbliche
- ⚠️ Mancano examples in docblock
- ⚠️ Inline comments minimi

**Suggerimento:**
Aggiungi `@example` in docblock per funzioni complesse.
```

#### C. Performance Review ⚡

```markdown
## ⚡ Performance Analysis

### Database Queries
**Status:** 🟢 GOOD

**Verificato:**
- Nessuna query dentro loop ✓
- Uso appropriato di `update_post_meta()` (single query) ✓
- No N+1 query problem

---

### Asset Loading
**Status:** 🟢 OPTIMAL

**Verificato:**
- Script caricato solo su checkout page (conditional enqueue) ✓
```php
if (is_checkout()) {
    wp_enqueue_script('mysite-checkout', ...);
}
```
- Dependencies dichiarate correttamente ✓

---

### Caching
**Status:** N/A - No caching needed for this feature

---

### Potential Bottlenecks
**Status:** 🟢 NONE FOUND

**Verificato:**
- Operazioni I/O minime
- No API calls external
- No file operations pesanti
```

#### D. WordPress/WooCommerce Standards 🎯

```markdown
## 🎯 WordPress/WooCommerce Standards

### Hook Usage
**Status:** 🟢 EXCELLENT

**Verificato:**
- ✅ Hook appropriati usati:
  - `woocommerce_after_order_notes` per display field
  - `woocommerce_checkout_process` per validation
  - `woocommerce_checkout_update_order_meta` per save
- ✅ Priorità adeguate (10 default)
- ✅ Numero parametri corretto

---

### Template Hierarchy
**Status:** 🟢 CORRECT

**Verificato:**
- Template in `templates/` directory (corretto per plugin) ✓
- Template overridable da theme ✓

---

### Internationalization (i18n)
**Status:** ⚠️ INCOMPLETE

**Issue:**

#### Multiple lines: hardcoded strings
```php
echo '<label>Delivery Instructions</label>';
```

**Fix Required:**
```php
echo '<label>' . __('Delivery Instructions', 'mysite-textdomain') . '</label>';
```

**Strings da tradurre:**
- Line 45: "Delivery Instructions"
- Line 67: "Please enter delivery instructions"
- Line 89: Error messages

**Checklist:**
- [ ] Tutte le stringhe wrappate in `__()` o `_e()`
- [ ] Text domain consistente ('mysite-textdomain')
- [ ] File .pot generato per traduttori

---

### Enqueue Best Practice
**Status:** 🟢 GOOD

**Verificato:**
- `wp_enqueue_script()` usato (no hardcoded script tags) ✓
- `wp_localize_script()` per passare dati PHP→JS ✓
```

#### E. Testing Review 🧪

```markdown
## 🧪 Testing

### Test Coverage
**Status:** ⚠️ NO TESTS PROVIDED

**Richiesto:**
- [ ] Unit test per validation logic
- [ ] Integration test per save to order meta
- [ ] E2E test per checkout flow completo

**Suggerimento Test Case:**
```php
/**
 * Test che campo sia salvato correttamente nell'ordine
 */
public function test_delivery_instructions_saved_to_order() {
    $order_id = $this->create_test_order();
    $_POST['delivery_instructions'] = 'Test instructions';

    mysite_save_delivery_instructions($order_id);

    $saved = get_post_meta($order_id, '_delivery_instructions', true);
    $this->assertEquals('Test instructions', $saved);
}
```

---

### Manual Testing Checklist
**Per Reviewer/QA:**
- [ ] Campo appare su checkout page
- [ ] Validazione funziona (max 500 char)
- [ ] Dato salvato in ordine
- [ ] Visibile in admin order page
- [ ] Funziona con guest checkout
- [ ] Funziona con registered user checkout
- [ ] Non rompe altri plugin checkout custom
- [ ] Mobile responsive
- [ ] Accessibilità (keyboard nav, screen reader)
```

### 4. Summary e Raccomandazioni

```markdown
## 📋 Review Summary

### Overall Assessment: 🟡 APPROVE WITH CHANGES

**Breakdown:**
- 🔴 Security: 2 warnings (XSS, validation)
- 🟡 Code Quality: Minor issues
- 🟢 Performance: Good
- 🟡 Standards: i18n incomplete
- ⚠️ Testing: No tests provided

---

## ✅ MUST FIX (Blockers)

### 1. XSS Vulnerability - Line 45
**Priority:** P0 - CRITICAL
**File:** `templates/checkout-field.php`

Escape output con `esc_attr()`:
```php
<input type="text" value="<?php echo esc_attr($_POST['delivery_instructions']); ?>">
```

### 2. Input Validation - Line 67
**Priority:** P0 - CRITICAL
**File:** `includes/checkout-handler.php`

Aggiungi validazione max length e error handling:
```php
$instructions = sanitize_textarea_field($_POST['delivery_instructions']);

if (strlen($instructions) > 500) {
    wc_add_notice(__('Instructions too long', 'textdomain'), 'error');
    return;
}
```

---

## ⚠️ SHOULD FIX (Recommended)

### 3. Internationalization
**Priority:** P1 - HIGH

Wrappa tutte le stringhe in `__()`/`_e()` con text domain consistente.

### 4. Error Handling
**Priority:** P2 - MEDIUM

Aggiungi check validità order object prima di `update_meta_data()`.

---

## 💡 NICE TO HAVE (Optional)

### 5. Documentation
Aggiungi esempi nei docblock e più inline comments.

### 6. Unit Tests
Aggiungi test coverage per validation e save logic.

---

## 🎯 Next Steps

1. **Author**: Fixa 🔴 blockers (XSS, validation)
2. **Author**: Implementa ⚠️ recommended fixes (i18n, error handling)
3. **Reviewer**: Re-review dopo update
4. **QA**: Esegui manual testing checklist
5. **Merge**: Dopo approval finale + tests pass

---

## 📚 References
- [WordPress Escaping Guide](https://developer.wordpress.org/apis/security/escaping/)
- [WooCommerce Checkout Hooks](https://woocommerce.com/document/tutorial-customising-checkout-fields-using-actions-and-filters/)
- [Input Validation Best Practices](https://developer.wordpress.org/apis/security/sanitizing-securing-output/)
```

## Output Format
Review strutturato in markdown con:
1. **Overview**: contesto PR
2. **Security Review**: vulnerabilità (se presenti, blocca merge)
3. **Code Quality**: standard, DRY, error handling
4. **Performance**: query, caching, bottleneck
5. **Standards**: WordPress/WooCommerce compliance
6. **Testing**: coverage e manual checklist
7. **Summary**: assessment overall, must-fix, should-fix, nice-to-have
8. **Next Steps**: action items chiari

## Tone
- **Costruttivo**: focus su miglioramento, non critica
- **Specifico**: indica line number, file, fix esatto
- **Educativo**: spiega "perché" non solo "cosa"
- **Prioritizzato**: distingui critical da nice-to-have
- **Incoraggiante**: riconosci parti ben fatte

## Agente Consigliato
Questo comando utilizza l'agente:
- `reviewer` per code review sistematico e quality assurance
