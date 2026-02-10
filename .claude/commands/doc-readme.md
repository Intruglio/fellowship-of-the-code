---
name: doc-readme
description: Genera documentazione completa (README, CHANGELOG, inline docs) per progetto WordPress
---

# Comando: Documentation & README

## Scopo
Genera documentazione completa e professionale per progetti WordPress/WooCommerce, inclusi README.md, CHANGELOG.md, code documentation (PHPDoc), e guide utente.

## Utilizzo
```bash
/doc-readme $ARGUMENTS
```

**Esempi:**
- `/doc-readme type: plugin name: "My Custom Plugin"`
- `/doc-readme type: theme update-changelog`
- `/doc-readme type: phpdoc file: includes/class-main.php`
- `/doc-readme type: user-guide audience: end-user`

## Workflow

### 1. Identifica Tipo Documentazione
Da `$ARGUMENTS`:
- **README.md** (plugin/theme)
- **CHANGELOG.md** (tracking versioni)
- **PHPDoc** (inline code documentation)
- **User Guide** (manuale utente)
- **API Documentation** (per hook/filtri custom)
- **Contributing Guide** (per open-source)

### 2. Analizza Progetto
Scansiona repository per estrarre:
- Nome e descrizione da plugin/theme header
- Versione corrente
- Requisiti (WP, PHP, WooCommerce versions)
- Features (da codice e commit history)
- Hook e filtri custom
- File structure

### 3. Genera Documentazione

---

## Template: README.md (Plugin)

```markdown
# Nome Plugin

![Version](https://img.shields.io/badge/version-1.2.3-blue)
![WordPress](https://img.shields.io/badge/wordpress-5.8%2B-blue)
![PHP](https://img.shields.io/badge/php-7.4%2B-purple)
![License](https://img.shields.io/badge/license-GPL--2.0%2B-green)

Breve descrizione del plugin in 1-2 frasi. Cosa fa e perché è utile.

---

## 📋 Indice

- [Caratteristiche](#caratteristiche)
- [Requisiti](#requisiti)
- [Installazione](#installazione)
- [Configurazione](#configurazione)
- [Utilizzo](#utilizzo)
- [Shortcode](#shortcode)
- [Hook per Sviluppatori](#hook-per-sviluppatori)
- [FAQ](#faq)
- [Changelog](#changelog)
- [Supporto](#supporto)
- [Contribuire](#contribuire)
- [Licenza](#licenza)

---

## ✨ Caratteristiche

- **Feature 1**: Descrizione breve benefit
- **Feature 2**: Descrizione breve benefit
- **Feature 3**: Descrizione breve benefit
- **WooCommerce Ready**: Integrazione nativa con WooCommerce
- **Multilingua**: Supporto WPML e Polylang
- **Developer Friendly**: Hook e filtri per personalizzazioni

---

## 📦 Requisiti

| Software | Versione Minima | Raccomandata |
|----------|-----------------|--------------|
| WordPress | 5.8 | 6.4+ |
| PHP | 7.4 | 8.1+ |
| WooCommerce | 6.0 (opzionale) | 8.5+ |
| MySQL | 5.6 | 8.0+ |

**Plugin Dipendenze:**
- Nessuna dipendenza obbligatoria
- WooCommerce (opzionale, per funzionalità e-commerce)

**Browser Supportati:**
- Chrome/Edge (ultimi 2 versioni)
- Firefox (ultimi 2 versioni)
- Safari (ultimi 2 versioni)

---

## 🚀 Installazione

### Metodo 1: Tramite WordPress Admin (Raccomandato)

1. Accedi alla dashboard WordPress
2. Vai su **Plugin → Aggiungi Nuovo**
3. Cerca "Nome Plugin"
4. Clicca **Installa Ora** e poi **Attiva**

### Metodo 2: Upload Manuale

1. Scarica il file `.zip` dalla [pagina releases](https://github.com/username/plugin/releases)
2. Vai su **Plugin → Aggiungi Nuovo → Carica Plugin**
3. Seleziona il file `.zip` e clicca **Installa Ora**
4. Attiva il plugin

### Metodo 3: Via FTP

1. Estrai il file `.zip`
2. Carica la cartella `nome-plugin` in `/wp-content/plugins/`
3. Attiva il plugin dalla dashboard WordPress

### Metodo 4: Composer (per sviluppatori)

```bash
composer require username/nome-plugin
```

---

## ⚙️ Configurazione

### Configurazione Base

1. Dopo l'attivazione, vai su **Impostazioni → Nome Plugin**
2. Configura le opzioni principali:
   - **Opzione 1**: Descrizione e valore di default
   - **Opzione 2**: Descrizione e valore di default
   - **API Key** (opzionale): Ottieni da [link provider]
3. Clicca **Salva Modifiche**

![Screenshot Settings Page](assets/screenshot-1.png)

### Configurazione Avanzata

Per utenti avanzati, puoi configurare via `wp-config.php`:

```php
// Abilita debug mode
define('NOMEPLUGIN_DEBUG', true);

