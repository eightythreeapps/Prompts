# Reusable Prompt: Modern Marketing Website Generator

Use this prompt to generate a complete marketing website using Next.js, TypeScript, Tailwind CSS, and GitHub Pages hosting.

---

## Project Requirements

Create a modern, SEO-optimized marketing website with the following specifications:

### Tech Stack
- **Framework**: Next.js 15 with App Router
- **Language**: TypeScript 5
- **Styling**: Tailwind CSS 4 with PostCSS
- **Icons**: Lucide React
- **Content**: Markdown/MDX with gray-matter for frontmatter parsing
- **Markdown Rendering**: react-markdown
- **Deployment**: GitHub Pages (static export)
- **Fonts**: Geist Sans and Geist Mono from Google Fonts

### Project Structure
```
project-root/
├── src/
│   ├── app/                    # Next.js App Router pages
│   │   ├── globals.css        # Global styles and CSS theme system
│   │   ├── layout.tsx         # Root layout with metadata and theme toggle
│   │   ├── page.tsx           # Landing page
│   │   ├── about/            # About page
│   │   │   └── page.tsx
│   │   ├── features/         # Features page
│   │   │   └── page.tsx
│   │   ├── blog/             # Blog system
│   │   │   ├── page.tsx      # Blog listing page
│   │   │   └── [slug]/       # Dynamic blog post pages
│   │   │       └── page.tsx
│   │   ├── privacy/          # Privacy policy page
│   │   │   └── page.tsx
│   │   ├── terms/            # Terms of service page
│   │   │   └── page.tsx
│   │   ├── sitemap.ts        # Auto-generated sitemap
│   │   ├── robots.ts         # SEO robots.txt
│   │   └── favicon.ico       # Favicon
│   ├── components/            # Reusable React components
│   │   ├── ThemeToggle.tsx   # Dark/light theme switcher
│   │   ├── ThemeAwareImage.tsx # Image component with theme support
│   │   └── ThemeAwareAppIcon.tsx # App icon component with theme support
│   ├── content/               # Content management
│   │   └── posts/            # MDX/Markdown blog posts
│   │       └── *.md          # Blog post files with frontmatter
│   └── lib/                  # Utility functions
│       ├── blog.ts           # Blog post processing (getAllPosts, getPostBySlug)
│       └── assets.ts         # Asset path helpers
├── public/                   # Static assets
│   ├── favicon.svg          # SVG favicon
│   ├── og-image.svg         # Open Graph image
│   ├── app-icon.svg         # App icon SVG
│   └── [screenshots/]       # Marketing images
├── package.json             # Dependencies and scripts
├── next.config.ts           # Next.js configuration
├── tailwind.config.ts       # Tailwind CSS configuration
├── tsconfig.json            # TypeScript configuration
├── eslint.config.mjs        # ESLint configuration
├── postcss.config.mjs       # PostCSS configuration
└── README.md                # Project documentation
```

### Configuration Files

#### package.json
```json
{
  "name": "project-name-website",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build --turbopack",
    "start": "next start",
    "lint": "eslint",
    "lint:fix": "eslint --fix",
    "type-check": "tsc --noEmit",
    "export": "next build",
    "deploy": "npm run build"
  },
  "dependencies": {
    "@mdx-js/loader": "^3.1.1",
    "@mdx-js/react": "^3.1.1",
    "@next/mdx": "^15.5.4",
    "@types/mdx": "^2.0.13",
    "gray-matter": "^4.0.3",
    "lucide-react": "^0.544.0",
    "next": "15.5.4",
    "react": "19.1.0",
    "react-dom": "19.1.0",
    "react-markdown": "^10.1.0"
  },
  "devDependencies": {
    "@eslint/eslintrc": "^3",
    "@tailwindcss/postcss": "^4",
    "@types/node": "^20",
    "@types/react": "^19",
    "@types/react-dom": "^19",
    "eslint": "^9",
    "eslint-config-next": "15.5.4",
    "tailwindcss": "^4",
    "typescript": "^5"
  }
}
```

#### next.config.ts
```typescript
import { NextConfig } from 'next';

const nextConfig: NextConfig = {
  pageExtensions: ['js', 'jsx', 'mdx', 'ts', 'tsx'],
  experimental: {
    mdxRs: true,
  },
  output: 'export',
  trailingSlash: true,
  images: {
    unoptimized: true,
  },
  typescript: {
    ignoreBuildErrors: true,
  },
  eslint: {
    ignoreDuringBuilds: true,
  },
};

export default nextConfig;
```

