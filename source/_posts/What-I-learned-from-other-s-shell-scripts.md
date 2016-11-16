title: 我从其他人的Shell脚本中学到的
date: 2013-08-22 02:11:04
updated: 2013-08-22 03:11:04
tags:
- shell
comments: true
categories: script
---
【感谢[@Elliptic_Yang](http://weibo.com/lixxx333) 的热心翻译。如果其他朋友也有不错的原创或译文，可以尝试推荐给伯乐在线。】
我是一个Shell脚本迷，也很喜欢从其他人的Shell脚本里学习一些有趣的东西。最近我偶然接触到用于方便ssh服务器双重认证的 authy-ssh 脚本。 浏览脚本后我学到了一些很酷的东西，在此也想分享给大家。
<!--more-->
## 让你的echo丰富多彩

很多时候，你会想让echo能以多种颜色区分不同输出。比如，绿色表示成功，红色告知失败，黄色提示警告。

``` bash
NORMAL=$(tput sgr0)
GREEN=$(tput setaf 2; tput bold)
YELLOW=$(tput setaf 3)
RED=$(tput setaf 1)

function red() {
    echo -e "$RED$*$NORMAL"
}

function green() {
    echo -e "$GREEN$*$NORMAL"
}

function yellow() {
    echo -e "$YELLOW$*$NORMAL"
}

# To print success
green "Task has been completed"

# To print error
red "The configuration file does not exist"

# To print warning
yellow "You have to use higher version."
```
这里使用 tput 来配置输出颜色，输出文本，最后再恢复默认输出颜色。如果想对 tpu 了解更多，参看 prompt-color-using-tput

## 输出debug信息

仅当设置DEBUG标志时才打印调试信息。
``` bash
function debug() {
    if [[ $DEBUG ]]
    then
        echo ">>> $*"
    fi
}

# For any debug message
debug "Trying to find config file"
```
还有来自于一些很酷的Geeks的单行debug函数：
``` bash
function debug() { ((DEBUG)) && echo ">>> $*"; }
function debug() { [ "$DEBUG" ] && echo ">>> $*"; }
```

## 检查特定的可执行文件是否存在

``` bash
OK=0
FAIL=1

function require_curl() {
    which curl &>/dev/null
    if [ $? -eq 0 ]
    then
      return $OK
    fi

    return $FAIL
}
```
这里使用 which 命令来查找可执行文件 curl 的路径。如果成功找到，则可执行文件文件是存在的，否则就不存在。 &>/dev/null 将标准输出和标准错误重定向到 /dv/null （也就是不显示在终端上了）。
一些朋友建议可以直接使用 which 返回的状态码。
``` bash
# From cool geeks at hacker news
function require_curl() { which "curl" &>/dev/null; }
function require_curl() { which -s "curl"; }
```

## 显示脚本的使用说明

在我开始写Shell脚本的初期，常会使用 echo 命令显示脚本的使用说明。 但当说明的文字较多时，echo 语句就会变得一团糟。随后我发现，可以使用 cat命令来显示使用说明。
``` bash
cat << EOF

Usage: myscript <command> <arguments>

VERSION: 1.0

Available Commands

    install - Install package

    uninstall - Uninstall package

    update - Update package

    list - List packages

EOF
```

这里的 `<<` 称为 here document，它可以将字符串放置在两个 `EOF` 之间。

## 用户设置 vs. 默认配置

我们有时会希望在用户没有提供设置参数时能够使用默认值。
`URL=${URL:-http://localhost:8080}`
这一语句检查环境变量 URL ，如果不存在，就将其设置为 localhost。

## 检查字符串的长度

``` bash
# ${#VARIABLE_NAME} 可以给出字符串的长度。
if [ ${#authy_api_key} != 32 ]
then
  red "you have entered a wrong API key"
  return $FAIL
fi
```

## 为读取输入设置时限

``` bash
READ_TIMEOUT=60
read -t "$READ_TIMEOUT" input

# if you do not want quotes, then escape it
input=$(sed "s/[;\`\"\$\' ]//g" <<< $input)

# For reading number, then you can escape other characters
input=$(sed 's/[^0-9]*//g' <<< $input)
```

## 获取目录名和文件名

``` bash
# To find base directory
APP_ROOT=`dirname "$0"`

# To find the file name
filename=`basename "$filepath"`

# To find the file name without extension
filename=`basename "$filepath" .html`
```
完结。 祝各位编程开心，享受这美好的一天吧!
原文链接： [Fizer Khan](http://www.fizerkhan.com/blog/posts/What-I-learned-from-other-s-shell-scripts.html) 翻译： [伯乐在线](http://blog.jobbole.com/) - [伯乐在线读者](http://blog.jobbole.com/author/jobbole/)
译文链接： [http://blog.jobbole.com/46245/](http://blog.jobbole.com/46245/)