// Custom cache duration (in secondi)
define('NOMEPLUGIN_CACHE_DURATION', 3600);

// Disabilita feature specifica
define('NOMEPLUGIN_DISABLE_FEATURE_X', true);
```

---

## 📖 Utilizzo

### Uso Base

**Esempio 1: Display via Shortcode**

Inserisci lo shortcode in qualsiasi pagina/post:

```
[nome_shortcode parametro="valore"]
```

**Esempio 2: Display via Block (Gutenberg)**

1. Apri editor Gutenberg
2. Cerca "Nome Plugin Block"
3. Configura opzioni nel sidebar
4. Pubblica pagina

**Esempio 3: Display via Widget**

1. Vai su **Aspetto → Widget**
2. Trascina "Nome Plugin Widget" in una sidebar
3. Configura opzioni widget
4. Salva

### Uso Avanzato (Codice)

Puoi chiamare funzionalità del plugin direttamente nel tuo theme:

```php
<?php
// Verifica che plugin sia attivo
if (function_exists('nomeplugin_get_data')) {
    $data = nomeplugin_get_data(array(
        'limit' => 10,
        'order' => 'DESC'
    ));

    foreach ($data as $item) {
        echo '<div>' . esc_html($item->title) . '</div>';
    }
}
?>
```

---

## 🎯 Shortcode

### `[nome_shortcode]`

Mostra contenuto custom con parametri configurabili.

**Parametri:**

| Parametro | Tipo | Default | Descrizione |
|-----------|------|---------|-------------|
| `id` | int | - | ID specifico elemento |
| `limit` | int | 10 | Numero elementi da mostrare |
| `order` | string | 'DESC' | Ordinamento: ASC o DESC |
| `category` | string | 'all' | Filtra per categoria |
| `template` | string | 'default' | Template da usare: default, grid, list |
| `show_image` | bool | true | Mostra/nascondi immagini |

**Esempi:**

```
<!-- Basic usage -->
[nome_shortcode]

<!-- Con parametri -->
[nome_shortcode limit="5" order="ASC"]

<!-- Filtrare per categoria -->
[nome_shortcode category="news" template="grid"]

<!-- ID specifico -->
[nome_shortcode id="42"]
```

**Output:**

![Shortcode Output Example](assets/screenshot-2.png)

---

## 🔧 Hook per Sviluppatori

Il plugin offre vari hook per personalizzazioni avanzate.

### Actions

#### `nomeplugin_before_render`

Esegue prima del rendering output.

**Parametri:**
- `$data` (array): Dati che verranno renderizzati
- `$args` (array): Argomenti shortcode/widget

**Esempio:**

```php
add_action('nomeplugin_before_render', 'my_custom_function', 10, 2);

function my_custom_function($data, $args) {
    // Log per debug
    error_log('Rendering data: ' . print_r($data, true));

    // Inserisci tracking code
    echo '<script>/* tracking code */</script>';
}
```

---

#### `nomeplugin_after_save`

Esegue dopo il salvataggio delle impostazioni.

**Parametri:**
- `$options` (array): Opzioni salvate

**Esempio:**

```php
add_action('nomeplugin_after_save', 'clear_custom_cache');

