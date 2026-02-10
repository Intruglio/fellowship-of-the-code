---
name: seo-page-draft
description: Genera draft contenuto SEO-optimized per pagine WordPress con meta tags e schema
---

# Comando: SEO Page Draft

## Scopo
Genera bozza contenuto ottimizzato SEO per pagine WordPress, inclusi meta tags, schema markup, heading structure e copy persuasivo basato su keyword research.

## Utilizzo
```bash
/seo-page-draft $ARGUMENTS
```

**Esempi:**
- `/seo-page-draft keyword: "scarpe running uomo" type: product-category`
- `/seo-page-draft page: about-us target: local-business`
- `/seo-page-draft product: "iPhone 15 Pro" focus: conversione`
- `/seo-page-draft blog-post: "come scegliere hosting wordpress"`

## Workflow

### 1. Analizza Input
Da `$ARGUMENTS` estrai:
- **Keyword target**: principale + secondarie
- **Tipo pagina**: homepage, product, category, blog post, about, contact
- **Intento ricerca**: informational, transactional, navigational
- **Audience**: chi leggerà (tecnici, consumer, B2B, etc.)
- **Competitor** (opzionale): URL competitor da analizzare

### 2. Keyword Research & Strategy

```markdown
## 🎯 SEO Content Strategy

**Target Keyword:** scarpe running uomo
**Search Volume:** 8,100/month (Italy)
**Keyword Difficulty:** Medium (45/100)
**Search Intent:** Commercial Investigation + Transactional

---

### Keyword Cluster

**Primary Keyword:**
- scarpe running uomo (8,100/mo)

**Secondary Keywords:**
- scarpe corsa uomo (2,900/mo)
- scarpe running uomo offerte (1,300/mo)
- migliori scarpe running uomo (880/mo)
- scarpe running ammortizzate uomo (590/mo)
- scarpe running professionali (480/mo)

**Long-Tail Keywords:**
- scarpe running uomo nike (720/mo)
- scarpe running uomo adidas (590/mo)
- scarpe running uomo asics (390/mo)
- scarpe running uomo decathlon (320/mo)

---

### Competitor Analysis

**Top 3 SERP:**
1. **RunningWarehouse.it** - Rank #1
   - Title: "Scarpe Running Uomo | Miglior Prezzo Online 2025"
   - Content Length: 2,400 words
   - Schema: Product, BreadcrumbList, Organization
   - Unique: Comparison table, video reviews

2. **Amazon.it** - Rank #2
   - Title: "Scarpe da Running Uomo: Amazon.it"
   - Strong: User reviews, variety
   - Weak: Thin content, no guides

3. **Nike.com** - Rank #3
   - Title: "Scarpe Running Uomo - Nike IT"
   - Strong: Brand authority, high-quality images
   - Weak: Limited product info, no comparisons

**Content Gap Opportunities:**
- ✅ Guida taglie specifiche per modelli
- ✅ Confronto ammortizzazione (pronatore/supinatore)
- ✅ Video unboxing/review
- ✅ Size guide interattivo
- ✅ FAQ dettagliate

---

### Content Angle
**Hook:** "Guida Definitiva + Confronto 2025"
**USP:** Tabella comparativa dettagliata + size guide + video reviews
**CTA:** "Trova la Tua Scarpa Perfetta" (quiz/filter tool)
```

### 3. Meta Tags Optimization

