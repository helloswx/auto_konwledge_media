# 自动化知识媒体处理流程

本项目用于自动化处理订阅的 Substack 邮件，并将其转换为演示文稿（PPT）。整个流程分为两个主要阶段：

## 流程概览

```
Substack 邮件 
  ↓
Gmail 触发器 (New Email)
  ↓
Zapier 过滤 (Filter conditions)
  ↓
创建文本文件 (Google Drive - Create File From Text)
  ↓
转换为 PDF (PDF.co - HTML to PDF Converter)
  ↓
上传 PDF (Google Drive - Upload File)
  ↓
本地处理 → Gamma 生成 PPT
```

**Zapier 自动化流程（5 步）**：
1. **Gmail** - 检测新邮件
2. **Filter by Zapier** - 过滤符合条件的邮件（如 Substack 邮件）
3. **Google Drive** - 创建文本文件保存原始邮件信息
4. **PDF.co** - 将邮件 HTML 内容转换为 PDF
5. **Google Drive** - 上传 PDF 文件

## 第一阶段：邮件收集与存储（Zapier + Google Drive）

### 1.1 流程说明

我们通过 **Zapier** 将订阅的 Substack 邮件进行过滤，然后将以下内容存储到 Google Drive：

- **原始邮件信息**：包含邮件标题、正文、发送时间等完整信息
- **邮件 PDF**：将邮件内容转换为 PDF 格式保存

> **重要说明**：两种格式各有用途
> - **原始内容**：可以提取其中的图片链接和在线资源，让 Gamma 更好地生成带有在线链接的 PPT
> - **PDF 格式**：可以用于其他工具（如其他 PPT 生成工具、文档分析工具等）来生成展示内容

> **注意**：由于 Google Drive 在内地访问不稳定，建议直接下载文件到本地进行处理。

### 1.2 Zapier 配置指南

> **重要提示**：
> - Zapier 工作流可以通过**内置的 AI 对话功能**自动生成，无需手动配置每个步骤
> - 只需描述您的需求（如："当收到来自 Substack 的邮件时，保存原始内容和 PDF 到 Google Drive"），AI 会自动创建完整的工作流
> - 由于 Zapier 的代码库无法下载和迁移，本文档仅作为流程介绍和参考，实际配置建议使用 Zapier 的 AI 助手

以下是手动配置的详细步骤（供参考）：

#### 步骤 1：创建新的 Zap

