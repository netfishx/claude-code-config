# CSS 布局规范：Zero-Margin

## 核心原则

间距是容器的职责，不是子元素的职责。

## 间距机制（优先级从高到低）

1. `gap` — flex/grid 容器内子元素间距
2. `padding` — 容器内边距、内容留白
3. 容器结构调整 — 需要不同间距的区域拆成独立容器

## 禁止使用

- `m-*`、`mx-*`、`my-*`、`mt-*` 等所有 margin 工具类
- `mx-auto`（用 `flex justify-center` 或 `grid place-items-center` 替代）
- 负 margin `-m-*`（重构容器结构替代）
- `space-x-*`、`space-y-*`（底层是 margin，用 `gap` 替代）

## 替代方案速查

| 需求 | 不用 | 改用 |
|------|------|------|
| 垂直间距 | `mt-*` | 父级 `flex flex-col gap-*` |
| 水平居中 | `mx-auto` | `flex justify-center` |
| 全宽突破 | `-mx-*` | 拆分容器结构 |
| 列表间距 | `space-y-*` | `flex flex-col gap-*` |

## 适用范围

仅约束应用层代码，第三方 UI 组件库内部不受限制。
