# Theme System Design Guide

Complete guide for implementing flexible, scalable theme systems in uni-app applications.

## Table of Contents

- [Basic Concepts](#basic-concepts)
- [Color Theory](#color-theory)
- [Multi-Theme Implementation](#multi-theme-implementation)
- [Dynamic Theme Switching](#dynamic-theme-switching)
- [Responsive Themes](#responsive-themes)
- [Best Practices](#best-practices)

## Basic Concepts

### Why Use Theme Systems?

- **Brand consistency** - Maintain visual identity across platforms
- **Multi-tenancy** - Support multiple brands or customer themes
- **Dark mode** - Implement light/dark themes
- **Accessibility** - Adjust for user preferences
- **Maintenance** - Centralized design tokens

### Theme System Components

1. **Colors** - Primary, secondary, semantic colors
2. **Typography** - Font sizes, weights, line heights
3. **Spacing** - Consistent spacing scale
4. **Shadows** - Elevation and depth
5. **Border Radius** - Rounded corners
6. **Animation** - Transitions and durations

## Color Theory

### Color Palette Structure

```scss
// Primary colors
$primary: #1890ff;
$primary-light: #40a9ff;
$primary-lighter: #69c0ff;
$primary-dark: #096dd9;
$primary-darker: #0050b3;

// Functional colors
$success: #52c41a;
$warning: #faad14;
$error: #f5222d;
$info: #1890ff;

// Neutral colors (grayscale)
$gray-1: #ffffff;
$gray-2: #fafafa;
$gray-3: #f5f5f5;
$gray-4: #e8e8e8;
$gray-5: #d9d9d9;
$gray-6: #bfbfbf;
$gray-7: #8c8c8c;
$gray-8: #595959;
$gray-9: #262626;
$gray-10: #000000;

// Semantic text colors
$text-primary: $gray-9;
$text-regular: $gray-7;
$text-secondary: $gray-6;
$text-placeholder: $gray-5;

// Semantic background colors
$bg-page: $gray-3;
$bg-card: $gray-1;
$bg-hover: $gray-2;
$bg-active: $gray-4;
```

### Choosing a Primary Color

Considerations:
- **Psychology** - Blue (trust), Green (growth), Orange (energy)
- **Industry** - Finance (blue/green), Healthcare (blue/green), Retail (orange/red)
- **Accessibility** - Ensure WCAG AA compliance (4.5:1 contrast ratio)
- **Platform** - Follow platform guidelines (iOS blue, Android green)

**Color generation tools:**
- Ant Design Color Generator: https://ant.design/docs/spec/colors
- Material Design Color Tool: https://material.io/resources/color/
- Coolors: https://coolors.co/

## Multi-Theme Implementation

### Approach 1: SCSS Variables per Build

Create different theme files for each build variant.

**Structure:**
```
src/styles/
├── theme-user.scss      # User theme
├── theme-admin.scss     # Admin theme
└── theme.scss           # Current theme (symlink)
```

**theme-user.scss:**
```scss
$primary: #1890ff;
$success: #52c41a;
$warning: #faad14;
$error: #f5222d;

$text-primary: #333333;
$text-secondary: #999999;

$bg-page: #f5f5f5;
$bg-card: #ffffff;
```

**theme-admin.scss:**
```scss
$primary: #0050b3;
$success: #52c41a;
$warning: #faad14;
$error: #f5222d;

$text-primary: #333333;
$text-secondary: #999999;

$bg-page: #f0f2f5;
$bg-card: #ffffff;
```

**Build script:**
```bash
# Create symlink before build
ln -sf src/styles/theme-user.scss src/styles/theme.scss

# Or use npm script
npm run build:user   # Uses theme-user.scss
npm run build:admin  # Uses theme-admin.scss
```

### Approach 2: CSS Custom Properties

Use CSS variables for runtime theme switching.

**Define CSS variables:**
```scss
:root {
  --primary-color: #1890ff;
  --success-color: #52c41a;
  --text-primary: #333333;
  --bg-page: #f5f5f5;
}

[data-theme="dark"] {
  --primary-color: #177ddc;
  --text-primary: #ffffff;
  --bg-page: #141414;
}
```

**Use in components:**
```vue
<template>
  <view class="card">
    <text class="title">Title</text>
  </view>
</template>

<style lang="scss" scoped>
.card {
  background-color: var(--bg-page);

  .title {
    color: var(--text-primary);
  }
}
</style>
```

**Switch themes at runtime:**
```typescript
const setTheme = (theme: string) => {
  document.documentElement.setAttribute('data-theme', theme);
};
```

### Approach 3: Pinia Store

Use Pinia for complex theme management.

**store/theme.ts:**
```typescript
import { defineStore } from 'pinia';

interface Theme {
  name: string;
  primary: string;
  primaryDark: string;
  success: string;
  warning: string;
  error: string;
  textPrimary: string;
  textSecondary: string;
  bgPage: string;
  bgCard: string;
}

export const useThemeStore = defineStore('theme', {
  state: (): { currentTheme: string; themes: Record<string, Theme> } => ({
    currentTheme: 'default',
    themes: {
      default: {
        name: 'Default',
        primary: '#1890ff',
        primaryDark: '#096dd9',
        success: '#52c41a',
        warning: '#faad14',
        error: '#f5222d',
        textPrimary: '#333333',
        textSecondary: '#999999',
        bgPage: '#f5f5f5',
        bgCard: '#ffffff',
      },
      dark: {
        name: 'Dark',
        primary: '#177ddc',
        primaryDark: '#1765ad',
        success: '#49aa19',
        warning: '#d89614',
        error: '#d32029',
        textPrimary: '#ffffff',
        textSecondary: '#8c8c8c',
        bgPage: '#141414',
        bgCard: '#1f1f1f',
      },
    },
  }),

  getters: {
    theme: (state) => state.themes[state.currentTheme],
  },

  actions: {
    setTheme(themeName: string) {
      if (!this.themes[themeName]) {
        console.error(`Theme "${themeName}" not found`);
        return;
      }
      this.currentTheme = themeName;
      uni.setStorageSync('theme', themeName);
    },

    loadSavedTheme() {
      const saved = uni.getStorageSync('theme');
      if (saved && this.themes[saved]) {
        this.currentTheme = saved;
      }
    },
  },
});
```

**Use in component:**
```vue
<script setup lang="ts">
import { useThemeStore } from '@/store/theme';
import { computed } from 'vue';

const themeStore = useThemeStore();

const themeStyles = computed(() => ({
  '--primary-color': themeStore.theme.primary,
  '--text-primary': themeStore.theme.textPrimary,
  '--bg-page': themeStore.theme.bgPage,
}));
</script>

<template>
  <view class="page" :style="themeStyles">
    <view class="card">
      Content here
    </view>
  </view>
</template>

<style lang="scss" scoped>
.page {
  background-color: var(--bg-page);
  color: var(--text-primary);
}

.card {
  background-color: var(--primary-color);
}
</style>
```

## Dynamic Theme Switching

### Theme Selector Component

```vue
<template>
  <view class="theme-selector">
    <view class="theme-list">
      <view
        class="theme-item"
        v-for="(theme, key) in themes"
        :key="key"
        :class="{ active: currentTheme === key }"
        @click="selectTheme(key)"
      >
        <view class="theme-preview" :style="{ backgroundColor: theme.primary }"></view>
        <text class="theme-name">{{ theme.name }}</text>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { useThemeStore } from '@/store/theme';

const themeStore = useThemeStore();

const themes = themeStore.themes;
const currentTheme = computed(() => themeStore.currentTheme);

const selectTheme = (key: string) => {
  themeStore.setTheme(key);
};
</script>

<style lang="scss" scoped>
.theme-selector {
  padding: $spacing-md;

  .theme-list {
    display: flex;
    flex-wrap: wrap;
    gap: $spacing-md;
  }

  .theme-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: $spacing-md;
    border: 2rpx solid transparent;
    border-radius: $border-radius-md;

    &.active {
      border-color: currentTheme.primary;
    }
  }

  .theme-preview {
    width: 80rpx;
    height: 80rpx;
    border-radius: $border-radius-md;
    margin-bottom: $spacing-sm;
  }

  .theme-name {
    font-size: $font-size-sm;
    color: $color-text-primary;
  }
}
</style>
```

### Platform-Specific Themes

```typescript
// Detect platform and set theme
const systemInfo = uni.getSystemInfoSync();
let defaultTheme = 'default';

// #ifdef MP-WEIXIN
defaultTheme = 'wechat';
// #endif

// #ifdef H5
defaultTheme = 'web';
// #endif

themeStore.setTheme(defaultTheme);
```

## Responsive Themes

### Platform Detection

```typescript
const getPlatformTheme = () => {
  const systemInfo = uni.getSystemInfoSync();

  // #ifdef MP-WEIXIN
  return 'wechat';
  // #endif

  // #ifdef H5
  return 'web';
  // #endif

  // #ifdef MP-ALIPAY
  return 'alipay';
  // #endif

  return 'default';
};
```

### Screen Size Adaptation

```scss
// Use rpx for responsive sizing
.container {
  width: 750rpx;        // Full width on all screens
  padding: 24rpx;        // Scales with screen
  font-size: 28rpx;      // Scales with screen
}

// Use media queries for specific breakpoints (mainly H5)
@media (min-width: 768px) {
  .container {
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

### Dark Mode Support

```typescript
// Detect system dark mode preference
const checkDarkMode = () => {
  const systemInfo = uni.getSystemInfoSync();

  // Check if system supports dark mode
  if (systemInfo.theme) {
    return systemInfo.theme === 'dark';
  }

  return false;
};

// Listen for theme changes (if supported)
uni.onThemeChange((res) => {
  console.log('Theme changed:', res.theme);
  themeStore.setTheme(res.theme === 'dark' ? 'dark' : 'default');
});
```

## Best Practices

### 1. Design Token Structure

Organize tokens logically:

```scss
// 1. Brand tokens
$brand-primary: #1890ff;
$brand-secondary: #722ed1;

// 2. Semantic tokens
$primary: $brand-primary;
$success: #52c41a;
$warning: #faad14;
$error: #f5222d;

// 3. Component tokens
$button-primary-bg: $primary;
$button-primary-text: #ffffff;

// 4. Override tokens
.button-primary--large {
  padding: $spacing-lg;
  font-size: $font-size-lg;
}
```

### 2. Theme Consistency

Maintain consistency across:
- Colors
- Typography
- Spacing
- Shadows
- Border radius
- Animation timing

### 3. Accessibility

- Ensure WCAG AA compliance (4.5:1 contrast ratio)
- Support dark mode
- Allow text size adjustments
- Test with color blindness simulators

### 4. Performance

- Use SCSS variables instead of runtime calculations where possible
- Minimize CSS custom properties updates
- Lazy load theme resources
- Avoid deep nesting in SCSS

### 5. Maintenance

- Document theme values and their purposes
- Use semantic names (e.g., `$color-primary`, not `$color-blue`)
- Group related variables together
- Comment complex theme logic
- Version theme changes

## Resources

- **Color Tools:**
  - Ant Design Color Generator: https://ant.design/docs/spec/colors
  - Material Design Color Tool: https://material.io/resources/color/
  - Coolors: https://coolors.co/

- **Accessibility:**
  - WebAIM Contrast Checker: https://webaim.org/resources/contrastchecker/
  - WCAG 2.1 Guidelines: https://www.w3.org/WAI/WCAG21/quickref/

- **Inspiration:**
  - Dracula Theme: https://draculatheme.com/
  - Nord Theme: https://nordtheme.com/
  - Material Design Palette: https://material.io/design/color/
