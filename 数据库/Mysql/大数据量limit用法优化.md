```sql
SELECT u.xuexinID, u.realName, u.mobile, u.createDate, u.enable
	, u.gender
FROM xx_user u
WHERE u.deleteFlag = 1
ORDER BY u.createDate DESC
LIMIT 100000, 10
```
（xx_user表中共有近300万数据）
如上的sql在执行时，平均执行时间为1172.31s.

### SQL诊断优化

![](image/sql-limit.jpg)


### sql改写建议

```sql
SELECT `u`.`xuexinID`, `u`.`realName`, `u`.`mobile`, `u`.`createDate`, `u`.`enable`, `u`.`gender`
FROM `xx_user` AS `u`
INNER JOIN (SELECT `xuexinid`
FROM `xx_user` AS `u`
WHERE `u`.`deleteFlag` = 0
ORDER BY `u`.`createDate` DESC
LIMIT 100000,10) AS `tmp_0` USING (`xuexinid`)

```