```markdown
## 📝 Meta Tags

### Title Tag
```html
<title>Scarpe Running Uomo 2025 | Guida + Confronto Modelli | NomeNegozio</title>
```
- **Length:** 58 caratteri ✅ (optimal: 50-60)
- **Keyword Position:** Inizio ✅
- **Brand:** Incluso ✅
- **Click Trigger:** Anno corrente, "Guida", "Confronto"

**Alternatives:**
- "Scarpe Running Uomo: Migliori Modelli 2025 + Offerte | NomeNegozio"
- "🏃 Scarpe Running Uomo | Recensioni + Prezzi Migliori 2025"

---

### Meta Description
```html
<meta name="description" content="Scopri le migliori scarpe running uomo 2025. Confronta Nike, Adidas, Asics con la nostra guida dettagliata. Tabella comparativa, size guide e offerte esclusive. ✓ Spedizione gratuita.">
```
- **Length:** 156 caratteri ✅ (optimal: 150-160)
- **Keyword:** Inclusa naturalmente ✅
- **CTA:** "Scopri", "Confronta" ✅
- **Benefit:** "Spedizione gratuita", "Offerte esclusive" ✅
- **Emoji:** ✓ (aumenta CTR) ✅

---

### Open Graph (Social Sharing)
```html
<meta property="og:title" content="Scarpe Running Uomo 2025: Guida Completa + Confronto Modelli">
<meta property="og:description" content="Trova le scarpe running perfette. Confronta 50+ modelli, leggi recensioni reali e approfitta delle offerte esclusive.">
<meta property="og:image" content="https://example.com/images/scarpe-running-uomo-og.jpg">
<meta property="og:url" content="https://example.com/scarpe-running-uomo">
<meta property="og:type" content="website">
<meta property="og:site_name" content="NomeNegozio">
```

### Twitter Card
```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Scarpe Running Uomo 2025 | Guida + Confronto">
<meta name="twitter:description" content="Scopri le migliori scarpe running. Confronta modelli, prezzi e recensioni.">
<meta name="twitter:image" content="https://example.com/images/scarpe-running-twitter.jpg">
```

---

### Canonical & Hreflang
```html
<link rel="canonical" href="https://example.com/scarpe-running-uomo">

<!-- Se sito multilingua -->
<link rel="alternate" hreflang="it" href="https://example.com/it/scarpe-running-uomo">
<link rel="alternate" hreflang="en" href="https://example.com/en/mens-running-shoes">
<link rel="alternate" hreflang="x-default" href="https://example.com/scarpe-running-uomo">
```
```

### 4. Schema Markup

```markdown
## 🔍 Schema.org Structured Data

### 1. Product Category Schema
```json
{
  "@context": "https://schema.org",
  "@type": "CollectionPage",
  "name": "Scarpe Running Uomo",
  "description": "Ampia selezione di scarpe da running per uomo. Nike, Adidas, Asics e altri brand top.",
  "url": "https://example.com/scarpe-running-uomo",
  "breadcrumb": {
    "@type": "BreadcrumbList",
    "itemListElement": [
      {
        "@type": "ListItem",
        "position": 1,
        "name": "Home",
        "item": "https://example.com"
      },
      {
        "@type": "ListItem",
        "position": 2,
        "name": "Scarpe",
        "item": "https://example.com/scarpe"
      },
      {
        "@type": "ListItem",
        "position": 3,
        "name": "Running Uomo",
        "item": "https://example.com/scarpe-running-uomo"
      }
    ]
  },
  "numberOfItems": 127,
  "offers": {
    "@type": "AggregateOffer",
    "priceCurrency": "EUR",
    "lowPrice": "49.99",
    "highPrice": "249.99",
    "offerCount": "127"
  }
}
```

### 2. Organization Schema (per credibilità)
```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "NomeNegozio",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png",
  "sameAs": [
    "https://www.facebook.com/nomenegozio",
    "https://www.instagram.com/nomenegozio",
    "https://twitter.com/nomenegozio"
  ],
  "contactPoint": {
    "@type": "ContactPoint",
    "telephone": "+39-02-1234567",
    "contactType": "Customer Service",
    "availableLanguage": ["Italian"]
  }
}
```

### 3. FAQ Schema (se pagina include FAQ)
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Quali sono le migliori scarpe running per principianti?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Per principianti consigliamo scarpe con buona ammortizzazione e supporto, come Nike Pegasus, Adidas Ultraboost o Asics Gel-Nimbus. Priorità: comfort, stabilità e prezzo accessibile (70-130€)."
      }
    },
    {
      "@type": "Question",
      "name": "Come scegliere la taglia giusta per scarpe running?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Misura il piede la sera (quando è più gonfio), lascia 0.5-1cm di spazio tra alluce e punta scarpa. Prova con calzini da running. Considera che alcune marche vestono più strette (es: Nike) o più larghe (es: Altra)."
      }
    }
  ]
}
```

### Implementazione WordPress
```php
// functions.php
function add_product_category_schema() {
    if (is_product_category('running-uomo')) {
        $schema = array(/* ... schema JSON ... */);
        echo '<script type="application/ld+json">' . json_encode($schema) . '</script>';
    }
}
add_action('wp_head', 'add_product_category_schema');
```
```

### 5. Content Structure

```markdown
## 📄 Content Draft

