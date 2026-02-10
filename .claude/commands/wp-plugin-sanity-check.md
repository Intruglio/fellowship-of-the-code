---
name: wp-plugin-sanity-check
description: Audit completo plugin WordPress per sicurezza, performance, compatibilità e best practice
---

# Comando: WP Plugin Sanity Check

## Scopo
Esegue audit completo di un plugin WordPress custom o esistente, verificando sicurezza, performance, compatibilità, coding standards e potenziali problemi.

## Utilizzo
```bash
/wp-plugin-sanity-check $ARGUMENTS
```

**Esempi:**
- `/wp-plugin-sanity-check wp-content/plugins/my-custom-plugin`
- `/wp-plugin-sanity-check my-custom-plugin`
- `/wp-plugin-sanity-check all` (tutti i plugin custom nel repo)

## Workflow

### 1. Plugin Discovery
Identifica plugin da analizzare:
- Path completo specificato
- Nome plugin (cerca in wp-content/plugins/)
- "all" = tutti i plugin custom (no vendor)

Estrai metadati da header file principale:
```php
/**
 * Plugin Name: My Custom Plugin
 * Plugin URI: https://example.com
 * Description: Does something
 * Version: 1.2.3
 * Author: Author Name
 * Text Domain: my-custom-plugin
 * Domain Path: /languages
 * Requires at least: 5.0
 * Requires PHP: 7.4
 */
```

### 2. Structure Analysis

```markdown
## 📁 Plugin Structure

**Plugin Name:** My Custom Plugin
**Version:** 1.2.3
**Main File:** `my-custom-plugin.php`
**Text Domain:** my-custom-plugin

### Directory Structure
```
my-custom-plugin/
├── my-custom-plugin.php (Main file) ✓
├── readme.txt ✓
├── languages/ ✓
│   └── my-custom-plugin.pot ✓
├── includes/
│   ├── class-plugin.php ✓
│   └── functions.php ✓
├── admin/
│   ├── class-admin.php ✓
│   └── css/admin.css ✓
├── public/
│   ├── js/public.js ✓
│   └── css/public.css ✓
├── assets/ ⚠️ (NON STANDARD - usare public/)
└── tests/ ❌ MISSING
```

**Status:** 🟡 GOOD (alcune migliorie)

**Issues:**
- ⚠️ Directory `assets/` non standard (usare `public/` o `admin/`)
- ❌ Manca directory `tests/` per unit testing
- ✅ Struttura generale pulita e organizzata

---
```

### 3. Security Audit 🔐

```markdown
## 🔐 Security Audit

### 3.1 Direct Access Protection
**Scopo:** Prevenire accesso diretto ai file PHP

**Status:** ⚠️ INCOMPLETE

**Files Controllati:**
```bash
grep -r "defined.*ABSPATH" --include="*.php"
```

**Issues:**
- ✅ `my-custom-plugin.php` - Protected
- ✅ `includes/class-plugin.php` - Protected
- ❌ `includes/functions.php` - NOT PROTECTED
- ❌ `admin/class-admin.php` - NOT PROTECTED

**Fix Required:**
Aggiungi all'inizio di ogni file PHP:
```php
<?php
// Exit if accessed directly
if (!defined('ABSPATH')) {
    exit;
}
```

---

### 3.2 Input Sanitization
**Scopo:** Prevenire injection attacks

**Status:** 🔴 CRITICAL ISSUES FOUND

**Scansione:**
```bash
grep -n "\$_POST\|\$_GET\|\$_REQUEST" --include="*.php" -r .
```

**Issues Trovati:**

#### 🔴 CRITICAL: includes/functions.php:45
```php
$user_input = $_POST['custom_field'];
update_option('my_option', $user_input);
```
**Problema:** Input non sanitizzato prima di salvare.

**Fix:**
```php
$user_input = isset($_POST['custom_field']) ? sanitize_text_field($_POST['custom_field']) : '';

// Validate
if (empty($user_input)) {
    return new WP_Error('invalid_input', __('Field cannot be empty', 'my-custom-plugin'));
}

update_option('my_option', $user_input);
```

#### 🔴 CRITICAL: admin/class-admin.php:89
```php
$sql = "SELECT * FROM {$wpdb->prefix}custom_table WHERE id = {$_GET['id']}";
$result = $wpdb->get_row($sql);
```
**Problema:** SQL Injection vulnerability!

**Fix:**
```php
$id = isset($_GET['id']) ? absint($_GET['id']) : 0;