#### tailwind.config.ts
```typescript
import { Config } from 'tailwindcss'

export default {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
} satisfies Config
```

#### tsconfig.json
```json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": [
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts",
    "next-env.d.ts",
    "out/types/**/*.ts"
  ],
  "exclude": ["node_modules"]
}
```

#### eslint.config.mjs
```javascript
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const compat = new FlatCompat({
  baseDirectory: __dirname,
});

const eslintConfig = [
  ...compat.extends("next/core-web-vitals", "next/typescript"),
  {
    ignores: [
      "node_modules/**",
      ".next/**",
      "out/**",
      "build/**",
      "next-env.d.ts",
    ],
  },
];

export default eslintConfig;
```

#### postcss.config.mjs
```javascript
const config = {
  plugins: ["@tailwindcss/postcss"],
};

export default config;
```

### Core Features

#### 1. Theme System (Dark/Light Mode)
- CSS custom properties for theme colors
- System preference detection with manual override
- Theme toggle component with localStorage persistence
- Smooth transitions between themes
- Glass morphism effects that adapt to theme

**CSS Theme Variables**:
```css
:root {
  --background: #ffffff;
  --foreground: #1a1a1a;
  --card-background: #f7fafc;
  --card-border: #e2e8f0;
  --accent: #00bcd4; /* Customize this color */
  --accent-hover: #00acc1;
  --text-secondary: #64748b;
  --text-muted: #94a3b8;
  --glass-bg: rgba(247, 250, 252, 0.8);
  --glass-border: rgba(226, 232, 240, 0.3);
}

[data-theme="dark"] {
  --background: #0f1419;
  --foreground: #ffffff;
  --card-background: #1e293b;
  --card-border: #334155;
  /* ... other dark theme colors */
}
```

#### 2. Blog System
- Markdown/MDX blog posts with frontmatter
- Automatic blog post discovery from `src/content/posts/`
- Draft post support (filtered in production)
- Read time calculation
- Tag support
- SEO-optimized individual post pages
- Blog listing page with post metadata

**Blog Post Frontmatter Format**:
```markdown
---
title: "Post Title"
date: "2024-01-15"
excerpt: "Brief description of the post"
author: "Author Name"
tags: ["tag1", "tag2"]
draft: false
---

Post content in Markdown...
```

#### 3. SEO Configuration
- Comprehensive metadata in `layout.tsx`
- Open Graph tags for social sharing
- Twitter Card support
- Auto-generated sitemap (`sitemap.ts`)
- Robots.txt configuration (`robots.ts`)
- Structured data ready
- Canonical URLs

#### 4. Responsive Design
- Mobile-first approach
- Breakpoints: sm, md, lg, xl
- Responsive typography scales
- Flexible grid layouts
- Touch-friendly interactive elements

#### 5. Performance Optimizations
- Static site generation (SSG)
- Next.js Image optimization (unoptimized for GitHub Pages)
- Font optimization with `next/font`
- Code splitting
- Minimal JavaScript bundle

### Required Components

#### ThemeToggle.tsx
- Client component with useState and useEffect
- Toggles between light/dark/system themes
- Persists preference in localStorage
- Shows sun/moon icons from Lucide React
- Glass morphism styling

#### ThemeAwareImage.tsx
- Wraps Next.js Image component
- Automatically switches between light/dark image variants
- Handles fallback images
- Proper alt text support

#### ThemeAwareAppIcon.tsx
- Displays app icon with theme awareness
- SVG-based for crisp rendering
- Responsive sizing

### GitHub Pages Deployment

#### Configuration
1. **Base Path**: Configure in `next.config.ts` if using GitHub Pages subdirectory:
   ```typescript
   basePath: process.env.NODE_ENV === 'production' ? '/repository-name' : '',
   ```

2. **Sitemap & Robots**: Update URLs in `sitemap.ts` and `robots.ts`:
   ```typescript
   // sitemap.ts
   url: 'https://username.github.io/repository-name/blog/...'
   
   // robots.ts
   sitemap: 'https://username.github.io/repository-name/sitemap.xml'
   ```

