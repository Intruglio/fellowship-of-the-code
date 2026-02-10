# Contributing to The Fellowship of WordPress Agents

First off, thank you for considering contributing to The Fellowship! 🎉

> "Even the smallest person can change the course of the future." — Gandalf

This document provides guidelines for contributing to this project. Following these guidelines helps maintain quality and makes it easier for maintainers to review and merge your contributions.

---

## 📋 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Agent Development Guidelines](#agent-development-guidelines)
- [Pull Request Process](#pull-request-process)
- [Style Guidelines](#style-guidelines)
- [Community](#community)

---

## 📜 Code of Conduct

This project follows the spirit of **The Fellowship**: collaboration, respect, and working together toward a common goal.

### Our Standards

✅ **DO**:
- Be respectful and inclusive
- Provide constructive feedback
- Help others learn and grow
- Give credit where it's due
- Stay on topic in discussions

❌ **DON'T**:
- Use offensive or inappropriate language
- Attack others personally
- Spam or troll
- Share private information without permission

---

## 🤝 How Can I Contribute?

### 🐛 Reporting Bugs

Before creating a bug report:
1. **Check existing issues** to avoid duplicates
2. **Test with the latest version** of Claude Code
3. **Gather information**: What agent? What command? What happened vs. expected?

**Create a bug report with**:
```markdown
**Agent**: Which agent (e.g., Gandalf, Aragorn)?
**Command used**: The exact command you ran
**Expected behavior**: What you expected to happen
**Actual behavior**: What actually happened
**Environment**:
  - Claude Code version:
  - WordPress version:
  - PHP version:
**Additional context**: Screenshots, error logs, etc.
```

---

### ✨ Suggesting Features

We love new ideas! Before suggesting:
1. **Check existing feature requests**
2. **Consider if it fits the project scope** (WordPress/WooCommerce agents)
3. **Think about which agent** it would enhance

**Feature request template**:
```markdown
**Agent(s) affected**: Which agent(s)?
**Problem**: What problem does this solve?
**Proposed solution**: Your idea
**Alternatives considered**: Other approaches you thought of
**LOTR inspiration**: (Optional) How does this fit the LOTR theme?
```

---

### 🎭 Proposing New Agents

Want to add a new character from LOTR as an agent?

**Requirements**:
1. **Unique expertise**: The agent must fill a gap, not duplicate existing agents
2. **LOTR character**: Must be an actual character from Tolkien's works
3. **Clear personality**: The character's traits must align with the agent's role
4. **Signature phrases**: Include at least 4 relevant LOTR quotes

**Proposal template**:
```markdown
**Character**: Name from LOTR
**Specialization**: What domain/expertise?
**Why this character**: How do their traits fit the role?
**Signature phrases**: List 4+ relevant quotes
**Use cases**: When would users call this agent?
**Doesn't overlap with**: Which existing agents are similar, and how is this different?
```

---

### 📝 Improving Documentation

Documentation improvements are always welcome!

**Good documentation PRs**:
- Fix typos or grammar
- Add missing examples
- Clarify confusing sections
- Translate to additional languages
- Add diagrams or visual aids

**For major doc changes**, open an issue first to discuss.

---

## 🛠️ Development Setup

### Prerequisites

1. **Claude Code**: Install from [claude.com/claude-code](https://claude.com/claude-code)
2. **Git**: Version control
3. **A text editor**: VS Code, Sublime, etc.
4. **Basic knowledge of**:
   - Markdown (for agent files)
   - WordPress/WooCommerce (optional but helpful)
   - LOTR lore (helpful for character accuracy)

### Setting Up Your Fork

1. **Fork the repository** on GitHub

2. **Clone your fork**:
   ```bash
   git clone https://github.com/Intruglio/fellowship-of-the-code.git
   cd fellowship-of-the-code
   ```

3. **Add upstream remote**:
   ```bash
   git remote add upstream https://github.com/Intruglio/fellowship-of-the-code.git
   ```

4. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```

5. **Make your changes** and test them

6. **Commit with clear messages**:
   ```bash
   git commit -m "feat: Add new signature phrase for Gandalf"
   ```

7. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

8. **Open a Pull Request** on GitHub

---

## 🧙‍♂️ Agent Development Guidelines

When creating or modifying agents, follow these rules:

### 1. File Structure

Every agent file must include:

```markdown
# [Character Name] - The [Title]
## [Role Description]

> **IT**: "[Quote in Italian]"
> **EN**: "[Quote in English]"

[Bilingual introduction in both IT and EN]

---

## 🌍 Language Support
[Language support section]

## 🎬 Signature Phrases
[Table of character quotes]

## Ruolo e Competenze / Role and Skills
[Bilingual sections...]

## Workflow
[How the agent works]

## Output Format
[Expected output structure]

## Examples
[Practical examples in both languages]
```

### 2. Character Accuracy

- **Stay true to the character**: Gandalf should be wise, not reckless
- **Use appropriate quotes**: The quote must fit the context
- **Maintain personality**: Each agent has distinct traits

### 3. Bilingual Requirements

All agent content must be available in:
- 🇮🇹 Italian
- 🇬🇧 English

**Format**:
```markdown
## Section Title / Titolo Sezione

**EN**: English content...

**IT**: Contenuto italiano...
```

Or use headers:
```markdown
### English
Content...

### Italiano
Contenuto...
```

### 4. Signature Phrases

Each agent must have **4-8 signature phrases** from LOTR.

**Format**:
```markdown
| IT 🇮🇹 | EN 🇬🇧 | When to Use |
|---------|---------|-------------|
| "Frase italiana" | "English quote" | Context for using this |
```

**Sources**:
- The Lord of the Rings books (preferred)
- The Hobbit
- Official film adaptations
- Tolkien's other works

### 5. Testing Your Agent

Before submitting:

1. **Test in Claude Code**:
   ```bash
   claude
   # Then: Claude, consult [YourAgent] for [test task]
   ```

2. **Verify**:
   - [ ] Responds in both Italian and English
   - [ ] Uses signature phrases appropriately
   - [ ] Stays in character
   - [ ] Provides actionable output
   - [ ] Follows the output format

3. **Get feedback**: Test with real WordPress/WooCommerce tasks

---

## 🔀 Pull Request Process

### Before Submitting

- [ ] **Test your changes** thoroughly
- [ ] **Update documentation** if needed
- [ ] **Check for typos** and grammar
- [ ] **Verify bilingual content** (IT and EN)
- [ ] **Follow the style guidelines** (below)

### PR Title Format

Use [Conventional Commits](https://www.conventionalcommits.org/):

- `feat: Add new Éowyn agent for deployment management`
- `fix: Correct Gandalf signature phrase translation`
- `docs: Update README with new agent`
- `style: Fix formatting in Aragorn agent`
- `refactor: Reorganize Legolas workflow section`
- `test: Add example usage for Gimli`

### PR Description Template

```markdown
## Description
Brief description of what this PR does

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Agent(s) Affected
Which agents are modified or added?

## Testing
How did you test this?

## Screenshots (if applicable)
Add screenshots of output, examples, etc.

## Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code (if needed)
- [ ] I have updated the documentation
- [ ] My changes are bilingual (IT + EN)
- [ ] I have tested with Claude Code
```

### Review Process

1. **Maintainer review**: A maintainer will review your PR
2. **Feedback**: You may be asked to make changes
3. **Approval**: Once approved, your PR will be merged
4. **Credit**: You'll be added to contributors list

---

## 📐 Style Guidelines

### Markdown

- Use `#` for headers (not underlines)
- Use `-` for unordered lists
- Use `1.` for ordered lists
- Use ` ``` ` for code blocks with language specification
- Use `**bold**` for emphasis (not `__bold__`)
- Use `*italic*` for lighter emphasis

### Writing Style

- **Be clear and concise**
- **Use active voice**: "Aragorn implements code" not "Code is implemented by Aragorn"
- **Avoid jargon** unless necessary (then explain it)
- **Use examples** to illustrate concepts
- **Format code** with proper indentation

### LOTR References

- **Use official character names**: "Gandalf the Grey" not "Gray Gandalf"
- **Quote accurately**: Check against source material
- **Respect canon**: Don't make up quotes or character traits
- **Credit sources**: Note if from books vs films

---

## 🌍 Adding Language Support

Want to add support for another language (e.g., Spanish, French, German)?

1. **Create language files**:
   - `LOTR_AGENTS_ES.md` (Spanish)
   - `LOTR_AGENTS_FR.md` (French)
   - etc.

2. **Update AGENTS_GUIDELINES.md** with the new language

3. **Translate agent content** (optional but helpful)

4. **Add flag emoji** and language code to README

5. **Test** with native speakers if possible

---

## 💬 Community

### Where to Discuss

- **GitHub Issues**: Bug reports, feature requests
- **GitHub Discussions**: General questions, ideas, showcase
- **Pull Requests**: Code reviews, implementation discussions

### Getting Help

**Stuck? Ask!**
- Open a **Discussion** on GitHub
- Check existing **Issues** for similar questions
- Tag maintainers in your PR if you need guidance

### Recognition

Contributors are listed in:
- GitHub Contributors page
- README acknowledgments (for significant contributions)
- Release notes (for features/fixes)

---

## 🎯 Contribution Ideas

Not sure where to start? Here are some ideas:

### Easy (Good First Issues)
- Fix typos or grammar in docs
- Add examples to existing agents
- Improve formatting consistency
- Add missing signature phrases

### Medium
- Translate docs to a new language
- Improve agent workflows
- Add new sections to agents (e.g., troubleshooting)
- Create tutorial content

### Advanced
- Design a new agent for a missing domain
- Improve agent interaction logic
- Create automated testing for agents
- Build integration tools

---

## 📜 License

By contributing, you agree that your contributions will be licensed under the same **MIT License** that covers this project.

---

## 🙏 Thank You!

> "The Fellowship of the Ring is now complete." — Elrond

Every contribution, no matter how small, helps make this project better. Thank you for being part of The Fellowship!

---

**Questions?** Open a [Discussion](https://github.com/Intruglio/fellowship-of-the-code/discussions) or [Issue](https://github.com/Intruglio/fellowship-of-the-code/issues).

**Ready to contribute?** Fork the repo and start coding! 🚀
