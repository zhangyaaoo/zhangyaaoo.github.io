[35 Practical Examples of Linux Find Command](https://www.tecmint.com/35-practical-examples-of-linux-find-command/)



- -type f
- -type d
- -name xxx
- -iname xxx



我们可以在/usr/local/bin目录下新建名为 `findf` 的脚本，其中内容为：

```shell
find $2 -type f -name $1
```

相当于我们自己定义了一个命令 `grepch` ，接下来我们就可以使用这个命令了，如：

```shell
grepch 'CHIP_ID' # 或者
grepch "CHIP_ID_*"
```


