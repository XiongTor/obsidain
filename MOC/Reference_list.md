# <div style="font-size: 24px; color: #C97C7C;"> ✏Reading and done</div>

```dataviewjs
let pages = dv.pages("#reading")
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
let pages = dv.pages("#done")
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

# <div style="font-size: 24px; color: #C97C7C;"> 📋Total list</div>

## 核质冲突

| 标题                                                                                                                          | 标签      | zotero                      |
| :-------------------------------------------------------------------------------------------------------------------------- | :------ | :-------------------------- |
| Why do phylogenomic data sets yield conflicting trees? Data type influences the avian tree of life more than taxon sampling | #综述 #前言 | [@2017](../references/@2017.md) |
## 方法论

| 标题                                                                                                                                                 | 标签              | zotero                                                                                                                                                                                               |
| :------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Orthology inference in nonmodel organisms using transcriptomes and low-coverage genomes: improving accuracy and matrix occupancy for phylogenomics | #旁系同源 #paragone | [@Ya Yang, Stephen A. Smith\_2014](../references/@Ya%20Yang,%20Stephen%20A.%20Smith_2014.md)                                                                                                              |
|                                                                                                                                                    |                 | [@Elizabeth S. Allman, Hector Baños, Jonathan D. Mitchell, John A. Rhodes\_2024](../references/@Elizabeth%20S.%20Allman,%20Hector%20Baños,%20Jonathan%20D.%20Mitchell,%20John%20A.%20Rhodes_2024.md) |
[@Ruyi Chen, Gabriel Foley, Mikael Bodén\_2024](../references/@Ruyi%20Chen,%20Gabriel%20Foley,%20Mikael%20Bodén_2024.md)