---
layout: post
title:  "PowerShell系列:文件管理"
date:   2023-04-19 16:30:18 +0800
---
1. 新建文件

   New-Item 名称

       New-Item

   摘要
       Creates a new item.
   语法
       New-Item [[-Path] <System.String[]>] [-Credential <System.Management.Automation.PSCredential>] [-Force] [-ItemType
       <System.String>] -Name <System.String> [-UseTransaction] [-Value <System.Object>] [-Confirm] [-WhatIf] [<CommonPara
       meters>]
       
       New-Item [[-Path] <System.String[]>] [-Credential <System.Management.Automation.PSCredential>] [-Force] [-ItemType
       <System.String>] [-UseTransaction] [-Value <System.Object>] [-Confirm] [-WhatIf] [<CommonParameters>]


   说明
       The `New-Item` cmdlet creates a new item and sets its value. The types of items that can be created depend on the l
       ocation of the item. For example, in the file system, `New-Item` creates files and folders. In the registry, `New-I
       tem` creates registry keys and entries.
       
       `New-Item` can also set the value of the items that it creates. For example, when it creates a new file, `New-Item`
        can add initial content to the file.

   ​

2. 删除文件 Remove-Item

3. PowerShell中编辑文本

   * 使用notepad文本编辑器


   * 使用nano文本编辑器

     首先，你必须安装 `chocolatey` 包，这将有助于在系统中安装 `nano`。确保以管理员身份打开 PowerShell 进行安装。

     ```
     Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
     ```

     `chocolatey` 包安装后，你可以运行此命令来安装 `nano` 编辑器。

     ```
     choco install nano
     ```

   * 使用vim编辑器

     `Vim` 是另一个可以在 PowerShell 控制台上使用的文本编辑器。首先，以管理员身份打开 PowerShell 并使用 `choco` 安装 `vim`。

     ```
     choco install vim
     ```

     安装后，你可以运行 `vim` 在 PowerShell 控制台上编辑文本文件。

     ```
     vim record.txt
     ```

   * 使用Emacs编辑器你也可以像其他文本编辑器一样使用 `emacs` 文本编辑器。

     你也可以像其他文本编辑器一样使用 `emacs` 文本编辑器。

     ```
     choco install emacs

     ```

     然后你可以在 PowerShell 控制台上使用 `emacs` 编辑文本文件。

     ```javascript
     emacs record.txt
     ```

​