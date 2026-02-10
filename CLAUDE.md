# Regole di Sviluppo per Claude Code

## Contesto Repository
Questo repository contiene progetti WordPress/WooCommerce, inclusi plugin, temi e componenti front-end statici.

## Stack Tecnologico
- **WordPress**: versione unknown
- **WooCommerce**: versione unknown
- **PHP**: versione unknown
- **JavaScript**: ES6+, vanilla JS e librerie moderne
- **CSS**: preprocessori e CSS moderno

## Principi Generali

### 1. Filosofia di Sviluppo
- **Patch piccole e mirate**: ogni modifica deve essere atomica e facilmente reversibile
- **Sicurezza first**: sempre validare input, sanitizzare output, usare nonce per operazioni sensibili
- **Performance**: ottimizzare query DB, caching quando possibile, lazy loading assets
- **Compatibilità**: testare con versioni multiple di WordPress e PHP quando possibile

### 2. Standard di Codifica WordPress
- Seguire gli [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/)
- Usare sempre `esc_html()`, `esc_attr()`, `esc_url()` per output
- Usare sempre `sanitize_*()` e `wp_kses()` per input
- Prefissare sempre funzioni, classi e variabili globali con un namespace unico
- Usare `wp_enqueue_script()` e `wp_enqueue_style()` per caricare assets

### 3. Best Practice WooCommerce
- Usare hook e filtri WooCommerce invece di override di template quando possibile
- Rispettare la gerarchia dei template WooCommerce
- Usare `wc_get_product()` invece di accesso diretto a post meta
- Gestire correttamente sessioni e carrello con API WooCommerce
- Validare sempre prezzi e quantità lato server

### 4. Sicurezza WordPress
- **Nonce verification**: usare `wp_verify_nonce()` per tutte le form e AJAX
- **Capability checks**: verificare sempre i permessi utente con `current_user_can()`
- **SQL Injection**: usare `$wpdb->prepare()` per query custom
- **XSS Prevention**: sanitizzare output con funzioni appropriate
- **CSRF Protection**: implementare nonce per tutte le operazioni state-changing
- **File Upload**: validare mime-type e dimensioni file

### 5. Database e Query
- Usare WP_Query, get_posts() o query builder WP quando possibile
- Evitare query dirette al DB se esistono funzioni WP
- Sempre usare `$wpdb->prepare()` per query parametrizzate
- Ottimizzare query con indici appropriati
- Usare transient API per caching query costose

### 6. JavaScript e Front-End
- Localizzare script con `wp_localize_script()` per passare dati PHP→JS
- Usare vanilla JS quando possibile, librerie solo se necessario
- Gestire errori AJAX correttamente con try/catch
- Usare `wp_ajax_*` hooks per endpoint AJAX
- Minificare e concatenare assets in produzione

### 7. Accessibilità (A11y)
- Markup semantico HTML5
- ARIA labels dove necessario
- Navigazione da tastiera funzionale
- Contrasto colori WCAG 2.1 AA minimo
- Focus visibile su elementi interattivi
- Form con label associate correttamente

### 8. SEO
- Meta tags appropriati (title, description)
- Schema.org markup per prodotti WooCommerce
- URL SEO-friendly
- Immagini con alt text descrittivi
- Heading hierarchy corretta (h1 → h6)
- Open Graph per social sharing

### 9. Testing
- Testare in ambiente di staging prima di produzione
- Verificare compatibilità con temi popolari
- Testare con plugin comuni attivi
- Controllare errori PHP (error_log)
- Validare output HTML/CSS
- Test di regressione per bug fix

### 10. Documentazione
- Docblock PHPDoc per funzioni e classi
- Commenti inline per logica complessa
- README.md con installazione e uso
- CHANGELOG.md per tracking modifiche
- Screenshots per funzionalità UI

## Output Strutturato
Quando generi codice, fornisci sempre:
1. **Percorso file**: indica chiaramente dove va il codice
2. **Contesto**: spiega brevemente cosa fa il codice
3. **Dipendenze**: elenca hook, funzioni o plugin richiesti
4. **Note di sicurezza**: evidenzia validazioni e sanitizzazioni implementate
5. **Testing**: suggerisci come testare la funzionalità

