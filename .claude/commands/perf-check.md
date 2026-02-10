---
name: perf-check
description: Analisi performance WordPress con focus su Core Web Vitals e ottimizzazioni
---

# Comando: Performance Check

## Scopo
Analizza performance di pagine WordPress/WooCommerce, identifica bottleneck e propone ottimizzazioni concrete per migliorare Core Web Vitals e velocità generale.

## Utilizzo
```bash
/perf-check $ARGUMENTS
```

**Esempi:**
- `/perf-check page: /product/example`
- `/perf-check template: single-product.php`
- `/perf-check site: https://example.com`
- `/perf-check all` (homepage, shop, product, cart, checkout)

## Workflow

### 1. Identifica Target
Da `$ARGUMENTS`:
- URL pagina specifica
- Template file
- "all" per analisi multi-pagina

### 2. Metriche Core Web Vitals

```markdown
## ⚡ Performance Analysis Report

**Target:** https://example.com/product/t-shirt-blue/
**Date:** 2026-01-09
**Tool:** Lighthouse / PageSpeed Insights

---

## 📊 Core Web Vitals

### Largest Contentful Paint (LCP)
**Current:** 4.2s 🔴 POOR
**Target:** < 2.5s (Good), < 4.0s (Needs Improvement)

**What is LCP:** Tempo per rendere il contenuto principale visibile.

**Cause Identified:**
1. **Hero image non ottimizzata** (2.4MB, 3000x2000px)
2. **Render-blocking CSS** (5 file esterni, 450KB total)
3. **No priority hint** su immagine hero

**Fixes:**

#### 1. Ottimizza Immagine Hero
```php
// Prima (BAD):
<img src="hero.jpg" alt="Product">

// Dopo (GOOD):
<img src="hero-800w.webp"
     srcset="hero-400w.webp 400w,
             hero-800w.webp 800w,
             hero-1200w.webp 1200w"
     sizes="(max-width: 600px) 400px,
            (max-width: 1000px) 800px,
            1200px"
     alt="Blue T-Shirt Product"
     width="1200"
     height="800"
     fetchpriority="high"
     loading="eager">
```

**Tool:** ImageMagick per resize + conversione WebP
```bash
convert hero.jpg -resize 800x -quality 85 hero-800w.webp
```

**Expected Impact:** LCP -1.5s → 2.7s

---

#### 2. Inline Critical CSS
```php
// functions.php
function inline_critical_css() {
    if (is_product()) {
        ?>
        <style id="critical-css">
        /* Critical styles per above-the-fold content */
        body { margin: 0; font-family: Arial, sans-serif; }
        .product-hero { display: block; max-width: 100%; }
        .product-title { font-size: 2rem; line-height: 1.2; }
        /* ... altri stili critici ... */
        </style>
        <?php
    }
}
add_action('wp_head', 'inline_critical_css', 1);

// Load full CSS async
function async_non_critical_css() {
    ?>
    <link rel="preload" href="<?php echo get_stylesheet_uri(); ?>" as="style" onload="this.onload=null;this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="<?php echo get_stylesheet_uri(); ?>"></noscript>
    <?php
}
add_action('wp_head', 'async_non_critical_css', 20);
```

**Tool:** Critical CSS Generator - https://github.com/addyosmani/critical

**Expected Impact:** LCP -0.5s → 2.2s ✅

---

### First Input Delay (FID) / Interaction to Next Paint (INP)
**Current FID:** 180ms 🟡 NEEDS IMPROVEMENT
**Current INP:** 320ms 🔴 POOR
**Target FID:** < 100ms | **Target INP:** < 200ms

**What is FID/INP:** Reattività alle interazioni utente.

**Cause Identified:**
1. **JavaScript pesante** bloccando main thread (jQuery plugins: 250KB)
2. **Rendering bloccante** durante parsing JS
3. **Long tasks** > 50ms

**Fixes:**

#### 1. Defer Non-Critical JS
```php
function optimize_js_loading($tag, $handle) {
    // Skip core scripts (jQuery needs special handling)
    $skip_handles = array('jquery-core', 'jquery-migrate');
    if (in_array($handle, $skip_handles)) {
        return $tag;
    }

    // Add defer to all other scripts
    if (strpos($tag, 'defer') === false) {
        $tag = str_replace(' src', ' defer src', $tag);
    }

    return $tag;
}
add_filter('script_loader_tag', 'optimize_js_loading', 10, 2);
```

#### 2. Code Splitting
```javascript
// Prima (BAD): Carica tutto upfront
import heavyLibrary from './heavy-library.js';

