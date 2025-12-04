### 1.待执行的sql脚本


#### sql脚本11月
```sql
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_duty_log` 
DROP COLUMN `stay_duration`;

```

#### sql脚本7月
```sql

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
ADD COLUMN `is_compilation` tinyint NULL DEFAULT 0 COMMENT '编报';

CREATE TABLE `db_syzh_eagle_web`.`t_chongqing2_report_bulletin`  (
  `id` int NOT NULL AUTO_INCREMENT,
  `report_id` int NULL COMMENT '上报id',
  `read_state` tinyint NULL DEFAULT 0 COMMENT '已阅状态',
  `create_dept_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT '' COMMENT '创建单位id',
  `company_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL DEFAULT '' COMMENT '公司id',
  `create_by` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL DEFAULT '' COMMENT '添加人',
  `create_time` datetime(0) NULL DEFAULT CURRENT_TIMESTAMP(0) COMMENT '创建时间',
  `deleted` tinyint(1) NOT NULL DEFAULT 0 COMMENT '是否删除（0=否，1=是）',
  PRIMARY KEY (`id`)
);
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_bulletin` COMMENT = '区县通报表';
-- 上面已发布uat

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_instruction` 
MODIFY COLUMN `is_sensitive` tinyint NULL DEFAULT 0 COMMENT '是否敏感数据';
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement_instruction_relation` 
ADD COLUMN `is_sensitive` tinyint NULL DEFAULT 0 COMMENT '是否敏感数据';

```

#### sql脚本4.1-7.15
```sql

-- 日期:4.1-7.15
-- 下面的已发布uat
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_instruction_execution_log` 
ADD COLUMN `from_id` int NULL COMMENT '来源id' AFTER `actual_requirement`,
ADD COLUMN `to_id` int NULL COMMENT '下一步id' AFTER `from_id`;

ALTER TABLE `db_syzh_eagle_web`.`t_file_relation` 
CHANGE COLUMN `DELETEd` `deleted` int NOT NULL DEFAULT 0 COMMENT '1=已删除' AFTER `create_by`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
ADD COLUMN `event_id` varchar(32) NULL COMMENT '事件id' AFTER `publish_date`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_instruction` 
ADD COLUMN `is_permanent_show` tinyint NULL COMMENT '是否chuang' AFTER `sort`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement_instruction_relation` 
ADD COLUMN `create_post` varchar(500) NULL COMMENT '创建人下发指令时申领的所有岗位';

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
ADD COLUMN `publish_date` datetime NULL COMMENT '文章发布时间' AFTER `human_opinion`;

ALTER TABLE `db_syzh_eagle_web`.`t_article_read_record` 
MODIFY COLUMN `dept_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL COMMENT '单位id';
ALTER TABLE `db_syzh_eagle_web`.`t_article_read_record` 
ADD COLUMN `type` int NULL DEFAULT 0 COMMENT '0已阅，24已报送';

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_info` 
ADD COLUMN `grading_type` varchar(20) NULL COMMENT '分级',
ADD COLUMN `classify_type` varchar(20) NULL COMMENT '分类',
ADD COLUMN `incident_time` datetime NULL COMMENT '事件发生时间',
ADD COLUMN `region_name` varchar(100) NULL COMMENT '涉及地区名称',
ADD COLUMN `collector_id` varchar(32) NULL COMMENT '采集人ID',
ADD COLUMN `collector_name` varchar(50) NULL COMMENT '采集人姓名',
ADD COLUMN `collect_dept_id` varchar(32) NULL COMMENT '采集单位ID',
ADD COLUMN `collect_dept_name` varchar(50) NULL COMMENT '采集单位名称',
ADD COLUMN `notify_districts` varchar(500) NULL COMMENT '通报区县(多个区县用逗号分隔)',
ADD COLUMN `is_important` tinyint NULL DEFAULT 0 COMMENT '是否重要。0否，1是',
ADD COLUMN `source_name` varchar(100) NULL COMMENT '信息来源',
ADD COLUMN `author_name` varchar(100) NULL COMMENT '作者',
ADD COLUMN `type` tinyint NULL COMMENT '上报类型 0区县上报 1手工上报',
ADD COLUMN `report_id` varchar(50) NULL COMMENT '上报id',
ADD COLUMN `file_id` varchar(36) NULL COMMENT '文件id',
ADD COLUMN `url` varchar(500) NULL COMMENT 'url';


ALTER TABLE `db_syzh_eagle_web`.`t_article_extension` 
ADD COLUMN `ai_compilation` varchar(3000) NULL COMMENT 'ai编报';


ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
ADD COLUMN `is_track` tinyint NULL DEFAULT 0 COMMENT '是否加入态势追踪 0否 1是' AFTER `sandworm_image_job_id`;


ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
MODIFY COLUMN `round_num` int NULL DEFAULT 0 COMMENT '第几次研判' AFTER `content`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_info` 
ADD COLUMN `sentiment_type` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '舆情类型' AFTER `url`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_instruction_execution_log` 
MODIFY COLUMN `execute_post` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '操作岗位' AFTER `execute_dept`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_info` 
MODIFY COLUMN `level_ai` int NULL DEFAULT NULL COMMENT '识别等级（如：一级、二级）' AFTER `summary_ai`;

ALTER TABLE `db_syzh_eagle_web`.`t_dynamic_tracking_src` 
ADD COLUMN `spread_task_id` varchar(32) NULL COMMENT '文章分析任务id' AFTER `update_time`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
MODIFY COLUMN `level` varchar(20) NULL DEFAULT NULL COMMENT '等级' AFTER `article_id`;
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_info` 
MODIFY COLUMN `level_ai` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '识别等级（如：一级、二级）' AFTER `summary_ai`;
ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_task_info` 
MODIFY COLUMN `task_level` varchar(20) NULL DEFAULT NULL COMMENT '等级，0=特级，1=一级，2=二级，3=三级' AFTER `task_num`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_report_info` 
ADD COLUMN `event_id` varchar(32) NULL COMMENT '事件id' AFTER `sentiment_type`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_judgement` 
MODIFY COLUMN `data_backup` varchar(5000) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '文章数据备份' AFTER `is_judgement`,
ADD COLUMN `human_opinion` varchar(2000) NULL COMMENT '人工意见' AFTER `is_track`;

ALTER TABLE `db_syzh_eagle_web`.`t_chongqing2_instruction` 
ADD COLUMN `sort` int NULL COMMENT '排序' AFTER `is_briefing`;
---- 上面已提交uat

```


