# uni-app Frontend Development Skill

[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-purple)](https://github.com/anthropics/claude-code)
[![uni-app](https://img.shields.io/badge/uni--app-3.0-blue)](https://uniapp.dcloud.net.cn/)
[![Vue.js](https://img.shields.io/badge/Vue-3.0-brightgreen)](https://vuejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)](https://www.typescriptlang.org/)

A comprehensive Claude Code skill for uni-app cross-platform frontend development with Vue 3 + TypeScript.

## About

This skill provides expert guidance for building cross-platform applications using **uni-app** framework. It covers everything from project setup to advanced patterns, including UI library integration, theme system design, and platform-specific optimizations.

## Features

- **Project Scaffolding**: Create new uni-app projects and pages with best practices
- **UI Library Integration**: Ready-to-use guides for uView Plus, uni-ui, TuniaoUI
- **Theme System Design**: Multi-theme support for multi-role or multi-brand applications
- **Platform Support**: WeChat Mini Program, H5, Alipay, Baidu, and more
- **Component Templates**: Reusable page and component patterns
- **Best Practices**: TypeScript, Pinia state management, Vite configuration

## Use Cases

Use this skill when you need to:

1. Create a new uni-app project or page
2. Integrate UI libraries (uView Plus, uni-ui, TuniaoUI)
3. Implement multi-theme design systems
4. Build WeChat Mini Programs, H5, or other platform apps
5. Set up SCSS theming and global styles
6. Configure Vite build system
7. Implement common patterns (navigation, data fetching, state management)

## Installation

### For Claude Code Users

1. Copy this skill to your Claude Code skills directory:
   ```bash
   cp -r uniapp-frontend ~/.claude/skills/
   ```

2. Restart Claude Code or reload skills

3. Use the skill by mentioning uni-app related tasks:
   ```
   Create a new uni-app page with TypeScript
   Help me integrate uView Plus UI library
   Design a multi-theme system for my app
   ```

## Project Structure

```
uniapp-frontend/
├── skill.md              # Main skill documentation
├── assets/
│   └── page-template.vue # Vue 3 + TypeScript page template
├── references/
│   ├── page-patterns.md     # Reusable page patterns
│   ├── theme-guide.md       # Theme system design guide
│   └── ui-libraries.md      # UI library integration guides
└── README.md             # This file
```

## Quick Examples

### Create a New Page

```bash
mkdir -p src/pages/newpage
touch src/pages/newpage/index.vue
```

```json
{
  "pages": [
    {
      "path": "pages/newpage/index",
      "style": {
        "navigationBarTitleText": "Page Title"
      }
    }
  ]
}
```

### Install uView Plus

```bash
npm install uview-plus
```

```typescript
// main.ts
import uviewPlus from "uview-plus";
app.use(uviewPlus);
```

### Basic Theme Setup

```scss
// src/styles/theme.scss
$theme-primary: #1890ff;
$theme-success: #52c41a;
$color-text-primary: #333333;
$spacing-md: 24rpx;
```

```typescript
// vite.config.ts
css: {
  preprocessorOptions: {
    scss: {
      additionalData: `@import "@/styles/theme.scss";`,
      api: "modern-compiler",
    },
  },
}
```

## Build Commands

```bash
# WeChat Mini Program
npm run dev:mp-weixin
npm run build:mp-weixin

# H5
npm run dev:h5
npm run build:h5

# Other platforms
npm run dev:mp-alipay
npm run dev:mp-baidu
npm run dev:mp-toutiao
```

## Documentation

- **[Page Patterns](references/page-patterns.md)** - Reusable page patterns (list, detail, form, search, empty states)
- **[Theme Guide](references/theme-guide.md)** - Complete theme system design with multi-theme patterns
- **[UI Libraries](references/ui-libraries.md)** - Integration guides for popular UI libraries
- **[Main Skill](skill.md)** - Comprehensive uni-app development guide

## Technologies

- **uni-app** - Cross-platform framework
- **Vue 3** - Progressive JavaScript framework
- **TypeScript** - Type-safe JavaScript
- **Vite** - Next-generation frontend tooling
- **Pinia** - State management
- **SCSS** - CSS preprocessor

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [uni-app](https://uniapp.dcloud.net.cn/) - The cross-platform framework
- [uView Plus](https://uview-plus.jiangruyi.com/) - UI component library
- [Claude Code](https://github.com/anthropics/claude-code) - AI-powered CLI tool

## Support

For issues, questions, or contributions, please visit the [GitHub Issues](https://github.com/enze00/uniapp-frontend-skills/issues) page.

---

Made with [Claude Code](https://github.com/anthropics/claude-code)
