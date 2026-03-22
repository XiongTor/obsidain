# <div style="font-size: 24px; color: #C97C7C;"> 🪾  Rosaceae cytonuclear</div>
```dataviewjs
let pages = dv.pages("#cytonuclear")
    .sort(b => b.file.mtime, 'desc');

dv.table(
    ["Title", "Year", "Modified Date", "Tags"],
    pages.map(b => [
        dv.fileLink(b.file.path, false, b.title ?? b.file.name),
        b.year ?? "",
        b.file.mtime.toFormat("yyyy-MM-dd HH:mm"),
        (b.tags ? b.tags.join(", ") : "")   // 处理 tags，多个标签用逗号隔开
    ])
);

```


```dataviewjs
let pages = dv.pages("#fruit")
    .sort(b => b.file.mtime, 'desc');

dv.table(
    ["Title", "Year", "Modified Date", "Tags"],
    pages.map(b => [
        dv.fileLink(b.file.path, false, b.title ?? b.file.name),
        b.year ?? "",
        b.file.mtime.toFormat("yyyy-MM-dd HH:mm"),
        (b.tags ? b.tags.join(", ") : "")   // 处理 tags，多个标签用逗号隔开
    ])
);

```