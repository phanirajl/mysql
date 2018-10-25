
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

[mysqld]

#=================================================#
# General
#=================================================#

datadir = /var/lib/mysql
socket = /var/lib/mysql/mysql.sock
port = 3306
max_allowed_packet = 128M
max_connections = 5000
max_user_connections = 5000
thread_cache_size = 5000
query_cache_type = 0
query_cache_size = 0
query_cache_limit = 0
tmp_table_size = 64M
max_heap_table_size = 64M
open_files_limit = 65535
back_log = 900
table_definition_cache = 1400
table_open_cache = 3000
sql_mode = NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
table_open_cache_instances = 16

#=================================================#
# InnoDB
#=================================================#

default_storage_engine = InnoDB
innodb_buffer_pool_size = 85G
innodb_log_file_size = 1000M
innodb_file_per_table = 1
innodb_stats_persistent = ON
innodb_stats_auto_recalc = ON
innodb_stats_on_metadata = OFF
innodb_open_files = 3000
innodb_flush_log_at_trx_commit = 0
innodb_log_buffer_size = 128M
innodb_buffer_pool_instance = 16
innodb_thread_concurrency = 16
innodb_buffer_pool_dump_at_shutdown = 1
innodb_buffer_pool_load_at_startup = 1
innodb_checksum_algorithm = crc32
innodb_write_io_threads = 16
innodb_read_io_threads = 16
innodb_strict_mode = ON
innodb_file_format_check = 1
innodb_lock_wait_timeout = 120
innodb_open_files = 3000
innodb_flush_neighbors = 0
innodb_io_capacity = 6000
innodb_io_capacity_max = 9000
innodb_flush_method = O_DIRECT
innodb_adaptive_hash_index = 1
innodb_monitor_enable = all

#=================================================#
# MyISAM
#=================================================#

key_buffer_size = 128M
myisam_recover_options = 'BACKUP,FORCE'

#=================================================#
# Error log
#=================================================#

log_error = /var/log/mysqld.log

#=================================================#
# Slow query log
#=================================================#

slow_query_log = 1
long_query_time = 1
log_queries_not_using_indexes = 1

#=================================================#
# General query log
#=================================================#

general_log_file = /var/log/mysql-general.log
general_log = 0

#=================================================#
# Replication
#=================================================#

server_id = 5996519199
log_bin = /var/lib/mysql/mysql-binlog
sync_binlog = 1
symbolic_links = 0
max_binlog_size = 1000M
expire_logs_days = 7
binlog_format = MIXED
binlog_cache_size = 128M
max_binlog_size = 1000M
log_slave_updates = 1
read_only = 0
master_info_repository = TABLE
relay_log_info_repository = TABLE
relay_log_recovery = 1

#=================================================#
# Other
#=================================================#

[mysqld_safe]

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
