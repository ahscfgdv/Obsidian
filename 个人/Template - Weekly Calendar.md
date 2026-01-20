<%*
/* 核心逻辑：从文件名解析日期 */
const fileName = tp.file.title;
let refDate;

// 1. 确定基准日期
if (/^\d{4}-W\d{2}$/.test(fileName)) {
    refDate = moment(fileName, "gggg-[W]ww");
} else {
    refDate = moment(); 
}

// 2. 基础变量计算
const monday = refDate.clone().startOf('isoWeek');
const sunday = refDate.clone().endOf('isoWeek');
const currentYear = refDate.format("YYYY");
const currentWeek = refDate.format("WW");
const prevWeek = refDate.clone().subtract(1, 'weeks').format("gggg-[W]ww");
const nextWeek = refDate.clone().add(1, 'weeks').format("gggg-[W]ww");

// 3. 【新增】自动生成每日计划模块
// 循环 7 次，生成每一天的标题（格式：01-19 Monday）
let dailySection = "";
for (let i = 0; i < 7; i++) {
    let dayCursor = monday.clone().add(i, 'days');
    // 如果想要中文星期，需确保 Obsidian 设置为中文，或强制使用 .locale('zh-cn')
    let dateStr = dayCursor.format("MM-DD dddd"); 
    dailySection += `### ${dateStr}\n\n- [ ] \n\n`;
}
_%>
# 📅 <% currentYear %>-W<% currentWeek %> 周记

**时间跨度**：[[<% monday.format("YYYY-MM-DD") %>]] 至 [[<% sunday.format("YYYY-MM-DD") %>]]
**导航**：[[<% prevWeek %>|⏪ 上一周]] | [[<% nextWeek %>|⏩ 下一周]]
**标签**：#Weekly #Review

---

## 🌈 本周高光 (Highlights)

> [!SUCCESS] 成就与进展
> 1. 
> 2. 
> 3. 

---

## 📅 每日记录 (Daily Log)

<% dailySection %>

---

## 📉 复盘与反思 (Reflections)

> [!WARNING] 问题与挑战
> - **问题**：
> - **原因**：
> - **改进**：

---

## 🚀 项目推进 (Tracker)

| 项目 | 进度 | 状态 | 备注 |
| :--- | :--- | :--- | :--- |
| **CS架构问答系统** | ⬛⬛⬛⬜⬜ | 🟡 开发中 | *Vue3前端页面搭建* |
| **Linux/运维** | ⬛⬛⬛⬜⬜ | 🟢 正常 | *ESXi维护* |
| **AI学习** | ⬛⬜⬜⬜⬜ | 🔴 待启动 | *LangChain研究* |

---

## 📥 输入与输出

- 📚 **阅读/学习**：
- 📝 **代码/笔记**：

---
