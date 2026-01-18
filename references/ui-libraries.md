# UI Library Integration Guide

Comprehensive guide for integrating popular UI libraries with uni-app.

## Table of Contents

- [uView Plus](#uview-plus)
- [uni-ui](#uni-ui)
- [TuniaoUI](#tuniaoui)
- [NutUI](#nutui)
- [Comparison](#comparison)

## uView Plus

**Overview:** Comprehensive UI component library with 60+ components.

**Installation:**

```bash
npm install uview-plus
```

**Configuration:**

**App.vue:**
```vue
<style lang="scss">
@import "uview-plus/index.scss";
</style>
```

**main.ts:**
```typescript
import uviewPlus from "uview-plus";
app.use(uviewPlus);
```

**vite.config.ts:**
```typescript
css: {
  preprocessorOptions: {
    scss: {
      additionalData: `@import "uview-plus/theme.scss";`,
      api: "modern-compiler",
    },
  },
}
```

**Common Components:**
- `u-button` - Buttons
- `u-input` - Input fields
- `u-navbar` - Navigation bar
- `u-card` - Cards
- `u-grid` - Grid layout
- `u-tabs` - Tabs
- `u-list` - Lists
- `u-icon` - Icons

**Documentation:** https://uviewui.com/

**Pros:**
- Large component library (60+ components)
- Good documentation with examples
- Active maintenance
- TypeScript support

**Cons:**
- Larger bundle size
- Some components have limited customization

## uni-ui

**Overview:** Official UI library by DCloud, lightweight and reliable.

**Installation:**

```bash
npm install @dcloudio/uni-ui
```

**Configuration:**

**pages.json:**
```json
{
  "easycom": {
    "autoscan": true,
    "custom": {
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
    }
  }
}
```

**main.ts:**
```typescript
import UniUI from "@dcloudio/uni-ui";
app.use(UniUI);
```

**Common Components:**
- `uni-nav-bar` - Navigation bar
- `uni-list` - List container
- `uni-list-item` - List items
- `uni-card` - Cards
- `uni-forms` - Form validation
- `uni-data-picker` - Data picker
- `uni-icons` - Icons

**Documentation:** https://uniapp.dcloud.net.cn/component/uniui/uni-ui.html

**Pros:**
- Official support from DCloud
- Lightweight
- Stable and reliable
- Good performance

**Cons:**
- Fewer components than third-party libraries
- Less feature-rich
- Documentation can be outdated

## TuniaoUI

**Overview:** Modern UI library with beautiful components, good for e-commerce and social apps.

**Installation:**

```bash
npm install @tuniao/tnui-vue3-uniapp
```

**Configuration:**

**App.vue:**
```vue
<style lang="scss">
@import "@tuniao/tnui-vue3-uniapp/index.scss";
</style>
```

**main.ts:**
```typescript
import TnUI from "@tuniao/tnui-vue3-uniapp";
app.use(TnUI);
```

**pages.json:**
```json
{
  "easycom": {
    "autoscan": true,
    "custom": {
      "^tn-(.*)": "@tuniao/tnui-vue3-uniapp/lib/components/tn-$1/tn-$1.vue"
    }
  }
}
```

**Common Components:**
- `tn-button` - Buttons
- `tn-input` - Input fields
- `tn-nav-bar` - Navigation bar
- `tn-card` - Cards
- `tn-tag` - Tags
- `tn-avatar` - Avatars
- `tn-image-upload` - Image upload

**Documentation:** https://www.letui.com/

**Pros:**
- Beautiful, modern design
- Good for e-commerce apps
- Rich animation effects
- Customizable themes

**Cons:**
- Smaller community
- Less documentation in English
- Newer library, less battle-tested

## NutUI

**Overview:** JD.com's mobile UI library, now supports uni-app via NutUI-React.

**Installation:**

```bash
# For Vue 3 projects
npm install @nutui/nutui-react
```

**Note:** NutUI primarily supports Vue 2/3 for web and React for uni-app. For Vue 3 + uni-app, consider other libraries first.

**Documentation:** https://nutui.jd.com/

## Comparison

| Feature | uView Plus | uni-ui | TuniaoUI |
|---------|-----------|--------|----------|
| Components | 60+ | 30+ | 40+ |
| Bundle Size | Larger | Smallest | Medium |
| Documentation | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| TypeScript | Full | Partial | Full |
| Customization | High | Medium | High |
| Community | Large | Official | Medium |
| Maintenance | Active | Stable | Active |
| Best For | General purpose | Performance | E-commerce, Social |

## Choosing a UI Library

### Use uView Plus if:
- You need a comprehensive component library
- Building a complex enterprise application
- Want lots of pre-built components
- Value extensive documentation

### Use uni-ui if:
- Performance is critical
- Building a simple app
- Want official DCloud support
- Prefer lightweight dependencies

### Use TuniaoUI if:
- Building an e-commerce app
- Want modern, beautiful UI
- Need rich animations
- Value customization options

## Migration Between Libraries

If you need to switch UI libraries:

1. **Replace imports:**
```vue
<!-- From uView Plus -->
<u-button>Click</u-button>

<!-- To uni-ui -->
<uni-button>Click</uni-button>

<!-- To TuniaoUI -->
<tn-button>Click</tn-button>
```

2. **Update configuration files:**
- Remove old library imports from App.vue
- Update vite.config.ts if using custom SCSS
- Update easycom configuration in pages.json

3. **Update component props:**
- Check documentation for prop differences
- Some components may have different APIs

## Using Multiple UI Libraries

You can use multiple UI libraries together, but be careful:

1. **Potential issues:**
- Style conflicts
- Larger bundle size
- Inconsistent design language

2. **Best practices:**
- Use one library as primary
- Only import specific components from secondary libraries
- Test thoroughly on all target platforms
- Consider using easycom to avoid manual imports

3. **Example:**
```json
// pages.json
{
  "easycom": {
    "custom": {
      "^u-(.*)": "uview-plus/components/u-$1/u-$1.vue",
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
    }
  }
}
```

## Custom Components

Sometimes it's better to build custom components:

**When to build custom:**
- Need highly specific design
- Want full control over behavior
- Need to optimize for specific platform
- Component doesn't exist in any library

**Example custom card component:**
```vue
<template>
  <view class="custom-card" @click="handleClick">
    <view class="card-header">
      <slot name="header">
        <text class="title">{{ title }}</text>
      </slot>
    </view>
    <view class="card-body">
      <slot></slot>
    </view>
    <view class="card-footer" v-if="$slots.footer">
      <slot name="footer"></slot>
    </view>
  </view>
</template>

<script setup lang="ts">
interface Props {
  title?: string;
}

defineProps<Props>();

const emit = defineEmits<{
  click: [];
}>();

const handleClick = () => {
  emit('click');
};
</script>

<style lang="scss" scoped>
.custom-card {
  background-color: $color-bg-card;
  border-radius: $border-radius-md;
  padding: $spacing-md;
  margin-bottom: $spacing-md;

  .card-header {
    margin-bottom: $spacing-sm;
  }

  .card-body {
    margin-bottom: $spacing-sm;
  }

  .card-footer {
    margin-top: $spacing-sm;
  }
}
</style>
```

## Best Practices

1. **Choose early** - Pick a UI library at project start
2. **Stay consistent** - Don't mix multiple libraries heavily
3. **Customize themes** - Adapt the library to your brand
4. **Test thoroughly** - Test on all target platforms
5. **Check updates** - UI libraries release updates regularly
6. **Read documentation** - Understand component APIs before using
7. **Consider bundle size** - Only import what you need
8. **Build custom when needed** - Not everything needs a library component

## Resources

- uni-app official plugins: https://ext.dcloud.net.cn/
- uView Plus: https://uviewui.com/
- uni-ui: https://uniapp.dcloud.net.cn/component/uniui/uni-ui.html
- TuniaoUI: https://www.letui.com/