// Dopo (GOOD): Dynamic import
document.querySelector('.open-modal').addEventListener('click', async () => {
    const { Modal } = await import('./modal.js');
    const modal = new Modal();
    modal.open();
});
```

#### 3. Riduci Payload JS
**Audit:**
```bash
# Analizza bundle size
npx webpack-bundle-analyzer stats.json
```

**Found:**
- Moment.js: 67KB (use date-fns: 15KB)
- Lodash intera: 70KB (use lodash-es + tree shaking)
- jQuery: 87KB (valuta vanilla JS per nuove feature)

**Expected Impact:** FID → 85ms ✅ | INP → 180ms ✅

---

### Cumulative Layout Shift (CLS)
**Current:** 0.32 🔴 POOR
**Target:** < 0.1 (Good), < 0.25 (Needs Improvement)

**What is CLS:** Stabilità visuale (elementi che "saltano").

**Cause Identified:**
1. **Immagini senza width/height** specificati
2. **Web fonts** causano FOIT (Flash Of Invisible Text)
3. **Ads/Embeds** inject senza placeholder
4. **Dynamic content** inject above fold

**Fixes:**

#### 1. Reserve Space per Immagini
```php
// Prima (BAD):
<img src="product.jpg" alt="Product">

// Dopo (GOOD):
<?php
$image_id = get_post_thumbnail_id();
$image_meta = wp_get_attachment_metadata($image_id);
$width = $image_meta['width'];
$height = $image_meta['height'];
?>
<img src="product.jpg"
     alt="Product"
     width="<?php echo $width; ?>"
     height="<?php echo $height; ?>"
     style="aspect-ratio: <?php echo $width / $height; ?>;">
```

**CSS:**
```css
img {
    max-width: 100%;
    height: auto;
}
```

#### 2. Font Loading Optimization
```php
// functions.php
function optimize_font_loading() {
    ?>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <?php
}
add_action('wp_head', 'optimize_font_loading', 5);
```

**CSS:**
```css
@font-face {
    font-family: 'Inter';
    font-style: normal;
    font-weight: 400;
    font-display: swap; /* Evita FOIT, show fallback subito */
    src: url('/fonts/inter-regular.woff2') format('woff2');
}
```

#### 3. Placeholder per Dynamic Content
```html
<!-- Reserve space per widget che carica async -->
<div class="reviews-widget" style="min-height: 300px;">
    <div class="loading-skeleton">Loading reviews...</div>
</div>
```

**Expected Impact:** CLS → 0.08 ✅

---

## 🚀 Performance Score Summary

| Metric | Before | After | Status |
|--------|--------|-------|--------|
| LCP | 4.2s | 2.2s | 🟢 GOOD |
| FID | 180ms | 85ms | 🟢 GOOD |
| INP | 320ms | 180ms | 🟡 OK |
| CLS | 0.32 | 0.08 | 🟢 GOOD |
| **Overall** | 🔴 45/100 | 🟢 88/100 | +43 points |

---

## 🔍 Additional Performance Issues

### 1. Database Queries
**Status:** 🔴 CRITICAL

**Analysis via Query Monitor:**
```
Total Queries: 127
Slowest Query: 1.8s
Duplicate Queries: 23
```

#### Issue: N+1 Query Problem
**Location:** `templates/related-products.php:45`

```php
// BAD: Query inside loop
$related = get_related_products();
foreach ($related as $product_id) {
    $product = wc_get_product($product_id); // ❌ Query per prodotto
    echo $product->get_name();
}
```

**Fix:**
```php
// GOOD: Bulk query con WP_Query
$args = array(
    'post_type'      => 'product',
    'post__in'       => $related_ids,
    'posts_per_page' => -1,
    'orderby'        => 'post__in'
);
$query = new WP_Query($args); // Single query

