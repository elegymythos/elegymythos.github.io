# Corthyn's Blog

A personal technical blog built with Hugo and the JuiceBar theme, deployed to GitHub Pages.

## Project Structure

```
Corthyn/
├── archetypes/          # Content templates
├── content/             # Blog content
│   └── posts/          # Blog posts
├── themes/             # Theme files (git submodule)
├── public/             # Generated static site
├── resources/          # Hugo resource cache
├── .github/            # GitHub Actions workflows
├── config.toml         # Hugo configuration
└── README.md           # This file
```

## Content

- `content/posts/` - Blog posts in Markdown format

## Development

### Prerequisites

- Hugo Extended v0.80.0 or higher
- Git

### Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/elegymythos/elegymythos.github.io.git
   cd elegymythos.github.io
   ```

2. Initialize submodules:
   ```bash
   git submodule update --init --recursive
   ```

3. Run Hugo server:
   ```bash
   hugo server -D
   ```

4. Visit `http://localhost:1313`

### Creating New Posts

```bash
hugo new posts/my-new-post.md
```

## Deployment

The site is automatically deployed to GitHub Pages via GitHub Actions when pushing to the main branch.

## Configuration

Edit `config.toml` to customize:
- Site title and author information
- Social links
- Navigation menu
- Theme parameters

## Theme

This blog uses the [JuiceBar](https://github.com/hotjuice/hugo-theme-juicebar) theme.
