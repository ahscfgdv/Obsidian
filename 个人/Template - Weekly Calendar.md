<%*
/* 核心逻辑：从文件名解析日期，确保点击"未来/过去"的周也能生成正确日期 */
const fileName = tp.file.title;
let refDate;

// 检查文件名是否符合 "2026-W04" 这种 ISO 周格式
// 如果不符合（比如你手动新建未命名文件），则默认使用当前时间
if (/^\d{4}-W\d{2}$/.test(fileName)) {
    refDate = moment(fileName, "gggg-[W]ww");
} else {
    refDate = moment(); 
}

// 计算这一周的周一和周日
const monday = refDate.clone().startOf('isoWeek').format("YYYY-MM-DD");
const sunday = refDate.clone().endOf('isoWeek').format("YYYY-MM-DD");
const currentYear = refDate.format("YYYY");
const currentWeek = refDate.format("WW");
const prevWeek = refDate.clone().subtract(1, 'weeks').format("gggg-[W]ww");
const nextWeek = refDate.clone().add(1, 'weeks').format("gggg-[W]ww");
_%>
# 📅 <% currentYear %>-W<% currentWeek %> 周记

**时间跨度**：[[<% monday %>]] 至 [[<% sunday %>]]
**上一周**：[[<% prevWeek %>]] | **下一周**：[[<% nextWeek %>]]
**标签**：#Weekly #Review

---

## 🌈 本周高光 (Highlights)

> [!SUCCESS] 成就与进展
> 1. 
> 2. 
> 3. 

## 📉 复盘与反思 (Reflections)

> [!WARNING] 问题与挑战
> - **问题**：
> - **原因**：
> - **改进**：

---

## 🚀 项目推进 (Tracker)

| 项目 | 进度 | 状态 | 备注 |
| :--- | :--- | :--- | :--- |
| **CS架构问答系统** | ⬛⬛⬜⬜⬜ | 🟡 开发中 | *Vue3前端页面搭建* |
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
       

### 🗓️ 待办清单
- [ ]
- 