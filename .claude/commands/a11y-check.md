---
name: a11y-check
description: Audit accessibilità WCAG 2.1 per pagine e componenti WordPress
---

# Comando: Accessibility Check

## Scopo
Analizza accessibilità di pagine, template o componenti WordPress/WooCommerce secondo standard WCAG 2.1 Level AA, fornendo checklist e fix specifici.

## Utilizzo
```bash
/a11y-check $ARGUMENTS
```

**Esempi:**
- `/a11y-check template: single-product.php`
- `/a11y-check page: /checkout`
- `/a11y-check component: navigation menu`
- `/a11y-check all templates`

## Workflow

### 1. Identifica Target
Da `$ARGUMENTS` determina:
- Template file specifico
- URL pagina live
- Componente specifico (form, menu, modal, etc.)
- "all" per audit completo sito

### 2. Analisi Strutturata

```markdown
## ♿ Accessibility Audit Report

**Target:** templates/single-product.php
**Standard:** WCAG 2.1 Level AA
**Date:** 2026-01-09

---

## 1. PERCEIVABLE (Percepibile)

### 1.1 Text Alternatives
**Guideline:** Fornire alternative testuali per contenuto non testuale.

#### Images
**Status:** ⚠️ ISSUES FOUND

##### ❌ Line 45: Product Gallery
```php
<img src="<?php echo $product_image; ?>">
```

**Problema:** Manca attributo `alt`.

**Impact:** Screen reader non può descrivere immagine.

**WCAG:** 1.1.1 Non-text Content (Level A)

**Fix:**
```php
<img src="<?php echo esc_url($product_image); ?>"
     alt="<?php echo esc_attr($product->get_name()); ?>"
     loading="lazy">
```

**Best Practice:**
- `alt` descrittivo per immagini informative
- `alt=""` (vuoto) per immagini decorative
- Non usare "image of" o "picture of" nell'alt

---

##### ⚠️ Line 67: Icon Buttons
```php
<button class="wishlist">
    <i class="fa fa-heart"></i>
</button>
```

**Problema:** Icon-only button senza label testuale.

**Impact:** Screen reader legge solo "button", utente non sa funzione.

**WCAG:** 1.1.1, 2.4.4 Link Purpose

**Fix:**
```php
<button class="wishlist" aria-label="<?php esc_attr_e('Add to wishlist', 'textdomain'); ?>">
    <i class="fa fa-heart" aria-hidden="true"></i>
</button>
```

**Alternative (preferita):**
```php
<button class="wishlist">
    <i class="fa fa-heart" aria-hidden="true"></i>
    <span class="sr-only"><?php _e('Add to wishlist', 'textdomain'); ?></span>
</button>
```

CSS:
```css
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    border: 0;
}
```

---

### 1.2 Time-based Media
**Status:** N/A - No video/audio in template

---

### 1.3 Adaptable
**Guideline:** Contenuto presentabile in modi diversi senza perdere informazioni.

#### Heading Structure
**Status:** 🔴 CRITICAL ISSUE

**Analysis:**
```html
Line 23: <h1>Product Name</h1>
Line 45: <h4>Description</h4>  ❌ Skip from h1 to h4
Line 67: <h2>Reviews</h2>
```

**Problema:** Salto da H1 a H4 (manca H2, H3).

**Impact:** Screen reader navigation confusa, struttura illogica.

**WCAG:** 1.3.1 Info and Relationships (Level A)

**Fix:**
```html
<h1>Product Name</h1>
<h2>Description</h2>     <!-- Was h4 -->
<h2>Reviews</h2>
<h3>Customer Review</h3> <!-- Sotto-sezione reviews -->
```

**Rule:** Heading hierarchy sequenziale (no salti), unico H1 per pagina.

---

#### Semantic HTML
**Status:** ⚠️ NEEDS IMPROVEMENT

##### Line 89: Product Info
```php
<div class="product-meta">
    <div>SKU: <?php echo $sku; ?></div>
    <div>Category: <?php echo $category; ?></div>
</div>
```

**Problema:** Liste rappresentate con `<div>` invece di `<ul>/<li>`.

**WCAG:** 1.3.1 Info and Relationships

**Fix:**
```php
<dl class="product-meta">
    <dt><?php _e('SKU:', 'textdomain'); ?></dt>
    <dd><?php echo esc_html($sku); ?></dd>

    <dt><?php _e('Category:', 'textdomain'); ?></dt>
    <dd><?php echo esc_html($category); ?></dd>
