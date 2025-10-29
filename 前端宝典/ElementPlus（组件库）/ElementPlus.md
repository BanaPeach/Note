# ElementPlus

作者：YanShijie

---

## 一、按需导入

1. 引入依赖

```shell
pnpm install element-plus
```

2. 引入插件

```shell
pnpm install -D unplugin-vue-components unplugin-auto-import
```

3. 然后把下列代码插入到你的 `Vite` 的配置文件中

```JS
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

4. 图标（可选）

```shell
pnpm install @element-plus/icons-vue
```

