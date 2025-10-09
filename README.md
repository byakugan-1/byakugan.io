## Byakugan.io — a Fovereignty-First Bitcoin blog

Byakuga.io is a GitHub Pages–hosted, open repository for articles, guides, and
notes about running Bitcoin with sovereignty and privacy. The name nods to the
Byakugan kekkei genkai.

![byakugan](/assets/img/readme.png)

## What you’ll find
- Reproducible guides focused on nodes, privacy, and self-hosting
- Minimal, auditable configurations over “one-click” black boxes
- Plain-language explanations of trade-offs and operational security
- Per-post comment threads on GitHub so anyone can react or discuss in the open

## How to contribute
You do not need to be a “shadowy super coder” to help. Every contribution helps.

- Request a topic or guide you think others will find useful
- Fix typos, broken links, or clarify confusing steps
- Update out-of-date content (versions, flags, screenshots)
- Propose a new guide or add practical tips to an existing one

### Quick paths
- Non-technical: open an issue describing what should change or be added
- Technical: fork the repo and submit a pull request with your edits

## Comments and reactions
Each post includes an open comment thread at the bottom that uses GitHub.
- Click the comment section under post to discuss, ask questions, or react
- Keep it constructive, specific, and respectful
- If something needs a code or content change, consider opening an issue or PR

## Local development (GitHub Pages/Jekyll)
The site is designed to build on GitHub Pages. You can also run it locally.

### Prerequisites
- Ruby and Bundler installed
- Jekyll (GitHub Pages supports a specific Jekyll stack)

### Build and serve locally
```bash
bundle install
bundle exec jekyll serve
```

Site will be available at:
- http://127.0.0.1:4000
- or the baseurl configured in _config.yml

## Repository structure (typical)
- _posts/ — dated Markdown posts (YYYY-MM-DD-title.md)
- _layouts/, _includes/, _data/ — theme and site structure
- assets/ — images, CSS, JS
- pages like about.md, index.md
- README.md (this file)

Note: Exact structure may varyrepo for details.

## Writing guidelines
- Use Markdown. Prefer headings, lists, and fenced code blocks over HTML
- Commands: fenced code blocks with language hints (e.g., ```bash)
- Configuration files: ```txt or appropriate language (e.g., ```yaml, ```ini)
- Be explicit about versions and commands; add verification steps where relevant
- Explain the “why”, not just the “how”
- Note privacy and security trade-offs where applicable

## Submitting changes
1. Fork the repository
2. Create a feature branch
3. Make your edits (keep commits focused and descriptive)
4. Run the site locally if possible to verify formatting/build
5. Open a pull request describing what you changed and why

## Issues
Use issues to:
- Suggest topics or guides
- Report inaccuracies, broken links, or outdated instructions
- Ask clarifying questions that could improve a post

Please include:
- The URL or file you’re referencing
- Steps to reproduce, if applicable
- Proposed fix or links to sources

## Scope and ethos
- Sovereignty and Bitcoin first: verify, minimize trust, retain control
- Privacy by default, sharing by choice
- Fewer moving parts where possible; clear, maintainable ops

## Acknowledgements
This project stands on the shoulders of the free and open-source community:
Bitcoin Core contributors, node operators, privacy researchers, and educators
who publish their work.

## License
Unless otherwise specified, content in this repository is available under an
open license. See LICENSE in the root of the repository.

## Disclaimer
Nothing here is financial advice. You are responsible for your own due
diligence, local laws, and maintenance. Test changes, keep good backups,
and verify before you trust.

Byakugan — see clearly, store privately, act intentionally.