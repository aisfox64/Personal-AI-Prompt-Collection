# Agent 规则

## 1. 用户指令与执行约束

- 当对话中包含 `[按步骤实现]` 等强调指令时，仅专注当前要求，不实现额外功能。
- 每当完成一个任务时，应当执行 `npm run lint` 检查，并修复代码中的 ESLint 与类型错误。
- 任务完成后，无须使用 `npm run dev` 启动项目，除非用户明确要求。
- 允许运行临时脚本以查询或测试数据，但不得修改生产代码逻辑。

---

## 2. 项目框架与依赖

- 框架版本与依赖必须明确定义于根目录 `package.json`。
- 新增依赖需写入 `package.json`，禁止通过 CDN 或全局变量引入未声明的库。
- 执行复杂任务前，需先通读项目结构（`src/` 目录及关键配置）。
- 项目文本（注释、文案、README）优先使用中文。

---

## 3. 项目结构与代码规范

### 3.1 目录结构

| 目录 | 用途 |
|------|------|
| `src/components` | 公共组件，一个文件夹对应一个/组组件 |
| `src/views/*/components` | 页面特有组件 |
| `src/views/*/modules` | 大型业务模块 |
| `src/api` | API 接口封装 |
| `src/stores` | Pinia 状态管理 |
| `src/utils` | 工具函数，尽量使用单文件 |
| `src/router` | 路由配置 |
| `src/styles` | 全局样式与主题 |
| `src/types` 或 `src/models` | 类型/模型定义 |

### 3.2 命名规范

- 组件文件：PascalCase（如 `StudyReport/index.vue`）
- 工具 / hooks / store 文件：camelCase（如 `useStudyFlow.ts`、`http.ts`）
- 目录：kebab-case（如 `chat-room/`、`vocabulary-select/`）

### 3.3 代码编写规范

- 结构须符合 Vue 3 + TypeScript 规范，代码遵循组件化。
- 组件必须使用 Vue 3 组合式 API 与 `<script setup>` 语法。
- 组件按 **脚本 - 模板 - 样式** 顺序编写。
- 提倡将组件拆分为功能单一的小组件，通过 props 通信。
- 模板中避免复杂逻辑；复杂逻辑抽至 composable 或 hooks。
- 样式采用 `scoped` + SCSS；兼容性由 PostCSS 处理。
- 业务数据必须定义 TypeScript 接口/类型，禁止使用 `any`，除非有明确理由并注释说明。
- 导入组件应使用路径别名 `@/`，仅同级组件间可用相对路径。
- 页面推荐使用 `index.vue` + `hooks.ts` 形式，业务逻辑置于 hooks 内并辅以中文注释。
- 不明确后代元素时，尽量避免标签选择器。

---

## 4. 组件与 UI 规范

- 组件库统一使用 **TDesign Vue Next**；若 TDesign 无对应能力，才允许使用 Vue 原生 `<slot>` 等基础能力。
- HTML 结构须语义化，样式需考虑响应式。
- 编写样式与交互时，须参考并使用：
  - `src/styles/theme.css` 中的 CSS 变量
  - `docs/Project-Design-Language.md` 中的设计规范（Modern Dark Neon 主题）
- 新增交互组件时，需保证可用键盘完成核心操作，并为关键元素添加合适的 `aria-*` 属性。

---

## 5. API 与数据层

- 所有 API 接口须符合 RESTful 规范。
- 新增接口调用时，应在 `src/api/` 中新增或扩展对应模块（如 `auth.ts`、`chat.ts`），不得在组件内直接拼接 URL 或使用 `axios`/`fetch` 原始调用。
- 接口入参和返回值必须定义 TypeScript 类型，类型可集中放在 `src/types/` 或对应 api 模块内。
- 新增接口应使用项目统一的 HTTP 封装（如 `src/utils/http.ts`），统一处理 baseURL、token 注入、错误提示等。

---

## 6. 路由与权限

- 新增页面时，须在 `src/router/index.ts` 中注册路由，并设置正确的 `meta`。
- 需登录页面：`meta: { requiresAuth: true }`。
- 管理后台（Admin）需在路由守卫中校验 `userStore.user?.is_admin`，未授权时跳转至首页或登录页。
- 页面组件须使用路由懒加载：`component: () => import("@/views/xxx/index.vue")`。

---

## 7. 状态管理（Pinia）

- 新增全局状态时，在 `src/stores/` 中创建或扩展现有 store。
- Store 命名遵循 `useXxxStore`，文件名与 store 名对应（如 `useUserStore` 对应 `user.ts`）。
- 避免在 store 中放置纯页面局部的 UI 状态，应放在组件或 composable 内。

---

## 8. 错误处理与安全

- API 错误、网络异常等，统一通过 TDesign 的 Message 或 Notification 提示用户。
- 禁止向用户展示完整错误堆栈或内部敏感信息。
- 禁止在 `localStorage` / `sessionStorage` 中明文存储敏感信息；token 等应由项目统一封装处理。
- 用户输入展示时需防 XSS：优先使用 TDesign 组件，避免对不可信内容使用 `v-html`。

---

## 9. 性能与依赖

- 页面级或大型业务模块应使用路由懒加载或 `defineAsyncComponent`。
- 新增依赖时评估体积，优先使用项目已有依赖，避免重复引入同类库。
- 大列表场景建议使用分页或虚拟滚动，避免一次性渲染过多 DOM。

---

## 10. 测试

- 测试相关文件置于根目录 `test/` 或项目约定的测试目录下。

---

## 11. 其他

- 修改涉及多文件时，保持类型定义、API、store 与视图之间的一致性。
- 完成功能开发后，应确保 `npm run build` 可正常通过。
- 不修改与当前任务无关的代码，不添加未在需求中提及的功能。