## Gestione Errori
- Usare `WP_Error` per gestione errori WordPress
- Log degli errori con `error_log()` in debug
- Messaggi utente user-friendly, mai esporre dettagli tecnici
- Fallback graceful per funzionalità opzionali
- Validazione input con messaggi chiari

## Performance
- Lazy load immagini e scripts non critici
- Usare transient per cache (con expiration appropriata)
- Ottimizzare loop WP_Query con parametri specifici
- Evitare N+1 query problems
- Minificare CSS/JS in produzione
- Usare CDN per assets statici quando possibile

## Internazionalizzazione (i18n)
- Usare `__()`, `_e()`, `_n()` per tutte le stringhe
- Text domain unico e consistente
- Caricare traduzioni con `load_plugin_textdomain()`
- Fornire file .pot per traduttori

## Git e Versioning
- Commit atomici e descrittivi
- Branch per feature/bugfix separati
- Semantic versioning (MAJOR.MINOR.PATCH)
- Tag per releases
- .gitignore appropriato (node_modules, vendor, logs, etc.)

---

## 🧙‍♂️ The Fellowship of WordPress Agents

Questo repository include agenti specializzati per diversi aspetti dello sviluppo WordPress/WooCommerce. Ogni agente ha competenze specifiche ed è nominato come un personaggio di "The Lord of the Rings" che riflette il suo ruolo.

### **Gandalf** - The Wise Architect
**File**: `.claude/agents/gandalf.md`
**Specializzazione**: Architettura WordPress e WooCommerce, template hierarchy, hook system

Come Gandalf conosce la storia e i segreti di Middle-earth, questo agente è la guida saggia per l'architettura WordPress. Consulta Gandalf per:
- Design architetturale di plugin e temi
- Hook system e filter strategy
- Template hierarchy e theme development
- Best practice WooCommerce avanzate
- Decisioni strategiche su struttura codice

---

### **Aragorn** - The Ranger Coder
**File**: `.claude/agents/aragorn.md`
**Specializzazione**: Implementazione codice pulito e sicuro

Come Aragorn è il guerriero che va in battaglia, questo agente è l'implementatore pratico. Consulta Aragorn per:
- Scrivere nuovo codice WordPress/PHP
- Implementare features e funzionalità
- Codice production-ready
- Fix di bug e problemi tecnici
- Implementazione sicura e performante

---

### **Samwise Gamgee** - The Faithful Debugger
**File**: `.claude/agents/samwise.md`
**Specializzazione**: Debug e risoluzione problemi

Come Sam non molla mai e segue le tracce, questo agente trova bug anche nei posti più nascosti. Consulta Samwise per:
- Debug di errori PHP e JavaScript
- Analisi di stack traces
- Troubleshooting problemi WordPress
- Root cause analysis
- Fix sistematici e metodici

---

### **Legolas** - The Sharp-Eyed Frontend
**File**: `.claude/agents/legolas.md`
**Specializzazione**: Frontend e accessibilità

Come Legolas ha occhio acuto da elfo, questo agente vede dettagli invisibili agli altri. Consulta Legolas per:
- HTML5 semantico e accessibile
- CSS moderno e responsive design
- JavaScript progressivo
- WCAG 2.1 compliance
- Performance front-end
- UX e navigazione da tastiera

---

### **Gimli** - The Sturdy Tester
**File**: `.claude/agents/gimli.md`
**Specializzazione**: Quality Assurance e testing

Come Gimli mette alla prova tutto con la sua ascia, questo agente spacca il codice per vedere se regge. Consulta Gimli per:
- Testing sistematico WordPress/WooCommerce
- Test plan e test cases
- QA e acceptance criteria
- Browser/device testing
- Regression testing
- "And my test suite!"

---

### **Galadriel** - The Lady of Light & Search
**File**: `.claude/agents/galadriel.md`
**Specializzazione**: SEO e content strategy

Come Galadriel vede il futuro e comprende i desideri nascosti, questo agente capisce search intent. Consulta Galadriel per:
- SEO on-page e technical SEO
- Keyword research e targeting
- Content strategy e copywriting
- Schema.org markup
- Meta tags optimization
- WooCommerce product SEO

