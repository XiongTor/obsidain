---
banner: "![[homepage_banner.jpeg]]"
banner_y: 0.136
---
---


# <div style="font-size: 28px; color: #C97C7C; margin-top: -70px;"> 🎃  Homepage</div>
```contributionWidget
id: 36211b43-2f14-493b-a553-bb53d9d8f338
type: multi
titleAlign: center
tabTitle: ""
maxWidthRatio: -1
backgroundStyle: none
widgets:
  - id: 1253347c-6c50-4e65-a69a-60d536846ed3
    type: multi
    titleAlign: center
    tabTitle: ""
    maxWidthRatio: -2
    backgroundStyle: none
    widgets:
      - id: ed80ce7a-288f-4fdd-bcb2-359d84cee037
        type: markdown
        titleAlign: center
        tabTitle: ""
        maxWidthRatio: 100
        backgroundStyle: card
        maxHeight: 193
        contentAlign: left
        markdownsSource: content
        markdownValue: |-
          <div class="title"; style="font-size: 18px; line-height: 24px; text-align: center;">MOC</div>
          <div style="font-size: 13px;">

          ````col
          ```col-md
          flexGrow=1
          ===
          📝[[Reference_list|Reference]]
          ```
          ```col-md
          flexGrow=1
          ===
          🗃[[Project|Project]]
          ```
          ```col-md
          flexGrow=1
          ===

          ```
          ````

          </div>
        backgroundColor: "#1d2129ff"
        fontColor: "#e6e0e0ff"
    layoutType: column
layoutType: column

```

```contributionWidget
id: daa2eb56-3e05-4a05-8bba-33235dc9c91c
type: multi
titleAlign: center
tabTitle: ""
maxWidthRatio: -1
backgroundStyle: none
widgets:
  - id: da3fe19c-767e-4997-b675-d6c155bc1681
    type: timing
    titleAlign: center
    tabTitle: ""
    maxWidthRatio: 63.51744186046512
    backgroundStyle: none
    showStartDateTime: true
    timeTextPattern: yMdHms
    startDateTime: 2025-06-17 21:39:02
    title: 主页运行时间
    backgroundColor: "#1d2129ff"
    fontColor: "#f3f8f6ff"
  - id: 8d1cec57-472c-4640-bcaa-f47f45ec88fd
    type: dateProgress
    titleAlign: center
    tabTitle: ""
    maxWidthRatio: -1
    backgroundStyle: none
    showDateIndicator: true
    showProgressIndicator: true
    startDateTimeType: $startOfWeek
    endDateTimeType: $endOfWeek
    uiType: bar
    title: Week
    backgroundColor: "#1d2129ff"
layoutType: column

```
```contributionWidget
id: 200cd383-1d29-41cf-819c-03101724f9df
type: multi
titleAlign: center
tabTitle: ""
maxWidthRatio: -1
backgroundStyle: none
widgets:
  - id: f9a7f183-de1f-499e-8424-987ebc2f77ea
    type: dataview
    titleAlign: center
    tabTitle: ""
    maxWidthRatio: 100
    query: |
      let today = dv.date("today").toFormat("yyyy-MM-dd");

      dv.taskList(
          dv.pages()
            .where(p => p.file.name.startsWith(today))   // ✅ 只抓取今天的日记
            .flatMap(page => {
              let tags = String(page.tags).split(" ");
              if (tags.includes("list")) {
                  return page.file.tasks.where(t => 
                      !t.completed &&
                      !t.text.contains("?") &&
                      !t.text.includes("？") &&
                      !t.text.contains("#pending") &&
                      !t.path.contains("Projects/Building Workflow") && 
                      (t.header.subpath == "Todo" || t.header.subpath == "Doing")
                  );
              } else {
                  return page.file.tasks.where(t => 
                      !t.completed &&
                      !t.text.contains("?") &&
                      !t.text.includes("？") &&
                      !t.text.contains("#pending") &&
                      !t.path.contains("Projects/Building Workflow")
                  );
              }
          })
      )
    queryType: dataviewjs
    backgroundStyle: none
    maxHeight: 446
    dynamicParamComponents: []
    backgroundColor: "#1d2129ff"
    fontColor: "#f0eaeaff"
    title: ""
layoutType: column

```
```contributionGraph
title: Contributions
graphType: default
dateRangeValue: 180
dateRangeType: LATEST_MONTH
startOfWeek: 1
showCellRuleIndicators: true
titleStyle:
  textAlign: left
  fontSize: 15px
  fontWeight: normal
dataSource:
  type: ALL_TASK
  value: ""
  dateField: {}
fillTheScreen: false
enableMainContainerShadow: false
cellStyleRules: []

```
