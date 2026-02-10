---
name: tests-plan
description: Genera test plan completo con test case, acceptance criteria e automation strategy
---

# Comando: Tests Plan

## Scopo
Crea piano di testing completo per feature WordPress/WooCommerce, includendo test case manuali, criteri di accettazione, test automatizzati (unit, integration, e2e) e strategia QA.

## Utilizzo
```bash
/tests-plan $ARGUMENTS
```

**Esempi:**
- `/tests-plan feature: "custom checkout field"`
- `/tests-plan type: regression scope: cart-checkout`
- `/tests-plan bug-fix: "issue #42 cart quantity update"`
- `/tests-plan release: "v2.0.0" full-regression`

## Workflow

### 1. Analizza Scope
Da `$ARGUMENTS`:
- **Feature nuova**: test plan completo da zero
- **Bug fix**: regression test + fix verification
- **Refactor**: test esistenti + performance benchmark
- **Release**: full regression + smoke test

### 2. Genera Test Plan

```markdown
# Test Plan: Custom Delivery Instructions Field

**Feature:** Aggiunta campo "Istruzioni di consegna" al checkout WooCommerce
**Version:** 1.3.0
**Test Lead:** Nome QA
**Date:** 2026-01-09
**Environment:** Staging (https://staging.example.com)

---

## 📋 Obiettivi Testing

### In Scope
- ✅ Visualizzazione campo su pagina checkout
- ✅ Validazione input (max 500 caratteri)
- ✅ Salvataggio dati nell'ordine
- ✅ Visualizzazione admin nella pagina ordine
- ✅ Compatibilità temi popolari (Storefront, Astra)
- ✅ Compatibilità plugin checkout (Stripe, PayPal)
- ✅ Responsive mobile e tablet
- ✅ Accessibilità keyboard e screen reader

### Out of Scope
- ❌ Integrazione con corrieri esterni (future release)
- ❌ Traduzioni lingue diverse da IT/EN (v1.4.0)
- ❌ Email notification customization (separate feature)

---

## 🎯 Test Strategy

### Testing Levels

**1. Unit Testing (Sviluppatore)**
- Funzioni validation input
- Funzioni sanitization
- Helper functions

**2. Integration Testing (QA + Dev)**
- Integrazione con checkout flow WooCommerce
- Salvataggio order meta
- Display admin order page

**3. Functional Testing (QA)**
- User flow completo checkout
- Edge cases e error handling
- Cross-browser testing

**4. Non-Functional Testing (QA)**
- Performance (caricamento pagina)
- Accessibilità (WCAG 2.1 AA)
- Security (XSS, SQL injection)

**5. User Acceptance Testing (Stakeholder)**
- Business requirements verificati
- Usability feedback
- Sign-off finale

---

## 🧪 Test Cases

### TC-001: Display Campo Checkout

**Priority:** P0 - Critical
**Type:** Functional
**Preconditions:**
- User logged o guest
- Carrello contiene almeno 1 prodotto
- Indirizzo billing compilato

**Steps:**
1. Aggiungi prodotto al carrello
2. Vai a checkout: `https://example.com/checkout`
3. Scrolla fino alla sezione "Istruzioni consegna"

**Expected Result:**
- ✅ Campo "Istruzioni di consegna" visibile
- ✅ Placeholder text: "Es: Citofono rotto, chiamare al cellulare"
- ✅ Label chiaro e associato a input (for/id match)
- ✅ Campo textarea (non input text)
- ✅ Character counter "0/500" visibile sotto campo

**Test Data:**
- N/A

**Attachments:**
- Screenshot campo: `TC-001-expected.png`

---

### TC-002: Validazione Max Length

**Priority:** P0 - Critical
**Type:** Functional - Negative Testing
**Preconditions:**
- User su pagina checkout
- Campo "Istruzioni consegna" visibile

**Steps:**
1. Compila campo con testo 501 caratteri:
   ```
   Lorem ipsum dolor sit amet, consectetur adipiscing elit... (501 chars)
   ```
2. Click "Procedi con l'ordine"