3. **Metadata Base**: Update in `layout.tsx`:
   ```typescript
   metadataBase: new URL('https://username.github.io/repository-name'),
   ```

#### GitHub Actions Workflow
Create `.github/workflows/deploy.yml`:
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./out

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

#### Repository Settings
1. Go to Settings → Pages
2. Source: GitHub Actions
3. Build and deployment: GitHub Actions workflow

### Design Patterns

#### Glass Morphism
```css
.glass {
  background: var(--glass-bg);
  backdrop-filter: blur(10px);
  border: 1px solid var(--glass-border);
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.glass-card {
  background: var(--card-background);
  border: 1px solid var(--card-border);
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}
```

#### Color Usage
- Always use CSS custom properties: `style={{ color: 'var(--foreground)' }}`
- Never hardcode colors in components
- Use semantic color tokens (foreground, text-secondary, accent, etc.)

#### Typography
- Use Geist Sans for body text
- Use Geist Mono for code blocks
- Responsive font sizes with Tailwind utilities
- Proper heading hierarchy

### Content Guidelines

#### Landing Page Sections
1. **Navigation**: Logo, menu items, CTA button
2. **Hero Section**: Headline, subheadline, primary CTA, hero image
3. **Feature Highlights**: 3-4 key features with icons
4. **Secondary Features**: 2-3 additional features
5. **App Interface Showcase**: Screenshots with descriptions
6. **Quick Guides**: How-to sections
7. **CTA Section**: Final call-to-action
8. **Footer**: Links, social media, legal pages

#### Blog Post Components
- Title with large typography
- Author, date, read time metadata
- Tag badges
- Markdown content with styled components
- Back navigation
- Related posts (optional)

### Development Workflow

1. **Install Dependencies**:
   ```bash
   npm install
   ```

2. **Development Server**:
   ```bash
   npm run dev
   ```
   Open http://localhost:3000

3. **Build for Production**:
   ```bash
   npm run build
   ```
   Output goes to `out/` directory

4. **Type Checking**:
   ```bash
   npm run type-check
   ```

5. **Linting**:
   ```bash
   npm run lint
   npm run lint:fix
   ```

### Customization Checklist

- [ ] Update project name in package.json
- [ ] Replace placeholder logo/brand elements
- [ ] Customize accent color in globals.css
- [ ] Update metadata in layout.tsx (title, description, URLs)
- [ ] Update sitemap.ts and robots.ts with correct URLs
- [ ] Replace placeholder content in pages
- [ ] Add actual marketing images to public/
- [ ] Configure GitHub Pages basePath if needed
- [ ] Update social media links
- [ ] Add app store/download links
- [ ] Customize blog post templates
- [ ] Update footer information

### Best Practices

1. **Accessibility**: Use semantic HTML, proper ARIA labels, keyboard navigation
2. **Performance**: Optimize images, use Next.js Image component, minimize bundle size
3. **SEO**: Comprehensive meta tags, structured data, proper heading hierarchy
4. **Mobile-First**: Design for mobile, enhance for desktop
5. **Type Safety**: Use TypeScript strictly, avoid `any` types
6. **Code Quality**: Follow ESLint rules, consistent formatting
7. **Content**: Use clear, concise copy, avoid jargon
8. **Testing**: Test on multiple devices and browsers before deployment

### Additional Features to Consider

- Analytics integration (Plausible, Google Analytics)
- Newsletter signup form
- Contact form
- Search functionality for blog
- RSS feed generation
- Comment system (optional)
- Multi-language support (i18n)

---

## Usage Instructions

When using this prompt:

1. **Replace placeholders**:
   - `project-name` → Your actual project name
   - `username` → Your GitHub username
   - `repository-name` → Your GitHub repository name
   - Color values in CSS → Your brand colors

2. **Provide context**:
   - What product/service is being marketed?
   - What are the key features to highlight?
   - What platforms/devices are supported?
   - What is the target audience?

3. **Content requirements**:
   - Landing page copy
   - Feature descriptions
   - Blog post topics (if applicable)
   - App store links
   - Social media links

4. **Branding**:
   - Logo/app icon files
   - Color palette
   - Typography preferences
   - Screenshots/marketing images

This prompt should generate a complete, production-ready marketing website that follows modern best practices and is optimized for GitHub Pages deployment.