### Heading Structure (H1-H6)

```html
<h1>Scarpe Running Uomo 2025: Guida Completa + Confronto Modelli</h1>

<!-- Intro Paragraph: 100-150 parole, include keyword primaria 2x -->

<h2>🏃 Migliori Scarpe Running Uomo per Categoria</h2>
  <h3>Miglior Rapporto Qualità-Prezzo</h3>
    <!-- Nike Revolution 6, Adidas Duramo, etc. -->

  <h3>Migliori per Principianti</h3>
    <!-- Asics Gel-Nimbus, Brooks Ghost, etc. -->

  <h3>Migliori per Maratoneti</h3>
    <!-- Nike ZoomX Vaporfly, Adidas Adizero, etc. -->

  <h3>Migliori per Trail Running</h3>
    <!-- Salomon Speedcross, Hoka Speedgoat, etc. -->

<h2>📊 Tabella Comparativa: Top 10 Scarpe Running Uomo</h2>
  <!-- Tabella con: Modello, Prezzo, Peso, Drop, Ammortizzazione, Rating -->

<h2>🛒 Come Scegliere le Scarpe Running Giuste</h2>
  <h3>Tipo di Appoggio (Pronazione)</h3>
  <h3>Peso e Drop</h3>
  <h3>Ammortizzazione vs. Reattività</h3>
  <h3>Terreno e Frequenza di Utilizzo</h3>

<h2>📏 Guida alle Taglie per Brand</h2>
  <h3>Nike - Tende a Vestire Stretto</h3>
  <h3>Adidas - Vestibilità Standard</h3>
  <h3>Asics - Generoso in Lunghezza</h3>

<h2>💰 Dove Comprare: Offerte e Sconti</h2>

<h2>❓ FAQ - Domande Frequenti</h2>
  <h3>Ogni quanto sostituire le scarpe running?</h3>
  <h3>Meglio scarpe ammortizzate o minimaliste?</h3>
  <h3>Posso usare scarpe running per camminare?</h3>

<h2>✅ Conclusioni: La Nostra Scelta</h2>
```

---

### Content Copy (Esempio Sezioni)

#### Introduzione (Above-the-Fold)
```markdown
# Scarpe Running Uomo 2025: Guida Completa + Confronto Modelli

Scegliere le **scarpe running uomo** giuste può fare la differenza tra un allenamento piacevole e un infortunio. Con oltre 200 modelli sul mercato, trovare la scarpa perfetta per il tuo stile di corsa e morfologia del piede può sembrare complesso.

In questa guida abbiamo analizzato e confrontato **i 50 migliori modelli di scarpe da running uomo 2025**, testati dai nostri esperti su oltre 5.000 km. Troverai:

✓ **Confronto dettagliato** per categoria (principianti, maratoneti, trail)
✓ **Tabella comparativa** con caratteristiche tecniche e prezzi
✓ **Guida taglie** specifica per ogni brand
✓ **Video recensioni** dei modelli top
✓ **Offerte esclusive** fino al -40%

👉 **Tempo di lettura:** 8 minuti | **Ultimo aggiornamento:** 9 Gennaio 2026
```

**SEO Elements:**
- ✅ H1 con keyword primaria
- ✅ Keyword density naturale (non stuffing)
- ✅ Bullet points per scannability
- ✅ Benefit-focused (non feature-focused)
- ✅ Social proof ("5.000 km testati")
- ✅ Urgency ("Offerte fino al -40%")
- ✅ Freshness signal (data aggiornamento)

---

