---
title: "Linux 文件管理实验"
date: 2026-10-20
draft: false
categories: ["Linux课程"]
tags: ["文件管理", "Vim", "grep", "sed", "awk"]
---

## 实验目标

练习 Linux 文件与目录的创建、复制、移动、链接，掌握 Vim 的插入、查找、替换，掌握 grep、sed、awk 与管道的组合使用。

## 文件与目录操作

```bash
cd ~
mkdir -p file_experiment/{dir1,dir2,dir3}
cd file_experiment
touch file1.txt file2.txt file3.log
echo "Hello Linux" > file1.txt
cp file1.txt dir1/
cp -r dir1 dir1_backup
mv *.txt dir1/
ln dir1/file1.txt hard_link_file1
ln -s dir1/file1.txt soft_link_file1
ls -li
```

`ls -li` 中硬链接与源文件的 inode 相同、链接计数为 2；软链接是独立 inode，类型显示为 `symbolic link`。这说明硬链接共享数据块，软链接保存的是目标路径。

![文件与链接操作](../../images/exp2.png)

## Vim 编辑

新建 `student_scores.txt`，录入 5 行数据后保存；再次打开，使用 `:set nu` 显示行号，使用 `/geology` 查找，使用替换命令修改性别字段：

```vim
:set nu
/geology
:%s/\<male\>/Man/g
```

这里有一个容易踩的坑：如果直接执行 `:%s/male/Man/g`，`female` 会被替换成 `feMan`，数据被破坏。使用单词边界 `\<male\>` 后只匹配完整的 male，替换结果为 4 处，female 保持不变。

## grep、sed、awk

```bash
grep ComputerScience student_scores.txt
grep -i "geo" student_scores.txt
grep -c Geology student_scores.txt
grep -rn "ssh" /etc/ 2>/dev/null | head -15
sed 's/ComputerScience/CS/g' student_scores.txt
sed -i.bak 's/ComputerScience/CS/g' student_scores.txt
awk '{sum+=$2} END {printf "总分: %.2f, 平均分: %.2f\n", sum, sum/NR}' student_scores.txt
awk '{print $4}' student_scores.txt | sort | uniq -c
```

sed 的 `-i` 会直接修改文件，`-i.bak` 会先备份；建议先不加 `-i` 预览结果，确认无误后再原地修改。awk 适合按字段统计，管道则把筛选、排序、计数组合起来。

## 遇到的问题

1. `:%s/male/Man/g` 误伤 female，改用 `\<male\>` 单词边界。
2. sed 原地修改前忘记备份，之后统一使用 `-i.bak`。
3. `grep -r` 搜索 /etc 时出现大量 Permission denied，使用 `2>/dev/null` 屏蔽错误输出。

## 小结

文件管理命令要配合 `ls -li`、`file` 等命令验证结果；Vim 的模式切换和正则边界需要多练习；grep/sed/awk 各有侧重，管道能把小工具组合成完整的数据处理流程。
