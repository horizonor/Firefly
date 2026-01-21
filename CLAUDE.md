# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Firefly is an Astro-based blog theme with extensive customization options, built as a fork of the fuwari theme. It features multiple layout systems, i18n support, and comprehensive widget/plugin architecture.

**Tech Stack**: Astro 5.16.8, TypeScript 5.9.2, Tailwind CSS, Svelte 5.46.1, pnpm 9

## Common Commands

### Development
```bash
# Install dependencies (pnpm is enforced via preinstall hook)
pnpm install

# Start development server on localhost:4321
pnpm dev

# Build for production (includes pagefind indexing)
pnpm build

# Preview built site
pnpm preview
```

### Code Quality
```bash
# Type checking
pnpm check

# Format code with Biome
pnpm format

# Lint and auto-fix with Biome
pnpm lint
```

### Content Management
```bash
# Create new blog post
pnpm new-post <filename>
```

## Architecture

### Path Aliases
The codebase uses TypeScript path aliases (configured in `tsconfig.json`):
- `@components/*` → `src/components/*`
- `@assets/*` → `src/assets/*`
- `@constants/*` → `src/constants/*`
- `@utils/*` → `src/utils/*`
- `@i18n/*` → `src/i18n/*`
- `@layouts/*` → `src/layouts/*`
- `@/*` → `src/*`

Always use these aliases when importing from these directories.

### Configuration System

Configuration is **modular** with each feature having its own config file in `src/config/`:
- All configs are re-exported through `src/config/index.ts`
- Import configs like: `import { siteConfig, profileConfig } from '@/config'`
- Configuration files cover: site settings, wallpaper, profile, navigation, sidebar, comments, music, sakura effects, fonts, ads, friends, sponsors, licenses, etc.

Key configuration files:
- `siteConfig.ts` - Site metadata, theme colors, language (`SITE_LANG`), pages toggle
- `sidebarConfig.ts` - Sidebar layout (single/dual sidebar configurations)
- `navBarConfig.ts` - Navigation menu structure
- `backgroundWallpaper.ts` - Background/banner configuration

### Content Collections

Defined in `src/content.config.ts`:
- **posts**: Blog posts (`.md`, `.mdx`) in `src/content/posts/` with rich frontmatter schema
- **spec**: Special pages in `src/content/spec/`

Post frontmatter schema includes: `title`, `published`, `updated`, `draft`, `description`, `image`, `tags`, `category`, `lang`, `pinned`, `author`, `comment`, etc.

### Component Architecture