#### Sezione Comparativa
```markdown
## 📊 Tabella Comparativa: Top 10 Scarpe Running Uomo

Abbiamo selezionato le **10 migliori scarpe running uomo** in base a prestazioni, comfort, durabilità e rapporto qualità-prezzo.

| Modello | Prezzo | Peso | Drop | Ammortizzazione | Rating | Miglior Per |
|---------|--------|------|------|-----------------|--------|-------------|
| Nike Pegasus 40 | €139 | 280g | 10mm | ⭐⭐⭐⭐ | 4.7/5 | Versatilità |
| Adidas Ultraboost 23 | €189 | 310g | 10mm | ⭐⭐⭐⭐⭐ | 4.6/5 | Comfort |
| Asics Gel-Nimbus 25 | €169 | 295g | 8mm | ⭐⭐⭐⭐⭐ | 4.8/5 | Lunghe distanze |
| Brooks Ghost 15 | €149 | 289g | 12mm | ⭐⭐⭐⭐ | 4.5/5 | Principianti |
| Hoka Clifton 9 | €159 | 245g | 5mm | ⭐⭐⭐⭐⭐ | 4.7/5 | Leggerezza |
| Saucony Ride 16 | €139 | 280g | 8mm | ⭐⭐⭐⭐ | 4.4/5 | Reattività |
| New Balance 880v13 | €149 | 298g | 8mm | ⭐⭐⭐⭐ | 4.5/5 | Stabilità |
| Mizuno Wave Rider 26 | €139 | 275g | 12mm | ⭐⭐⭐⭐ | 4.6/5 | Pronatori |
| On Cloudstratus | €179 | 290g | 7mm | ⭐⭐⭐⭐⭐ | 4.7/5 | Tecnologia |
| Nike ZoomX Invincible | €199 | 325g | 9mm | ⭐⭐⭐⭐⭐ | 4.8/5 | Max Comfort |

[Vedi Confronto Dettagliato] → Link a pagina dedicata o anchor #confronto

### Come Leggere la Tabella

- **Peso:** Ideale 250-300g per running su strada
- **Drop:** Differenza altezza tacco-avampiede (8-12mm = tradizionale, 0-4mm = minimalista)
- **Ammortizzazione:** ⭐⭐⭐⭐⭐ = massima, ⭐⭐⭐ = media, ⭐ = minima
- **Rating:** Media recensioni utenti verificati

💡 **Pro Tip:** Se sei principiante, inizia con drop 10-12mm e buona ammortizzazione (Asics Nimbus o Brooks Ghost).
```

**SEO Elements:**
- ✅ Structured data (Table)
- ✅ Rich content (ratings, specs)
- ✅ Internal linking
- ✅ Keyword variations naturali
- ✅ Call-to-action

---

#### FAQ Section
```markdown
## ❓ Domande Frequenti (FAQ)

### Ogni quanto tempo devo sostituire le scarpe running?

**Risposta:** Generalmente ogni **600-800 km**, o 6-8 mesi per chi corre 3-4 volte/settimana. Segnali di usura:
- Suola esterna consumata (specialmente tallone)
- Ammortizzazione compressa (scarpa sembra "piatta")
- Dolori articolari dopo la corsa

**Consiglio:** Annota i km percorsi su app (Strava, Garmin) o calendario.

---

### Meglio scarpe ammortizzate o minimaliste?

**Risposta:** Dipende dal tuo livello e morfologia:

**Ammortizzate (Drop 10-12mm):** ✅ Ideali per:
- Principianti
- Lunghe distanze
- Sovrappeso
- Terreni duri (asfalto)

**Minimaliste (Drop 0-4mm):** ✅ Ideali per:
- Corridori esperti
- Brevi distanze/velocità
- Terreni morbidi
- Tecnica di corsa ottimale

⚠️ **Transizione graduale** se passi da ammortizzate a minimaliste (rischio infortuni).

---

### Posso usare scarpe running anche per camminare o palestra?

**Risposta:** Sì per **camminata veloce**, no per **palestra pesi**.

- ✅ **Camminata:** Running shoes vanno benissimo, anzi offrono comfort superiore
- ❌ **Palestra pesi:** Troppa ammortizzazione = instabilità durante squat/stacchi. Meglio scarpe piatte (Converse, Nike Metcon)
- ✅ **Tapis roulant:** Perfette

**Eccezione:** Se fai solo cardio (ellittica, cyclette), running shoes ok.

---

### [Altre 5-7 FAQ...]
```

**SEO Elements:**
- ✅ FAQ Schema markup (rich snippet)
- ✅ Long-tail keywords
- ✅ Natural language (voice search)
- ✅ Comprehensive answers
- ✅ Internal link opportunities

---