if ($id <= 0) {
    return new WP_Error('invalid_id', __('Invalid ID', 'my-custom-plugin'));
}

$result = $wpdb->get_row($wpdb->prepare(
    "SELECT * FROM {$wpdb->prefix}custom_table WHERE id = %d",
    $id
));
```

---

### 3.3 Output Escaping
**Scopo:** Prevenire XSS attacks

**Status:** 🔴 CRITICAL ISSUES FOUND

**Scansione:**
```bash
grep -n "echo.*\$\|print.*\$" --include="*.php" -r .
```

**Issues Trovati:**

#### 🔴 CRITICAL: admin/templates/settings.php:23
```php
<input type="text" value="<?php echo $option_value; ?>">
```
**Problema:** Output non escaped.

**Fix:**
```php
<input type="text" value="<?php echo esc_attr($option_value); ?>">
```

#### 🔴 CRITICAL: public/templates/display.php:67
```php
<div class="content"><?php echo $user_content; ?></div>
```
**Fix:**
```php
<div class="content"><?php echo wp_kses_post($user_content); ?></div>
```

---

### 3.4 Nonce Verification
**Scopo:** Prevenire CSRF attacks

**Status:** 🔴 MISSING IN CRITICAL PLACES

**Forms Trovati:**
```bash
grep -n "<form" --include="*.php" -r .
```

**Issues:**

#### 🔴 CRITICAL: admin/templates/settings.php:15
```php
<form method="post" action="">
    <!-- form fields -->
</form>
```
**Problema:** Nessun nonce field.

**Fix:**
```php
<form method="post" action="">
    <?php wp_nonce_field('my_plugin_save_settings', 'my_plugin_nonce'); ?>
    <!-- form fields -->
</form>
```

**Processing:**
```php
// In handler function
if (!isset($_POST['my_plugin_nonce']) || !wp_verify_nonce($_POST['my_plugin_nonce'], 'my_plugin_save_settings')) {
    wp_die(__('Security check failed', 'my-custom-plugin'));
}

if (!current_user_can('manage_options')) {
    wp_die(__('Unauthorized', 'my-custom-plugin'));
}

// Process form...
```

---

### 3.5 Capability Checks
**Scopo:** Controllo accessi admin

**Status:** ⚠️ INCONSISTENT

**Admin Functions:**
```bash
grep -n "add_menu_page\|add_submenu_page" --include="*.php" -r .
```

**Issues:**

#### ⚠️ admin/class-admin.php:34
```php
add_menu_page('My Plugin', 'My Plugin', 'read', 'my-plugin');
```
**Problema:** Capability 'read' troppo permissiva per admin page.

**Fix:**
```php
add_menu_page('My Plugin', 'My Plugin', 'manage_options', 'my-plugin');
```

---

### 3.6 File Upload Security (se applicabile)
**Status:** N/A - No file upload in this plugin

---

### Security Score: 🔴 45/100 (FAILING)

**Critical Issues:** 6
**Warnings:** 3
**Recommendation:** ❌ DO NOT USE in production until fixed
```

### 4. Performance Analysis ⚡

```markdown
## ⚡ Performance Analysis

### 4.1 Asset Loading
**Status:** ⚠️ NEEDS OPTIMIZATION

**Scripts Enqueued:**
```bash
grep -n "wp_enqueue" --include="*.php" -r .
```

**Issues:**

#### ⚠️ includes/class-plugin.php:56
```php
add_action('wp_enqueue_scripts', 'load_assets');