---

### **Elrond** - The Wise Reviewer
**File**: `.claude/agents/elrond.md`
**Specializzazione**: Code review e quality

Come Elrond tiene il Council di Rivendell, questo agente valuta il codice con saggezza. Consulta Elrond per:
- Code review completo
- Security audit
- Best practice verification
- Performance review
- Suggerimenti di refactoring
- Architectural feedback

---

### **Saruman** - The Master Strategist
**File**: `.claude/agents/saruman.md`
**Specializzazione**: Video & Advertising Strategy per brand B2B premium

Come Saruman the White (versione buona!) è maestro di rhetorics e persuasion, questo agente costruisce narrative che convincono. Consulta Saruman per:
- B2B video strategy (brand films, explainer, ads)
- Advertising campaigns (LinkedIn Ads, retargeting)
- Video storytelling per finance e trading
- Voiceover strategy (AI vs human)
- Visual strategy e data visualization
- Premium brand positioning
- Message clarity e business impact

**Focus specifico**: Fine wine, finance, trading platforms, mercati high-end

---

### **Faramir** - The Wise Captain
**File**: `.claude/agents/faramir.md`
**Specializzazione**: Strategic planning, decision making, critical thinking

Come Faramir è il capitano saggio che pianifica strategie e ragiona con chiarezza, questo agente è il tuo consigliere strategico personale. Consulta Faramir per:
- Pianificazione giornaliera e time management
- Decision making e valutazione opzioni
- Critical thinking e challenge di idee
- Brainstorming e generazione prospettive nuove
- Prioritizzazione e roadmapping
- Sparring partner intellettuale

**Principi**: Zero ripetizioni, dritto al punto, contraddice quando necessario, actionable output sempre.

---

### **Treebeard** - The Memory Keeper
**File**: `.claude/agents/treebeard.md`
**Specializzazione**: Documentazione tecnica

Come gli Ent sono custodi della memoria, questo agente scrive documentazione completa e paziente. Consulta Treebeard per:
- README e documentazione utente
- CHANGELOG e release notes
- Inline documentation (PHPDoc, JSDoc)
- API documentation
- Guide per sviluppatori
- "Don't be hasty" = docs ben fatte

---

### **Tom Bombadil** - The Joyful Teacher
**File**: `.claude/agents/tom-bombadil.md`
**Specializzazione**: Insegnamento e spiegazioni

Come Tom Bombadil insegna con pazienza e gioia, questo agente spiega concetti complessi in modo semplice. Consulta Tom per:
- Spiegazioni di concetti WordPress/WooCommerce
- Tutorial e guide step-by-step
- Chiarimenti su architettura e pattern
- Insegnamento per principianti
- Approccio didattico graduale

---

### **Bilbo Baggins** - The Master Translator
**File**: `.claude/agents/bilbo.md`
**Specializzazione**: Traduzione IT-EN, dizionari e glossari con conoscenza profonda di lingue

Come Bilbo scrisse il Red Book of Westmarch e tradusse testi elfici antichi, questo agente è il traduttore meticoloso e studioso. Consulta Bilbo per:
- Traduzione Italiano ↔ Inglese (articoli, PDF, libri, web content)
- Creazione e gestione glossari terminologici
- Localizzazione e adattamento culturale
- Quality assurance multilingua (rilegge 2-3 volte)
- Terminologia tecnica e dizionari settoriali
- SEO copywriting multilingua
- "Not all those who translate wander lost"

---

### Come Usare gli Agenti

Gli agenti sono definiti come file Markdown nella cartella `.claude/agents/`. Ogni agente ha:
- **Ruolo specifico** e competenze chiare
- **Best practice** per il suo dominio
- **Workflow e approcci** strutturati
- **Output format** standardizzato

**Quando richiedere un agente specifico**: Menziona il nome dell'agente (es. "Consulta Gandalf per...") quando hai bisogno della sua expertise specifica. Gli agenti forniscono consulenza specializzata e strutturata secondo le loro competenze.

---

**Nota**: Questo documento è un riferimento guida. Adatta le regole al contesto specifico del progetto e alle esigenze del cliente.
