**查看慢日志**
- my.cnf定义slow_query_log_file参数
- 通过mysqldumpslow或pt-query-digest工具对慢查询日志进行分析汇总

**优化慢查询**
- 查看SQL执行计划：explain sql;
- 查看表索引：show index from tb_name;
- 查看表结构：show create table tb_name;
- 通过profiling查看SQL耗时主要花在哪里
- 通过Optimizer Trace观看SQL的执行过程，观察SQL执行计划选取的依据

