---
layout: post
title:  "CLI 描述语言"
date:   2023-04-23 16:48:18 
---



# Commend-Line接口描述语言

`docopt` 可以帮你:

- 为命令行应用定义接口并且自动为其生成解析器

`docopt` 基于数十年程序接口在帮助消息和man pages中形成的使用惯例.`docopt`中的接口描述如以下帮助消息,它比较固定.如:

```
Naval Fate.

Usage:
  naval_fate ship new <name>...
  naval_fate ship <name> move <x> <y> [--speed=<kn>]
  naval_fate ship shoot <x> <y>
  naval_fate mine (set|remove) <x> <y> [--moored|--drifting]
  naval_fate -h | --help
  naval_fate --version

Options:
  -h --help     Show this screen.
  --version     Show version.
  --speed=<kn>  Speed in knots [default: 10].
  --moored      Moored (anchored) mine.
  --drifting    Drifting mine.

```

该例描述可执行接口`naval_fate`,它可以执行不同组合的*命令*(`ship`,`new`,`move`,等),*选项*(`-h`, `--help`, `--speed=<kn>`, 等.),和位置参数(`<name>`, `<x>`, `<y>`).  

该例使用中括号"`[ ]`",括号"`( )`",  管道符"`|`" and 省略符号 "`...`" 来表达可选,必须,互斥,和重复元素.总之,这些元素形成了有效的使用方式,每次执行都以程序名`nava_fate`开始.

在Usage下方,有一组带描述的可选参数.描述了选项是否有长/短形式(`-h`, `--help`),选项是否有参数(`--speed=<kn>`),和选项是否有默认值(`[default: 10]`).

`docopt`实现会提取信息并且生成命令行参数解析器,当程序调用`-h` or `--help`选项时,接口描述文本会作为帮助显示出来.

## 使用方式

关键字`usage:`(不区分大小写)和明显空行之间的是使用方式.`usage`后的第一个词是程序的名称.这是一个无命令行参数最小实例程序:

```
Usage: my_program

```

程序有几种不同的模式,使用不同的参数可以描述这种模式:

```
Usage:
  my_program command --option <argument>
  my_program [<optional-argument>]
  my_program --another-option=<with-argument>
  my_program (--either-that-option | <or-this-argument>)
  my_program <repeating-argument> <repeating-argument>...

```

下面描述每个元素和结构.我们将使用 "word"来描述被 空格,"`[]()|`"其中之一,或者 "`...`"等字符分割的一串字符.

### <argument> 参数

Words starting with "`<`", ending with "`>`" and upper-case words are interpreted as positional arguments.

`<`开头,`>`并且小写的单词被称作位置参数.

```
Usage: my_program <host> <port>

```

### -o --option

Words starting with one or two dashes (with exception of "`-`", "`--`" by themselves) are interpreted as short (one-letter) or long options, respectively.

一个'-'或两个'--'开始的单词(除过'-','--'本身)都分别被解释为短/长选项.

- Short options can be "stacked" meaning that `-abc` is equivalent to `-a -b -c`.
- Long options can have arguments specified after space or equal "`=`" sign:
  `--input=ARG` is equivalent to `--input ARG`.
- Short options can have arguments specified after *optional* space:
  `-f FILE` is equivalent to `-fFILE`.

Note, writing `--input ARG` (as opposed to `--input=ARG`) is ambiguous, meaning it is not possible to tell whether `ARG` is option's argument or a positional argument. In usage patterns this will be interpreted as an option with argument *only* if a description (covered below) for that option is provided. Otherwise it will be interpreted as an option and separate positional argument.

There is the same ambiguity with the `-f FILE` and `-fFILE` notation. In the latter case it is not possible to tell whether it is a number of stacked short options, or an option with an argument. These notations will be interpreted as an option with argument *only* if a description for the option is provided.

### command

All other words that do *not* follow the above conventions of `--options` or `<arguments>` are interpreted as (sub)commands.

### [optional elements]

Elements (options, arguments, commands) enclosed with square brackets "`[ ]`" are marked to be *optional*. It does not matter if elements are enclosed in the same or different pairs of brackets, for example:

```
Usage: my_program [command --option <argument>]

```

is equivalent to:

```
Usage: my_program [command] [--option] [<argument>]

```

### (required elements)

*All elements are required by default*, if not included in brackets "`[ ]`". However, sometimes it is necessary to mark elements as required explicitly with parens "`( )`". For example, when you need to group mutually-exclusive elements (see next section):

```
Usage: my_program (--either-this <and-that> | <or-this>)

```

