# <div style="text-align:center;color:#FF9900">Quartz</div>
## 1. Cron表达式
### 1.1 基础知识
> 自行搜索

### 1.2 疑难点
#### 1.2.1 字符‘C’
下面是英文解释：
> The 'C' character is allowed for the day-of-month and day-of-week fields. This character is short-hand for "calendar". This means values are calculated against the associated calendar, if any. If no calendar is associated, then it is equivalent to having an all-inclusive calendar. A value of "5C" in the day-of-month field means "the first day included by the calendar on or after the 5th". A value of "1C" in the day-of-week field means "the first day included by the calendar on or after sunday".

个人理解：
> nC - 表示每月/每周第n天；虽然普通情况下和`n`表达的意思形同，但是要注意，是和日历相关的；
