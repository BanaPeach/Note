# Vue

作者：YanShijie

---

## 一、Vue配置@路径别名

> **相关网址：**https://juejin.cn/post/7459045397118664755

1. 配置vite.config.ts文件

```ts
export default defineConfig({
   ...
    resolve: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
            '@components': path.resolve(__dirname, 'src/components'),
            '@store': path.resolve(__dirname, 'src/store')
        }
    },
})
```

> `import path from 'path'`报错：Vue: Cannot find module path or its corresponding type declarations
>
> 解决办法：
>
> 1. 安装 Node 类型并声明给 TS
>
> ```shell
> pnpm add -D @types/node
> ```

2. 配置`tsconfig.json`，`tsconfig.node.json·`，`tsconfig.app.json`

```ts
{
  "compilerOptions": {
     ...
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
}
```

## 二、router-vue使用

