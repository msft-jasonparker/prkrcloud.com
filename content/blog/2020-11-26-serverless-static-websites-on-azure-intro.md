+++
title = "Serverless Static Websites on Azure Intro"
description = "Hosting your website or blog for a fraction of the cost of traditional hosting"
author = "Jason Parker"
date = "2020-11-26T05:56:24-07:00"
tags = ["azure","website","cdn","content delivery network","blog","static","hugo","custom domain"]
categories = ["Azure","Hugo","GitHub"]
comments = false
removeBlur = false
[[images]]
  src = "img/main/static-web-site.jpg"
  alt = ""
  stretch = ""
draft = "true"
#<a href="" target="_blank"></a>
+++

## First things first

> Thanks to <a href="" target="_blank"></a>[Matthew Fisher](https://blog.bacongobbler.com/) for his initial article and instructions.

While you don't need to be a '*coder*' or a '*developer*' to follow this article, there are a few foundational housekeeping items that we should probably clear up. The concepts and instructions in this article assume that you have a small knowledge about <a href="https://git-scm.com/" target="_blank">Git</a>, <a href="https://www.github.com" target="_blank">GitHub</a>, Microsoft Azure and <a href="https://code.visualstudio.com/" target="_blank">Microsoft VS Code</a>. So before you get started, do yourself a quick favor and do the following:

- Create a **FREE** <a href="https://github.com/join" target="_blank">GitHub</a> account
  - Github is a website for developers and programmers to collaboratively work on code. Github offers a version control system, which allows for seamless collaboration without the compromising the integrity of the original project.
- Create a **FREE** <a href="https://azure.microsoft.com/en-us/free/" target="_blank">Azure Account</a>
  - Azure is the Microsoft Cloud platform. **New** accounts receive a $200 credit for the first 30 days.
- Download and install <a href="https://git-scm.com/downloads" target="_blank">Git</a>
  - Git is an application you'll need to install on your PC or Mac. Once installed you can use the git commands from your favorite console, I prefer PowerShell.
- Download and install <a href="https://code.visualstudio.com/#alt-downloads" target="_blank">Microsoft VS Code</a>
- **[OPTIONAL]** Buy a custom domain name <a href="https://www.namecheap.com" target="_blank">Namecheap</a> is who I use)

> **WARNING!**
>
> I have had these tools and systems setup for a while and setting all these up from greenfield may require some bing or google-fu.

---

If you're ready to get this going, let's move on to [**Serverless Static Websites on Azure (Part 1)**](../2020-11-27-serverless-static-websites-on-azure-part-1)

---

### Series Table of Contents

Intro: Serverless Static Websites on Azure

[**Part 1: Getting Started with Hugo**]({{< ref "blog/2020-11-27-serverless-static-websites-on-azure-part-1.md" >}})

[**Part 2: Working with GitHub Repositori**]({{< ref "blog/2020-11-28-serverless-static-websites-on-azure-part-2.md" >}})