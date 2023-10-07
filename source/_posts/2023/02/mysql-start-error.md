---
title: "MySQL 启动 error [InnoDB: Missing MLOG_CHECKPOINT]"
date: 2023-02-28 09:59:20
tags: [mysql]
---





```
2023-02-28  9:38:50 0 [ERROR] InnoDB: Missing MLOG_CHECKPOINT at 6701716404 between the checkpoint 6701714063 and the end 6701716404.
2023-02-28  9:38:50 0 [ERROR] InnoDB: Plugin initialization aborted with error Generic error
2023-02-28  9:38:51 0 [Note] InnoDB: Starting shutdown...
2023-02-28  9:38:52 0 [ERROR] Plugin 'InnoDB' init function returned error.
2023-02-28  9:38:52 0 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
2023-02-28  9:38:52 0 [Note] Plugin 'FEEDBACK' is disabled.
2023-02-28  9:38:52 0 [ERROR] Unknown/unsupported storage engine: innodb
2023-02-28  9:38:52 0 [ERROR] Aborting
```





Try the following:

1. Set `innodb_log_checksums = ON` on master and slave.
2. Remove `rm /var/lib/mysql/ib_logfile*`.
3. Restart.