function load_assets() {
    wp_enqueue_script('my-plugin-js', plugin_dir_url(__FILE__) . 'public/js/script.js');
    wp_enqueue_style('my-plugin-css', plugin_dir_url(__FILE__) . 'public/css/style.css');
}
```

**Problemi:**
1. Assets caricati su TUTTE le pagine (anche dove non servono)
2. No versioning (caching issues)
3. No minification mention

**Fix:**
```php
function load_assets() {
    // Carica solo dove serve (es: shortcode page)
    if (!is_singular() || !has_shortcode(get_post()->post_content, 'my_shortcode')) {
        return;
    }

    wp_enqueue_script(
        'my-plugin-js',
        plugin_dir_url(__FILE__) . 'public/js/script.min.js', // Use minified
        array('jquery'), // Declare dependencies
        MY_PLUGIN_VERSION, // Version for cache busting
        true // Load in footer
    );

    wp_enqueue_style(
        'my-plugin-css',
        plugin_dir_url(__FILE__) . 'public/css/style.min.css',
        array(),
        MY_PLUGIN_VERSION
    );
}
```

---

### 4.2 Database Queries
**Status:** 🔴 CRITICAL PERFORMANCE ISSUES

**Analysis:**
```bash
grep -n "get_posts\|WP_Query\|wpdb->" --include="*.php" -r .
```

**Issues:**

#### 🔴 CRITICAL: includes/functions.php:123
```php
function display_items() {
    $categories = get_terms('category');

    foreach ($categories as $cat) {
        // ⚠️ QUERY INSIDE LOOP - N+1 PROBLEM!
        $posts = get_posts(array('category' => $cat->term_id));

        foreach ($posts as $post) {
            echo $post->post_title;
        }
    }
}
```

**Problema:** Se 10 categorie, esegue 11 query (1 + 10). Con 100 categorie = 101 query!

**Fix:**
```php
function display_items() {
    // Single query con tax_query
    $posts = get_posts(array(
        'post_type'      => 'post',
        'posts_per_page' => -1,
        'tax_query'      => array(
            array(
                'taxonomy' => 'category',
                'operator' => 'EXISTS'
            )
        )
    ));

    // Group by category in PHP (1 query total)
    $by_category = array();
    foreach ($posts as $post) {
        $cats = wp_get_post_categories($post->ID);
        foreach ($cats as $cat_id) {
            $by_category[$cat_id][] = $post;
        }
    }

    // Display
    foreach ($by_category as $cat_id => $posts) {
        $cat = get_term($cat_id);
        echo '<h3>' . esc_html($cat->name) . '</h3>';
        foreach ($posts as $post) {
            echo '<p>' . esc_html($post->post_title) . '</p>';
        }
    }
}
```

---

### 4.3 Caching
**Status:** ❌ NO CACHING IMPLEMENTED

**Opportunità:**

#### includes/functions.php:78
```php
function get_external_data() {
    $response = wp_remote_get('https://api.example.com/data');
    return json_decode(wp_remote_retrieve_body($response));
}
```

**Problema:** API call ad ogni page load (lento + API rate limits).

**Fix con Transient:**
```php
function get_external_data() {
    $cache_key = 'my_plugin_external_data';
    $cached = get_transient($cache_key);

    if (false !== $cached) {
        return $cached;
    }

    // Cache miss - fetch fresh data
    $response = wp_remote_get('https://api.example.com/data', array(
        'timeout' => 10
    ));

    if (is_wp_error($response)) {
        error_log('API call failed: ' . $response->get_error_message());
        return array(); // Fallback
    }

    $data = json_decode(wp_remote_retrieve_body($response));

    // Cache for 1 hour
    set_transient($cache_key, $data, HOUR_IN_SECONDS);

    return $data;
}
```

---

### Performance Score: 🔴 50/100

**Issues:** N+1 queries, no caching, assets non-optimized
**Estimated Impact:** High - page load +2-3 secondi
```

### 5. WordPress Standards Compliance 🎯

