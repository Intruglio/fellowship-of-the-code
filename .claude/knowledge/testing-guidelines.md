# WordPress Testing Guidelines

Testing strategies and checklists for WordPress/WooCommerce projects.

---

## Testing Philosophy

1. **Test early, test often**: Catch bugs before production
2. **Realistic scenarios**: Test real-world usage patterns
3. **Edge cases matter**: Test limits and boundary conditions
4. **Automate repetitive tests**: Save time, ensure consistency
5. **Document everything**: Clear steps to reproduce bugs

---

## WordPress Base Testing Checklist

### Content Management
- [ ] Create/edit/delete posts
- [ ] Create/edit/delete pages
- [ ] Upload images (JPEG, PNG, GIF, SVG)
- [ ] Upload documents (PDF, DOC)
- [ ] Test file size limits
- [ ] Create/manage categories
- [ ] Create/manage tags
- [ ] Test post revisions
- [ ] Test auto-save functionality

### User Management
- [ ] User registration
- [ ] User login/logout
- [ ] Password reset
- [ ] User role permissions
- [ ] Profile updates
- [ ] Multi-factor authentication (if enabled)

### Comments
- [ ] Submit comment (logged in/out)
- [ ] Comment moderation
- [ ] Spam detection
- [ ] Reply to comments
- [ ] Delete comments

### Navigation
- [ ] Test all menu items
- [ ] Breadcrumb navigation
- [ ] Search functionality
- [ ] 404 page for invalid URLs
- [ ] Pagination on archive pages

### Widgets & Sidebars
- [ ] Add/remove widgets
- [ ] Widget content displays correctly
- [ ] Widgets in different sidebars

---

## WooCommerce Testing Checklist

### Product Display
- [ ] Single product page displays correctly
- [ ] Product archive/category pages
- [ ] Product search
- [ ] Product filtering
- [ ] Product sorting (price, name, popularity)
- [ ] Variable products (select variations)
- [ ] Grouped products
- [ ] Product image gallery
- [ ] Product reviews

### Shopping Cart
- [ ] Add to cart from product page
- [ ] Add to cart from archive page
- [ ] Update cart quantities
- [ ] Remove items from cart
- [ ] Cart totals calculate correctly
- [ ] Apply coupon code
- [ ] Remove coupon
- [ ] Cross-sells display
- [ ] Cart persistence (logged in/out)
- [ ] Empty cart message

### Checkout Process
- [ ] Guest checkout (if enabled)
- [ ] Logged-in user checkout
- [ ] Create account during checkout
- [ ] Billing form validation
- [ ] Shipping form validation
- [ ] Different billing/shipping addresses
- [ ] Shipping method selection
- [ ] Payment method selection
- [ ] Tax calculation
- [ ] Coupon discount applied correctly
- [ ] Order total calculation
- [ ] Terms & conditions checkbox
- [ ] Place order button works

### Payment Gateways
- [ ] Test mode payment (Stripe test cards)
- [ ] Successful payment flow
- [ ] Failed payment handling
- [ ] Payment timeout handling
- [ ] Webhook processing (if applicable)
- [ ] Refund processing (admin)

### Order Management
- [ ] Order confirmation email sent
- [ ] Order appears in My Account
- [ ] Order details correct
- [ ] Order status workflow (pending → processing → completed)
- [ ] Order notes (admin/customer)
- [ ] Cancel order
- [ ] Refund order

### My Account
- [ ] Dashboard overview
- [ ] View orders
- [ ] View order details
- [ ] Download products (if applicable)
- [ ] Edit account details
- [ ] Edit billing address
- [ ] Edit shipping address
- [ ] Change password
- [ ] Logout

### Stock Management
- [ ] In-stock products purchasable
- [ ] Out-of-stock products not purchasable
- [ ] Low stock notifications
- [ ] Backorders (if enabled)
- [ ] Stock updates after purchase

---

## Cross-Browser Testing

### Browsers to Test
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)
- [ ] Mobile Safari (iOS)
- [ ] Chrome Mobile (Android)

### What to Check
- [ ] Layout renders correctly
- [ ] All interactive elements work
- [ ] Forms submit correctly
- [ ] JavaScript functions properly
- [ ] CSS animations/transitions
- [ ] Console errors (check DevTools)

---

## Device Testing

### Screen Sizes
- [ ] Desktop (1920x1080)
- [ ] Laptop (1366x768)
- [ ] Tablet portrait (768px)
- [ ] Tablet landscape (1024px)
- [ ] Mobile (375px - iPhone)
- [ ] Mobile (360px - Android)

### Responsive Checklist
- [ ] Navigation menu (mobile hamburger)
- [ ] Images scale correctly
- [ ] Text readable (min 16px mobile)
- [ ] Buttons/links tappable (min 44x44px)
- [ ] Forms usable on mobile
- [ ] Checkout process on mobile
- [ ] Product gallery on mobile
- [ ] No horizontal scrolling
- [ ] Touch gestures work

---

## Accessibility Testing

### Keyboard Navigation
- [ ] All interactive elements reachable via Tab
- [ ] Focus visible on all elements
- [ ] Logical tab order
- [ ] Skip to content link works
- [ ] Modals trap focus
- [ ] Esc closes modals/dropdowns