</dl>
```

**Alternative con lista:**
```php
<ul class="product-meta">
    <li><strong><?php _e('SKU:', 'textdomain'); ?></strong> <?php echo esc_html($sku); ?></li>
    <li><strong><?php _e('Category:', 'textdomain'); ?></strong> <?php echo esc_html($category); ?></li>
</ul>
```

---

### 1.4 Distinguishable
**Guideline:** Facile vedere e sentire contenuto.

#### Color Contrast
**Status:** 🔴 FAILS WCAG AA

**CSS Analysis:**
```css
/* style.css:45 */
.price {
    color: #999;              /* Light gray */
    background: #fff;         /* White */
}
/* Contrast ratio: 2.8:1 ❌ FAIL (required: 4.5:1) */
```

**WCAG:** 1.4.3 Contrast (Minimum) - Level AA

**Fix:**
```css
.price {
    color: #767676;           /* Darker gray */
    background: #fff;
}
/* Contrast ratio: 4.54:1 ✅ PASS */
```

**Tool:** https://webaim.org/resources/contrastchecker/

**Requirements:**
- Normal text: 4.5:1 (AA), 7:1 (AAA)
- Large text (18pt+/14pt+ bold): 3:1 (AA), 4.5:1 (AAA)
- UI components: 3:1

---

#### Resize Text
**Status:** ⚠️ ISSUES

**CSS Analysis:**
```css
/* Fixed pixel sizes break zoom */
.product-title {
    font-size: 24px;
}
```

**WCAG:** 1.4.4 Resize Text (Level AA)

**Better:**
```css
.product-title {
    font-size: 1.5rem;        /* Relative to root, scales with zoom */
}
```

---

## 2. OPERABLE (Utilizzabile)

### 2.1 Keyboard Accessible
**Guideline:** Tutta la funzionalità accessibile da tastiera.

#### Keyboard Navigation
**Status:** 🔴 CRITICAL ISSUES

##### ❌ Line 123: Custom Dropdown
```javascript
$('.dropdown-trigger').click(function() {
    $(this).next('.dropdown-menu').toggle();
});
```

**Problema:**
1. Solo click event (no keyboard)
2. No focus management
3. No Esc to close

**Impact:** Utenti tastiera non possono usare dropdown.

**WCAG:** 2.1.1 Keyboard (Level A)

**Fix:**
```javascript
// Gestisci keyboard interaction
$('.dropdown-trigger').on('click keydown', function(e) {
    // Space or Enter attiva dropdown
    if (e.type === 'click' || e.key === ' ' || e.key === 'Enter') {
        e.preventDefault();
        const $menu = $(this).next('.dropdown-menu');
        const isExpanded = $(this).attr('aria-expanded') === 'true';

        // Toggle dropdown
        $(this).attr('aria-expanded', !isExpanded);
        $menu.toggle();

        // Focus primo item se opening
        if (!isExpanded) {
            $menu.find('a, button').first().focus();
        }
    }

    // Esc chiude dropdown
    if (e.key === 'Escape') {
        $(this).attr('aria-expanded', 'false');
        $(this).next('.dropdown-menu').hide();
        $(this).focus(); // Return focus to trigger
    }
});
```

**HTML Fix:**
```php
<button class="dropdown-trigger"
        aria-expanded="false"
        aria-haspopup="true"
        aria-controls="dropdown-menu-1">
    <?php _e('Options', 'textdomain'); ?>
</button>
<ul id="dropdown-menu-1" class="dropdown-menu" hidden>
    <li><a href="#">Option 1</a></li>
    <li><a href="#">Option 2</a></li>
</ul>
```

---

#### Focus Visible
**Status:** 🔴 CRITICAL

**CSS Analysis:**
```css
/* Line 34: REMOVES FOCUS OUTLINE - NEVER DO THIS! */
*:focus {
    outline: none; ❌
}
```

**Problema:** Tastiera users non vedono dove sono.

**WCAG:** 2.4.7 Focus Visible (Level AA)

**Fix:**
```css
/* Remove default, add custom focus style */
*:focus {
    outline: 3px solid #0066cc;
    outline-offset: 2px;
}

/* Alternative: preserve default */
*:focus {
    outline: auto; /* Browser default */
}

