> **Usage:  grep  [OPTION]...  PATTERN  [FILE]...**
>
> 如果搜索的文件名不指定，就默认在标准输入里查找。也可以显式的指定在标准输入里查找，用 - 作为文件名。

常用的OPTION：

- -i 忽略大小写
- -w 全词匹配
- -v 反向查找
- -r 递归查找
- -n 打印匹配行的行号
- -l 只打印匹配到的文件的文件名
- -c 打印文件里匹配行的数量
- -C num 匹配行的上下num行都打印出来
- -B num 匹配行的上num行也打印出来
- -A num 匹配行的下num行也打印出来



常用命令示例：

```shell
grep -rna 'scan_vol' . --include="*.[ch]"
```

在当前目录下的所有C文件和H文件中，查找字符串 'scan_vol'，其中：使用 -a 参数是因为有些文本文件会被grep识别为二进制文件。

注意：这个命令在bash和zsh中都能正确执行，但是命令`grep -rna 'scan_vol' ./* --include=*.[ch]` 只能在bash中正确执行。