```markdown
## 🎯 WordPress Standards

### 5.1 Coding Standards (PHPCS)
**Status:** ⚠️ VIOLATIONS FOUND

**Scansione:**
```bash
phpcs --standard=WordPress --extensions=php -p .
```

**Output:**
```
FILE: includes/functions.php
--------------------------------------
45 | ERROR | Nonce verification missing
67 | WARNING | Variable "$post" overriding global
89 | ERROR | Expected 1 space after IF keyword; 0 found
```

**Fix Required:** Run `phpcbf` auto-fixer + manual fixes.

---

### 5.2 Naming Conventions
**Status:** ✅ GOOD

**Verificato:**
- ✅ Funzioni prefissate: `myplugin_*`
- ✅ Classi namespace/prefix: `MyPlugin_*`
- ✅ Constants prefissate: `MY_PLUGIN_*`
- ✅ Hook names unique: `myplugin_*`

---

### 5.3 Internationalization (i18n)
**Status:** 🔴 INCOMPLETE

**Text Domain:**
```bash
grep -n "__\|_e\|_n\|_x\|esc_html__" --include="*.php" -r .
```

**Issues:**
- ✅ Header dichiara text domain: `my-custom-plugin`
- ❌ 15 hardcoded strings non traducibili trovati
- ⚠️ Text domain inconsistent in alcuni file ('myplugin' vs 'my-custom-plugin')
- ❌ File `.pot` non aggiornato (mancano stringhe nuove)

**Hardcoded Strings:**
```
includes/functions.php:45: echo "Settings saved";
admin/class-admin.php:89: return "Error occurred";
```

**Fix:**
```php
echo __('Settings saved', 'my-custom-plugin');
return __('Error occurred', 'my-custom-plugin');
```

**Generate .pot:**
```bash
wp i18n make-pot . languages/my-custom-plugin.pot
```

---

### 5.4 Enqueue Practices
**Status:** ⚠️ ISSUES (vedi Performance section)

---

### 5.5 Database Schema
**Status:** ⚠️ NON-STANDARD

#### includes/install.php:23
```php
$sql = "CREATE TABLE {$wpdb->prefix}my_table (
    id INT NOT NULL AUTO_INCREMENT,
    data TEXT,
    PRIMARY KEY (id)
)";
```

**Issues:**
- ⚠️ Missing `$wpdb->get_charset_collate()`
- ⚠️ No dbDelta() usage (problemi update schema)

**Fix:**
```php
global $wpdb;
$charset_collate = $wpdb->get_charset_collate();
$table_name = $wpdb->prefix . 'my_table';

$sql = "CREATE TABLE $table_name (
    id bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    data TEXT NOT NULL,
    created_at datetime DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY  (id)
) $charset_collate;";

require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
dbDelta($sql);
```

---

### Standards Score: 🟡 65/100

**Issues:** i18n incomplete, alcuni standard violations
```

### 6. Compatibility & Dependencies ⚙️

```markdown
## ⚙️ Compatibility

### 6.1 WordPress Version
**Requires at least:** 5.0
**Tested up to:** Unknown ⚠️

**Recommendation:** Test con latest WP version e update header.

---

### 6.2 PHP Version
**Requires PHP:** 7.4
**Status:** ✅ REASONABLE

**Code Analysis:**
```bash
phpcs --standard=PHPCompatibility --runtime-set testVersion 7.4- .
```

**Issues:**
- ✅ No PHP 8.0+ specific syntax
- ⚠️ Alcune deprecated notices PHP 8.1 (implicit conversions)

---

### 6.3 Plugin Dependencies
**Detected:**
- ✅ No hard dependencies
- ⚠️ Usa WooCommerce hooks ma no check se WC attivo

**Fix:**
```php
// Check WC active before using WC functions
if (!class_exists('WooCommerce')) {
    add_action('admin_notices', function() {
        echo '<div class="error"><p>';
        echo __('My Plugin requires WooCommerce to be active.', 'my-custom-plugin');
        echo '</p></div>';
    });
    return;
}
```

---

### 6.4 Theme Independence
**Status:** ✅ GOOD

**Verificato:**
- ✅ No hard-coded theme paths
- ✅ Usa template_include filter (themeable)
- ✅ No assumptions su theme structure
```

### 7. Documentation & Maintainability 📚

```markdown
## 📚 Documentation

### 7.1 README.txt
**Status:** ⚠️ INCOMPLETE

**Presenti:**
- ✅ Plugin description
- ✅ Installation instructions
- ⚠️ Changelog outdated (ultima entry: 6 mesi fa)

**Mancanti:**
- ❌ Frequently Asked Questions (FAQ)
- ❌ Screenshots
- ❌ Upgrade notices
- ❌ Tested up to version

---

### 7.2 Code Documentation
**Status:** 🟡 ADEQUATE

**PHPDoc Coverage:**
```bash
phpdoc-coverage . --exclude=vendor
```

**Result:** 62% functions documented

**Issues:**
- ✅ Classi documentate bene
- ⚠️ Molte funzioni mancano docblock
- ❌ Parametri/return types non sempre documentati

---

### 7.3 Inline Comments
**Status:** 🟡 MODERATE

**Observation:**
- Logica complessa generalmente commentata
- Troppi commenti ovvi ("// Set variable")
- Mancano "why" comments per decisioni architetturali

---

### 7.4 Changelog
**Status:** ⚠️ OUTDATED

**Recommendation:** Update CHANGELOG.md con:
- Versioni recenti
- Breaking changes evidenziati
- Security fixes highlighted
```

### 8. Final Report