/* Se devi custom, SEMPRE fornisci alternative */
.button:focus {
    outline: 2px solid currentColor;
    outline-offset: 2px;
}
```

---

### 2.2 Enough Time
**Status:** ⚠️ CHECK REQUIRED

#### Timeout Warning
**Issue:** Se checkout ha timeout sessione, serve warning.

**WCAG:** 2.2.1 Timing Adjustable (Level A)

**Fix:**
```javascript
// Avvisa 2 minuti prima scadenza
let sessionTimeout = 20 * 60 * 1000; // 20 min
let warningTime = 18 * 60 * 1000;    // 18 min

setTimeout(() => {
    // Alert accessibile (aria-live)
    const $alert = $('<div role="alert" class="timeout-warning">')
        .text('Session expires in 2 minutes. Continue shopping to extend?')
        .appendTo('body');

    // Focus su dialog per screen reader
    $alert.attr('tabindex', '-1').focus();

    // Opzione extend
    $('<button>').text('Extend session').click(() => {
        // Renew session via AJAX
        extendSession();
        $alert.remove();
    }).appendTo($alert);
}, warningTime);
```

---

### 2.3 Seizures and Physical Reactions
**Status:** ✅ PASS - No flashing content

---

### 2.4 Navigable
**Guideline:** Aiutare navigazione e trovare contenuto.

#### Skip Links
**Status:** ❌ MISSING

**WCAG:** 2.4.1 Bypass Blocks (Level A)

**Fix:** Aggiungi skip link in `header.php`:
```php
<a href="#main-content" class="skip-link">
    <?php _e('Skip to main content', 'textdomain'); ?>
</a>

<!-- Later in page -->
<main id="main-content" tabindex="-1">
    <!-- Content -->
</main>
```

**CSS:**
```css
.skip-link {
    position: absolute;
    top: -40px;
    left: 0;
    background: #000;
    color: #fff;
    padding: 8px;
    z-index: 100;
}

.skip-link:focus {
    top: 0; /* Show on focus */
}
```

---

#### Page Title
**Status:** ✅ GOOD

```php
<title><?php wp_title('|', true, 'right'); bloginfo('name'); ?></title>
```

---

#### Link Purpose
**Status:** ⚠️ ISSUES

##### Line 156: Ambiguous Links
```php
<a href="<?php echo $product_url; ?>">Read more</a>
<a href="<?php echo $product_url; ?>">Read more</a>
```

**Problema:** Link identici con destinazioni diverse (ambigui).

**WCAG:** 2.4.4 Link Purpose (Level A)

**Fix:**
```php
<a href="<?php echo esc_url($product_url); ?>">
    <?php printf(__('Read more about %s', 'textdomain'), $product_name); ?>
</a>
```

**Alternative (visual + SR):**
```php
<a href="<?php echo esc_url($product_url); ?>">
    Read more
    <span class="sr-only">about <?php echo esc_html($product_name); ?></span>
</a>
```

---

## 3. UNDERSTANDABLE (Comprensibile)

### 3.1 Readable
**Status:** ✅ GOOD

**Verified:**
- ✅ `lang` attribute in `<html>`
- ✅ Text readable (no jargon eccessivo)

---

### 3.2 Predictable
**Guideline:** Pagine appaiono e operano in modo prevedibile.

#### On Focus/Input
**Status:** ✅ PASS - No automatic changes on focus

---

### 3.3 Input Assistance
**Guideline:** Aiutare utenti ad evitare e correggere errori.

#### Form Labels
**Status:** 🔴 CRITICAL

##### ❌ Line 234: Checkout Form
```php
<input type="email" name="billing_email" placeholder="Email">
```

**Problema:** Placeholder NON sostituisce label.

**WCAG:** 3.3.2 Labels or Instructions (Level A)

**Fix:**
```php
<label for="billing_email"><?php _e('Email Address', 'textdomain'); ?> *</label>
<input type="email"
       id="billing_email"
       name="billing_email"
       placeholder="nome@esempio.com"
       required
       aria-required="true"
       aria-describedby="email-hint">
<span id="email-hint" class="hint">
    <?php _e('We\'ll never share your email', 'textdomain'); ?>
</span>
```

---

#### Error Identification
**Status:** 🔴 NEEDS FIX

##### Line 267: Validation Errors
```javascript
if (!isValid) {
    $('.field').addClass('error'); // Solo visual feedback
}
```

**Problema:** Errore comunicato solo via colore (non accessibile).

**WCAG:** 3.3.1 Error Identification (Level A)

**Fix:**
```javascript
if (!isValid) {
    $('.field').addClass('error')
        .attr('aria-invalid', 'true');

    // Messaggio errore testuale
    const errorMsg = $('<span class="error-message" role="alert">')
        .attr('id', 'email-error')
        .text('Please enter a valid email address');

    $('.field').attr('aria-describedby', 'email-error')
        .after(errorMsg);

    // Focus su primo errore
    $('.error').first().focus();
}
```

**HTML Result:**
```html
<input type="email"
       class="field error"
       aria-invalid="true"
       aria-describedby="email-error">
