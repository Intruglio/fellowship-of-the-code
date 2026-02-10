# WordPress Security Standards

Security checklist and best practices for WordPress/WooCommerce development.

---

## Security Checklist (Always Apply)

### Input Validation & Sanitization
- [ ] All user input validated before processing
- [ ] Use `sanitize_text_field()` for text input
- [ ] Use `sanitize_email()` for email addresses
- [ ] Use `sanitize_url()` for URLs
- [ ] Use `wp_kses()` or `wp_kses_post()` for HTML content
- [ ] Use `absint()` for integers
- [ ] Never trust `$_GET`, `$_POST`, `$_REQUEST` directly

### Output Escaping
- [ ] `esc_html()` for HTML content
- [ ] `esc_attr()` for HTML attributes
- [ ] `esc_url()` for URLs
- [ ] `esc_js()` for JavaScript strings
- [ ] `wp_kses_post()` for post content (allows safe HTML)

### Nonce Verification
- [ ] All forms have nonce fields
- [ ] All AJAX calls verify nonce
- [ ] Use `wp_create_nonce('action_name')` to create
- [ ] Use `wp_verify_nonce()` or `check_ajax_referer()` to verify

**Example:**
```php
// Create nonce in form
<input type="hidden" name="my_nonce" value="<?php echo wp_create_nonce('my_action'); ?>">

// Verify nonce on submit
if (!isset($_POST['my_nonce']) || !wp_verify_nonce($_POST['my_nonce'], 'my_action')) {
    wp_die('Security check failed');
}
```

### Capability Checks
- [ ] Always check user permissions before sensitive operations
- [ ] Use `current_user_can('capability')`
- [ ] Common capabilities: `manage_options`, `edit_posts`, `publish_posts`
- [ ] WooCommerce: `manage_woocommerce`, `edit_shop_orders`

**Example:**
```php
if (!current_user_can('manage_options')) {
    wp_die('Unauthorized access');
}
```

### SQL Injection Prevention
- [ ] Use `$wpdb->prepare()` for all custom queries
- [ ] Never concatenate user input into SQL queries
- [ ] Use WordPress query functions when possible (WP_Query, get_posts)

**Example:**
```php
global $wpdb;
$user_id = absint($_POST['user_id']);
$results = $wpdb->get_results(
    $wpdb->prepare(
        "SELECT * FROM {$wpdb->posts} WHERE post_author = %d",
        $user_id
    )
);
```

### File Upload Security
- [ ] Validate file type (MIME type, not just extension)
- [ ] Validate file size
- [ ] Use WordPress upload functions (`wp_handle_upload()`)
- [ ] Never trust client-provided filename
- [ ] Store uploads outside web root when possible

**Example:**
```php
$allowed_types = array('image/jpeg', 'image/png', 'image/gif');
$file_type = wp_check_filetype($file['name']);

if (!in_array($file_type['type'], $allowed_types)) {
    wp_die('Invalid file type');
}
```

---

## OWASP Top 10 for WordPress

### 1. SQL Injection
**Prevention:** Use `$wpdb->prepare()` for all queries
```php
// ❌ WRONG
$wpdb->query("DELETE FROM {$wpdb->posts} WHERE ID = {$_POST['id']}");

// ✅ CORRECT
$wpdb->query($wpdb->prepare("DELETE FROM {$wpdb->posts} WHERE ID = %d", $_POST['id']));
```

### 2. Cross-Site Scripting (XSS)
**Prevention:** Escape all output
```php
// ❌ WRONG
echo $_POST['name'];

// ✅ CORRECT
echo esc_html($_POST['name']);
```

### 3. Cross-Site Request Forgery (CSRF)
**Prevention:** Use nonces for all state-changing operations
```php
check_ajax_referer('my_nonce', 'nonce');
```

### 4. Insecure Authentication
**Prevention:**
- Use WordPress authentication system
- Enforce strong passwords
- Implement rate limiting for login attempts
- Use `wp_authenticate()` instead of custom auth

### 5. Security Misconfiguration
**Prevention:**
- Set `WP_DEBUG` to `false` in production
- Disable file editing: `define('DISALLOW_FILE_EDIT', true);`
- Use strong database password
- Keep WordPress/plugins/themes updated
- Remove default "admin" username

### 6. Sensitive Data Exposure
**Prevention:**
- Never store passwords in plain text
- Use HTTPS for all pages
- Don't log sensitive data
- No credentials in code (use wp-config.php constants)
- Use `wp_hash_password()` for password storage

### 7. Broken Access Control
**Prevention:**
- Always check `current_user_can()` before sensitive operations
- Validate user owns the resource before modification
- Use nonces to prevent unauthorized actions

```php
// Check if user can edit this post
if (!current_user_can('edit_post', $post_id)) {
    wp_die('Unauthorized');
}
```

### 8. Insecure Deserialization
**Prevention:**
- Avoid `unserialize()` on user input
- Use JSON instead of serialized data when possible
- Validate data type after unserialization

### 9. Using Components with Known Vulnerabilities
**Prevention:**
- Keep WordPress core updated
- Keep plugins/themes updated
- Remove unused plugins/themes
- Use plugins from reputable sources only
- Regular security audits

### 10. Insufficient Logging & Monitoring
**Prevention:**
- Log security events (failed logins, permission errors)
- Use `error_log()` in debug mode
- Monitor for suspicious activity
- Set up security monitoring tools

```php
if (!wp_verify_nonce($_POST['nonce'], 'action')) {
    error_log('Nonce verification failed for user ' . get_current_user_id());
    wp_die('Security check failed');
}
```

---

## Security Testing Checklist

- [ ] Try SQL injection in search: `' OR '1'='1`
- [ ] Try XSS in forms: `<script>alert('xss')</script>`
- [ ] Try accessing `/wp-admin` without login
- [ ] Try modifying another user's order without permissions
- [ ] Try AJAX requests without nonce
- [ ] Try file upload with `.php` extension
- [ ] Verify error messages don't expose sensitive info
- [ ] Test CSRF by submitting forms without nonce

---

## Common Security Functions Reference

```php
// Nonces
wp_create_nonce('action_name')
wp_verify_nonce($nonce, 'action_name')
check_ajax_referer('nonce_name', 'nonce_field')

// Capabilities
current_user_can('capability')
user_can($user, 'capability')

// Sanitization
sanitize_text_field($string)
sanitize_email($email)
sanitize_url($url)
sanitize_key($key)
wp_kses($html, $allowed_html)
wp_kses_post($html)

// Escaping
esc_html($text)
esc_attr($text)
esc_url($url)
esc_js($text)
esc_textarea($text)

// Database
$wpdb->prepare($query, ...$values)

// Validation
is_email($email)
absint($number)
wp_check_filetype($filename)
```

---

**Remember:** Security is not optional. Every input is potentially malicious until proven otherwise.
