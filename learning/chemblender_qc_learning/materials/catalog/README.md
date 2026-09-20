# 资料目录索引与按需 OCR

本目录记录学习包本地资料的身份核验和目录定位入口。源 PDF 未被修改。

## 方法

- 有可靠文字层或 PDF outline 的文件：用 fitz 直接读取目录或章节锚点。
- 中文扫描件 BK03：只渲染并 OCR PDF 第 11–18 页。
- 无可靠文字层的 BK04：只渲染并 OCR 前 30 页；实际目录落在 PDF 第 4、6–8、10 页。
- 原始 OCR JSON 与同页 Markdown 位于 raw/BK03 和 raw/BK04；页图位于 pages/BK03 和 pages/BK04。
- OCR 文本只是检索入口。公式、图表、关键定义和科学结论必须回看对应页图与正文。

## 已登记资料

17 项本地文件已写入 reading_manifest.json，均包含相对路径、SHA-256 和版次/身份证据：

- BK01–BK05：5 项教材
- RV01–RV05：5 项综述/观点文章
- SW01、SW02、SW04、SW05：4 项软件文章
- FD01–FD03：3 项方法文章

## 缺失或未选文件

- SW03：Multiwfn 文章，尚未保存。
- DT01–DT04：在线文档，尚未保存为本地文件。
- 另外的候选重复/分卷文件保留在 materials 下，但没有冒充目标资源登记：BK02 的两份无文字层扫描、BK04 的另一份中册扫描、BK05 的 DJVU，以及 RV01 之外的 weak-interactions 文章。

## 后续正文 OCR

需要正文时，先根据 toc_text.md 或 raw 目录确定 PDF 页码，再只渲染那些页，并沿用 20260911 项目的 examforge.cli ocr。调用前设置 EXAMFORGE_HOME=D:\workspace\20260911 和 PYTHONPATH=D:\workspace\20260911\src。不要对整本 PDF 执行 repair 或批量 OCR。
