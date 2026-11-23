---
title: "用 Bash 计算 1+2+…+n：one2n 脚本"
date: 2026-10-20
draft: false
categories: ["Linux课程", "平时作业"]
tags: ["Bash", "脚本", "one2n"]
---

## 题目

编写一个名为 `one2n` 的 Bash 脚本，计算 `1+2+…+n`，要求：不带参数时提示用法，参数必须是大于 0 的整数，最后输出计算结果。

## 脚本内容

```bash
#!/bin/bash
# one2n：计算 1+2+...+n
if [ $# -ne 1 ]; then
    echo "用法: $0 <n>"
    echo "示例: $0 5"
    exit 1
fi

n=$1

if ! [[ "$n" =~ ^[0-9]+$ ]] || [ "$n" -le 0 ]; then
    echo "错误: n 必须是大于 0 的整数"
    exit 1
fi

sum=0
for ((i=1; i<=n; i++)); do
    sum=$((sum + i))
done

echo "1+2+...+$n = $sum"
```

## 运行结果

```bash
chmod +x one2n
./one2n 100
# 1+2+...+100 = 5050
./one2n
# 用法: ./one2n <n>
# 示例: ./one2n 5
./one2n abc
# 错误: n 必须是大于 0 的整数
```

![one2n 运行结果](../../images/one2n.png)

## 加入 PATH

将脚本放到 `~/bin`，在 `~/.bashrc` 末尾加入：

```bash
export PATH=$PATH:$HOME/bin
```

执行 `source ~/.bashrc` 或重新登录后，直接运行 `one2n 50` 输出 `1+2+...+50 = 1275`。

## 小结

`$#` 用于检查参数个数，`$1` 取第一个参数，`[[ ... =~ ... ]]` 做正则校验，`for ((...))` 是 C 风格的循环，`$((...))` 做算术展开。把个人脚本加入 PATH 后，使用更方便，但不要将当前目录 `.` 放入 PATH，避免执行到同名恶意程序。
