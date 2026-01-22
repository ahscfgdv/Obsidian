<%*
// ==========================================
// 1. 日期计算逻辑 (保持不变)
// ==========================================
const fileName = tp.file.title;
let refDate;
if (/^\d{4}-W\d{2}$/.test(fileName)) {
    refDate = moment(fileName, "gggg-[W]ww");
} else {
    refDate = moment(); 
}

const monday = refDate.clone().startOf('isoWeek');
const sunday = refDate.clone().endOf('isoWeek');
const currentYear = refDate.format("YYYY");
const currentWeek = refDate.format("WW");
const prevWeek = refDate.clone().subtract(1, 'weeks').format("gggg-[W]ww");
const nextWeek = refDate.clone().add(1, 'weeks').format("gggg-[W]ww");

// ==========================================
// 2. 每日计划生成逻辑 (保持不变)
// ==========================================
let dailySection = "";
for (let i = 0; i < 7; i++) {
    let dayCursor = monday.clone().add(i, 'days');
    // 如果需要中文星期，可改为 dayCursor.locale('zh-cn').format("MM-DD dddd")
    let dateStr = dayCursor.format("MM-DD dddd"); 
    dailySection += `### ${dateStr}\n\n- [ ] \n\n`;
}

// ==========================================
// 3. 【新增】一言 API 获取逻辑
// ==========================================
let hitokotoContent = "正在加载金句..."; // 默认文案
try {
    // 参数说明：c=a(动画), c=b(漫画), c=c(游戏), c=d(文学), c=h(影视), c=i(诗词)
    // encode=json: 获取详细信息以便组合出处
    const apiUrl = "https://v1.hitokoto.cn/?c=b&c=c&c=d&c=h&c=i&encode=json";
    
    // 发起网络请求
    const response = await fetch(apiUrl);
    // 检查网络状态 
    if (!response.ok) { throw new Error("Network response was not ok"); } 
    // 关键修复点：直接使用 .json() 方法，而不是 JSON.parse() 
    const data = await response.json();
    
    // 组合成 "句子 —— 出处" 的格式
    // 如果没有作者(from_who)，就只显示出处(from)
    const author = data.from_who ? ` (${data.from_who})` : "佚名";
    hitokotoContent = `${data.hitokoto} —— 《${data.from}》 -- ${author}`;
    
} catch (error) {
    console.error("一言API请求失败:", error);
    hitokotoContent = "所谓无底深渊，下去，也是前程万里。 —— 《木心》 (网络请求失败，这是备用金句)";
}
_%>
# 📅 <% currentYear %>-W<% currentWeek %> 周记

**时间跨度**：[[<% monday.format("YYYY-MM-DD") %>]] 至 [[<% sunday.format("YYYY-MM-DD") %>]]
**导航**：[[<% prevWeek %>|⏪ 上一周]] | [[<% nextWeek %>|⏩ 下一周]]
**标签**：#Weekly #Review

---

## 🌈 本周高光 (Highlights)

> [!QUOTE] 本周金句
> <% hitokotoContent %>

> [!SUCCESS] 成就与进展
> 1. 
> 2. 
> 3. 

---
## 📅 本周作息与习惯追踪打卡

|   星期   | ☀️ 起床时间 | 🌙 睡觉时间 | ✅ 每日必做 (打卡) |     |     |
| :----: | :-----: | :-----: | :---------: | :-- | --- |
| **周一** |         |         |             |     |     |
| **周二** |         |         |             |     |     |
| **周三** |         |         |             |     |     |
| **周四** |         |         |             |     |     |
| **周五** |         |         |             |     |     |
| **周六** |         |         |             |     |     |
| **周日** |         |         |             |     |     |
- [ ] 
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

## 🔭 下周规划 (Next Week)

### 🎯 核心目标
1. [ ] 
2. [ ] 