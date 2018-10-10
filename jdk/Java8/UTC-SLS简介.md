# <div style="text-align:center;color:#FF9900">UTC-SLS简介</div>

## 1. UTC与UTC-SLS

### 1.1 区别：

* 除了最后1000秒（包含插入的闰秒），之前的秒数UTC和UTC-SLS没有区别；

* UTC-SLS是如何消化掉闰秒（Leap Second）的：

  * 当插入闰秒的时候，最后1000 UTC秒（包括插入的闰秒）等分成UTC-SLS的999秒

    > During the last 1000 seconds of a UTC day with inserted leap second, a UTC-SLS clock slows down to by 0.1% to 0.999 times its normal rate, such that the last 1000 UTC seconds (including the inserted leap second) span exactly the same time as the corresponding 999 "slow seconds" on the UTC-SLS clock.

  * 当删除闰秒的时候，最后1000 UTC秒（不包括删除的闰秒）等分成UTC-SLS的1001秒

    > During the last 1000 seconds of a day with deleted leap second, a UTC-SLS clock accelerates by 0.1% to 1.001 times its normal rate, such that the last 1000 UTC seconds (excluding the deleted leap second) span exactly the same time as the corresponding 1001 “fast seconds” on the UTC-SLS clock.

* 在每个整小时（和半小时），UTC和UTC-SLS是相同的;

