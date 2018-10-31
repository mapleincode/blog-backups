---
title: Expect 使用小记
date: 2018-08-08 15:05:26
tags: Expect
---
# Expect 使用小记

Expect 是 unix 系统中用来进行自动化控制和测试的软件工具，由 DonLibes 制作，作为 Tcl 脚本语言的一个扩展，应用在交互式软件中如 telnet，ftp，Passwd，fsck，rlogin，tip，ssh 等等。该工具利用Unix 伪终端包装其子进程，允许任意程序通过终端接入进行自动化控制。



## 基本指令

- spawn

  ```bash
  spawn ssh $user@$host -p $port
  ```

- expect & send

  ```bash
  expect "password:"
  send "$password\r"
  ```

  ```bash
  expect {
      "yes/no" {
          send "yes\r"
      }
      "password:" {
          send "$password\r"
      }
      "default" {
          exit 1
      }
  }
  ```

- set

  设置变量属性

- puts

  ```
  puts "hello world"
  puts stdout "hello"
  puts stderr "hello"
  ```

- interact

  执行完成后保持交互状态，并把控制权交给控制台。

- expect eof

  执行完退出 expect 脚本

- exit 0

- $expect_out(buffer) expect 匹配的字符串

- $expect_out(N, string)  expect 匹配的多个字符串。(N = 0~9)

- timeout expect 默认超时时间  -1 为无限。默认的超时时间是 10s。

### expect 的选项

| 选项     | 说明                                          |
| -------- | --------------------------------------------- |
| -gl      | 保护以"-"开始的模式不被认为是选项             |
| -re      | 正则表达式，在单项前面指定                    |
| -ex      | 匹配确切的字串，不对*、^和$等特殊字符进行翻译 |
| -nocase  | 不区分大小写                                  |
| -timeout | 设置当前 expect 的超时时间                    |



## 用 expect 部署一个 docker 项目

```bash
#!/usr/bin/expect

if {$argc < 3} {
puts stderr "$argv0 err params\n"
exit 1
}

set folder [lindex $argv 0]
set branch [lindex $argv 1]
set port [lindex $argv 2]

puts $folder
puts $branch
puts $port


set repo "ssh://git@git.mapleincode.com/mapleincode/demo.git"

set passwd "mapleintest"
set user "maple"
set timeout -1

spawn ssh $user@192.168.2.141

expect "password:"
send "$passwd\r"

expect "Last login"
send "git clone $repo $folder\r"

expect {
    "done." {
        send "cd $folder\r"
    }
    "already exists" {
        puts stderr "the folder has been exists!"
        exit 1
    }
}

send "git checkout $branch\r"

expect "Switched to"

send "echo \"COFFEE_PORT=$port\" > .env\r"

send "docker-compose build\r"

expect "Successfully tagged"

send "docker-compose up -d\r"

expect "Creating"

send "docker-compose logs -f --tail=100\r"

interact

# expect eof
# exit 0
```


