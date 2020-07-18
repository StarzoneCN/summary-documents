# <div style="text-align:center;color:#FF9900">MariaDB</div>
*对于加锁的处理，可以说就是数据库对于事务处理的精髓所在*

## 优化点
1. [关于对MySQL的SQL_NO_CACHE的理解和用法举例]

## 优秀博文
[Innodb中的事务隔离级别和锁的关系][] | [何为幻读？][] | [更多关于ORDER BY][Mysql应用之类似Oracle null first/last用法]
[MYSQL排序按照自定义的顺序排序][]
```
select * from A order by IF(ISNULL(a),1,0),a desc
```
[行级锁、表级锁、索引锁] | [MySQL的锁机制 - 记录锁、间隙锁、临键锁] | [全面了解mysql锁机制（InnoDB）与问题排查] | [SSD基本原理]



[Innodb中的事务隔离级别和锁的关系]:https://tech.meituan.com/2014/08/20/innodb-lock.html
[何为幻读？]:https://segmentfault.com/a/1190000016566788
[MYSQL排序按照自定义的顺序排序]:https://blog.csdn.net/hutongling/article/details/90177862
[Mysql应用之类似Oracle null first/last用法]:https://blog.csdn.net/u014427391/article/details/87297068 'Mysql应用之类似Oracle null first/last用法'
[行级锁、表级锁、索引锁]:https://blog.csdn.net/u014749862/article/details/80087035
[MySQL的锁机制 - 记录锁、间隙锁、临键锁]:https://zhuanlan.zhihu.com/p/48269420
[全面了解mysql锁机制（InnoDB）与问题排查]:https://juejin.im/post/5b82e0196fb9a019f47d1823
[SSD基本原理]:http://oserror.com/backend/ssd-principle/
[关于对MySQL的SQL_NO_CACHE的理解和用法举例]:https://blog.csdn.net/csd753111111/article/details/100428394?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase
