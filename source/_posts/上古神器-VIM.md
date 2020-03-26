---
title: 上古神器 VIM
date: 2020-1-12 10:24:00
tags: VIM
categories: Tools
---

> [A Byte Of VIM](http://vim.swaroopch.com/)
> [按键功能总览](http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html)
> [参考博客一](https://www.cnblogs.com/yangjig/p/6014198.html)

> 不可错过的VIM视频教程：[一](https://www.bilibili.com/video/av55498503)、[二](https://www.bilibili.com/video/av55664166)、[三](https://www.bilibili.com/video/av56020134)、[四](https://www.bilibili.com/video/av67091857)

==约定：==

*命令  Y  （大写 Y）  即输入：Shift + y*

*命令 c-v  即输入：Ctrl + v* 

# VIM的各种模式

- 普通模式/命令模式（**Normal Mode**）
- 插入模式（**Insert Mode**）
- 覆盖模式（**Replace Mode**）
- 可视模式（**Visual Mode**）
- 可视行模式（**Visual-Line Mode**）
- 可视块模式（**Visual-Block Mode**）

### 各种模式之间的切换

- 其他任何模式下都可以使用`Esc` 切换到 **普通模式（Normal Mode）**
- 普通模式下：输入`R` 切换到 **覆盖模式（Replace Mode）**  （`r` 替换单个字符，例如：`ra`，将当前光标处的字符替换为a）
- 普通模式下：输入`v`, `V`，`c-v` 切换到 **可视模式（Visual Mode）**
  - `v` 以**字符**为单位选择, 选中光标所在的字符, （结合**上下左右**可以选取更多）
  - `V` 以**行**为单位选择, 选中光标所在行, （结合 **上下** 可以选取多行）
  - `c-v` ，结合**上下左右**，可以选中一个矩阵块。
- 普通模式下，切换到 **插入模式（Insert Mode）** 的方法：
  - `a` 在当前**光标之后**插入内容
  - `A` 在当前**行尾**插入内容
  - `i` 在当前**光标之前**插入内容
  - `I` 在当前**行首**插入内容
  - `o` **向下**插入新的一行，并进入插入模式
  - `O` **向上**插入新的一行，并进入插入模式
  - `cc` （双击c）删除当前行，并进入插入模式
  - `C` **删除**从**当前光标处**到**行尾**的所有字符，并进入插入模式
  - `s` 删除当前光标处的字符，并进入插入模式
  - `S` 删除当前行，并进入插入模式 （感觉和`cc`没有区别）


```markdown
+-----------+  i,I,a,A,o,O,cc,C,s,S +-----------+
| Normal    +---------->------------+ Insert    |
| mode      |                       | mode      |
|           +----------<------------+           |
+-+---+-----+        <Esc>          +-----------+
    |  |    \  \   
    |  |     \  \
    |  |      \  \
    |  |       \  \
v,  |  |        \  v  R, r
V   V  ^ <Esc>   \  \
c-v |  |    <Esc> ^  \
    |  |           \  \
    |  |            \  \
    |  |             \  \
+---+---+----+      +---+---+----+
| Visual     |      | Replace    |
| mode       |      | mode       |
+------------+      +------------+
```

在**普通模式**下，我们输入的字符被当做命令去执行。在**插入模式**和**覆盖模式**下，我们输入的字符就是我们编辑的内容。在**可视模式**下，我们可以选中内容，选中后，可以继续输入命令，对选中的内容进行操作。

### VIM 按键功能总览图
![vi-vim-cheat-sheet](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/vi-vim-cheat-sheet.gif)

注：注意区分各个按键的颜色。Operator和Motion的颜色不一样。


# {Count}Operator{Count}Motion

### 首先，看看我们有哪些Operators

下面列出一些常用的，更多内容参考自[这里](http://vimdoc.sourceforge.net/htmldoc/motion.html#operator)。

| Operator | Function |
| -------- | -------- |
| d        | delete   |
| c        | change   |
| y        | yank     |
| =        | indent   |

### 其次，看看我们有哪些Motions

下面列出一些常用的，上（k）下（j）左（h）右（l），下一个单词（w/W），上一个单词（b/B）。还有很多各式各样的Motion，参考[这里](http://vimdoc.sourceforge.net/htmldoc/motion.html)。

```markdown
                     gg 
                     ? 
                    C-b 
                     H 
                     { 
                     k 
^  F  T  (  ge b  h     l  w  e  )  t  f  $ 
                     j 
                     } 
                     L 
                    C-f 
                     / 
                     G 
```

| Motion  | Function                                                |
| ------- | ------------------------------------------------------- |
| $       | end of line                                             |
| 0       | first character of line                                 |
| ^       | first non-blank character of the line                   |
| f{char} | 当前行，向右查找字符，光标跳到该字符。（inclusive）     |
| F{char} | 当前行，向左查找字符，光标跳到该字符。（exclusive）     |
| t{char} | 当前行，向右查找字符，光标跳到该字符之前。（inclusive） |
| T{char} | 当前行，向左查找字符，光标跳到该字符之后。（exclusive） |
| {number}gg | 光标跳到第number行，如果不输入number，就是跳到首行 |
| G          | 光标跳到文件尾行                                   |
| H          | 跳转到当前屏幕的顶行                               |
| L          | 跳转到当前屏幕的底行                               |
| w          | 下一个单词的开始                                   |
| e          | 下一个单词的结尾                                   |
| b          | 上一个单词的开始                                   |
| ge         | 上一个单词的结尾                                   |

注：

1. 光标在字符间移动，有两种模式：inclusive 和 exclusive 两种，区别是：是否包含最后一个字符。详见[这里](http://vimdoc.sourceforge.net/htmldoc/motion.html#inclusive)


### Operator 和 Motion 的组合：{Count}Operator{Count}Motion

> The motion commands can be used after an operator command, to have the command operate on the text that was moved over.  That is the text between the cursor position before and after the motion.  Operators are generally used to delete or change text. If the motion includes a count and the operator also had a count before it, the two counts are multiplied. For example: "2d3w" deletes six words.
>
> 一个操作后面跟上一个动作，达到的效果是：**在动作前后覆盖的内容上执行那个操作**。

这是一个非常强大的功能，可以自己尝试各种组合，打开属于自己的世界。
举一些例子：

1. `d2j` **类似的命令我就经常使用到** ，2j 光标会从当前行向下移动两行（光标扫过三行），d 表示删除。所以该命令会删除三行内容（包括当前行）。
2. `y2w` 2w 光标会向后跳两个单词，y 表示复制。所以这个命令会复制从当前光标开始出的两个单词。
3. `yfa` fa 会找到字符a，并将光标跳到该字符处。所以这个命令会从当前光标处一直复制到字符a。注意：实际我发现：`f` 只会在当前行查找。
4. `gUw` gU 是一个operator，表示将字符换成大写，w 移动一个单词。所以这个命令会将当前单词换成大写（当然也是从当前光标处开始）。
5. `cw` 删除当前单词（当然也是从当前光标处开始），并进入插入模式。
6. `ci)` **这个我就经常使用到**， 删除 （） 里的内容并进入插入模式，类似的命令还有`ci"` ：删除 双引号之间的内容并进入插入模式。需要说明的是：删除不是从光标处开始的，而是将（）内，或者 “” 内的内容都删除。更强大的是，括号里的内容跨行也可以。
7. `ct\` **这个我也经常使用到**，删除从当前光标处开始直到字符 `\` 之前的内容，不能跨行。


### 查找 和 替换

查找：

| Input | Func                 |
| ----- | -------------------- |
| /     | 向下查找字符串       |
| ?     | 向上查找字符串       |
| *     | 向后查找当前光标所在单词 |
| #     | 向前查找当前光标所在单词 |
| n     | 跳到下一个匹配处     |
| N     | 跳到上一个匹配处     |

替换：

- `:m,n s/oldstring/newstring/g` 替换字符串  （m,n 指定替换从m行到n行）
- `:.,$ s/oldstring/newstring/g`
- `:% s/oldstring/newstring/g`

> `. `表示当前行
>
> `%` 表示所有行
>
> `$` 表示文件最后
>
> `g` 表示全局替换，若无g则只替换每行找到的第一个字符串
>
> 如果替换字符串中有/，可以使用+ #作为分隔符


# 自定义快捷键实现小功能

### LEADER 键

LEADER键简单的说就是一个前缀键，默认是 `\`，在上上面的图中，可以看到：`\` 是 not used。我们可以将 LEADER键+其他的键，组合成自己想要的快捷键。我们也可以将LEADER键重新map成其他更好按的键位，比如空格。方法是：`let mapleader=" "` 。

### 自定义分屏快捷键

```
" split the screens to up (horizontal), 
"                    down (horizontal), 
"                    left (vertical), 
"                    right (vertical).
map sj :set nosplitbelow<CR>:split<CR>:set splitbelow<CR>
map sk :set splitbelow<CR>:split<CR>
map sh :set nosplitright<CR>:vsplit<CR>:set splitright<CR>
map sl :set splitright<CR>:vsplit<CR>
```

分屏后，可用通过 `:edit filename` 来打开新的文件。

我们还可以自己定义调整分屏大小的快捷键，如下：

```
" Resize splits with arrow keys
map <up> :res +2<CR>
map <down> :res -2<CR>
map <left> :vertical resize-2<CR>
map <right> :vertical resize+2<CR>
```

![split-screen](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/split-screen.gif)



### 自定义占位符

```vim
" Press space twice to jump to the next '<++>' and edit it
noremap <LEADER><LEADER> <Esc>/<++><CR>:nohlsearch<CR>c4i
```

这样设置之后，我在普通模式下，输入按下两次空格键后，会自动查找下一个'<++>'，删除它，并进入写入模式。

![place-holder](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/place-holder.gif)

# 插件的使用

我使用的vim插件管理器是vim-plug。安装和使用参考[这里](https://github.com/junegunn/vim-plug)。

# Little Tips

### :norm

可视行模式下，选中行后，可以输入`:normal` 或者 `:norm` 可以对每一行执行普通模式下的 commands 命令。


![visual-block-norm](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/visual-block-norm.gif)



### :set filetype=xxx

vim 打开文件时，是根据文件的后缀来区分文件类型，显示出不同的语法高亮。但是有时文件名没有后缀，vim 识别不出文件类型，为了让语法高亮显示正确，我们需要手动设置文件类型。命令如：`:set filetype=make` `:set filetype=python` `:set filetype=vim` 等。

To see the list of language types available, check the `$VIMRUNTIME/syntax/` directory. 

If you want the power of syntax highlighting for any Unix shell output, just pipe it to Vim. For example, `svn diff | vim -R -`.  Notice the dash in the end which tells Vim that it should read text from its standard input.

### map 与 noremap 的区别

map 是 vim 很强大的功能，可以让使用 vim 变的更顺手。除非自己明确的知道映射的结果，否则，没有特殊原因的情况下，应该使用 `noremap` ，胡乱使用 `map` 可能会有一些意想不到的结果。

```vim
map b a
map c b
```

在有上面两个映射的情况下，如果按下 c 键，vim 首先将 c 键映射为 b 键，然后 vim 会再去检查 b 键有没有被映射，此时，b 键又会被映射为 a  键。

```vim
map b a
noremap c b
```

使用 `noremap` 时，当按下 c 键会被映为 b 键，此时 vim 不会再去检查 b 键是否有映射，而是认为最终希望输入的就是 b 键。 







**其它：** 

1. 显示行号
> :set nu (不显示行号: set nonu)
>
> 需要总是显示行号时：将命令追加到~/.vim/vimrc文件中

2. 并列代开其它文件
> :vsp <filename>

3. 并列打开的文件之间切换
> Ctrl + w

| Input      | Func                                               |
| ---------- | -------------------------------------------------- |
| {number}gg | 光标跳到第number行，如果不输入number，就是跳到首行 |
| G          | 光标跳到文件尾行                                   |
| {n}dd      | 删除当前行                                         |
| {n}yy      | 复制当前行                                         |
| ZZ         | 保存并退出                                         |
| ZQ         | 不保存并退出                                       |
| zz         | 将当前行显示在屏幕中间                             |





# 遇到的问题记录

- Visual Mode 下，白色字看不见的问题

![visual-wt-1](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/visual-wt-1.jpg)

Visual mode 选中后，就成下面的样子了：

![visual-wt-2](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/visual-wt-2.jpg)

这是因为高亮颜色设置不对，`:highlight<CR>` 命令可查看所以高亮的颜色配置。

By the way，我的终端软件是Xshell6(Home)，也有可能和终端软件有关系。那我们就改了试试，看能不能生效。

改前的配置：

```shell
Visual         xxx term=reverse ctermbg=7 guibg=LightGrey
```

命令：

```shell
:hi Visual term=reverse cterm=reverse ctermbg=none guibg=none
```

改后的配置和效果：

```shell
Visual         xxx term=reverse cterm=reverse guibg=none
```

![visual-wt-3](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/vi/visual-wt-3.jpg)







