# ER Diagram Designer · 数据库 ER 图设计器

> Drag out tables, wire relationships between row ports, and get real `CREATE TABLE` / `ALTER TABLE ... FOREIGN KEY` statements for MySQL, PostgreSQL or SQLite — while you draw.

**English** | [简体中文](README.zh-CN.md)

<a href="https://bushimashen.github.io/er-diagram/"><b>▶ Live demo</b></a>

## Features

- **Canvas editor** — drag tables, drag from a row port to another table's row to create a relation, pan on empty space, wheel to zoom, snap-to-grid (hold Alt to disable)
- **Live SQL** — one click gives you the full schema. Switch between MySQL / PostgreSQL / SQLite; identifier quoting, `COMMENT`, `ENGINE=` and `AUTO_INCREMENT`-style differences follow the dialect
- **Reverse import** — paste existing `CREATE TABLE` + `ALTER TABLE ... ADD CONSTRAINT ... FOREIGN KEY` (and inline `REFERENCES`) and it rebuilds the diagram
- **JSON round-trip** — export/import the whole diagram, including positions and colours
- **3 templates** — blog, e-commerce orders, RBAC permissions
- **PNG export** — renders to an offscreen canvas at the content bounding box, 2× scale, so the image doesn't depend on your current zoom
- **Undo / redo** (Ctrl+Z / Ctrl+Shift+Z), auto-layout, fit-to-view
- **Autosaves to localStorage** — close the tab, come back, it's still there

## Two things that are easy to get wrong

**Tables are emitted in dependency order.** If `articles` has an FK to `users`, `CREATE TABLE users` must come
first or the script fails on any real database. So the generator runs a topological sort over the relation
graph and emits referenced tables first; a cycle falls back to declaration order instead of dropping tables.

**Relations are stored as field-to-field pairs, not table-to-table.** That's what lets the FK clause name the
actual column (`FOREIGN KEY (author_id) REFERENCES users (id)`) rather than guessing. The `1` end and the `N`
end are distinguished at drag time: dragging right-to-left flips which side gets the foreign key.

## Usage

Download `index.html` and double-click it. No install, no build, no network.

```bash
python -m http.server 8000   # or serve it any way you like
```

## Data model

```js
table = { id, name, comment, color, x, y, fields: [field] }
field = { id, name, type, pk, fk, notnull, unique, def, comment }
rel   = { id, a, aField, b, bField, card, optional, onDelete, onUpdate }
```

`a`/`aField` is the "one" side, `b`/`bField` is the "many" side. Export the JSON from the toolbar to see exactly
this shape — importing a hand-edited file works too.

## Limits (honest list)

- The SQL parser handles the common `CREATE TABLE` shapes. Views, triggers, partitions, `CHECK` constraints and
  generated columns are ignored, not modelled.
- Indexes other than `PRIMARY KEY` / inline `UNIQUE` are not represented.
- No multi-schema / multi-database namespace.

For anything past that, reach for a real modelling tool — this one is for getting a schema out of your head fast.

## License

MIT — see [LICENSE](LICENSE).