1. 登录 [Zapier](https://zapier.com)
2. 点击 "Create Zap" 创建新的自动化流程

#### 步骤 2：设置触发器（Trigger）

1. **选择应用**：搜索并选择 "Gmail"
2. **选择触发器**：选择 "New Email"
3. **配置触发器**：
   - 连接您的 Gmail 账号
   - 设置检查频率（例如：每 15 分钟检查一次新邮件）
   - 测试触发器，确保能够检测到新邮件

#### 步骤 3：添加过滤条件（Filter）

1. **选择应用**：搜索并选择 "Filter by Zapier"
2. **配置过滤条件**：设置条件，只让符合条件的邮件继续流程
   - **条件示例**：
     - 发件人包含 `substack.com` 或 `@substack.com`
     - 或者主题包含特定关键词
   - **设置方式**：
     - 选择字段：例如 "From Email" 或 "Subject"
     - 选择条件：例如 "contains"（包含）
     - 输入值：例如 `substack.com`
3. **测试过滤**：确保只有符合条件的邮件会通过

#### 步骤 4：添加操作（Action）- 创建文本文件

1. **选择应用**：选择 "Google Drive"
2. **选择操作**：选择 "Create File From Text"
3. **配置参数**：
   - **File Name**：使用邮件主题和日期，例如：
     ```
     原始邮件数据 - {{Subject}} - {{Date}}
     ```
   - **File Content**：包含邮件的完整信息，例如：
     ```
     标题: {{Subject}}
     发件人: {{From Name}} <{{From Email}}>
     日期: {{Date}}
     收件人: {{To}}
     
     正文:
     {{Body HTML}} 或 {{Body Plain}}
     ```
   - **Folder**：选择 Google Drive 中的目标文件夹
   - **Convert to Google Docs format**：建议选择 "Yes"（转换为 Google Docs 格式，便于后续处理）

#### 步骤 5：添加操作（Action）- 转换为 PDF

1. **选择应用**：搜索并选择 "PDF.co"（或类似的 HTML to PDF 转换服务）
2. **选择操作**：选择 "HTML to PDF Converter"
3. **配置参数**：
   - **HTML Content**：使用邮件的 HTML 内容，例如 `{{Body HTML}}`
   - 或者使用之前创建的文本文件内容
   - **PDF Options**（可选）：
     - 页面大小：A4
     - 边距：默认
     - 其他格式选项根据需要设置
4. **输出**：PDF.co 会返回转换后的 PDF 文件

#### 步骤 6：添加操作（Action）- 上传 PDF

1. **选择应用**：选择 "Google Drive"
2. **选择操作**：选择 "Upload File"
3. **配置参数**：
   - **File**：选择步骤 5 中 PDF.co 生成的 PDF 文件
   - **File Name**：例如 `{{Subject}} - {{Date}}.pdf`
   - **Folder**：选择与步骤 4 相同的文件夹（或创建新的 PDF 文件夹）
   - **Convert to Google Docs format**：选择 "No"（保持 PDF 格式）

#### 步骤 7：测试和激活

1. 点击 "Test" 测试 Zap 是否正常工作
2. 确认测试成功后，点击 "Turn on Zap" 激活自动化流程

### 1.3 数据获取

配置完成后，Zapier 会自动将符合条件的 Substack 邮件存储到 Google Drive。您可以：

1. **直接下载**：从 Google Drive 下载**原始邮件文档**和 PDF 文件到本地
2. **使用 API**：通过 Google Drive API 自动下载（需要额外配置）

> **重要提示**：
> - **用于 Gamma 生成 PPT**：请下载**原始邮件文档**（`.docx` 格式）到 `gamma_local/dataset/` 目录
> - **用于其他工具**：可以使用 PDF 文件
> - 建议定期从 Google Drive 下载文件到本地进行处理

## 第二阶段：Gamma PPT 生成（本地处理）

### 2.1 流程说明

获取到邮件的原始信息后，可以使用 **Gamma API** 将邮件内容转换为专业的演示文稿（PPT）。

> **重要说明**：由于 Gamma 目前不支持直接下载生成的 PPT 文件，本项目的子模块（`gamma_local`）只会记录生成 PPT 的 URL，您可以通过浏览器访问该 URL 查看和分享演示文稿。

### 2.2 数据转换与优化

> **为什么使用原始邮件内容而非 PDF**：
> - 原始邮件内容（`.docx` 格式）保留了完整的文本结构和**在线图片链接**
> - Gamma 可以识别并处理文本中的图片 URL，从而在生成的 PPT 中正确显示在线图片
> - PDF 格式虽然便于其他工具使用，但图片链接可能丢失或无法提取

为了确保 Gamma 能够更好地读取和处理邮件内容，特别是**在线图片信息**，需要对原始邮件数据进行转换：

#### 2.2.1 图片链接提取

Gamma 可以识别和处理文本中的图片 URL。项目会自动：

1. **提取图片链接**：从原始邮件文档中提取所有图片 URL（包括：
   - 直接图片链接（`.jpg`, `.png`, `.gif` 等）
   - 图片托管服务链接（Imgur、ImgBB、Cloudinary 等）
   - 内嵌图片的 URL

2. **优化图片显示**：
   - 将图片 URL 添加到输入文本的末尾
   - 在 `additionalInstructions` 中提示 Gamma 使用提供的图片
   - 设置 `imageOptions.source` 为 `"noImages"` 以使用提供的图片而非 AI 生成

#### 2.2.2 文本格式化

项目会自动：

- 提取邮件正文和标题
- 保留段落结构
- 提取表格内容
- 清理不必要的格式字符

### 2.3 使用方法

详细的使用方法请参考 [`gamma_local/README.md`](gamma_local/README.md)。

#### 快速开始

1. **安装依赖**：
   ```bash
   cd gamma_local
   conda env create -f environment.yml
   conda activate gamma_local
   ```

2. **配置 API Key**：
   - 复制 `env_example.txt` 为 `.env`
   - 在 `.env` 中设置您的 Gamma API Key

3. **准备数据**：
   - 将从 Google Drive 下载的**原始邮件文档**（`.docx` 格式，非 PDF）放入 `dataset/` 目录
   - **重要**：使用原始文档而非 PDF，因为原始文档包含可提取的图片链接，能让 Gamma 更好地生成 PPT

4. **运行处理**：
   ```bash
   python main.py
   ```

5. **查看结果**：
   - 生成的 PPT URL 会保存在 `generation_records.json` 中
   - 可以通过浏览器访问 URL 查看演示文稿

### 2.4 生成记录管理

项目会自动记录所有生成的 PPT：

- **记录文件**：`gamma_local/generation_records.json`
- **记录内容**：
  - 文件路径和哈希值（用于去重）
  - 生成任务 ID
  - Gamma 演示文稿 URL
  - 生成状态和时间戳

**常用命令**：
```bash
# 查看所有生成记录
python main.py --list

# 强制重新生成（即使已存在记录）
python main.py --force
```

## 项目结构

```
auto_konwledge_media/
├── README.md                    # 本文件（主流程说明）
└── gamma_local/                # Gamma PPT 生成子模块
    ├── README.md               # Gamma 模块详细说明
    ├── main.py                 # 主处理程序
    ├── dataset/                # 输入文件目录（存放原始邮件数据）
    ├── output/                 # 输出目录（如有导出功能）
    ├── generation_records.json # 生成记录（自动创建）
    ├── requirements.txt        # Python 依赖
    ├── environment.yml         # Conda 环境配置
    └── env_example.txt         # 环境变量配置示例
```

## 注意事项

1. **Zapier 配置**：
   - **推荐使用 AI 助手**：Zapier 内置的 AI 对话功能可以自动生成工作流，只需描述需求即可
   - Zapier 是付费服务，需要订阅才能使用多步骤自动化
   - 建议定期检查 Zap 的运行状态
   - 可以设置 Zap 运行通知以便及时发现问题
   - 本流程使用了 5 个步骤，需要 Zapier 的付费计划
   - **注意**：Zapier 的代码库无法下载和迁移，因此本文档仅作为流程介绍

2. **PDF.co 服务**：
   - PDF.co 是第三方 HTML 转 PDF 服务，需要在 Zapier 中连接账号
   - 可能需要注册 PDF.co 账号并获取 API Key
   - 也可以使用其他类似的 HTML to PDF 转换服务（如 PDFShift、HTML/CSS to PDF API 等）
   - 如果邮件内容较复杂，可能需要调整 PDF 转换参数以获得更好的效果

3. **Google Drive 访问**：
   - 在内地访问 Google Drive 可能不稳定
   - 建议使用 VPN 或定期手动下载文件
   - 可以考虑使用其他云存储服务（如 OneDrive、Dropbox）替代

4. **Gamma API**：
   - 需要注册 Gamma 账号并获取 API Key
   - API 有使用限制，请查看 [Gamma 开发者文档](https://developers.gamma.app)
   - 生成的 PPT 只能通过 URL 访问，无法直接下载

5. **图片处理**：
   - 确保邮件中的图片链接是可公开访问的
   - 如果图片链接需要认证，Gamma 可能无法访问
   - 建议使用公开的图片托管服务

## 后续优化方向

- [ ] 自动化 Google Drive 文件下载
- [ ] 支持更多邮件服务（Outlook、其他邮箱）
- [ ] 优化图片提取和处理逻辑
- [ ] 支持批量处理多个邮件
- [ ] 添加邮件内容分析和摘要功能

## 许可证

MIT License