### Screen Reader Testing
- [ ] Test with NVDA (Windows) or VoiceOver (Mac)
- [ ] All images have alt text
- [ ] Form labels properly associated
- [ ] ARIA labels where needed
- [ ] Headings in logical order
- [ ] Link text descriptive

### Color Contrast
- [ ] Text/background contrast ≥ 4.5:1
- [ ] Large text contrast ≥ 3:1
- [ ] UI components contrast ≥ 3:1
- [ ] Focus indicators visible

---

## Security Testing

### Input Validation
- [ ] Try SQL injection: `' OR '1'='1`
- [ ] Try XSS: `<script>alert('xss')</script>`
- [ ] Special characters in forms (àèìòù, €, ™)
- [ ] Very long input strings (1000+ chars)
- [ ] Empty required fields
- [ ] Invalid email formats
- [ ] Negative numbers in quantity fields

### Authentication
- [ ] Access `/wp-admin` without login (should redirect)
- [ ] Try accessing other user's orders
- [ ] Try modifying other user's data
- [ ] Test CSRF (submit form without nonce)
- [ ] Test password strength requirements
- [ ] Test login rate limiting

### File Upload
- [ ] Upload `.php` file (should be blocked)
- [ ] Upload oversized file (should be blocked)
- [ ] Upload invalid MIME type
- [ ] Upload image with embedded code

---

## Performance Testing

### Page Load Times
- [ ] Homepage < 3s
- [ ] Product page < 3s
- [ ] Category page < 3s
- [ ] Checkout page < 3s
- [ ] Test on 4G mobile connection

### Database Queries
- [ ] Use Query Monitor plugin
- [ ] Check query count per page
- [ ] Identify slow queries (> 1s)
- [ ] Look for N+1 query problems
- [ ] Check for duplicate queries

### Tools to Use
- Google PageSpeed Insights (desktop/mobile scores)
- GTmetrix (waterfall analysis)
- Chrome Lighthouse (performance audit)
- Query Monitor (WordPress plugin)

---

## Edge Cases & Stress Testing

### Edge Cases
- [ ] Quantity = 0
- [ ] Quantity = 999999
- [ ] Product price = 0
- [ ] Expired coupon
- [ ] Minimum order amount not met
- [ ] Maximum coupon usage reached
- [ ] Product goes out of stock during checkout
- [ ] Session timeout during checkout
- [ ] Network error during payment
- [ ] Multiple payment attempts

### Stress Testing
- [ ] 100+ concurrent users
- [ ] 1000+ products in catalog
- [ ] 100+ items in cart
- [ ] Multiple simultaneous checkouts
- [ ] High traffic spike simulation

---

## Test Case Template

```markdown
### TC-001: Add Product to Cart

**Objective:** Verify user can add product to cart

**Preconditions:**
- User logged in (or guest)
- Product available in stock

**Steps:**
1. Navigate to product page
2. Select quantity: 2
3. Click "Add to Cart"
4. Go to cart page

**Expected Result:**
- Success message: "Product added to cart"
- Cart badge shows "2"
- Cart contains 2 items of product
- Subtotal calculated correctly

**Actual Result:**
[Fill during test]

**Status:** ✅ PASS / ❌ FAIL

**Notes:**
[Any issues or observations]
```

---

## Bug Report Template

```markdown
### BUG-042: Checkout Fails with Special Characters

**Severity:** 🔴 High (blocks transactions)
**Priority:** P1 (urgent fix)

**Environment:**
- WordPress: 6.4.2
- WooCommerce: 8.5.0
- Theme: Storefront 4.0
- PHP: 8.1
- Browser: Chrome 120

**Description:**
Checkout fails if name contains accented characters (à, è, ì, ò, ù)

**Steps to Reproduce:**
1. Add product to cart
2. Proceed to checkout
3. Enter billing first name: "José"
4. Fill other fields
5. Click "Place Order"

**Expected:**
Order completes successfully

**Actual:**
Error: "Invalid billing first name"

**Screenshots:**
[Attach error screenshot]

**Console Errors:**
```
Uncaught Error: Invalid character in sanitize_text_field()
```

**Suggested Fix:**
Use proper Unicode-safe sanitization

**Workaround:**
Use ASCII-only characters in names
```

---

## Automated Testing (Overview)

### PHPUnit (Unit Testing)
```php
class ProductTest extends WP_UnitTestCase {
    public function test_product_price() {
        $product = WC_Helper_Product::create_simple_product();
        $product->set_regular_price(100);
        $product->set_sale_price(80);
        $product->save();

        $this->assertEquals(80, $product->get_price());
    }
}
```

### Codeception (Acceptance Testing)
```php
$I->amOnPage('/product/test-product');
$I->click('Add to Cart');
$I->see('Product added to cart');
```

### Cypress (E2E Testing)
```javascript
cy.visit('/product/test-product');
cy.get('.add-to-cart').click();
cy.contains('Product added to cart').should('be.visible');
```

---

## Regression Testing Workflow

1. **Identify changed area** (feature/bug fix)
2. **Test the specific change**
3. **Test related functionality**
4. **Run smoke tests** (critical paths)
5. **Verify old bugs not reintroduced**

---

**Goal:** Zero critical bugs in production. Every feature tested before release.
