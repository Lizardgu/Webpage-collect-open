# 网址笔记本（单文件 HTML 应用）

这是一个轻量的单文件前端工具（HTML/CSS/JS），用于在浏览器中管理网址书签与备注。支持标签分类、彩色标签显示、导入/导出、按标签批量打开、favicon 自动加载、本地持久化等功能。将此 README.md 放到你的 GitHub 仓库即可说明项目目的与使用方法。

---

## 功能概览

- 单文件应用：一个 .html 文件包含所有界面、样式与脚本，直接在浏览器打开使用。
- 添加网址
  - 双击页面空白或点击 “添加” 弹出添加模态。
  - 行内输入布局（左侧显示字段名：网址 / 标签 / 备注；右侧为输入框）。
  - 输入框没有 placeholder（示例文字）。
  - 标签支持多值（逗号分隔），可点击已有标签快速添加。
  - 自动尝试加载 favicon（site-root /favicon.ico）；失败时显示内置 SVG 占位图。
- 单击打开
  - 单击卡片（除按钮与备注编辑区）在新标签页打开网址。
  - 每条记录仍保留“打开”按钮。
- 标签（分类）
  - 每条记录可包含多个标签（tags 字段）。
  - 标签以彩色 pill 显示：通过标签名哈希映射到固定配色表（同名标签颜色一致）。
  - 页面顶部支持按标签筛选（全部 / 无标签 / 单个标签）。
- 一键打开（按标签）
  - “一键打开所有”按钮会弹出选择对话框，选择：全部 / 无标签 / 指定标签，随后打开匹配条目（浏览器可能阻止大量弹窗）。
- 导入 / 导出
  - 导出：将当前数据（url、note、favicon、tags）导出为 JSON 文件。
  - 导入：从本地 JSON 文件导入，支持“合并”或“覆盖”两种模式，并做基本校验（确保为数组且每项包含 url）。
- 编辑 / 删除 / 备注
  - 可直接在条目处编辑备注（自动保存）。
  - 编辑按钮可在模态中修改 url / 标签 / 备注。
  - 支持删除单条记录。
- 本地持久化
  - 使用 localStorage 保存数据（key：url-notebook-v1-tags-colored）。
- 控制台工具
  - window._urlNotebook.getItems()
  - window._urlNotebook.import(array)
  - window._urlNotebook.clear()

---

## 快速开始

1. 将仓库中的单文件 HTML（例如 `notebook.html`）下载或保存到本地。
2. 在浏览器中打开该文件（双击或拖放到浏览器窗口）。
3. 常用操作：
   - 添加：双击页面空白或点击顶部“添加”，填写右侧输入框（网址、标签、备注），点击“保存并添加”。
   - 打开：单击卡片或点击“打开”按钮在新标签页打开网址。
   - 筛选：顶部下拉选择标签过滤显示项。
   - 一键打开：点击“一键打开所有”选择标签范围并打开对应网址（注意可能被浏览器拦截）。
   - 导出/导入：点击“导出”下载 JSON；点击“导入”选择 JSON 文件并选择合并或覆盖导入。
   - 编辑备注：在条目的备注处直接修改或点击“编辑”按钮在模态中修改。

---

## 数据结构（每条记录示例）

```json
{
  "url": "https://example.com/",
  "note": "一些备注",
  "favicon": "https://example.com/favicon.ico",
  "tags": ["工作", "教程"]
}
```

---

## 实现细节与注意事项

- favicon 加载策略：尝试使用站点根目录的 /favicon.ico（作为 img.src）。若加载失败，img.onerror 回退到内置 SVG 占位图。受跨域与站点配置限制。
- 浏览器弹窗限制：批量调用 window.open 可能被浏览器拦截。对于大量打开（默认实现中对 >30 或 >50 项会提示确认），建议分批或允许弹窗。
- localStorage：仅在本机浏览器存储，非跨设备同步。如果需要跨设备同步，需要后端存储或云服务。
- 标签颜色：颜色由代码中的 COLOR_PALETTE 数组与标签名哈希函数决定。修改 COLOR_PALETTE 可调整配色。
- 导入格式：应为 JSON 数组，数组项应至少包含 url 字段；tags 字段可以是数组或逗号分隔字符串。

---

## 样式与自定义（输入框边框示例）

项目中已将输入框边框做得更显眼（更粗、更明显的非聚焦边框与聚焦时的发光效果）。如需自定义：

在 HTML 文件的 `<style>` 部分找到如下规则并修改：

- 选择器：
  - input[type="url"], input[type="text"], input[type="file"], select
  - input:focus / select:focus

- 可修改项举例：
  - border（粗细与颜色）例如：`border: 2px solid rgba(255,255,255,0.12);`
  - 聚焦边框颜色：`border-color: rgba(59,130,246,0.95);`
  - 聚焦阴影：`box-shadow: 0 6px 18px rgba(59,130,246,0.18), 0 0 0 6px rgba(59,130,246,0.06);`
  - 圆角：`border-radius: 12px;`

示例 CSS（可直接替换或参考）：

```css
input[type="url"],
input[type="text"],
input[type="file"],
select {
  flex: 1;
  padding: 10px;
  border-radius: 12px;
  border: 2px solid rgba(255,255,255,0.12);
  background: rgba(255,255,255,0.02);
  color: #e6eef8;
  outline: none;
  transition: border-color 160ms ease, box-shadow 160ms ease, background-color 160ms ease;
}

input[type="url"]:focus,
input[type="text"]:focus,
input[type="file"]:focus,
select:focus {
  border-color: rgba(59,130,246,0.95);
  box-shadow: 0 6px 18px rgba(59,130,246,0.18), 0 0 0 6px rgba(59,130,246,0.06);
  background: rgba(255,255,255,0.03);
}
```

---

## 开发与目录建议

当前实现为单文件（index.html / notebook.html）。若拆分项目，建议目录如下：

- index.html
- css/
  - styles.css
- js/
  - app.js
- README.md
- LICENSE

将 CSS 与 JS 提取出来更利于维护与版本控制。

---

## 可选扩展建议

- 拖拽排序并保存顺序
- 为标签支持自定义颜色与图标
- 支持 CSV 导出/导入（仅 url、备注、tags）
- 批量操作：批量删除、批量标签变更、批量导出
- 后端同步（示例 API、用户认证、加密存储）
- 更稳健的 favicon 获取：使用后端抓取或第三方 favicon 服务

---

## 贡献与许可证

- 建议在仓库中添加 LICENSE（如 MIT）。
- 欢迎提交 Issues 与 Pull Requests，贡献功能或样式改进。

---

## 变更与开发历史（从最初需求到最终功能）

简要回顾项目演进（便于放在 CHANGELOG 中）：

1. 初始：双击添加、备注输入、自动 favicon、保存 localStorage、一键打开所有。
2. 添加单击行打开功能（卡片单击在新标签页打开）。
3. 增加导出/导入 JSON（合并或覆盖）。
4. 增加标签支持（多标签、筛选、按标签打开）。
5. 修改 UI：添加模态内输入采用行内布局（标签在左、输入框在右）、移除 placeholder。
6. 添加标签颜色（基于标签哈希与固定调色板）。
7. 调整样式：使输入框边框更显眼，并提供可配置示例。

---

## 联系与下一步

如果你需要，我可以：

- 将当前单文件 HTML（含最新样式与功能）打包并提供下载链接（或把内容粘贴给你）。
- 将 README.md 转成仓库中的实际文件并提交（你可提供仓库 URL 或我生成文件内容供你粘贴到新仓库）。
- 实现任意上面提到的扩展（拖拽排序、标签颜色自定义、CSV 导出、后端同步等）。

---

感谢使用！如果你希望我立即把完整的单文件 HTML（包含最新样式）作为文件内容输出，或生成一个 ZIP 包结构说明，请告诉我你需要的格式。