if ($query->have_posts()) {
    while ($query->have_posts()) {
        $query->the_post();
        global $product;
        echo $product->get_name();
    }
    wp_reset_postdata();
}
```

**Expected Impact:** Queries 127 → 48, Page load -0.8s

---

### 2. Caching Strategy
**Status:** ❌ NO CACHING IMPLEMENTED

#### Object Caching per Query Costose
```php
function get_bestsellers() {
    $cache_key = 'bestsellers_month_' . date('Y-m');
    $bestsellers = wp_cache_get($cache_key);

    if (false === $bestsellers) {
        // Expensive query
        $bestsellers = expensive_bestseller_query();

        // Cache for 1 hour
        wp_cache_set($cache_key, $bestsellers, '', HOUR_IN_SECONDS);
    }

    return $bestsellers;
}
```

#### Transient API per API Calls
```php
function get_currency_rates() {
    $rates = get_transient('currency_rates');

    if (false === $rates) {
        $response = wp_remote_get('https://api.exchangerate.com/latest');
        $rates = json_decode(wp_remote_retrieve_body($response));

        // Cache 6 hours
        set_transient('currency_rates', $rates, 6 * HOUR_IN_SECONDS);
    }

    return $rates;
}
```

#### Page Caching (Plugin)
**Recommended:** WP Rocket, W3 Total Cache, LiteSpeed Cache

**Config:**
- Cache HTML pages (exclude cart, checkout, my-account)
- Browser caching (1 year for static assets)
- GZIP compression
- Database cleanup (revisions, trash, transients)

---

### 3. Asset Optimization
**Status:** ⚠️ NEEDS IMPROVEMENT

#### Minification
```php
// functions.php - Enable in production
define('SCRIPT_DEBUG', false); // Use minified versions
```

**CSS/JS Minification:**
```bash
# Build process (package.json)
npm install --save-dev cssnano terser

# package.json scripts
{
  "scripts": {
    "build:css": "postcss src/style.css -o dist/style.min.css",
    "build:js": "terser src/script.js -o dist/script.min.js --compress --mangle"
  }
}
```

#### Concatenation
```php
// Combine multiple CSS files
function combine_styles() {
    wp_deregister_style('style-1');
    wp_deregister_style('style-2');

    wp_enqueue_style('combined-styles', get_template_directory_uri() . '/dist/combined.min.css', array(), '1.0.0');
}
add_action('wp_enqueue_scripts', 'combine_styles', 999);
```

---

### 4. Image Optimization
**Status:** 🔴 MAJOR ISSUES

**Analysis:**
- Total image size: 8.4MB (45 images)
- Largest: 2.4MB (hero.jpg)
- Format: All JPEG (no WebP)
- No lazy loading

#### WebP Conversion
```php
// functions.php - Serve WebP when supported
function webp_support($image_url) {
    $webp_url = preg_replace('/\.(jpg|jpeg|png)$/i', '.webp', $image_url);

    if (file_exists(str_replace(home_url(), ABSPATH, $webp_url))) {
        return $webp_url;
    }

    return $image_url;
}

// In template
$image_url = webp_support(wp_get_attachment_url($image_id));
```

**Server Config (.htaccess):**
```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{HTTP_ACCEPT} image/webp
  RewriteCond %{REQUEST_FILENAME}.webp -f
  RewriteRule ^(.*)\.(jpg|jpeg|png)$ $1.$2.webp [T=image/webp,E=accept:1,L]
</IfModule>

<IfModule mod_headers.c>
  Header append Vary Accept env=REDIRECT_accept
</IfModule>
```

#### Lazy Loading
```php
// WordPress 5.5+ native lazy loading
add_filter('wp_lazy_loading_enabled', '__return_true');

// Template
<img src="product.jpg" alt="Product" loading="lazy">
```

**JS Fallback per old browsers:**
```javascript
if ('loading' in HTMLImageElement.prototype) {
    // Native lazy loading supported
} else {
    // Fallback: use Intersection Observer
    const images = document.querySelectorAll('img[loading="lazy"]');
    const imageObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const img = entry.target;
                img.src = img.dataset.src;
                imageObserver.unobserve(img);
            }
        });
    });

    images.forEach(img => imageObserver.observe(img));
}
```

#### Responsive Images
```php
// functions.php - Register custom image sizes
add_image_size('product-thumbnail', 300, 300, true);
add_image_size('product-medium', 600, 600, true);
add_image_size('product-large', 1200, 1200, false);

// Template
echo wp_get_attachment_image(
    $image_id,
    'product-large',
    false,
    array(
        'srcset' => wp_get_attachment_image_srcset($image_id, 'product-large'),
        'sizes'  => '(max-width: 600px) 100vw, (max-width: 1000px) 50vw, 1200px'
    )
);
```

---

### 5. Third-Party Scripts
**Status:** ⚠️ IMPACTING PERFORMANCE

**Detected:**
- Google Analytics: 45KB (blocking)
- Facebook Pixel: 38KB (blocking)
- Live Chat widget: 120KB (render-blocking)

#### Load Async/Defer
```php
function defer_third_party_scripts($tag, $handle) {
    $defer_scripts = array('google-analytics', 'facebook-pixel', 'livechat');

    if (in_array($handle, $defer_scripts)) {
        return str_replace(' src', ' defer src', $tag);
    }

    return $tag;
}
add_filter('script_loader_tag', 'defer_third_party_scripts', 10, 2);
```

#### Delay Non-Essential Scripts
```javascript
// Load live chat only after user interaction
let chatLoaded = false;

