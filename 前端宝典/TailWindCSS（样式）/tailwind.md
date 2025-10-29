# TailWindCSS 

作者：YanShijie

---

## 一、配置

1. 引入依赖

```shell
pnpm add tailwindcss@next @tailwindcss/vite@next
```

2. 接下来，将我们的 Vite 插件添加到您的`vite.config.ts`文件中：

```js
import { defineConfig } from 'vite';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [
    tailwindcss()
  ],
});
```

3. 最后，将 Tailwind 导入到主 CSS 文件中：

```css
@import "tailwindcss";
```