<span id="email-error" class="error-message" role="alert">
    Please enter a valid email address
</span>
```

---

## 4. ROBUST (Robusto)

### 4.1 Compatible
**Guideline:** Massimizzare compatibilità con user agents e assistive tech.

#### Valid HTML
**Status:** ⚠️ ERRORS FOUND

**Validation:** https://validator.w3.org/

**Errors:**
```
Line 45: Duplicate ID "product-1"
Line 67: <div> cannot be child of <ul>
Line 89: Unclosed <div> tag
```

**Fix:** Validate and fix markup errors.

---

#### ARIA Usage
**Status:** ⚠️ SOME ISSUES

##### ✅ GOOD: Modal
```php
<div role="dialog"
     aria-modal="true"
     aria-labelledby="modal-title">
    <h2 id="modal-title">Modal Title</h2>
</div>
```

##### ❌ BAD: Redundant ARIA
```html
<button role="button">Submit</button>  <!-- Redundant role -->
```

**Fix:**
```html
<button>Submit</button>  <!-- Button già ha role="button" implicit -->
```

**Rule:** No ARIA è meglio di bad ARIA. Usa ARIA solo quando HTML semantico non basta.

---

## 📊 ACCESSIBILITY SCORE

**Overall:** 🔴 48/100 (FAILING)

| Category | Score | Issues |
|----------|-------|--------|
| Perceivable | 40/100 | Missing alt, contrast fails |
| Operable | 35/100 | Keyboard nav broken, no focus |
| Understandable | 55/100 | Form errors not accessible |
| Robust | 60/100 | Invalid HTML, ARIA misuse |

---

## 🚨 CRITICAL FIXES (Blockers)

1. **Add alt text** to all images
2. **Fix focus outline** removal (restore visibility)
3. **Fix keyboard navigation** on dropdowns/modals
4. **Add form labels** (not just placeholders)
5. **Fix color contrast** (price, links)
6. **Fix heading hierarchy** (no skips)

**Priority:** P0 - Fix before launch

---

## ⚠️ HIGH PRIORITY

1. Skip links per navigazione
2. Error messages accessibili (aria-live, role="alert")
3. Link purpose più specifici
4. ARIA attributes corretti
5. Valid HTML (fix errors)

---

## 💡 IMPROVEMENTS

1. ARIA landmarks (main, nav, aside)
2. Breadcrumbs schema
3. Increased touch target size (44x44px min)
4. Reduced animation (prefers-reduced-motion)

---

## 🧪 TESTING RECOMMENDATIONS

### Manual Testing
- [ ] Keyboard-only navigation (unplug mouse)
- [ ] Screen reader (NVDA/VoiceOver/JAWS)
- [ ] Zoom 200% (text should reflow)
- [ ] Color blindness simulator
- [ ] Disable CSS (content order logical?)

### Automated Tools
- [ ] axe DevTools browser extension
- [ ] WAVE evaluation tool
- [ ] Lighthouse accessibility audit
- [ ] Pa11y CLI scanner

### Screen Readers
- **Windows:** NVDA (free), JAWS (paid)
- **Mac:** VoiceOver (built-in, Cmd+F5)
- **Linux:** Orca

---

## 📚 RESOURCES

- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM Articles](https://webaim.org/articles/)
- [A11Y Project Checklist](https://www.a11yproject.com/checklist/)
- [WordPress Accessibility Handbook](https://make.wordpress.org/accessibility/handbook/)

---

**Audit Date:** 2026-01-09
**Auditor:** Claude Code - A11y Check
**Standard:** WCAG 2.1 Level AA
```

## Output
Report completo con:
1. **Analisi per principio WCAG**: Perceivable, Operable, Understandable, Robust
2. **Issue specifici**: line number, problema, impact, WCAG ref
3. **Fix dettagliati**: codice corretto funzionante
4. **Score categorizzato**: per area WCAG
5. **Priorità**: critical, high, improvements
6. **Testing guide**: tool e metodologia
7. **Resources**: link documentazione

## Agente Consigliato
Questo comando utilizza l'agente:
- `frontend-a11y` per expertise accessibilità e WCAG compliance
