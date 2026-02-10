# Fellowship Knowledge Base

Centralized WordPress/WooCommerce knowledge shared across all agents.

---

## Purpose

This knowledge base eliminates duplication across agent files by centralizing common patterns, standards, and guidelines that multiple agents reference.

**Before optimization:** ~3,443 lines total (significant duplication)
**After optimization:** Shared knowledge extracted, agents focus on specific expertise

---

## Knowledge Files

### [`wordpress-core.md`](wordpress-core.md)
**What:** Core WordPress/WooCommerce patterns and APIs
**Used by:** Gandalf, Aragorn, Samwise, Galadriel
**Contains:**
- Essential hooks (WP & WC)
- Common patterns (AJAX, enqueue, WP_Query)
- WordPress/WooCommerce APIs
- Coding standards

### [`security-standards.md`](security-standards.md)
**What:** Security best practices and OWASP guidelines
**Used by:** Aragorn, Elrond, Samwise
**Contains:**
- Security checklist (nonces, sanitization, escaping)
- OWASP Top 10 for WordPress
- Security testing procedures
- Common security functions reference

### [`performance-metrics.md`](performance-metrics.md)
**What:** Performance standards and optimization techniques
**Used by:** Gimli, Legolas, Galadriel
**Contains:**
- Core Web Vitals targets
- Performance benchmarks
- WooCommerce-specific optimizations
- Caching strategies
- Performance testing tools

### [`testing-guidelines.md`](testing-guidelines.md)
**What:** Testing strategies and checklists
**Used by:** Gimli, Elrond
**Contains:**
- WordPress base testing checklist
- WooCommerce testing scenarios
- Cross-browser/device testing
- Security testing
- Bug report templates

---

## How Agents Use Knowledge Base

Instead of duplicating content, agents now **reference** shared knowledge:

**Example (Old Way - Duplicated):**
```markdown
# Aragorn Agent

## Security Checklist
- [ ] Nonce verification
- [ ] Capability checks
- [ ] Input sanitization
[... 50 lines of duplicated security content ...]
```

**Example (New Way - Referenced):**
```markdown
# Aragorn Agent

## Security
Follow security guidelines in `.claude/knowledge/security-standards.md`

Aragorn-specific security focus:
- Production-ready code review
- Security implementation in features
```

---

## Benefits

### 1. **Reduced Token Usage**
- Eliminate ~1,500+ lines of duplication
- Faster context loading
- More efficient API calls

### 2. **Single Source of Truth**
- Update security standards once, all agents benefit
- Consistency across all agents
- Easier maintenance

### 3. **Agent Focus**
- Agents stay focused on their specific expertise
- Cleaner, more readable agent files
- Better separation of concerns

### 4. **Scalability**
- Add new agents without duplicating knowledge
- Easy to extend knowledge base with new topics

---

## Usage Guidelines

### For Users
When working with an agent, you may see references like:
```
"Refer to security-standards.md for OWASP checklist"
```

The agent has access to this knowledge and will apply it appropriately.

### For Contributors
When adding new agents:
1. Check if knowledge already exists in this folder
2. Reference existing knowledge instead of duplicating
3. Only add agent-specific workflows/patterns to agent file
4. Add new shared knowledge here if needed by 2+ agents

---

## Knowledge Base Structure

```
.claude/knowledge/
├── README.md                    # This file
├── wordpress-core.md            # WP/WC core patterns
├── security-standards.md        # Security best practices
├── performance-metrics.md       # Performance guidelines
└── testing-guidelines.md        # Testing strategies
```

---

## Maintenance

### When to Update Knowledge Base
- WordPress/WooCommerce version updates
- New security vulnerabilities discovered
- Performance best practices evolve
- Testing tools/methods change

### Update Process
1. Identify outdated information
2. Update centralized knowledge file
3. Verify agents still reference correctly
4. Test that agents apply updates appropriately

---

## Future Expansion

Potential new knowledge files:
- `accessibility-standards.md` (WCAG guidelines)
- `seo-best-practices.md` (SEO checklists)
- `woocommerce-apis.md` (WC-specific deep dive)
- `gutenberg-blocks.md` (Block editor development)

Add new files when knowledge is used by 2+ agents.

---

**Last updated:** 2026-02-10
**Maintained by:** The Fellowship of WordPress Agents
