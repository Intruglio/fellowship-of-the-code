# WordPress Core Knowledge Base

Shared WordPress/WooCommerce patterns and APIs used by all Fellowship agents.

---

## Essential WordPress Hooks

### WordPress Core
- `init`: Register post types, taxonomies, enqueue scripts
- `wp_enqueue_scripts`: Load frontend assets
- `admin_enqueue_scripts`: Load backend assets
- `save_post`: Hook when saving a post
- `pre_get_posts`: Modify query before execution
- `template_redirect`: Custom redirects

### WooCommerce
- `woocommerce_before_checkout_form`: Before checkout
- `woocommerce_after_add_to_cart_button`: After add to cart button
- `woocommerce_order_status_changed`: Order status change
- `woocommerce_checkout_process`: Checkout validation
- `woocommerce_thankyou`: Thank you page
- `woocommerce_email_before_order_table`: Customize emails

---

## Common WordPress Patterns

### AJAX Endpoint WordPress
```php
// Backend
add_action('wp_ajax_my_action', 'handle_ajax');
add_action('wp_ajax_nopriv_my_action', 'handle_ajax');

function handle_ajax() {
    check_ajax_referer('my_nonce', 'nonce');

    if (!current_user_can('manage_options')) {
        wp_send_json_error('Unauthorized');
    }

    $data = sanitize_text_field($_POST['data']);
    // Process...
    wp_send_json_success($result);
}
```

### Enqueue Assets Conditionally
```php
function enqueue_assets() {
    if (is_product()) {
        wp_enqueue_style('product-custom',
            get_stylesheet_directory_uri() . '/assets/product.css',
            array(), '1.0.0'
        );

        wp_enqueue_script('product-gallery',
            get_stylesheet_directory_uri() . '/assets/product.js',
            array('jquery'), '1.0.0', true
        );

        // Localize for passing PHP data to JS
        wp_localize_script('product-gallery', 'productData', array(
            'ajaxurl' => admin_url('admin-ajax.php'),
            'nonce'   => wp_create_nonce('product_nonce')
        ));
    }
}
add_action('wp_enqueue_scripts', 'enqueue_assets');
```

### WP_Query Custom Query
```php
$args = array(
    'post_type'      => 'product',
    'posts_per_page' => 10,
    'meta_query'     => array(
        array(
            'key'     => '_custom_field',
            'value'   => 'value',
            'compare' => '='
        )
    ),
    'tax_query' => array(
        array(
            'taxonomy' => 'product_cat',
            'field'    => 'slug',
            'terms'    => 'category-slug'
        )
    )
);
$query = new WP_Query($args);
```

### Add Custom Field to WooCommerce Product
```php
// 1. Add field in backend
add_action('woocommerce_product_options_general_product_data', 'add_custom_field');

// 2. Save field
add_action('woocommerce_process_product_meta', 'save_custom_field');

// 3. Display in frontend
add_action('woocommerce_single_product_summary', 'display_custom_field', 25);
```

---

## WordPress/WooCommerce APIs

### Product API
```php
$product = wc_get_product($product_id);
$product->get_name();
$product->get_price();
$product->is_in_stock();
$product->set_regular_price(100);
$product->save();
```

### Cart API
```php
WC()->cart->add_to_cart($product_id, $quantity);
WC()->cart->get_cart_contents_count();
WC()->cart->get_cart_total();
```

### Order API
```php
$order = wc_get_order($order_id);
$order->get_status();
$order->update_status('completed');
$order->get_billing_email();
```

---

## Database Queries

### Prepared Statements
```php
global $wpdb;
$results = $wpdb->get_results(
    $wpdb->prepare(
        "SELECT * FROM {$wpdb->posts} WHERE ID = %d AND post_status = %s",
        $id,
        'publish'
    )
);
```

### Transient API (Caching)
```php
$data = get_transient('my_cached_data');
if (false === $data) {
    // Expensive operation
    $data = expensive_query();
    set_transient('my_cached_data', $data, HOUR_IN_SECONDS);
}
```

---

## Internationalization (i18n)

```php
__('Text to translate', 'text-domain');
_e('Echo translated text', 'text-domain');
_n('Singular', 'Plural', $count, 'text-domain');

sprintf(
    __('You have %d items', 'text-domain'),
    $count
);
```

---

## WordPress Coding Standards

1. **Prefixes**: Always prefix functions, classes, globals with unique namespace
2. **Hooks**: Use WordPress hooks instead of modifying core
3. **Escaping**: Always escape output with `esc_html()`, `esc_attr()`, `esc_url()`
4. **Sanitization**: Always sanitize input with `sanitize_*()` functions
5. **Prepared Statements**: Use `$wpdb->prepare()` for custom queries
6. **Naming**: `lowercase_with_underscores` for functions/variables
7. **Indentation**: Tabs for indentation, spaces for alignment
8. **PHPDoc**: Document all functions and classes
