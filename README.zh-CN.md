# 数据库 ER 图设计器 · ER Diagram Designer

> 拖出表、从行端口连线建立关系，边画边拿到真实可跑的 `CREATE TABLE` / `ALTER TABLE ... FOREIGN KEY`，支持 MySQL / PostgreSQL / SQLite。

[English](README.md) | **简体中文**

<a href="https://bushimashen.github.io/er-diagram/"><b>▶ 在线试用</b></a>

## 能做什么

- **画布编辑** —— 拖表移动，从某行的端口拖到另一张表的行就建立关系；空白拖动平移、滚轮缩放、吸附网格（按住 Alt 关闭吸附）
- **实时 SQL** —— 一键得到整份建表语句，可切 MySQL / PostgreSQL / SQLite；标识符引号、`COMMENT`、`ENGINE=` 等方言差异会跟着变
- **反向导入** —— 把已有的 `CREATE TABLE` + `ALTER TABLE ... ADD CONSTRAINT ... FOREIGN KEY`（含行内 `REFERENCES`）粘回来，直接还原成图
- **JSON 往返** —— 导出/导入整张图，含坐标与配色
- **3 个模板** —— 博客、电商订单、RBAC 权限
- **PNG 导出** —— 按内容包围盒离屏重绘、2 倍图，导出结果与当前缩放无关
- **撤销 / 重做**（Ctrl+Z / Ctrl+Shift+Z）、自动排列、适应画布
- **自动存本地** —— 关掉标签页再回来还在

## 两个最容易做错的点

**建表顺序必须按依赖来。** 如果 `articles` 有外键指向 `users`，那 `CREATE TABLE users` 必须先执行，
否则脚本在任何真实数据库上都跑不过。所以生成器会对关系图做**拓扑排序**，被引用的表先输出；
遇到环则退回原顺序，而不是把表丢掉。

**关系存的是「字段对字段」，不是「表对表」。** 这样才能生成精确的外键子句
（`FOREIGN KEY (author_id) REFERENCES users (id)`），而不是靠猜。拖拽时就区分了 1 端和 N 端——
从右往左拖会翻转哪一边拿外键。

## 怎么用

下载 `index.html` 双击打开。不用安装、不用构建、不用联网。

```bash
python -m http.server 8000   # 或者用任意静态服务
```

## 数据结构

```js
table = { id, name, comment, color, x, y, fields: [field] }
field = { id, name, type, pk, fk, notnull, unique, def, comment }
rel   = { id, a, aField, b, bField, card, optional, onDelete, onUpdate }
```

`a` / `aField` 是「1」端，`b` / `bField` 是「N」端。从工具栏导出 JSON 就能看到完全长这样，手改后再导入也能用。

## 明确不做的事

- SQL 解析只覆盖常见的 `CREATE TABLE` 写法。视图、触发器、分区、`CHECK` 约束、生成列都不建模。
- 除 `PRIMARY KEY` 与行内 `UNIQUE` 之外的索引不体现。
- 不支持多 schema / 多库命名空间。

超出这些就该上专业建模工具了——这个工具的目标是「把脑子里的表结构快速变成能跑的 SQL」。

## 许可

MIT —— 见 [LICENSE](LICENSE)。
