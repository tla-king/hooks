# 自定义hooks封装实践

[自定义hooks封装实践](https://tla-king.github.io/pt) 以实战为主的文章，主要讲解实际项目中如何使用hooks以及一些最佳实践。

>     充分使用 React 16.8 的新特性 Hook的便捷性
>     能明显 提升开发效率,避免重复造轮子
>     减少代码量
>     实现组件间的解耦
>     实用，用的好，可以早点下班。。。。。。

### 常用技术栈

[![React](https://img.shields.io/badge/react-^16.12.0-brightgreen.svg?style=flat-square)](https://github.com/facebook/react)
[![Ant Design](https://img.shields.io/badge/ant--design-^4.3.3-yellowgreen.svg?style=flat-square)](https://github.com/ant-design/ant-design)
[![dva](https://img.shields.io/badge/dva-^2.1.0-orange.svg?style=flat-square)](https://github.com/dvajs/dva)
[![umi](https://img.shields.io/badge/umi-^3.1.4-orange.svg?style=flat-square)](https://github.com/github.com/umijs/umi)


-   基于[react](https://github.com/facebook/react)，[ant-design](https://github.com/ant-design/ant-design)，[dva](https://github.com/dvajs/dva)，[Mock](https://github.com/nuysoft/Mock) 企业级后台管理系统最佳实践。
-   基于 Antd UI 设计语言，提供后台管理系统常见使用场景。
-   基于[dva](https://github.com/dvajs/dva)动态加载 Model 和路由，按需加载。
-   使用[umi](https://github.com/umijs/umi)本地调试和构建，其中 Mock 功能实现脱离后端独立开发。
-   基础框架参考[antd-admin](https://github.com/zuiidea/antd-admin)或[antd-pro](https://github.com/umijs/ant-design-pro)


### 相关文档
- [hooks简介](https://react.docschina.org/docs/hooks-intro.html) React Hooks 官方文档
- [自定义 Hook](https://react.docschina.org/docs/hooks-custom.html) 通过自定义 Hook，可以将组件逻辑提取到可重用的函数中
- [ahooks](https://ahooks.js.org/zh-CN/) 丰富的自定义 React Hooks 库,内含常用且高质量的 Hooks。
