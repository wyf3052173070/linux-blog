---
title: "Shell 脚本与 Makefile 实践"
date: 2026-10-20
draft: false
categories: ["Linux课程"]
tags: ["Shell", "Bash", "Makefile"]
---

## 实验目标

编写年龄分房脚本与备份脚本，使用 `bash -x` 调试，使用 case 改写多分支，最后用 Makefile 的三个版本完成 C 程序的编译、运行与清理。

## 年龄分房脚本

```bash
#!/bin/bash
clear
read -p "Input your age！ " age
if ! [[ "$age" =~ ^[0-9]+$ ]]; then
    echo "错误：请输入有效的数字！"
    exit 1
fi
if [ "$age" -ge 20 ] && [ "$age" -le 29 ]; then
    echo "Please go to room 101！"
elif [ "$age" -ge 30 ] && [ "$age" -le 39 ]; then
    echo "Please go to room 201！"
elif [ "$age" -ge 40 ] && [ "$age" -le 49 ]; then
    echo "Please go to room 301！"
elif [ "$age" -ge 50 ] && [ "$age" -le 59 ]; then
    echo "Please go to room 401！"
elif [ "$age" -ge 60 ] && [ "$age" -le 69 ]; then
    echo "Please go to room 501！"
else
    echo "Please wait at the door！"
fi
```

![年龄脚本运行结果](../../images/exp3.png)

赋权后运行 `./age_room.sh`，分别输入 25、35、45、55，输出 101、201、301、401；输入 18、70 输出 door，输入 abc 触发数字校验。

## 备份脚本

```bash
LOG_DIR="./backup_logs"
mkdir -p "$LOG_DIR"
TS=$(date +"%Y-%m-%d-%H-%M-%S")
LOG_FILE="$LOG_DIR/$TS.log"
echo "Backup Process Begins" | tee -a "$LOG_FILE"
for item in "$@"; do
    if [ -e "$item" ]; then
        cp -r "$item" "${item}.bak.$TS"
        echo "$TS SUCCESS in backup file/directory($item)" | tee -a "$LOG_FILE"
    else
        echo "$TS FAILED: $item not found" | tee -a "$LOG_FILE"
    fi
done
echo "Backup Process Ends" | tee -a "$LOG_FILE"
```

`tee -a` 同时把内容写到屏幕和日志；`for item in "$@"` 遍历所有参数，`-e` 判断文件或目录是否存在。

## case 改写

```bash
case $age in
    [2][0-9]) echo "Please go to room 101！" ;;
    [3][0-9]) echo "Please go to room 201！" ;;
    [4][0-9]) echo "Please go to room 301！" ;;
    [5][0-9]) echo "Please go to room 401！" ;;
    [6][0-9]) echo "Please go to room 501！" ;;
    *)        echo "Please wait at the door！" ;;
esac
```

## Makefile 三个版本

版本 1 使用显式规则，版本 2 引入变量、自动变量和模式规则，版本 3 增加 `run`、`distclean`、`depends`、`help` 等目标：

```make
CC = gcc
CFLAGS = -Wall -g -O2
LDFLAGS = -lm
TARGET = hello
SRCS = main.c hello.c
OBJS = $(SRCS:.c=.o)
DEPS = hello.h
VERSION = 1.0

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^ $(LDFLAGS)

%.o: %.c $(DEPS)
	$(CC) $(CFLAGS) -c $< -o $@

run: $(TARGET)
	./$(TARGET)

clean:
	rm -f $(TARGET) $(OBJS)

distclean: clean
	rm -f *.bak.*
	rm -rf backup_logs/

.PHONY: all run clean distclean depends help
```

## 遇到的问题

1. 脚本第一次运行时没有执行权限，提示 Permission denied，使用 `chmod +x` 解决。
2. Makefile 的缩进必须是 Tab，使用空格会报 `missing separator`。
3. `bash -x` 输出较长，先重定向到文件再查看，或者只跟踪关键段落。

## 小结

Shell 脚本要处理参数个数、类型校验和错误分支；`tee` 适合同时保留屏幕输出和日志；Makefile 的变量、自动变量和伪目标能显著减少重复，提高多文件项目的构建效率。