function loadLiveChat() {
    if (!chatLoaded) {
        const script = document.createElement('script');
        script.src = 'https://cdn.livechat.com/widget.js';
        document.body.appendChild(script);
        chatLoaded = true;
    }
}

// Load on first interaction
['click', 'scroll', 'touchstart'].forEach(event => {
    window.addEventListener(event, loadLiveChat, { once: true });
});

// Or after 5 seconds
setTimeout(loadLiveChat, 5000);
```

---

## 📋 Performance Optimization Checklist

### Critical (Do First)
- [ ] Optimize images (resize, WebP, responsive)
- [ ] Inline critical CSS
- [ ] Add width/height to images (CLS fix)
- [ ] Fix N+1 query problems
- [ ] Enable page caching
- [ ] Defer/async non-critical JS

### High Priority
- [ ] Implement object caching (Redis/Memcached)
- [ ] Lazy load below-fold images
- [ ] Minify CSS/JS
- [ ] Remove unused CSS/JS (PurgeCSS)
- [ ] CDN for static assets
- [ ] Database optimization (indexes, cleanup)

### Nice to Have
- [ ] HTTP/2 or HTTP/3
- [ ] Preload critical resources
- [ ] Service Worker for offline
- [ ] Code splitting for JS
- [ ] Reduce third-party scripts

---

## 🛠 Tools & Plugins

### Analysis Tools
- **Lighthouse** (Chrome DevTools)
- **PageSpeed Insights** (Google)
- **GTmetrix** (detailed report)
- **WebPageTest** (filmstrip, waterfall)
- **Query Monitor** (WP plugin - database queries)

### Optimization Plugins
- **WP Rocket** (caching, minify, lazy load) - Premium
- **Autoptimize** (CSS/JS optimization) - Free
- **ShortPixel** (image compression) - Freemium
- **Redis Object Cache** (persistent caching)
- **Asset CleanUp** (disable unused CSS/JS per page)

### Monitoring
- **New Relic** / **Datadog** (APM)
- **Google Search Console** (Core Web Vitals report)
- **Cloudflare Analytics** (if using CF)

---

## 📈 Expected Results

**Before Optimization:**
- Page Load: 6.2s
- Total Size: 9.8MB
- Requests: 142
- Lighthouse Score: 45/100

**After Optimization:**
- Page Load: 1.8s ⬇️ -4.4s (71% faster)
- Total Size: 1.2MB ⬇️ -8.6MB (88% smaller)
- Requests: 38 ⬇️ -104 (73% fewer)
- Lighthouse Score: 88/100 ⬆️ +43 points

**Business Impact:**
- Bounce rate: -25%
- Conversions: +18%
- SEO ranking: improved (Core Web Vitals factor)
- Mobile experience: significantly better

---

## 🎯 Implementation Priority

### Week 1: Quick Wins
1. Enable page caching (plugin)
2. Optimize images (compress, WebP)
3. Defer JavaScript
4. Fix N+1 queries

**Estimated Impact:** Score 45 → 68

### Week 2: Advanced Optimization
1. Implement object caching
2. Inline critical CSS
3. Lazy load images
4. Optimize fonts

**Estimated Impact:** Score 68 → 82

### Week 3: Fine-Tuning
1. Code splitting
2. CDN setup
3. Database optimization
4. Remove unused assets

**Estimated Impact:** Score 82 → 88+

---

**Report Date:** 2026-01-09
**Auditor:** Claude Code - Performance Check
```

## Output
Report dettagliato con:
1. **Core Web Vitals**: LCP, FID/INP, CLS con fix specifici
2. **Database Performance**: query optimization
3. **Caching Strategy**: implementazione multi-livello
4. **Asset Optimization**: immagini, CSS, JS
5. **Third-party Scripts**: gestione e defer
6. **Checklist prioritizzato**: critical → nice-to-have
7. **Tools**: analisi e plugin consigliati
8. **Expected Results**: before/after metrics

## Agente Consigliato
Questo comando utilizza agenti:
- `frontend-a11y` per ottimizzazioni front-end
- `wp-woo-expert` per best practice WordPress/WooCommerce
