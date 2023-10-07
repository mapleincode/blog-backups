---
title: RabbitMQ 重启后无法启动问题
tags: [rabbitmq, mq]
date: 2023-02-28 09:55:53

---

## 问题

```
BOOT FAILED
===========

Error description:
    init:do_boot/3 line 817
    init:start_em/1 line 1109
    rabbit:start_it/1 line 474
    rabbit:broker_start/1 line 350
    rabbit:start_loaded_apps/2 line 600
    app_utils:manage_applications/6 line 126
    lists:foldl/3 line 1263
    rabbit:'-handle_app_error/1-fun-0-'/3 line 723
throw:{could_not_start,ra,
       {ra,
        {{shutdown,
          {failed_to_start_child,ra_system_sup,
           {shutdown,
            {failed_to_start_child,ra_log_sup,
             {shutdown,
              {failed_to_start_child,ra_log_wal_sup,
               {shutdown,
                {failed_to_start_child,ra_log_wal,
                 {{case_clause,{ok,<<0,0,0,0,0>>}},
                  [{ra_log_wal,open_existing,1,
                    [{file,"src/ra_log_wal.erl"},{line,646}]},
                   {ra_log_wal,'-recover_wal/2-lc$^0/1-0-',1,
                    [{file,"src/ra_log_wal.erl"},{line,265}]},
                   {ra_log_wal,recover_wal,2,
                    [{file,"src/ra_log_wal.erl"},{line,268}]},
                   {ra_log_wal,init,1,
                    [{file,"src/ra_log_wal.erl"},{line,214}]},
                   {gen_batch_server,init_it,6,
                    [{file,"src/gen_batch_server.erl"},{line,133}]},
                   {proc_lib,init_p_do_apply,3,
                    [{file,"proc_lib.erl"},{line,249}]}]}}}}}}}}},
         {ra_app,start,[normal,[]]}}}}
Log file(s) (may contain more information):
   C:/Users/AIMLExpert/AppData/Roaming/RabbitMQ/log/rabbit@DESKTOP-N0Q3S7C.log
   C:/Users/AIMLExpert/AppData/Roaming/RabbitMQ/log/rabbit@DESKTOP-N0Q3S7C_upgrade.log

{"init terminating in do_boot",{could_not_start,ra,{ra,{{shutdown,{failed_to_start_child,ra_system_sup,{shutdown,{failed_to_start_child,ra_log_sup,{shutdown,{failed_to_start_child,ra_log_wal_sup,{shutdown,{failed_to_start_child,ra_log_wal,{{case_clause,{ok,<<0,0,0,0,0>>}},[{ra_log_wal,open_existing,1,[{file,"src/ra_log_wal.erl"},{line,646}]},{ra_log_wal,'-recover_wal/2-lc$^0/1-0-',1,[{file,"src/ra_log_wal.erl"},{line,265}]},{ra_log_wal,recover_wal,2,[{file,"src/ra_log_wal.erl"},{line,268}]},{ra_log_wal,init,1,[{file,"src/ra_log_wal.erl"},{line,214}]},{gen_batch_server,init_it,6,[{file,"src/gen_batch_server.erl"},{line,133}]},{proc_lib,init_p_do_apply,3,[{file,"proc_lib.erl"},{line,249}]}]}}}}}}}}},{ra_app,start,[normal,[]]}}}}}
init terminating in do_boot ({could_not_start,ra,{ra,{{shutdown,{_}},{ra_app,start,[_]}}}})

Crash dump is being written to: C:\Users\AIMLExpert\AppData\Roaming\RabbitMQ\log\erl_crash.dump...done
```



## 解决办法

```
I had the same issue on Arch Linux due to a crash dump. It was saying in the logs that it had problem with reverting a WAL file that was 0 bytes in size. After removing the WAL file the service started. Locate the WAL in /var/lib/rabbitmq/mnesia with find /var/lib/rabbitmq/ -name "*.wal" and remove it. Restart the service afterwards.
```





## 参考

- https://stackoverflow.com/questions/60488824/having-a-hard-time-getting-rabbitmq-server-started-and-wonder-why-keep-getting-t