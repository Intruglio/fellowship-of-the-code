# WordPress Performance Metrics & Optimization

Performance standards and optimization techniques for WordPress/WooCommerce.

---

## Core Web Vitals (Google)

### Largest Contentful Paint (LCP)
- **Target:** < 2.5 seconds
- **Measures:** Loading performance
- **What:** Time until largest content element is rendered
- **How to improve:**
  - Optimize images (WebP, lazy loading)
  - Improve server response time
  - Remove render-blocking resources
  - Use CDN

### First Input Delay (FID)
- **Target:** < 100 milliseconds
- **Measures:** Interactivity
- **What:** Time from first user interaction to browser response
- **How to improve:**
  - Break up long JavaScript tasks
  - Use web workers
  - Defer non-critical JavaScript
  - Minimize main thread work

### Cumulative Layout Shift (CLS)
- **Target:** < 0.1
- **Measures:** Visual stability
- **What:** Sum of all unexpected layout shifts
- **How to improve:**
  - Set width/height on images and videos
  - Don't insert content above existing content
  - Use transform animations instead of properties that trigger layout

---

## Performance Benchmarks

### Page Load Time
- **Target:** < 3 seconds on 4G mobile
- **Measure with:** Google PageSpeed Insights, GTmetrix

### Time to First Byte (TTFB)
- **Target:** < 600ms
- **What:** Server response time
- **How to improve:**
  - Optimize database queries
  - Enable object caching (Redis, Memcached)
  - Use page caching
  - Optimize PHP (use PHP 8+)

### First Contentful Paint (FCP)
- **Target:** < 1.8 seconds
- **What:** Time to first text/image rendered
- **How to improve:**
  - Inline critical CSS
  - Defer non-critical CSS
  - Optimize fonts (font-display: swap)

---

## WordPress-Specific Optimizations

### Database Query Optimization
```php
// ❌ SLOW: N+1 query problem
foreach ($posts as $post) {
    $author = get_user_by('id', $post->post_author);
}

// ✅ FAST: Single query with JOIN
$posts = $wpdb->get_results("
    SELECT p.*, u.display_name
    FROM {$wpdb->posts} p
    LEFT JOIN {$wpdb->users} u ON p.post_author = u.ID
    WHERE p.post_type = 'post'
");
```

### Transient Caching
```php
// Cache expensive operations
$data = get_transient('expensive_data');
if (false === $data) {
    $data = expensive_database_query();
    set_transient('expensive_data', $data, HOUR_IN_SECONDS);
}
return $data;
```

### Conditional Asset Loading
```php
// Only load assets where needed
function conditional_scripts() {
    if (is_singular('product')) {
        wp_enqueue_script('product-js');
    }
    if (is_checkout()) {
        wp_enqueue_script('checkout-js');
    }
}
add_action('wp_enqueue_scripts', 'conditional_scripts');
```

### Optimize Autoloaded Options
```php
// Check autoloaded options size
SELECT SUM(LENGTH(option_value)) as autoload_size
FROM wp_options
WHERE autoload = 'yes';

// Set large options to not autoload
update_option('large_option', $value, 'no'); // 'no' = don't autoload
```

---

## WooCommerce Performance

### Product Query Optimization
```php
// Use 'posts_per_page' instead of showing all products
$args = array(
    'post_type' => 'product',
    'posts_per_page' => 12, // Don't use -1
    'no_found_rows' => true, // Skip pagination count query if not needed
    'update_post_meta_cache' => false, // Skip if not using post meta
    'update_post_term_cache' => false, // Skip if not using terms
);
```

### Cart Fragment Caching
```php
// Disable cart fragments on non-shop pages
add_action('wp_enqueue_scripts', 'disable_cart_fragmentation', 99);
function disable_cart_fragmentation() {
    if (!is_shop() && !is_product() && !is_cart() && !is_checkout()) {
        wp_dequeue_script('wc-cart-fragments');
    }
}
```

### Object Caching for Sessions
- Use Redis or Memcached for WooCommerce sessions
- Prevents database bloat from session data
- Significantly improves cart/checkout performance

---

## Performance Testing Tools

### Google Tools
- **PageSpeed Insights**: Overall performance score
- **Lighthouse**: Detailed performance audit
- **Chrome DevTools**: Network waterfall, performance profiling

### Third-Party Tools
- **GTmetrix**: Performance analysis with actionable recommendations
- **WebPageTest**: Detailed loading analysis
- **Pingdom**: Speed testing from multiple locations

### WordPress Plugins
- **Query Monitor**: Debug queries, hooks, HTTP requests
- **Debug Bar**: Performance profiling
- **P3 (Plugin Performance Profiler)**: Identify slow plugins

---

## Performance Optimization Checklist

### Images
- [ ] Use modern formats (WebP, AVIF)
- [ ] Implement lazy loading (`loading="lazy"`)
- [ ] Use responsive images (srcset, sizes)
- [ ] Compress images (80-85% quality)
- [ ] Set explicit width/height attributes
- [ ] Use CDN for image delivery

### CSS
- [ ] Inline critical CSS
- [ ] Defer non-critical CSS
- [ ] Minify and concatenate CSS files
- [ ] Remove unused CSS
- [ ] Use CSS containment where possible

### JavaScript
- [ ] Defer non-critical JavaScript
- [ ] Minify and concatenate JS files
- [ ] Remove unused JavaScript
- [ ] Use async for independent scripts
- [ ] Split code bundles (code splitting)

### Caching
- [ ] Enable page caching (WP Rocket, W3 Total Cache)
- [ ] Enable object caching (Redis, Memcached)
- [ ] Enable browser caching (htaccess headers)
- [ ] Use transient API for expensive queries
- [ ] Enable CDN for static assets

### Database
- [ ] Optimize database tables regularly
- [ ] Clean up post revisions
- [ ] Remove spam comments
- [ ] Limit autoloaded options
- [ ] Use indexed columns in queries
- [ ] Delete transient data periodically

### Server
- [ ] Use PHP 8+ (significant performance boost)
- [ ] Enable OPcache
- [ ] Use HTTP/2 or HTTP/3
- [ ] Enable Gzip/Brotli compression
- [ ] Use dedicated hosting (avoid shared)
- [ ] Enable server-side caching

---

## Performance Monitoring

### Key Metrics to Track
- Page load time (desktop/mobile)
- Core Web Vitals (LCP, FID, CLS)
- Database query count per page
- TTFB (server response time)
- Memory usage
- Number of HTTP requests

### Monitoring Tools
- Google Analytics (page load times)
- Google Search Console (Core Web Vitals)
- New Relic / Datadog (APM)
- Query Monitor plugin (development)

---

## WooCommerce-Specific Performance

### Product Images
```php
// Disable zoom/slider/gallery on simple products
add_filter('woocommerce_single_product_zoom_enabled', '__return_false');
add_filter('woocommerce_single_product_flexslider_enabled', '__return_false');
```

### Reduce Checkout Scripts
```php
// Remove unnecessary scripts on checkout
add_action('wp_enqueue_scripts', 'optimize_checkout', 99);
function optimize_checkout() {
    if (is_checkout()) {
        wp_dequeue_style('wc-blocks-style');
        // Only dequeue what you don't need
    }
}
```

### Optimize Product Variations
- Limit variation count (max 50-100)
- Use variable product swatches instead of dropdowns
- Lazy load variation data

---

**Target:** Under 3 seconds load time, 90+ PageSpeed score, all Core Web Vitals in green.
