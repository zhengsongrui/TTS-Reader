# TXT 小说导入与读取优化（仅参考）

项目：uni-app x + Android

目标：支持大体积 TXT，避免导入和阅读时一次性加载整本小说。

## 1. 文件存储

原始 TXT 保存在 App 本地文件目录：

```text
books/{bookId}.txt
```

SQLite 不保存正文，只保存：

```text
books:
id
title
filePath
fileSize
encoding

chapters:
id
bookId
chapterIndex
title
startByte
endByte
```

---

## 2. 导入

禁止：

```ts
const text = await file.text()
```

禁止一次性读取整个 TXT。

采用**分块读取 + 增量解析**：

```text
TXT
 ↓
每次读取约 2MB
 ↓
增量解码
 ↓
解析章节
 ↓
记录 startByte / endByte
 ↓
继续读取下一块
```

需要正确处理 Chunk 边界，不能因为章节标题、换行符或 UTF-8 多字节字符刚好跨 Chunk 而解析错误。

章节解析不要对整本小说使用大型正则，采用逐行/逐段增量扫描。

---

## 3. 章节索引

必须保存**字节偏移量**：

```json
{
  "chapterIndex": 100,
  "title": "第一百章",
  "startByte": 52348921,
  "endByte": 52417382
}
```

不要保存 JS 字符串下标。

章节解析完成后批量写入 SQLite，避免每个章节单独提交事务。

---

## 4. 第二次打开

不要重新扫描 TXT。

直接：

```text
SQLite
 ↓
读取章节索引
 ↓
获取 startByte / endByte
```

---

## 5. 章节读取

阅读某一章时：

```text
SQLite 查询章节
 ↓
得到 startByte / endByte
 ↓
Android RandomAccessFile / FileChannel
 ↓
seek(startByte)
 ↓
只读取 endByte - startByte
 ↓
按照保存的 encoding 解码
 ↓
返回章节文本
```

禁止为了读取某一章而从文件头读取到目标章节。

禁止将整个 TXT 转换成 JS String。

---

## 6. 编码

至少考虑：

```text
UTF-8
GBK
GB18030
UTF-16
```

导入时确定编码并保存到 `books.encoding`，之后读取章节使用相同编码。

---

## 7. 线程

文件读取、解码、章节解析、SQLite 批量写入不能阻塞 Android UI 主线程。

优先使用 uni-app x 的 UTS + Android 原生后台线程能力。

---

## 最终要求

第一次导入：

```text
分块读取 → 增量解析 → 建立 byte offset 索引 → SQLite
```

以后阅读：

```text
SQLite 索引 → 定位 byte offset → 随机读取对应章节
```

核心原则：

**无论 TXT 是 10MB、100MB 还是 500MB，都不能因为读取单章而把整本小说加载进内存。**