```markdown
## 📊 FINAL SANITY CHECK REPORT

### Overall Score: 🔴 52/100 (FAILING)

**Category Breakdown:**
| Category | Score | Status |
|----------|-------|--------|
| 🔐 Security | 45/100 | 🔴 CRITICAL |
| ⚡ Performance | 50/100 | 🔴 POOR |
| 🎯 WP Standards | 65/100 | 🟡 ADEQUATE |
| ⚙️ Compatibility | 75/100 | 🟢 GOOD |
| 📚 Documentation | 60/100 | 🟡 ADEQUATE |

---

## 🚨 CRITICAL ISSUES (Must Fix Before Production)

1. **SQL Injection** - `admin/class-admin.php:89`
2. **XSS Vulnerabilities** - Multiple files, unescaped output
3. **Missing Nonce Verification** - Forms without CSRF protection
4. **N+1 Query Problem** - `includes/functions.php:123`
5. **No Input Sanitization** - Multiple $_POST/$_GET uses

**Estimated Fix Time:** 4-6 ore

---

## ⚠️ HIGH PRIORITY (Should Fix Soon)

1. Direct access protection missing in 8 files
2. Asset loading non-optimized (loads everywhere)
3. No caching for expensive operations
4. i18n incomplete (15+ hardcoded strings)
5. Inconsistent capability checks

**Estimated Fix Time:** 2-3 ore

---

## 💡 IMPROVEMENTS (Nice to Have)

1. Add unit tests
2. Update documentation (README, changelog)
3. Improve code comments
4. Add error logging
5. Implement proper error handling with WP_Error

**Estimated Fix Time:** 3-4 ore

---

## 🎯 Recommendations

### Immediate Actions
1. **DO NOT deploy to production** - critical security issues
2. Fix SQL injection e XSS vulnerabilities (Priority 1)
3. Add nonce verification to all forms
4. Implement input sanitization

### Short-Term (1-2 settimane)
1. Optimize database queries (fix N+1)
2. Implement caching strategy
3. Complete i18n (translate all strings)
4. Add conditional asset loading

### Long-Term
1. Setup automated testing (PHPUnit)
2. Implement CI/CD con security scanning
3. Regular security audits
4. Performance monitoring

---

## 📋 Action Plan Checklist

### Security Fixes
- [ ] Fix SQL injection in `admin/class-admin.php:89`
- [ ] Escape all output (esc_html, esc_attr, wp_kses)
- [ ] Add nonce to all forms
- [ ] Sanitize all $_POST/$_GET input
- [ ] Add capability checks to admin functions
- [ ] Add direct access protection to all files

### Performance Fixes
- [ ] Fix N+1 query in `includes/functions.php:123`
- [ ] Implement transient caching for API calls
- [ ] Conditional asset loading
- [ ] Minify CSS/JS

### Standards Compliance
- [ ] Complete i18n (translate all strings)
- [ ] Fix PHPCS violations
- [ ] Update README.txt
- [ ] Generate/update .pot file
- [ ] Test with latest WordPress version

### Testing
- [ ] Manual security testing
- [ ] Performance testing (Query Monitor)
- [ ] Cross-browser testing
- [ ] Accessibility audit

---

## 🔄 Next Steps

1. **Develop**: Apply fixes as per priority
2. **Test**: Verify in staging environment
3. **Re-audit**: Run sanity check again
4. **Deploy**: Only after score > 75/100
5. **Monitor**: Track errors post-deployment

---

**Audit Date:** 2026-01-09
**Auditor:** Claude Code - WP Plugin Sanity Check
**Plugin Version:** 1.2.3
```

## Output
Report completo markdown con:
1. **Structure Analysis**: file organization
2. **Security Audit**: vulnerabilità e fix
3. **Performance Analysis**: bottleneck e ottimizzazioni
4. **Standards Compliance**: WordPress best practice
5. **Compatibility**: WP/PHP versions, dependencies
6. **Documentation**: quality e completeness
7. **Final Score**: overall + per categoria
8. **Action Plan**: prioritized checklist

## Tools Suggeriti
- `phpcs` con WordPress standards
- `phpcbf` per auto-fix
- `phpstan` per static analysis
- `wp-cli i18n` per .pot generation
- Query Monitor plugin per performance

## Agente Consigliato
Questo comando utilizza agenti:
- `reviewer` per code quality audit
- `wp-woo-expert` per WordPress best practice
- `debugger` per issue identification