Another use case is when you need to specify that *if one element is present, then another one is required*, which you can achieve as:

```
Usage: my_program [(<one-argument> <another-argument>)]

```

In this case, a valid program invocation could be with either no arguments, or with 2 arguments.

### element|another

Mutually-exclusive elements can be separated with a pipe "`|`" as follows:

```
Usage: my_program go (--up | --down | --left | --right)

```

Use parens "`( )`" to group elements when *one* of the mutually exclusive cases is required. Use brackets "`[ ]`" to group elements when *none* of the mutually exclusive cases is required:

```
Usage: my_program go [--up | --down | --left | --right]

```

Note, that specifying several patterns works exactly like pipe "`|`", that is:

```
Usage: my_program run [--fast]
       my_program jump [--high]

```

is equivalent to:

```
Usage: my_program (run [--fast] | jump [--high])

```

### element...

Use ellipsis "`...`" to specify that the argument (or group of arguments) to the left could be repeated one or more times:

```
Usage: my_program open <file>...
       my_program move (<from> <to>)...

```

You can flexibly specify the number of arguments that are required. Here are 3 (redundant) ways of requiring zero or more arguments:

```
Usage: my_program [<file>...]
       my_program [<file>]...
       my_program [<file> [<file> ...]]

```

One or more arguments:

```
Usage: my_program <file>...

```

Two or more arguments (and so on):

```
Usage: my_program <file> <file>...

```

### [options]

"`[options]`" is a shortcut that allows to avoid listing all options (from list of options with descriptions) in a pattern. For example:

```
Usage: my_program [options] <path>

--all             List everything.
--long            Long output.
--human-readable  Display in human-readable format.

```

is equivalent to:

```
Usage: my_program [--all --long --human-readable] <path>

--all             List everything.
--long            Long output.
--human-readable  Display in human-readable format.

```

This can be useful if you have many options and all of them are applicable to one of patterns. Alternatively, if you have both short and long versions of options (specified in option description part), you can list either of them in a pattern:

```
Usage: my_program [-alh] <path>

-a, --all             List everything.
-l, --long            Long output.
-h, --human-readable  Display in human-readable format.

```

More details on how to write options' descriptions will follow below.

### [--]

A double dash "`--`", when not part of an option, is often used as a convention to separate options and positional arguments, in order to handle cases when, e.g., file names could be mistaken for options. In order to support this convention, add "`[--]`" into your patterns before positional arguments.

```
Usage: my_program [options] [--] <file>...

```

Apart from this special meaning, "`--`" is just a normal command, so you can apply any previously-described operations, for example, make it required (by dropping brackets "`[ ]`")

### [-]

A single dash "`-`", when not part of an option, is often used by convention to signify that a program should process `stdin`, as opposed to a file. If you want to follow this convention add "`[-]`" to your pattern. "`-`" by itself is just a normal command, which you can use with any meaning.

## Option descriptions

Option descriptions consist of a list of options that you put below your usage patterns. It is optional to specify them if there is no ambiguity in usage patterns (described in the `--option` section above).

An option's description allows to specify:

- that some short and long options are synonymous,
- that an option has an argument,
- and the default value for an option's argument.

The rules are as follows:

Every line that starts with "`-`" or "`--`" (not counting spaces) is treated as an option description, e.g.:

```
Options:
  --verbose   # GOOD
  -o FILE     # GOOD
Other: --bad  # BAD, line does not start with dash "-"

```

To specify that an option has an argument, put a word describing that argument after a space (or equals "`=`" sign) as shown below. Follow either `<angular-brackets>` or `UPPER-CASE` convention for options' arguments. You can use a comma if you want to separate options. In the example below, both lines are valid, however it is recommended to stick to a single style.

```
-o FILE --output=FILE       # without comma, with "=" sign
-i <file>, --input <file>   # with comma, without "=" sign

```

Use two spaces to separate options with their informal description.

```
--verbose MORE text.    # BAD, will be treated as if verbose
                        # option had an argument MORE, so use
                        # 2 spaces instead
-q        Quit.         # GOOD
-o FILE   Output file.  # GOOD
--stdout  Use stdout.   # GOOD, 2 spaces

```

If you want to set a default value for an option with an argument, put it into the option's description, in the form `[default: <the-default-value>]`.

```
--coefficient=K  The K coefficient [default: 2.95]
--output=FILE    Output file [default: test.txt]
--directory=DIR  Some directory [default: ./]

```

## [Try `docopt` in your browser](http://try.docopt.org/)

## Implementations

`docopt` is available in numerous programming languages. Official implementations are listed under the [docopt organization on GitHub](https://github.com/docopt).