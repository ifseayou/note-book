Pg库在使用过程中，遇到了这样一个问题：

```sql
ERROR: canceling statement due to conflict with recovery
Detail: User query might have needed to see row versions that must be removed
```

该问题在[请点击这里](https://stackoverflow.com/questions/14592436/postgresql-error-canceling-statement-due-to-conflict-with-recovery)  

网上有人基于此做了一个[demo ](https://developer.aliyun.com/article/14644), 

```
Bitmap Index Scan
Bitmap Heap Scan 
```

pg库内部是怎么玩的？

