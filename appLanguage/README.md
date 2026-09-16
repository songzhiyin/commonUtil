# Excel 多语言资源编辑工具

一个纯浏览器端的单文件工具，用于预览、编辑 Excel 表格，并支持 Android / iOS 多语言资源文件的导入导出与数据合并。所有操作均在本地完成，不会上传任何数据。

---

## 功能特性

- **Excel 导入/导出**：支持 `.xlsx` / `.xls`
- **Android 资源导入/导出**：解析 `values-*/strings.xml`，导出为 Android `values` 目录 ZIP
- **iOS 资源导入/导出**：
  - 旧规则：`*.lproj/Localizable.strings`
  - 新规则：`.xcstrings`（Xcode 15+ String Catalog）
- **表格编辑**：
  - 点击表头修改列标题、删除列、左移/右移列
  - 点击单元格编辑整行数据、删除行
  - 新增行、新增列（支持自增填充）
- **数据追加（列）**：按匹配列将另一个文件的数据列合并到当前表格，支持 Excel / Android / iOS 数据源
- **数据追加（行）**：按列匹配规则将另一个文件的行数据追加到当前表格，支持 Excel / Android / iOS 数据源
- **导出增强**：
  - 导出时自由选择需要导出的列
  - 跳过 key 为空的行（Android / iOS 导出）
  - 导出文件名自动附加时间戳
- **自动识别**：拖拽文件夹或选择文件夹时，自动识别 Android 或 iOS 资源类型
- **全选/全不选**：所有列选择列表均支持全选与半选状态

---

## 快速开始

1. 下载本仓库中的 HTML 文件（例如 `index.html`）
2. 用浏览器打开（推荐 Chrome / Edge / Safari 最新版）
3. 拖拽文件/文件夹到页面，或点击按钮选择数据来源
4. 开始编辑、追加数据或导出

> 无需安装任何依赖，无需网络连接（首次加载会从 CDN 获取 SheetJS 与 JSZip）。

---

## 使用说明

### 1. 数据导入

支持三种数据来源，拖拽或点击选择均可：

| 类型 | 说明 |
|------|------|
| Excel | `.xlsx` / `.xls` 文件 |
| Android 资源 | 包含 `values-*/strings.xml` 的文件夹 |
| iOS 资源（旧） | 包含 `*.lproj/Localizable.strings` 的文件夹 |
| iOS 资源（新） | 包含 `*.xcstrings` 文件的文件夹（Xcode 15+） |

导入后表格首列为 `Key`（Excel 导入时自动识别 Key / name 列）。

---

### 2. 表格编辑

- **点击表头**：弹出编辑列弹窗，可修改标题、左移/右移列、删除列
- **点击数据单元格**：弹出编辑行弹窗，可修改整行数据、删除该行
- **工具栏按钮**：
  - `➕ 新增一行`：在指定位置插入空白行
  - `➕ 新增一列`：在指定位置插入新列，支持自增填充（如 `key1, key2, key3`）

---

### 3. 数据追加（列）

将一个文件（Excel / Android / iOS）的某些列，按匹配列合并到当前表格中。

**操作流程**：

1. 点击 `🔗 数据追加(列)`，选择数据源类型
2. 选择目标文件（或文件夹）
3. 设置匹配列：左侧选目标文件的列，右侧选原始文件的列（允许列标题不同，按数据内容匹配）
4. 勾选要追加的列（已存在列会覆盖，新列自动添加）
5. 点击「确认追加」

**结果**：匹配列值相同的行，其对应列数据会更新到当前表格；新列自动追加到表格末尾。

---

### 4. 数据追加（行）

将一个文件（Excel / Android / iOS）的行数据追加到当前表格末尾。

**操作流程**：

1. 点击 `📥 数据追加(行)`，选择数据源类型
2. 选择目标文件（或文件夹）
3. 配置列匹配规则：每个目标文件列 → 原始文件列（同名列默认自动对齐，可手动修改）
4. 勾选要追加的列（仅显示已匹配的列）
5. 设置起始行（从目标文件第几行开始追加），下方实时预览该行第一个已匹配列的内容
6. 点击「确认追加」

**结果**：从起始行到目标文件末尾的所有行，按匹配规则写入当前表格。

---

### 5. 导出

#### 导出 Excel

- 点击 `💾 导出修改后的 Excel`
- 勾选要导出的列（默认全选）
- 生成带时间戳的 `.xlsx` 文件，文件名格式：`原文件名_MMDDHHmmss.xlsx`

#### 导出 Android ZIP

- 点击 `📦 导出 Android ZIP`
- 选择 Key 列（默认自动识别 `Android Key` / `key` / `name`）
- 勾选要导出的语言列（自动映射为 `values-xx` 文件夹）
- 生成 `android_strings_MMDDHHmmss.zip`，包含 `values-*/strings.xml`
- **自动跳过 key 为空的行**

#### 导出 iOS 资源

- 点击 `📦 导出 iOS 资源`
- **自动判断模式**：
  - **String Catalog 模式**：若表格中存在 `iOS DocumentName` 列，则按该列分组，每组生成一个 `.xcstrings` 文件，打包为 `ios_xcstrings_MMDDHHmmss.zip`
  - **传统 .lproj 模式**：若不存在 `iOS DocumentName` 列，则生成 `ios_strings_MMDDHHmmss.zip`，包含 `*.lproj/Localizable.strings`
- **自动跳过 key 为空的行**
- String Catalog 模式下，`iOS DocumentName` 列的值即为文件名（自动去除 `.xcstrings` 后缀）

---

## 技术栈

- [SheetJS (xlsx)](https://sheetjs.com/) — Excel 解析与生成
- [JSZip](https://stuk.github.io/jszip/) — ZIP 打包
- 原生 JavaScript / HTML / CSS

---

## 浏览器兼容性

推荐使用最新版 Chrome、Edge、Safari。需要支持以下 API：

- File API、FileReader
- Drag & Drop
- Blob、URL.createObjectURL
- `webkitdirectory`（文件夹选择）

---

## 注意事项

- 所有处理均在浏览器内存中完成，**不会上传任何数据**
- 刷新或关闭页面会丢失未导出的修改，离开前会弹出提醒
- 导出文件名会自动附加时间戳，避免覆盖
- iOS String Catalog 模式要求表格中存在 `iOS DocumentName` 列，且每行填写对应的文件名（不含扩展名）
- Android / iOS 导出时会自动跳过 key 列为空的行
- 语言映射表内置 50+ 种常见语言，支持 BCP-47、iOS 风格、Android 风格自动转换

---

## 更新日志

### v1.0
- 支持 Excel 导入导出
- 支持 Android 资源导入导出
- 支持 iOS 资源导入导出（`.lproj` 与 `.xcstrings`）
- 支持数据追加（列/行），数据源支持 Excel / Android / iOS
- 表头编辑、列移动/删除、行编辑
- 列选择全选/半选
- 导出时跳过空 key 行
- 自动识别资源类型

---

## License

MIT