**Expected Result:**
- ❌ Ordine NON procede
- ✅ Messaggio errore: "Istruzioni troppo lunghe (massimo 500 caratteri)"
- ✅ Campo evidenziato in rosso (border-color: #e74c3c)
- ✅ Focus torna su campo con errore
- ✅ Contatore mostra "501/500" in rosso

**Test Data:**
- `test-data/long-text-501-chars.txt`

---

### TC-003: Caratteri Speciali e HTML

**Priority:** P0 - Critical (Security)
**Type:** Security Testing
**Preconditions:**
- User su pagina checkout

**Steps:**
1. Compila campo con:
   ```html
   <script>alert('XSS')</script>
   Testo normale & caratteri àèéìòù
   Emoji: 🏠🔑
   ```
2. Completa ordine

**Expected Result:**
- ✅ Ordine completato con successo
- ✅ HTML tags stripped (sanitizzato)
- ✅ Caratteri accentati preservati
- ✅ Emoji preservati
- ✅ In admin ordine, visualizza testo sanitizzato (no script execution)

**Test Data:**
- `test-data/malicious-input.txt`

**Security Check:**
- [ ] No alert popup (XSS prevented)
- [ ] View page source: no `<script>` tag in HTML
- [ ] Browser console: no JS errors

---

### TC-004: Campo Vuoto (Opzionale)

**Priority:** P1 - High
**Type:** Functional
**Preconditions:**
- User su pagina checkout

**Steps:**
1. NON compilare campo "Istruzioni consegna" (lascia vuoto)
2. Compila tutti altri campi obbligatori
3. Click "Procedi con l'ordine"

**Expected Result:**
- ✅ Ordine completato con successo (campo opzionale)
- ✅ In admin, campo non visualizzato (o mostra "N/A")
- ✅ No warning o errori

---

### TC-005: Salvataggio Dati Ordine

**Priority:** P0 - Critical
**Type:** Integration Testing
**Preconditions:**
- User su pagina checkout

**Steps:**
1. Compila campo con: "Consegna dopo le 18:00, citofono 3° piano"
2. Completa ordine (payment test mode)
3. Vai in admin: WooCommerce → Ordini
4. Apri ordine appena creato
5. Scrolla a sezione "Istruzioni di consegna"

**Expected Result:**
- ✅ Campo "Istruzioni di consegna" visualizzato in admin
- ✅ Testo preservato esattamente come inserito
- ✅ Formattazione preservata (a capo, spazi)
- ✅ Sezione posizionata sotto "Billing Address"

**Database Verification:**
```sql
SELECT meta_value FROM wp_postmeta
WHERE post_id = {order_id}
AND meta_key = '_delivery_instructions';
```
- ✅ Record esistente con testo corretto

---

### TC-006: Checkout Guest vs Registered

**Priority:** P1 - High
**Type:** Functional
**Preconditions:**
- Carrello contiene prodotti

**Steps:**
**Test 6A - Guest Checkout:**
1. Logout (se logged)
2. Checkout come guest
3. Compila campo istruzioni: "Test guest"
4. Completa ordine

**Test 6B - Registered User:**
1. Login con account registrato
2. Checkout
3. Compila campo istruzioni: "Test registered"
4. Completa ordine

**Expected Result (Both):**
- ✅ Campo funziona identicamente per guest e registered
- ✅ Dati salvati correttamente in entrambi i casi
- ✅ In admin, entrambi ordini mostrano istruzioni

---

### TC-007: Compatibilità Payment Gateway

**Priority:** P0 - Critical
**Type:** Integration Testing
**Preconditions:**
- Payment gateway configurati: Stripe, PayPal, COD

**Steps:**
1. Aggiungi prodotto, vai a checkout
2. Compila campo istruzioni: "Test gateway X"
3. Seleziona payment method: **Stripe**
4. Completa ordine
5. Ripeti con **PayPal**
6. Ripeti con **Cash on Delivery**

**Expected Result:**
- ✅ Campo visibile con tutti gateway
- ✅ Dati salvati indipendentemente da gateway
- ✅ No conflitti JS con gateway scripts
- ✅ Ordine completato per tutti gateway

---

### TC-008: Mobile Responsive

**Priority:** P1 - High
**Type:** Non-Functional (UI/UX)
**Preconditions:**
- User su mobile device (o DevTools mobile view)

**Test Devices:**
- iPhone 13 (390x844)
- Samsung Galaxy S21 (360x800)
- iPad (768x1024)

**Steps:**
1. Apri checkout su mobile
2. Scrolla a campo istruzioni
3. Tap su campo (apre keyboard)
4. Digita testo lungo (200+ chars)
5. Scrolla pagina

**Expected Result:**
- ✅ Campo visibile e usabile senza zoom
- ✅ Textarea width: 100% container (no overflow)
- ✅ Touch target > 44x44px (accessibility)
- ✅ Keyboard non copre campo (viewport adjust)
- ✅ Character counter visibile
- ✅ Layout non rotto in portrait/landscape

**Screenshot:**
- `TC-008-mobile-portrait.png`
- `TC-008-mobile-landscape.png`

---

### TC-009: Accessibilità Keyboard

**Priority:** P1 - High
**Type:** Accessibility (WCAG 2.1 AA)
**Preconditions:**
- User su pagina checkout
- No mouse (solo keyboard)

**Steps:**
1. Naviga checkout con Tab key
2. Tab fino a campo "Istruzioni consegna"
3. Verifica focus visible
4. Digita testo
5. Tab out

**Expected Result:**
- ✅ Campo raggiungibile via Tab
- ✅ Focus outline visibile (min 2px, contrasto 3:1)
- ✅ Tab order logico (dopo billing fields, prima payment)
- ✅ No keyboard trap
- ✅ Enter key NON submits form (textarea multiline)

**Screen Reader Test (NVDA/VoiceOver):**
- ✅ Label annunciato: "Istruzioni di consegna, campo testo opzionale"
- ✅ Placeholder annunciato
- ✅ Character counter annunciato via aria-live

---

### TC-010: Performance Impact

**Priority:** P2 - Medium
**Type:** Non-Functional (Performance)
**Preconditions:**
- Staging environment
- Browser DevTools Network/Performance tab open

**Steps:**
1. Measure checkout page load time SENZA feature
2. Attiva feature
3. Measure checkout page load time CON feature
4. Ripeti 5 volte, calcola media

**Expected Result:**
- ✅ Impact < 100ms page load time
- ✅ No additional HTTP requests
- ✅ JS bundle size increase < 5KB
- ✅ Lighthouse score impact < 2 points

**Metrics:**
- Before: LCP 2.1s, FID 80ms
- After: LCP 2.15s (+50ms ✅), FID 82ms (+2ms ✅)

---

### TC-011: Error Handling - Server Error

**Priority:** P2 - Medium
**Type:** Error Handling
**Preconditions:**
- Simula server error (500) durante submit

**Steps:**
1. Compila checkout correttamente
2. **Simula:** Disabilita temporaneamente endpoint save order
3. Click "Procedi con l'ordine"

**Expected Result:**
- ✅ Errore generico mostrato: "Errore durante ordine, riprova"
- ✅ Dati form NON persi (persistiti in session/localStorage)
- ✅ User può riprovare senza re-compilare tutto
- ✅ Log error lato server (per debug)

---

## 📊 Test Matrix: Cross-Browser / Cross-Device

| Test Case | Chrome | Firefox | Safari | Edge | iOS Safari | Android Chrome |
|-----------|--------|---------|--------|------|------------|----------------|
| TC-001    | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| TC-002    | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| TC-003    | ✅ | ✅ | ✅ | ✅ | ⏳ | ⏳ |
| TC-004    | ✅ | ✅ | ✅ | ✅ | ⏳ | ⏳ |
| TC-005    | ✅ | ⏳ | ⏳ | ⏳ | N/A | N/A |
| TC-008    | N/A | N/A | N/A | N/A | ✅ | ✅ |

**Legend:**
- ✅ Pass
- ❌ Fail
- ⏳ Not tested yet
- N/A Not applicable

---

## 🤖 Test Automation

### Unit Tests (PHPUnit)

```php
/**
 * Test validation logic
 */
class Test_Delivery_Instructions extends WP_UnitTestCase {

    public function test_validate_max_length() {
        $short_text = str_repeat('a', 500);
        $long_text = str_repeat('a', 501);

        $this->assertTrue(nomeplugin_validate_instructions($short_text));
        $this->assertFalse(nomeplugin_validate_instructions($long_text));
    }

    public function test_sanitize_html() {
        $malicious = '<script>alert("xss")</script>Testo normale';
        $sanitized = nomeplugin_sanitize_instructions($malicious);

        $this->assertStringNotContainsString('<script>', $sanitized);
        $this->assertStringContainsString('Testo normale', $sanitized);
    }

    public function test_save_to_order_meta() {
        $order_id = $this->create_test_order();
        $instructions = 'Test delivery instructions';

        nomeplugin_save_instructions($order_id, $instructions);

        $saved = get_post_meta($order_id, '_delivery_instructions', true);
        $this->assertEquals($instructions, $saved);
    }
}
```

**Run:**
```bash
phpunit --filter Test_Delivery_Instructions
```

---

### E2E Tests (Cypress)

```javascript
// cypress/integration/checkout-delivery-instructions.spec.js

describe('Checkout - Delivery Instructions', () => {
    beforeEach(() => {
        // Setup: Aggiungi prodotto al carrello
        cy.addProductToCart('simple-product');
        cy.visit('/checkout');
    });

    it('TC-001: Campo visibile su checkout', () => {
        cy.get('#delivery_instructions')
            .should('be.visible')
            .and('have.attr', 'placeholder')
            .and('contain', 'Citofono');

        cy.get('label[for="delivery_instructions"]')
            .should('contain', 'Istruzioni di consegna');
    });

    it('TC-002: Validazione max 500 caratteri', () => {
        const longText = 'a'.repeat(501);

        cy.get('#delivery_instructions').type(longText);
        cy.get('#place_order').click();

        // Attendi validazione
        cy.get('.woocommerce-error')
            .should('be.visible')
            .and('contain', 'troppo lunghe');

        cy.get('#delivery_instructions')
            .should('have.class', 'error');
    });

    it('TC-003: XSS prevention', () => {
        const xssPayload = '<script>window.xssTriggered=true</script>';

        cy.get('#delivery_instructions').type(xssPayload);
        cy.fillCheckoutFields(); // Helper compila altri campi
        cy.get('#place_order').click();

        // Verifica ordine completato
        cy.url().should('include', '/order-received/');

        // Verifica XSS non eseguito
        cy.window().then((win) => {
            expect(win.xssTriggered).to.be.undefined;
        });
    });

    it('TC-005: Salvataggio in admin ordine', () => {
        const instructions = 'Consegna dopo le 18:00';

        cy.get('#delivery_instructions').type(instructions);
        cy.fillCheckoutFields();
        cy.get('#place_order').click();

        // Estrai order ID da URL
        cy.url().then((url) => {
            const orderId = url.match(/order-received\/(\d+)/)[1];

            // Login come admin
            cy.loginAsAdmin();
            cy.visit(`/wp-admin/post.php?post=${orderId}&action=edit`);

            // Verifica campo presente
            cy.contains('Istruzioni di consegna')
                .parent()
                .should('contain', instructions);
        });
    });
});
```

**Run:**
```bash
npx cypress run --spec "cypress/integration/checkout-delivery-instructions.spec.js"
```

---

## ✅ Acceptance Criteria

Feature è accettata se:

### Functional
- [x] Campo visibile su checkout per tutti i user (guest/registered)
- [x] Validazione max 500 caratteri funziona
- [x] Input sanitizzato (no XSS)
- [x] Dati salvati correttamente in order meta
- [x] Visualizzazione in admin order page
- [x] Compatibile con payment gateway standard (Stripe, PayPal, COD)

### Non-Functional
- [x] Performance: < 100ms impact su page load
- [x] Accessibilità: WCAG 2.1 AA compliant
- [x] Mobile: Responsive su device standard (320px+)
- [x] Cross-browser: Funziona su Chrome, Firefox, Safari, Edge
- [x] Security: Pass security audit (no vulnerabilities)

### Documentation
- [x] README aggiornato con feature
- [x] PHPDoc completo per funzioni
- [x] User guide per admin

### Testing
- [x] Code coverage > 80%
- [x] All test cases PASS
- [x] No critical/high bugs aperti

---

## 🐛 Bug Tracking

### Critical Bugs (Block Release)
*Nessuno al momento*

### High Priority Bugs
- **BUG-127**: Character counter non aggiorna real-time su Safari
  - Status: In Progress
  - Assigned: Developer X
  - ETA: 2026-01-11

### Medium/Low Bugs
- **BUG-128**: Placeholder text cut off su mobile landscape (minor UX)
  - Status: Backlog
  - Priority: P3

---

## 📈 Test Metrics

### Execution Progress
- **Total Test Cases:** 11
- **Executed:** 9 (82%)
- **Passed:** 8 (89%)
- **Failed:** 1 (11%)
- **Blocked:** 1 (9%)
- **Not Run:** 2 (18%)

### Coverage
- **Requirements Coverage:** 95% (19/20 requirements)
- **Code Coverage (Unit):** 87%
- **E2E Coverage:** 70% (core flows)

### Defects
- **Critical:** 0
- **High:** 1
- **Medium:** 1
- **Low:** 3
- **Defect Density:** 0.45 bugs/KLOC

---

## 🚀 Release Checklist

Prima del deploy produzione:

### Development
- [ ] All code merged to `main` branch
- [ ] Version bumped in plugin header (1.3.0)
- [ ] CHANGELOG.md updated

### Testing
- [ ] All P0/P1 test cases PASS
- [ ] No critical/high bugs open
- [ ] Regression test suite PASS
- [ ] Performance benchmarks meet criteria
- [ ] Accessibility audit PASS (WAVE, axe)
- [ ] Security scan PASS (WPScan, Snyk)

### Documentation
- [ ] README.md updated
- [ ] User guide published
- [ ] API documentation generated (PHPDoc)
- [ ] Video tutorial recorded (optional)

### Deployment
- [ ] Backup production DB e files
- [ ] Deploy to staging
- [ ] Smoke test staging
- [ ] Stakeholder sign-off
- [ ] Deploy to production
- [ ] Smoke test production
- [ ] Monitor error logs 24h

### Post-Release
- [ ] Announcement blog post
- [ ] Email users (changelog)
- [ ] Update WordPress.org repo (if applicable)
- [ ] Monitor support tickets

---

## 👥 Test Team

| Role | Name | Responsibilities |
|------|------|------------------|
| QA Lead | Nome QA | Test planning, execution, sign-off |
| Developer | Nome Dev | Unit tests, bug fixes |
| Product Owner | Nome PO | Requirements, acceptance criteria |
| DevOps | Nome Ops | Test env setup, CI/CD |

---

## 📅 Timeline

| Phase | Start | End | Duration |
|-------|-------|-----|----------|
| Test Planning | 2026-01-08 | 2026-01-09 | 2d |
| Test Execution | 2026-01-10 | 2026-01-12 | 3d |
| Bug Fixing | 2026-01-13 | 2026-01-15 | 3d |
| Regression | 2026-01-16 | 2026-01-16 | 1d |
| Sign-off | 2026-01-17 | 2026-01-17 | 1d |
| **Release** | **2026-01-18** | | |

---

**Test Plan Version:** 1.0
**Last Updated:** 2026-01-09
**Status:** 🟢 On Track
```

## Output
Test plan completo con:
1. **Test Strategy**: levels, scope, priorities
2. **Test Cases**: dettagliati con step, expected results, test data
3. **Test Matrix**: cross-browser/device coverage
4. **Automation**: unit tests (PHPUnit), E2E (Cypress)
5. **Acceptance Criteria**: checklist feature-ready
6. **Bug Tracking**: template e metrics
7. **Release Checklist**: pre/post deployment
8. **Timeline**: schedule testing phases

## Agente Consigliato
Questo comando utilizza l'agente:
- `qa-tester` per test planning, case design e quality assurance strategy
