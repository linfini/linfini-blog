### 存储过程

实例表user

```
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `email` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `sex` int(255) DEFAULT NULL,
  `schoolName` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8mb4;
```

```
-- 创建存储过程
delimiter $$
create procedure `yeecode`(in `ageMinLimit` int,in `ageMaxLimit` int,out `count` int,out `maxAge` int)
begin
select count(*),max(age) into count,maxage from user where age>=ageMinLimit and age<=ageMaxLimit;
end$$
```