function clear_custom_cache($options) {
    // Pulisci cache quando settings cambiano
    wp_cache_flush();

    // O cache specifiche
    delete_transient('nomeplugin_cached_data');
}
```

---

### Filters

#### `nomeplugin_output`

Modifica output HTML prima di visualizzarlo.

**Parametri:**
- `$output` (string): HTML output
- `$data` (array): Dati raw
- `$args` (array): Argomenti

**Return:** (string) HTML modificato

**Esempio:**

```php
add_filter('nomeplugin_output', 'wrap_output_in_div', 10, 3);

function wrap_output_in_div($output, $data, $args) {
    // Wrappa output in container custom
    return '<div class="my-custom-wrapper">' . $output . '</div>';
}
```

---

#### `nomeplugin_query_args`

Modifica parametri query prima dell'esecuzione.

**Parametri:**
- `$args` (array): WP_Query args

**Return:** (array) Args modificati

**Esempio:**

```php
add_filter('nomeplugin_query_args', 'exclude_certain_posts');

function exclude_certain_posts($args) {
    // Escludi post ID 123, 456
    $args['post__not_in'] = array(123, 456);

    return $args;
}
```

---

### Complete Hook Reference

Vedi [API_REFERENCE.md](docs/API_REFERENCE.md) per lista completa hook.

---

## ❓ FAQ

### Come posso personalizzare lo stile CSS?

Puoi aggiungere CSS custom nel tuo theme:

```css
/* Nel file style.css del tuo tema */
.nomeplugin-wrapper {
    background: #f5f5f5;
    padding: 20px;
}

.nomeplugin-item {
    border: 1px solid #ddd;
    margin-bottom: 10px;
}
```

In alternativa, usa il filtro `nomeplugin_enqueue_styles`:

```php
add_filter('nomeplugin_enqueue_styles', '__return_false'); // Disabilita stili plugin
```

---

### Il plugin è compatibile con il mio tema?

Il plugin è testato con i seguenti temi:
- ✅ Twenty Twenty-Four
- ✅ Astra
- ✅ GeneratePress
- ✅ OceanWP
- ✅ Storefront (WooCommerce)

Se riscontri problemi con un tema specifico, [apri un ticket](https://github.com/username/plugin/issues).

---

### Come posso tradurre il plugin?

Il plugin è translation-ready. Puoi:

1. **Via Plugin (Loco Translate):**
   - Installa Loco Translate
   - Vai su Loco Translate → Plugin
   - Seleziona "Nome Plugin" e crea nuova traduzione

2. **Manualmente:**
   - Usa il file `languages/nome-plugin.pot`
   - Traduci con Poedit
   - Salva come `nome-plugin-it_IT.po` e `.mo`
   - Carica in `/wp-content/languages/plugins/`

3. **Contribute:**
   - Traduzioni via [translate.wordpress.org](https://translate.wordpress.org)

---

### Come posso contribuire al progetto?

Vedi [CONTRIBUTING.md](CONTRIBUTING.md) per le linee guida.

---

### Dove trovo supporto?

- 📧 **Email:** [email protected]
- 💬 **Forum:** [WordPress.org Forum](https://wordpress.org/support/plugin/nome-plugin)
- 🐛 **Bug Report:** [GitHub Issues](https://github.com/username/plugin/issues)
- 📚 **Documentazione:** [docs.example.com](https://docs.example.com)

---

## 📜 Changelog

Vedi [CHANGELOG.md](CHANGELOG.md) per cronologia dettagliata modifiche.

### [1.2.3] - 2026-01-09

#### Added
- Nuova feature X per gestire Y
- Supporto WooCommerce 8.5+
- Opzione "Z" in settings

#### Changed
- Migliorata performance query (30% più veloce)
- Aggiornato design settings page

#### Fixed
- Bug #42: Shortcode non visualizzava su pagine con sidebar
- XSS vulnerability in campo custom (security)
- Compatibilità PHP 8.2

#### Deprecated
- Funzione `old_function()` → Usa `new_function()`

[Vedi changelog completo →](CHANGELOG.md)

---

## 🤝 Contribuire

Contributi, issues e feature requests sono benvenuti!

1. Fork del repository
2. Crea branch feature (`git checkout -b feature/AmazingFeature`)
3. Commit modifiche (`git commit -m 'Add AmazingFeature'`)
4. Push branch (`git push origin feature/AmazingFeature`)
5. Apri Pull Request

Vedi [CONTRIBUTING.md](CONTRIBUTING.md) per dettagli.

---

## 👥 Autori

**Nome Autore** - [@twitter](https://twitter.com/username) - [email protected]

**Contributors:**
- [Nome Contributor 1](https://github.com/contributor1)
- [Nome Contributor 2](https://github.com/contributor2)

Vedi [lista completa contributors](https://github.com/username/plugin/contributors).

---

## 📄 Licenza

Questo progetto è rilasciato sotto licenza GPL-2.0+. Vedi [LICENSE](LICENSE) file per dettagli.

```
Copyright (C) 2024 Nome Autore

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.
```

---

## 🙏 Riconoscimenti

- [WordPress](https://wordpress.org) - The platform
- [WooCommerce](https://woocommerce.com) - E-commerce integration
- [Library X](https://example.com) - Used for feature Y

---

## 📸 Screenshots

### 1. Settings Page
![Settings Page](assets/screenshot-1.png)
Pagina impostazioni plugin con opzioni configurabili.

### 2. Frontend Display
![Frontend Display](assets/screenshot-2.png)
Esempio output frontend con template grid.

### 3. Block Editor Integration
![Block Editor](assets/screenshot-3.png)
Integrazione Gutenberg block con opzioni sidebar.

---

## 🔗 Link Utili

- [Plugin Homepage](https://example.com/plugin)
- [Live Demo](https://demo.example.com)
- [Video Tutorial](https://youtube.com/watch?v=xxx)
- [Documentation](https://docs.example.com)
- [GitHub Repository](https://github.com/username/plugin)

---

**Made with ❤️ by [Nome Autore](https://example.com)**

*Se questo plugin ti è utile, considera di lasciare una ⭐ su GitHub o una recensione su WordPress.org!*
```

