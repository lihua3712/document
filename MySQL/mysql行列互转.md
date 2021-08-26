 **原始图例：** 
![输入图片说明](https://images.gitee.com/uploads/images/2021/0826/155800_85f77e58_5296156.png "屏幕截图.png")
行转列：

```
SELECT uname,uid, -- 正常查询的字段
SUM(
CASE
WHEN course ='英语' THEN score ELSE 0 -- 需要转换的字段else 0
END) '英语',
SUM(
CASE 
WHEN course= '物理'  THEN score ELSE 0
END) '物理',
SUM(
CASE  
WHEN course='化学' THEN score ELSE 0
END) '化学' ,
SUM(score) 'total'
FROM course
GROUP BY uid

UNION 

SELECT 'Total',NULL,
SUM(IF(`course`='英语',score,0)) '英语',
SUM(IF(`course`='物理',score,0)) '物理',
SUM(IF(`course`='化学',score,0)) '化学',
SUM(score) 'total'
FROM course
```
