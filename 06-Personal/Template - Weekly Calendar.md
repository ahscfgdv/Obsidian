<%*
// 获取当前文件名的周数信息，如果没有则使用当前日期
let currentMoment = moment(tp.file.title, "YYYY-[W]ww", true).isValid() ? moment(tp.file.title, "YYYY-[W]ww") : moment();
let startOfWeek = currentMoment.clone().startOf('isoWeek');
let endOfWeek = currentMoment.clone().endOf('isoWeek');
let prevWeek = currentMoment.clone().subtract(1, 'weeks').format("YYYY-[W]ww");
let nextWeek = currentMoment.clone().add(1, 'weeks').format("YYYY-[W]ww");
-%>
# 📅 <% currentMoment.format("YYYY-[W]ww") %> 周记

**时间跨度**：[[<% startOfWeek.format("YYYY-MM-DD") %>]] 至 [[<% endOfWeek.format("YYYY-MM-DD") %>]]
**导航**：[[<% prevWeek %>|⏪ 上一周]] | [[<% nextWeek %>|⏩ 下一周]]
**标签**： #Weekly #Review

---

## 🌈 本周高光 (Highlights)

> [!QUOTE] 本周金句
> 只有分离后才能懂的事，却没有了感慨的时间。 —— 《宝石之国》 --  (波尔茨)

> [!SUCCESS] 成就与进展
> 1. 
> 2. 
> 3. 

---
## 📅 本周作息与习惯追踪打卡

|   星期   | ☀️ 起床时间 | 🌙 睡觉时间 |  刷牙   | 做饭  | 锻炼  | 拉伸  |
| :----: | :-----: | :-----: | :---: | :-: | :-: | :-: |
| **周一** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周二** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周三** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周四** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周五** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周六** |  11:30  |  7:20   | 11:00 |     |     |     |
| **周日** |  11:30  |  7:20   | 11:00 |     |     |     |

## 📅 每日记录 (Daily Log)

### 03-16 星期一

- [ ] Epic领游戏
### 03-17 星期二

- [ ] 
### 03-18 星期三

- [ ] 

### 03-19 星期四

- [ ] 

### 03-20 星期五

- [ ] 

### 03-21 星期六

- [ ] 洗衣服

### 03-22 星期日

- [ ] 刮胡子
- [ ] 洗澡


---

## 📉 复盘与反思 (Reflections)

> [!WARNING] 问题与挑战
> - **问题**：
> - **原因**：
> - **改进**：

---