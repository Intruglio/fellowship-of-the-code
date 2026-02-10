# 🧙‍♂️ The Fellowship of WordPress Agents
### Specialized Claude Code Agents for WordPress & WooCommerce Development

![WordPress](https://img.shields.io/badge/WordPress-Ready-21759B?style=for-the-badge&logo=wordpress)
![WooCommerce](https://img.shields.io/badge/WooCommerce-Compatible-96588A?style=for-the-badge&logo=woocommerce)
![PHP](https://img.shields.io/badge/PHP-Supported-777BB4?style=for-the-badge&logo=php)
![Fellowship](https://img.shields.io/badge/Agents-12-green?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

> "Even the smallest person can change the course of the future." — Gandalf

A collection of **12 specialized AI agents** inspired by *The Lord of the Rings* characters, each expert in a specific domain of WordPress and WooCommerce development. Built for [Claude Code](https://claude.com/claude-code), the official CLI tool from Anthropic.

---

## 🌟 What is This?

The Fellowship is a set of **specialized AI agents** that work with Claude Code to provide expert assistance in:

- 🏗️ **Architecture & Planning** — Strategic design and system architecture
- ⚔️ **Code Implementation** — Clean, secure, production-ready code
- 🐛 **Debugging & Testing** — Root cause analysis and QA
- 🎨 **Frontend & UX** — Accessible, performant user interfaces
- 🔍 **SEO & Content** — Search optimization and content strategy
- 📚 **Documentation** — Comprehensive technical documentation
- 🌍 **Translation** — Multilingual content and localization
- 🎬 **Video & Marketing** — B2B strategy and brand positioning

Each agent has a **distinct personality** and expertise, modeled after iconic LOTR characters.

---

## 🚀 Quick Start

### Prerequisites

1. **Claude Code** — Install from [claude.com/claude-code](https://claude.com/claude-code)
2. **Active Claude subscription** (Pro, Team, or Enterprise)

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/Intruglio/fellowship-of-the-code.git
   cd fellowship-of-the-code
   ```

2. Copy the `.claude` directory to your WordPress project:
   ```bash
   cp -r .claude /path/to/your/wordpress/project/
   cp CLAUDE.md /path/to/your/wordpress/project/
   ```

3. Start using agents in your project:
   ```bash
   cd /path/to/your/wordpress/project
   claude
   ```

4. Request an agent:
   ```
   Claude, consult Gandalf to design the architecture for a new plugin
   ```

---

## 👥 Meet The Fellowship

| Agent | Role | Expertise | When to Use |
|-------|------|-----------|-------------|
| 🧙‍♂️ **[Gandalf](/.claude/agents/gandalf.md)** | Architect | WordPress architecture, hook system, template hierarchy | Design plugin/theme architecture |
| ⚔️ **[Aragorn](/.claude/agents/aragorn.md)** | Implementer | Clean PHP/WordPress code, security, best practices | Write production-ready code |
| 🥔 **[Samwise](/.claude/agents/samwise.md)** | Debugger | Debugging, troubleshooting, root cause analysis | Find and fix bugs |
| 🏹 **[Legolas](/.claude/agents/legolas.md)** | Frontend Expert | HTML5, CSS, JavaScript, accessibility, performance | Build accessible UIs |
| ⚒️ **[Gimli](/.claude/agents/gimli.md)** | QA Tester | Testing, QA, test plans, regression testing | Create comprehensive tests |
| 🌟 **[Galadriel](/.claude/agents/galadriel.md)** | SEO Specialist | SEO, content strategy, schema.org, keyword research | Optimize for search engines |
| 👑 **[Elrond](/.claude/agents/elrond.md)** | Code Reviewer | Code review, security audit, best practices | Review code quality |
| 🎬 **[Saruman](/.claude/agents/saruman.md)** | Marketing Strategist | Video strategy, B2B advertising, brand positioning | Plan marketing campaigns |
| 🛡️ **[Faramir](/.claude/agents/faramir.md)** | Strategic Advisor | Planning, decision-making, prioritization | Plan your day/project |
| 🌳 **[Treebeard](/.claude/agents/treebeard.md)** | Documentation Writer | README, CHANGELOG, API docs, inline documentation | Write comprehensive docs |
| 🎵 **[Tom Bombadil](/.claude/agents/tom-bombadil.md)** | Teacher | Explanations, tutorials, teaching concepts | Learn WordPress concepts |
| 📚 **[Bilbo](/.claude/agents/bilbo.md)** | Translator | IT↔EN translation, glossaries, localization | Translate content |

---

## 📖 Documentation

- **[🇬🇧 Quick Reference Guide (English)](LOTR_AGENTS_EN.md)** — Fast lookup for all agents
- **[🇮🇹 Guida Rapida (Italiano)](LOTR_AGENTS_IT.md)** — Riferimento rapido in italiano
- **[Development Guidelines](CLAUDE.md)** — WordPress/WooCommerce best practices
- **[Agent Guidelines](/.claude/agents/AGENTS_GUIDELINES.md)** — Common rules for all agents

---

## 🌍 Language Support

All agents are **bilingual**: 🇮🇹 Italian & 🇬🇧 English

Agents automatically detect your language or you can specify:
```
Claude, consult Aragorn to implement this feature (respond in English)
```

```
Claude, consulta Aragorn per implementare questa funzionalità (rispondi in italiano)
```

---

## 💡 Example Usage

### Planning a New Feature

```
Claude, consult Faramir to plan the development of a custom
WooCommerce checkout flow with multi-step validation.
```

**Faramir will**:
- Break down the task into actionable steps
- Identify risks and dependencies
- Provide a prioritized roadmap
- Suggest time estimates

---

### Implementing the Feature

```
Claude, consult Gandalf to design the architecture for the
multi-step checkout, then have Aragorn implement it.
```

**Gandalf will**:
- Design hook strategy
- Plan database schema
- Define integration points

**Aragorn will**:
- Write secure, production-ready code
- Follow WordPress coding standards
- Implement proper validation

---

### Testing & Review

```
Claude, consult Gimli to create a test plan for the checkout flow,
then have Elrond review the code.
```

**Gimli will**:
- Create comprehensive test cases
- Define acceptance criteria
- Plan regression testing

**Elrond will**:
- Review code quality
- Audit security
- Suggest improvements

---

## 🎯 Recommended Workflow

For a complete WordPress/WooCommerce project:

### Phase 1: Planning & Architecture
1. **Faramir** → Define strategy and priorities
2. **Gandalf** → Design technical architecture

### Phase 2: Implementation
3. **Aragorn** → Implement backend code
4. **Legolas** → Implement frontend

### Phase 3: Quality Assurance
5. **Samwise** → Debug any issues
6. **Gimli** → Run comprehensive tests
7. **Elrond** → Final code review

### Phase 4: Launch
8. **Galadriel** → Optimize for SEO
9. **Treebeard** → Complete documentation

### Phase 5: Special Tasks
- **Saruman** → Marketing/video strategy (if needed)
- **Tom Bombadil** → User training (if needed)
- **Bilbo** → Translation/localization (if needed)

---

## 🎬 Signature Phrases

Each agent occasionally uses **iconic LOTR quotes** relevant to the task:

> **Gandalf**: "All we have to decide is what to do with the time that is given us."

> **Aragorn**: "There is always hope."

> **Samwise**: "I can't carry it for you, but I can carry you!"

> **Faramir**: "I would not use this thing, even in my darkest hour."
> _(When rejecting shortcuts or bad practices)_

See [AGENTS_GUIDELINES.md](/.claude/agents/AGENTS_GUIDELINES.md) for the complete phrase library.

---

## 🛠️ Tech Stack

- **WordPress**: 5.0+
- **WooCommerce**: 5.0+
- **PHP**: 7.4+
- **JavaScript**: ES6+
- **Claude Code**: Latest version

---

## 📂 Project Structure

```
fellowship-of-the-code/
├── README.md                      # This file
├── CLAUDE.md                      # WordPress development guidelines
├── LOTR_AGENTS_EN.md             # Quick reference (English)
├── LOTR_AGENTS_IT.md             # Guida rapida (Italiano)
├── LICENSE                        # MIT License
├── .gitignore                     # Git ignore patterns
└── .claude/
    └── agents/
        ├── AGENTS_GUIDELINES.md   # Common agent rules
        ├── gandalf.md             # Architecture agent
        ├── aragorn.md             # Implementation agent
        ├── samwise.md             # Debugging agent
        ├── legolas.md             # Frontend agent
        ├── gimli.md               # Testing agent
        ├── galadriel.md           # SEO agent
        ├── elrond.md              # Review agent
        ├── saruman.md             # Marketing agent
        ├── faramir.md             # Planning agent
        ├── treebeard.md           # Documentation agent
        ├── tom-bombadil.md        # Teaching agent
        └── bilbo.md               # Translation agent
```

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Ways to contribute:
- 🐛 Report bugs or issues with agents
- ✨ Suggest new features or agent improvements
- 📝 Improve documentation
- 🌍 Add support for more languages
- 🎭 Suggest new LOTR character agents

---

## 📜 License

This project is licensed under the **MIT License** - see [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Anthropic** for creating Claude and Claude Code
- **J.R.R. Tolkien** for The Lord of the Rings universe
- **WordPress & WooCommerce communities** for their amazing work

---

## 🔗 Links

- **Claude Code**: [claude.com/claude-code](https://claude.com/claude-code)
- **Claude API**: [docs.anthropic.com](https://docs.anthropic.com)
- **WordPress**: [wordpress.org](https://wordpress.org)
- **WooCommerce**: [woocommerce.com](https://woocommerce.com)

---

## 📬 Support

- **Issues**: [GitHub Issues](https://github.com/Intruglio/fellowship-of-the-code/issues)
- **Discussions**: [GitHub Discussions](https://github.com/Intruglio/fellowship-of-the-code/discussions)

---

## 🌟 Star History

If you find this project helpful, please consider giving it a ⭐ on GitHub!

---

**The Fellowship is ready. Choose your agent and let the journey begin!** 🗡️

> "All we have to decide is what to do with the time that is given us." — Gandalf

---

**Made with ❤️ for the WordPress & Claude communities**
