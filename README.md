
## recently been doing 
> [关于最近在看的几本书](https://www.bilibili.com/video/BV1gB4y1i7w3?spm_id_from=333.337.search-card.all.click)

## 个人周报
>  [2022年个人周报](https://github.com/leegangan/his-life/issues/1)--2022-07-05
> 
> 
> 
>


## 多数据源数据每日落库


> 数据源分为两种类型：
- 1.拥有所有权   
- 2.不具备所有权 （仅提供查询接口）

> 假设现有n个数据源

> - 1.order1 （拥有所有权、可以对原表进行写操作）
> - 2.order2  （不具备所有权、仅提供查询接口）
> 
> 最终需要将数据落库到表 record


> 同步数据要做的事情有两件 
> - 1.分批当日查询数据 
> - 2.最终确认数据同步状态
> 
> 
> 对于order1 
> 
> 在数据源上增加同步状态的标识字段 collectStatus 默认为 NEW
> 同步过程中 
> - 1.分批查询当日状态为NEW的记录 
> - 2.将查询结果 batch insert（可使用 insert ignore） 
> - 3.update order1同步状态 为COLLECTED 
>  
> tips：注意要将这两个操作要放入同一个事务 避免重复同步数据
> 
> 
> 
> 对于order2处理方式 需要提供分页查询和count接口
> 由于无法在原表上新增标识 因此只能通过 对比两边数量来进行终态确认
> - 1.count 比较order2 与order2同步过来的record 数量是否一致
> - 一致则返回 不需要进行同步
> - 2.分批查询当天记录
> - 提供几种方式来进行查询
> - 3.1可以通过 count 进行数据分批查询 
> - 3.2可以通过while循环 分页查询
> - 4.将数据进行插入
> 
> 
> 