---

## Template: CHANGELOG.md

```markdown
# Changelog

Tutte le modifiche significative a questo progetto sono documentate in questo file.

Il formato è basato su [Keep a Changelog](https://keepachangelog.com/it/1.0.0/),
e questo progetto aderisce a [Semantic Versioning](https://semver.org/lang/it/).

## [Unreleased]

### Added
- Feature in sviluppo X
- Supporto per PHP 8.3

### Changed
- TBD

## [1.2.3] - 2026-01-09

### Added
- Nuovo shortcode parameter `template` per layouts custom
- Supporto WooCommerce High-Performance Order Storage (HPOS)
- Opzione "Auto-update cache" in settings
- Translation: Tedesco (de_DE) al 100%
- API endpoint REST `/wp-json/nomeplugin/v1/data`

### Changed
- **BREAKING:** Rimosso supporto WordPress < 5.8
- Migliorata performance query prodotti (-40% tempo esecuzione)
- Aggiornato design settings page (UI più moderna)
- Aumentato cache default duration: 1h → 6h
- Dependencies: jQuery 3.7.1 → 3.7.3

### Fixed
- **Security:** XSS vulnerability in admin settings form [CVE-2026-XXXX]
- Bug #127: Shortcode non funzionava con page builder Elementor
- Bug #134: Conflict con plugin "Example Plugin X"
- PHP Notice: Undefined index quando WooCommerce non attivo
- CSS: Layout mobile rotto su iPhone SE (320px width)
- Typo in traduzione italiana "Salvato" → "Salva"

### Deprecated
- Funzione `nomeplugin_old_function()` - Usa `nomeplugin_new_function()` invece
  Will be removed in v2.0.0
- Hook `nomeplugin_legacy_filter` - Usa `nomeplugin_filter` invece

### Removed
- Supporto PHP 7.3 (end of life)
- jQuery UI dependency (non più necessaria)
- Old migration script per versioni < 1.0

### Security
- Sanitizzazione input form admin migliorata (prevent XSS)
- Nonce verification aggiunta a tutti i form AJAX
- Capability check su endpoint REST API custom

---

## [1.2.2] - 2025-12-15

### Fixed
- Hotfix: Critical bug impediva salvataggio settings
- PHP Fatal Error su WP 6.4 con PHP 8.1

---

## [1.2.1] - 2025-12-10

### Changed
- Performance: Ridotto numero query DB (-25%)

### Fixed
- Bug #112: Cache non veniva invalidata dopo update post
- Compatibility: Conflict con plugin "Cache Enabler"

---

## [1.2.0] - 2025-11-20

### Added
- **Major Feature:** Gutenberg Block per visual editing
- Widget per sidebar/footer
- Export/Import settings (JSON format)
- WP-CLI command support: `wp nomeplugin refresh-cache`

### Changed
- Refactor codebase: OOP structure
- Minimum WP version: 5.6 → 5.8

### Fixed
- Bug #89: Memory leak su siti con 10k+ prodotti

---

## [1.1.0] - 2025-10-05

### Added
- Supporto WooCommerce
- Filtro per categoria
- Pagination

### Changed
- UI/UX settings page migliorata

---

## [1.0.1] - 2025-09-10

### Fixed
- Bug critico: Plugin causava WSOD su alcuni hosting

---

## [1.0.0] - 2025-09-01

### Added
- 🎉 Initial release
- Shortcode `[nome_shortcode]`
- Settings page basic
- Multilingual support (i18n ready)
- Support per Custom Post Types

---

## Link Versioni

- [Unreleased]: https://github.com/username/plugin/compare/v1.2.3...HEAD
- [1.2.3]: https://github.com/username/plugin/compare/v1.2.2...v1.2.3
- [1.2.2]: https://github.com/username/plugin/compare/v1.2.1...v1.2.2
- [1.2.1]: https://github.com/username/plugin/compare/v1.2.0...v1.2.1
- [1.2.0]: https://github.com/username/plugin/compare/v1.1.0...v1.2.0
- [1.1.0]: https://github.com/username/plugin/compare/v1.0.1...v1.1.0
- [1.0.1]: https://github.com/username/plugin/compare/v1.0.0...v1.0.1
- [1.0.0]: https://github.com/username/plugin/releases/tag/v1.0.0

---

## Categorie Changelog

- **Added**: Nuove feature
- **Changed**: Modifiche a funzionalità esistenti
- **Deprecated**: Feature che saranno rimosse in futuro
- **Removed**: Feature rimosse
- **Fixed**: Bug fix
- **Security**: Vulnerability fix

## Versionamento

Usiamo [SemVer](http://semver.org/): `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes (incompatibilità retroattiva)
- **MINOR**: Nuove feature (backward-compatible)
- **PATCH**: Bug fix (backward-compatible)
```

---

## Template: PHPDoc (Inline Code)

```php
<?php
/**
 * File principale del plugin Nome Plugin.
 *
 * Questo file contiene la classe principale del plugin e gestisce
 * l'inizializzazione, registrazione hook e inclusione dependencies.
 *
 * @package     NomePlugin
 * @subpackage  Core
 * @author      Nome Autore <[email protected]>
 * @copyright   2024 Nome Autore
 * @license     GPL-2.0+
 * @link        https://example.com
 * @since       1.0.0
 */