Components are organized by function (see `src/components/README.md`):
- **layout/** - Page structure (Navbar, Footer, Sidebar, PostCard, etc.)
- **controls/** - Navigation/interaction (BackToTop, FloatingTOC, Search, theme switchers)
- **common/** - Reusable UI primitives (Icon, ImageWrapper, Pagination, Dropdown, etc.)
- **widget/** - Sidebar widgets (Calendar, Categories, Tags, SiteStats, TOC, Announcement, Advertisement)
- **features/** - Global enhancements (FontManager, FancyboxManager, KatexManager, SakuraEffect, MusicPlayer, Live2D/Spine widgets)
- **pages/** - Page-specific components (e.g., bangumi section)
- **comment/** - Third-party comment integrations (Twikoo, Waline, Giscus, Disqus, Artalk)
- **analytics/** - Analytics integrations (Google Analytics, Microsoft Clarity)
- **misc/** - Utilities (IconifyLoader, License, SharePoster)

### Utilities

Key utility modules in `src/utils/`:
- `content-utils.ts` - Content collection helpers
- `layout-utils.ts` - Layout system utilities
- `navigation-utils.ts` - Navigation helpers
- `date-utils.ts` - Date formatting (uses dayjs)
- `image-utils.ts` - Image handling
- `url-utils.ts` - URL utilities
- `language-utils.ts` - i18n helpers
- `widget-manager.ts` - Widget management
- `tocUtils.ts` - Table of contents generation

### Internationalization

- Language configuration: `SITE_LANG` in `siteConfig.ts`
- Supported: `zh_CN`, `zh_TW`, `en`, `ja`, `ru`
- i18n files in `src/i18n/languages/`
- Import language utilities from `@i18n/*`

### Routing

Key pages in `src/pages/`:
- `[...page].astro` - Paginated post listing (main index)
- `posts/[...slug].astro` - Individual post pages
- `archive.astro` - Post archive
- `friends.astro` - Friend links
- `about.astro` - About page
- `guestbook.astro` - Guestbook
- `sponsor.astro` - Sponsor page
- `bangumi.astro` - Bangumi (anime/game tracking)
- `search.astro` - Search page
- `404.astro` - Error page
- `api/` - API endpoints
- `og/` - Open Graph image generation

### Layout System

Firefly supports multiple layout modes (configurable via `sidebarConfig.ts`):
- Single sidebar (left or right)
- Dual sidebars (left + right)
- Post layout: list (single-column), grid (multi-column), waterfall

Widget placement is configured per-sidebar in config.

### Markdown/MDX Pipeline

Configured in `astro.config.mjs`:

**Remark plugins**:
- `remark-math` - LaTeX math syntax
- `remark-reading-time` - Reading time calculation
- `remark-excerpt` - Post excerpt generation
- `remark-directive` - Custom directive support
- `remark-sectionize` - Section wrapping
- `remark-mermaid` - Mermaid diagram support

**Rehype plugins**:
- `rehype-katex` - KaTeX rendering (with mhchem for chemistry)
- `rehype-callouts` - Admonitions/callouts (GitHub, Obsidian, VitePress themes)
- `rehype-slug` - Heading IDs
- `rehype-autolink-headings` - Heading anchor links
- `rehype-mermaid` - Mermaid processing
- `rehype-figure` - Figure/caption generation
- `rehype-email-protection` - Email obfuscation (base64/rot13)
- `rehype-components` - Custom components (GitHub card)

**Expressive Code**: Enhanced code blocks with collapsible sections, line numbers, custom copy button

### Styling

- **Tailwind CSS** with `@tailwindcss/typography` for prose
- **Biome** for formatting (tabs, double quotes for JS)
- Custom styles in `src/styles/`
- Theme switching via `data-theme` attribute
- Custom fonts configured in `fontConfig.ts`

### Page Transitions

Uses **Swup** (@swup/astro) for smooth page transitions:
- Containers: `main`, `#right-sidebar-dynamic`, `#floating-toc-wrapper`
- Transition class prefix: `transition-swup-`

### Search

Pagefind integration:
- Built during production build (`astro build && pagefind --site dist`)
- Search component in `src/components/controls/Search.svelte`

### Third-Party Integrations

- **Comment systems**: Twikoo, Waline, Giscus, Disqus, Artalk (config in `commentConfig.ts`)
- **Music player**: APlayer + MetingJS (config in `musicConfig.ts`)
- **Analytics**: Google Analytics, Microsoft Clarity
- **Live mascots**: Live2D and Spine models (config in `pioConfig.ts`)
- **Effects**: Sakura petals, typewriter text, Fancybox lightbox

## Development Notes

### Environment
- **Node.js**: ≥22
- **pnpm**: ≥9 (enforced - npm/yarn will be rejected)
- Biome formatting uses **tabs** for indentation

### Content Creation
- Posts go in `src/content/posts/` (`.md` or `.mdx`)
- Use frontmatter as defined in `src/content.config.ts`
- Set `image: "api"` to use random cover image API

### Adding Features
- New config files should be added to `src/config/` and re-exported in `index.ts`
- Components should follow the organization in `src/components/README.md`
- Utility functions go in `src/utils/`
- Use path aliases for imports

### SEO
- Sitemap auto-generated (filters pages based on `siteConfig.pages` toggles)
- RSS feed at `/rss.xml` and `/rss.astro`
- OG image generation in `src/pages/og/`

### Custom Directives
Markdown supports custom directives processed by `parseDirectiveNode` plugin. Example: GitHub card component for repo embeds.
