# Vue项目脚手架搭建

作者：YanShijie

---

## 一、Vite 构建项目

1. 命令行输入以下命令，并根据提示完成初始项目构建

```shell
npm create vite@latest
```

## 二、ESLint+Prettier 配置（实现代码规范化）

1. 安装`ESlint`到开发依赖中

```shell
pnpm add eslint@latest -D
```

```json
// 安装成功后在package.json会看到如下结果
"devDependencies": {
    ...
	"eslint": "^9.38.0",
    ...
},
```

2. 配置命令

> 我们在项目中安装`ESlint`，最终是会通过命令`pnpm lint` 或者`pnpm lint:fix` 去执行，这个命令会用项目中安装的`eslint`去检查指定目录/文件的代码，最终输出不符合规则的代码错误信息。所以接下来就是要配置命令让`ESlint`起作用。

```json
// 在package.json中配置如下命令
"scripts": {
    "lint": "eslint",
    "lint:fix": "eslint --fix",
    ...
  },
```

