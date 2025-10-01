# **Complete Next.js Website Setup Prompt**

## **Project Overview**
Create a modern Next.js website with a frosted glass aesthetic, light/dark mode support, and theme-aware app icons. This setup includes static site generation, MDX blog support, and a comprehensive theming system.

## **Tech Stack & Dependencies**

### **Core Framework**
- **Next.js 15.5.4** with Turbopack
- **React 19.1.0** with TypeScript
- **Static Site Generation** (`output: 'export'`)

### **Styling & UI**
- **Tailwind CSS 4** with PostCSS
- **Lucide React** for icons
- **Custom CSS Variables** for theming
- **Frosted Glass Effects** with backdrop-filter

### **Content & Blog**
- **MDX Support** with `@mdx-js/react` and `@next/mdx`
- **React Markdown** for content rendering
- **Gray Matter** for frontmatter parsing

### **Development Tools**
- **ESLint** with Next.js config
- **TypeScript 5** with strict mode
- **Path aliases** (`@/*` → `./src/*`)

## **Package.json Configuration**
```json
{
  "name": "your-project-name",
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

## **Configuration Files**

### **next.config.ts**
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
};

export default nextConfig;
```

### **tsconfig.json**
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
    "plugins": [{"name": "next"}],
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

### **postcss.config.mjs**
```javascript
const config = {
  plugins: ["@tailwindcss/postcss"],
};

export default config;
```

### **eslint.config.mjs**
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

## **Theming System**

### **CSS Variables (globals.css)**
```css
:root {
  /* Light theme colors */
  --background: #ffffff;
  --foreground: #1a1a1a;
  --card-background: #f7fafc;
  --card-border: #e2e8f0;
  --accent: #00bcd4;
  --accent-hover: #00acc1;
  --text-secondary: #64748b;
  --text-muted: #94a3b8;
  --glass-bg: rgba(247, 250, 252, 0.8);
  --glass-border: rgba(226, 232, 240, 0.3);
}

[data-theme="dark"] {
  /* Dark theme colors */
  --background: #0f1419;
  --foreground: #ffffff;
  --card-background: #1e293b;
  --card-border: #334155;
  --accent: #00bcd4;
  --accent-hover: #00acc1;
  --text-secondary: #cbd5e1;
  --text-muted: #94a3b8;
  --glass-bg: rgba(30, 41, 59, 0.8);
  --glass-border: rgba(51, 65, 85, 0.3);
}

@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    /* Dark theme colors */
    --background: #0f1419;
    --foreground: #ffffff;
    --card-background: #1e293b;
    --card-border: #334155;
    --accent: #00bcd4;
    --accent-hover: #00acc1;
    --text-secondary: #cbd5e1;
    --text-muted: #94a3b8;
    --glass-bg: rgba(30, 41, 59, 0.8);
    --glass-border: rgba(51, 65, 85, 0.3);
  }
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card-bg: var(--card-background);
  --color-card-border: var(--card-border);
  --color-accent: var(--accent);
  --color-accent-hover: var(--accent-hover);
  --color-text-secondary: var(--text-secondary);
  --color-text-muted: var(--text-muted);
  --color-glass-bg: var(--glass-bg);
  --color-glass-border: var(--glass-border);
}

body {
  background: var(--background);
  color: var(--foreground);
  font-family: var(--font-geist-sans), Arial, Helvetica, sans-serif;
  transition: background-color 0.3s ease, color 0.3s ease;
}

/* Frosted glass effect */
.glass {
  background: var(--glass-bg);
  backdrop-filter: blur(10px);
  border: 1px solid var(--glass-border);
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}

.glass-card {
  background: var(--card-background);
  border: 1px solid var(--card-border);
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
}

/* Theme transition animations */
* {
  transition: background-color 0.3s ease, border-color 0.3s ease, color 0.3s ease;
}
```

## **Required Components**

### **ThemeToggle.tsx**
```typescript
'use client';

import { useState, useEffect } from 'react';
import { Sun, Moon, Monitor } from 'lucide-react';

export default function ThemeToggle() {
  const [theme, setTheme] = useState<'light' | 'dark' | 'system'>('system');
  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
    const savedTheme = localStorage.getItem('theme') as 'light' | 'dark' | 'system' || 'system';
    setTheme(savedTheme);
    applyTheme(savedTheme);
  }, []);

  const applyTheme = (newTheme: 'light' | 'dark' | 'system') => {
    const root = document.documentElement;
    
    if (newTheme === 'system') {
      const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      root.setAttribute('data-theme', systemPrefersDark ? 'dark' : 'light');
    } else {
      root.setAttribute('data-theme', newTheme);
    }
  };

  const handleThemeChange = (newTheme: 'light' | 'dark' | 'system') => {
    setTheme(newTheme);
    localStorage.setItem('theme', newTheme);
    applyTheme(newTheme);
  };

  if (!mounted) {
    return (
      <div className="w-10 h-10 rounded-lg glass-card flex items-center justify-center">
        <Monitor className="w-5 h-5" style={{ color: 'var(--text-secondary)' }} />
      </div>
    );
  }

  return (
    <div className="flex rounded-lg glass-card overflow-hidden">
      <button
        onClick={() => handleThemeChange('light')}
        className={`p-2 ${theme === 'light' ? 'glass' : ''}`}
        style={{ color: theme === 'light' ? 'var(--accent)' : 'var(--text-secondary)' }}
      >
        <Sun className="w-5 h-5" />
      </button>
      <button
        onClick={() => handleThemeChange('dark')}
        className={`p-2 ${theme === 'dark' ? 'glass' : ''}`}
        style={{ color: theme === 'dark' ? 'var(--accent)' : 'var(--text-secondary)' }}
      >
        <Moon className="w-5 h-5" />
      </button>
      <button
        onClick={() => handleThemeChange('system')}
        className={`p-2 ${theme === 'system' ? 'glass' : ''}`}
        style={{ color: theme === 'system' ? 'var(--accent)' : 'var(--text-secondary)' }}
      >
        <Monitor className="w-5 h-5" />
      </button>
    </div>
  );
}
```