### Call-to-Action (Conversione)
```markdown
## 🛒 Pronto a Scegliere le Tue Scarpe Running Perfette?

Non lasciare che scarpe sbagliate rovinino i tuoi allenamenti. Investi nelle scarpe giuste e senti la differenza ad ogni falcata.

### 👉 Prossimi Passi:

1. **[Usa il Nostro Quiz Interattivo](#quiz)** - Trova il modello perfetto in 2 minuti
2. **[Confronta Prezzi e Offerte](#offerte)** - Risparmia fino al 40% sui top brand
3. **[Leggi Recensioni Verificate](#recensioni)** - Esperienze reali di altri runner

---

### 🎁 Offerta Esclusiva Lettori

**Codice sconto -15% su Nike e Adidas:** `RUNNING2025`
Valido fino al 31 Gennaio 2026 | Min. spesa €89

[SCOPRI LE OFFERTE →]

---

💬 **Hai Dubbi?** Contatta i nostri esperti runner:
- 📧 Email: [email protected]
- 💬 Live Chat: Disponibile Lun-Ven 9-18
- ☎️ Tel: +39 02 1234567
```

**Conversion Elements:**
- ✅ Clear CTA buttons
- ✅ Limited-time offer (urgency)
- ✅ Social proof (trust)
- ✅ Multiple conversion paths
- ✅ Low friction (quiz, chat)
```

### 6. Internal Linking Strategy

```markdown
## 🔗 Internal Linking Plan

### Outbound Links (da questa pagina)
- [Scarpe Running Donna] → /scarpe-running-donna
- [Abbigliamento Running] → /abbigliamento-running
- [Accessori Running] → /accessori-running-orologi-gps
- [Come Iniziare a Correre] → /blog/guida-principianti-running
- [Allenamento per 10km] → /blog/programma-allenamento-10km

### Inbound Links (verso questa pagina)
- Da [Homepage] → anchor: "Scarpe Running Uomo"
- Da [Blog: Preparare Prima Maratona] → anchor: "migliori scarpe running per maratona"
- Da [Pagina Nike] → anchor: "vedi tutte le scarpe running uomo"
- Da [Guida Taglie] → anchor: "scopri i modelli consigliati"

### Anchor Text Variation
- 🎯 Exact match (20%): "scarpe running uomo"
- 🎯 Partial match (40%): "scarpe da running per uomo", "calzature running maschili"
- 🎯 Branded (20%): "guida NomeNegozio", "scopri su NomeNegozio"
- 🎯 Generic (20%): "clicca qui", "scopri di più", "leggi la guida"
```

### 7. Image Optimization

```markdown
## 🖼 Image SEO

### Hero Image
**Filename:** `scarpe-running-uomo-2025-guida.jpg` (keyword-rich)
**Alt Text:** "Confronto migliori scarpe running uomo 2025 con Nike Adidas Asics"
**Title:** "Guida Scarpe Running Uomo"
**Size:** Max 200KB (compressed WebP)
**Dimensions:** 1200x630px (ottimale per OG)

### Product Images
```html
<img src="nike-pegasus-40-uomo.webp"
     alt="Nike Pegasus 40 scarpa running uomo blu laterale"
     title="Nike Pegasus 40"
     width="600"
     height="400"
     loading="lazy">
```

### Image Schema
```json
{
  "@type": "ImageObject",
  "url": "https://example.com/images/nike-pegasus-40.jpg",
  "caption": "Nike Pegasus 40 - Scarpa running versatile per uomo",
  "width": 600,
  "height": 400
}
```

**Best Practices:**
- ✅ Descriptive filenames (no IMG_1234.jpg)
- ✅ Alt text unique per ogni immagine
- ✅ Compressed (TinyPNG, ImageOptim)
- ✅ Responsive (srcset)
- ✅ Lazy loading (below fold)
```

## Output
Draft completo con:
1. **Keyword Strategy**: primarie, secondarie, long-tail
2. **Competitor Analysis**: gap content opportunities
3. **Meta Tags**: title, description, OG, Twitter
4. **Schema Markup**: JSON-LD per rich snippet
5. **Content Structure**: H1-H6 hierarchy ottimale
6. **Content Copy**: intro, sezioni, FAQ, CTA
7. **Internal Linking**: strategia inbound/outbound
8. **Image SEO**: optimization checklist

## Agente Consigliato
Questo comando utilizza l'agente:
- `seo-copy-content` per strategia SEO e copywriting persuasivo
