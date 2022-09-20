#### 一、mysql 基础库

```
                        
engine_cost               
event                     
func                      
               
gtid_executed             
help_category             
help_keyword              
help_relation             
help_topic                
innodb_index_stats        
innodb_table_stats        
ndb_binlog_index          
                    
proc                      
procs_priv                
proxies_priv              
server_cost               
servers                   
slave_master_info         
slave_relay_log_info      
slave_worker_info         
                  
               
time_zone                 
time_zone_leap_second     
time_zone_name            
time_zone_transition      
time_zone_transition_type


plugin
插件表，用户自己安装的密码插件啥的

db tables_priv columns_priv
库 表 列的权限表
user
用户表

general_log slow_log
通用日志和慢查询表
```

#### 二、information_schema 元数据库

```
CHARACTER_SETS                        
COLLATIONS                            
COLLATION_CHARACTER_SET_APPLICABILITY 
COLUMNS                               
COLUMN_PRIVILEGES                     
ENGINES                               
EVENTS                                
FILES                                 
GLOBAL_STATUS                         
GLOBAL_VARIABLES                      
KEY_COLUMN_USAGE                      
OPTIMIZER_TRACE                       
PARAMETERS                            
PARTITIONS                            
PLUGINS                               
PROCESSLIST                           
PROFILING                             
REFERENTIAL_CONSTRAINTS               
ROUTINES                              
SCHEMATA                              
SCHEMA_PRIVILEGES                     
SESSION_STATUS                        
SESSION_VARIABLES                     
STATISTICS                            
TABLES                                
TABLESPACES                           
TABLE_CONSTRAINTS                     
TABLE_PRIVILEGES                      
TRIGGERS                              
USER_PRIVILEGES                       
VIEWS                                 
INNODB_LOCKS                          
INNODB_TRX                            
INNODB_SYS_DATAFILES                  
INNODB_FT_CONFIG                      
INNODB_SYS_VIRTUAL                    
INNODB_CMP                            
INNODB_FT_BEING_DELETED               
INNODB_CMP_RESET                      
INNODB_CMP_PER_INDEX                  
INNODB_CMPMEM_RESET                   
INNODB_FT_DELETED                     
INNODB_BUFFER_PAGE_LRU                
INNODB_LOCK_WAITS                     
INNODB_TEMP_TABLE_INFO                
INNODB_SYS_INDEXES                    
INNODB_SYS_TABLES                     
INNODB_SYS_FIELDS                     
INNODB_CMP_PER_INDEX_RESET            
INNODB_BUFFER_PAGE                    
INNODB_FT_DEFAULT_STOPWORD            
INNODB_FT_INDEX_TABLE                 
INNODB_FT_INDEX_CACHE                 
INNODB_SYS_TABLESPACES                
INNODB_METRICS                        
INNODB_SYS_FOREIGN_COLS               
INNODB_CMPMEM                         
INNODB_BUFFER_POOL_STATS              
INNODB_SYS_COLUMNS                    
INNODB_SYS_FOREIGN                    
INNODB_SYS_TABLESTATS 
```

#### 三、sys（performance_schema的视图库）

```
host_summary                                  
host_summary_by_file_io                       
host_summary_by_file_io_type                  
host_summary_by_stages                        
host_summary_by_statement_latency             
host_summary_by_statement_type                
innodb_buffer_stats_by_schema                 
innodb_buffer_stats_by_table                  
innodb_lock_waits                             
io_by_thread_by_latency                       
io_global_by_file_by_bytes                    
io_global_by_file_by_latency                  
io_global_by_wait_by_bytes                    
io_global_by_wait_by_latency                  
latest_file_io                                
memory_by_host_by_current_bytes               
memory_by_thread_by_current_bytes             
memory_by_user_by_current_bytes               
memory_global_by_current_bytes                
memory_global_total                           
metrics                                       
processlist                                   
ps_check_lost_instrumentation                 
schema_auto_increment_columns                 
schema_index_statistics                       
schema_object_overview                        
schema_redundant_indexes                      
schema_table_lock_waits                       
schema_table_statistics                       
schema_table_statistics_with_buffer           
schema_tables_with_full_table_scans           
schema_unused_indexes                         
session                                       
session_ssl_status                            
statement_analysis                            
statements_with_errors_or_warnings            
statements_with_full_table_scans              
statements_with_runtimes_in_95th_percentile   
statements_with_sorting                       
statements_with_temp_tables                   
sys_config                                    
user_summary                                  
user_summary_by_file_io                       
user_summary_by_file_io_type                  
user_summary_by_stages                        
user_summary_by_statement_latency             
user_summary_by_statement_type                
version                                       
wait_classes_global_by_avg_latency            
wait_classes_global_by_latency                
waits_by_host_by_latency                      
waits_by_user_by_latency                      
waits_global_by_latency                       
x$host_summary                                
x$host_summary_by_file_io                     
x$host_summary_by_file_io_type                
x$host_summary_by_stages                      
x$host_summary_by_statement_latency           
x$host_summary_by_statement_type              
x$innodb_buffer_stats_by_schema               
x$innodb_buffer_stats_by_table                
x$innodb_lock_waits                           
x$io_by_thread_by_latency                     
x$io_global_by_file_by_bytes                  
x$io_global_by_file_by_latency                
x$io_global_by_wait_by_bytes                  
x$io_global_by_wait_by_latency                
x$latest_file_io                              
x$memory_by_host_by_current_bytes             
x$memory_by_thread_by_current_bytes           
x$memory_by_user_by_current_bytes             
x$memory_global_by_current_bytes              
x$memory_global_total                         
x$processlist                                 
x$ps_digest_95th_percentile_by_avg_us         
x$ps_digest_avg_latency_distribution          
x$ps_schema_table_statistics_io               
x$schema_flattened_keys                       
x$schema_index_statistics                     
x$schema_table_lock_waits                     
x$schema_table_statistics                     
x$schema_table_statistics_with_buffer         
x$schema_tables_with_full_table_scans         
x$session                                     
x$statement_analysis                          
x$statements_with_errors_or_warnings          
x$statements_with_full_table_scans            
x$statements_with_runtimes_in_95th_percentile 
x$statements_with_sorting                     
x$statements_with_temp_tables                 
x$user_summary                                
x$user_summary_by_file_io                     
x$user_summary_by_file_io_type                
x$user_summary_by_stages                      
x$user_summary_by_statement_latency           
x$user_summary_by_statement_type              
x$wait_classes_global_by_avg_latency          
x$wait_classes_global_by_latency              
x$waits_by_host_by_latency                    
x$waits_by_user_by_latency                    
x$waits_global_by_latency
```

#### 四、performance_schema 比较专业

 ```

 ```