### **ThemeAwareAppIcon.tsx**
```typescript
'use client';

import Image from 'next/image';
import { useState, useEffect } from 'react';

interface ThemeAwareAppIconProps {
  width?: number;
  height?: number;
  className?: string;
  alt?: string;
}

export default function ThemeAwareAppIcon({ 
  width = 32, 
  height = 32, 
  className = "rounded-lg",
  alt = "App Icon"
}: ThemeAwareAppIconProps) {
  const [isDark, setIsDark] = useState(false);
  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
    const checkTheme = () => {
      const root = document.documentElement;
      const theme = root.getAttribute('data-theme');
      const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      
      if (theme === 'dark' || (theme !== 'light' && systemPrefersDark)) {
        setIsDark(true);
      } else {
        setIsDark(false);
      }
    };

    checkTheme();

    // Listen for theme changes
    const observer = new MutationObserver(checkTheme);
    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['data-theme']
    });

    // Listen for system theme changes
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    mediaQuery.addEventListener('change', checkTheme);

    return () => {
      observer.disconnect();
      mediaQuery.removeEventListener('change', checkTheme);
    };
  }, []);

  if (!mounted) {
    // Show light icon by default during SSR
    return (
      <Image 
        src="/AppIconLight-1024.png" 
        alt={alt} 
        width={width} 
        height={height} 
        className={className}
      />
    );
  }

  return (
    <Image 
      src={isDark ? "/AppIconDark-1024.png" : "/AppIconLight-1024.png"} 
      alt={alt} 
      width={width} 
      height={height} 
      className={className}
    />
  );
}
```

## **Layout Structure**

### **layout.tsx**
```typescript
import type { Metadata } from "next";
import { Geist, Geist_Mono } from "next/font/google";
import "./globals.css";
import ThemeToggle from "@/components/ThemeToggle";

const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});

export const metadata: Metadata = {
  title: "Your App Name - Tagline",
  description: "Your app description",
  // ... other metadata
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
        <div className="fixed top-4 right-4 z-50">
          <ThemeToggle />
        </div>
        {children}
      </body>
    </html>
  );
}
```

## **Key Features to Implement**

1. **Frosted Glass Aesthetic**: Use `.glass` and `.glass-card` classes
2. **Light/Dark Mode**: Automatic system detection + manual toggle
3. **Theme-Aware Icons**: Dynamic switching between light/dark PNG icons
4. **Static Site Generation**: Configured for deployment
5. **MDX Blog Support**: Content management with frontmatter
6. **Responsive Design**: Mobile-first approach with Tailwind
7. **TypeScript**: Strict mode with path aliases
8. **Performance**: Optimized images and fonts

## **File Structure**
```
src/
├── app/
│   ├── globals.css          # Theme system
│   ├── layout.tsx           # Root layout with theme toggle
│   ├── page.tsx             # Home page
│   ├── about/page.tsx        # About page
│   ├── blog/
│   │   ├── page.tsx         # Blog listing
│   │   └── [slug]/page.tsx  # Individual posts
│   └── ...                  # Other pages
├── components/
│   ├── ThemeToggle.tsx      # Theme switching component
│   └── ThemeAwareAppIcon.tsx # Dynamic icon component
├── content/
│   └── posts/              # MDX blog posts
└── lib/
    ├── assets.ts           # Asset management
    └── blog.ts             # Blog utilities
```

## **Setup Commands**
```bash
# Initialize project
npx create-next-app@latest your-project --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"

# Install additional dependencies
npm install @mdx-js/loader @mdx-js/react @next/mdx @types/mdx gray-matter lucide-react react-markdown

# Install dev dependencies
npm install -D @eslint/eslintrc @tailwindcss/postcss @types/node @types/react @types/react-dom eslint eslint-config-next tailwindcss typescript

# Run development server
npm run dev
```

## **Usage Instructions**

1. **Replace placeholder values** in package.json, metadata, and component props
2. **Add your app icons** as `AppIconLight-1024.png` and `AppIconDark-1024.png` in the public folder
3. **Customize colors** in the CSS variables section of globals.css
4. **Create your pages** following the established patterns
5. **Add blog posts** as MDX files in the content/posts directory

This prompt provides everything needed to recreate the exact same setup with consistent theming, components, and configuration across multiple projects.
