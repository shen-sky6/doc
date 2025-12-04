
```sql

ALTER TABLE `db_syzh_data_market`.`t_xdata_subscribe_task` 
ADD COLUMN `client_id` varchar(32) NULL COMMENT 'client_id' AFTER `remark`;

ALTER TABLE `db_syzh_data_market`.`t_xdata_subscribe_task` 
ADD COLUMN `type` tinyint NULL DEFAULT 0 COMMENT '0主专题，1子专题' AFTER `client_id`;

```