// Exit if accessed directly.
if (!defined('ABSPATH')) {
    exit;
}

/**
 * Main plugin class.
 *
 * Singleton class che gestisce lifecycle del plugin.
 * Handle hook registration, asset loading e dependency management.
 *
 * @since 1.0.0
 */
class NomePlugin_Main {

    /**
     * Plugin version.
     *
     * @since 1.0.0
     * @var string
     */
    const VERSION = '1.2.3';

    /**
     * Singleton instance.
     *
     * @since 1.0.0
     * @var NomePlugin_Main|null
     */
    private static $instance = null;

    /**
     * Plugin options.
     *
     * @since 1.0.0
     * @var array
     */
    private $options = array();

    /**
     * Get singleton instance.
     *
     * Ensures only one instance of the class is loaded or can be loaded.
     *
     * @since 1.0.0
     * @static
     * @return NomePlugin_Main Singleton instance.
     *
     * @example
     * $plugin = NomePlugin_Main::get_instance();
     * $options = $plugin->get_options();
     */
    public static function get_instance() {
        if (null === self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    /**
     * Constructor.
     *
     * Initialize plugin by setting up hooks and loading dependencies.
     * Private to prevent direct instantiation (Singleton pattern).
     *
     * @since 1.0.0
     * @access private
     */
    private function __construct() {
        $this->load_dependencies();
        $this->define_hooks();
        $this->load_options();
    }

    /**
     * Recupera prodotti in base a filtri custom.
     *
     * Esegue query ottimizzata sui prodotti WooCommerce applicando filtri
     * per categoria, prezzo e disponibilità. Implementa caching per performance.
     *
     * @since 1.1.0
     * @param array $args {
     *     Parametri query opzionali.
     *
     *     @type int    $category_id  ID categoria prodotto. Default 0 (tutte).
     *     @type float  $min_price    Prezzo minimo EUR. Default 0.
     *     @type float  $max_price    Prezzo massimo EUR. Default 0 (no limite).
     *     @type bool   $in_stock     Solo prodotti disponibili. Default true.
     *     @type int    $limit        Numero massimo prodotti. Default 10.
     *     @type string $orderby      Campo ordinamento: 'price', 'title', 'date'. Default 'date'.
     *     @type string $order        Direzione: 'ASC' o 'DESC'. Default 'DESC'.
     * }
     * @return WP_Query|WP_Error Query object con prodotti o WP_Error in caso di errore.
     *
     * @throws InvalidArgumentException Se $category_id non è un intero valido.
     *
     * @example
     * // Basic usage
     * $products = get_filtered_products();
     *
     * // Con filtri
     * $products = get_filtered_products(array(
     *     'category_id' => 5,
     *     'min_price'   => 10.00,
     *     'max_price'   => 100.00,
     *     'limit'       => 20,
     *     'orderby'     => 'price',
     *     'order'       => 'ASC'
     * ));
     *
     * if (!is_wp_error($products) && $products->have_posts()) {
     *     while ($products->have_posts()) {
     *         $products->the_post();
     *         // Display product
     *     }
     * }
     */
    public function get_filtered_products($args = array()) {
        // Validation
        $defaults = array(
            'category_id' => 0,
            'min_price'   => 0,
            'max_price'   => 0,
            'in_stock'    => true,
            'limit'       => 10,
            'orderby'     => 'date',
            'order'       => 'DESC'
        );

        $args = wp_parse_args($args, $defaults);

        // Sanitize
        $args['category_id'] = absint($args['category_id']);
        $args['min_price']   = floatval($args['min_price']);
        $args['max_price']   = floatval($args['max_price']);
        $args['limit']       = absint($args['limit']);

        // Cache key
        $cache_key = 'nomeplugin_products_' . md5(serialize($args));
        $cached = get_transient($cache_key);

        if (false !== $cached) {
            return $cached;
        }

        // Build query
        // ... (implementation) ...

        // Cache result (1 hour)
        set_transient($cache_key, $query, HOUR_IN_SECONDS);

        return $query;
    }

    /**
     * Salva opzioni plugin nel database.
     *
     * Valida, sanitizza e salva opzioni. Esegue hook after_save per
     * permettere estensioni custom. Invalida cache appropriata.
     *
     * @since 1.0.0
     * @param array $options Array associativo opzioni da salvare.
     * @return bool True se salvataggio riuscito, false altrimenti.
     *
     * @fires nomeplugin_before_save Before saving options.
     * @fires nomeplugin_after_save  After options saved successfully.
     */
    public function save_options($options) {
        // ... (implementation) ...
    }
}

/**
 * Helper function per accesso istanza plugin.
 *
 * @since 1.0.0
 * @return NomePlugin_Main Singleton instance.
 */
function nomeplugin() {
    return NomePlugin_Main::get_instance();
}

// Initialize plugin
nomeplugin();
```

---

## Output
Genera documentazione completa:
1. **README.md**: completo con badges, esempi, screenshot
2. **CHANGELOG.md**: versioned history con semantic versioning
3. **PHPDoc**: inline code documentation con @param, @return, @example
4. **Markdown**: ben formattato, GitHub-friendly, TOC
5. **SEO-friendly**: searchable, clear headings
6. **User-focused**: esempi pratici, FAQ, troubleshooting

## Agente Consigliato
Questo comando utilizza l'agente:
- `doc-writer` per documentazione tecnica chiara e completa
