# Innodb Cardinality
## 目的
```
拒绝低选择性 如性别，可能筛选出来50%的rows
崇尚高选择性 如用户名，几乎唯一
```
	
## 如何判断高选择性
```
show index 查看 Cardinality
Cardinality/n_rows_in_table越接近1，越高
```
		
## Innodb更新Cardinality的时机
```
  策略1：表中1/16的数据已发生过变化
  策略2：stat_modified_counter＞2 000 000 000 （防止策略1重复更新一行，总更新行数没怎么变化）
```

## 计算Cardinality算法
```
1、获取B+树索引中叶子节点的数量，记为A
2、随机找到8个叶子结点（Leaf Page）每个页不同记录的个数,P1,P2...P8
3、(P1+P2+..P8) *A / 8
```

## 影响参数
### innodb_stats_sample_pages
```
设置采样页的数量，默认8
```
### innodb_stats_method
```
如何对待索引中出现NULL值记录
			nulls_equal 视为相等
				索引页：NULL、NULL、1、2、2、3、3、3，Cardinality=4
			nulls_unequal 视为不相等
				索引页：NULL、NULL、1、2、2、3、3、3，Cardinality=5
			nulls_ignored 视为忽略
				索引页：NULL、NULL、1、2、2、3、3、3，Cardinality=3
```
### innodb_stats_on_metadata
```
每次show tables; show index; 访问information_schema架构下的tables以及statistics的时候
是否需要重新计算索引的Cardinality值
```
### innodb_stats_persistent
```
是否要把Cardinality的值保存到磁盘上，重启db的时候减少重新计算
```

## 总结：
![image](https://image-static.segmentfault.com/387/226/3872265385-5c77e6da20b87)
