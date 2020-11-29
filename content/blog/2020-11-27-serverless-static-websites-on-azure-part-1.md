+++
title = "Serverless Static Websites on Azure (Part 1)"
description = "Hosting your website or blog for a fraction of the cost of traditional hosting"
author = "Jason Parker"
date = "2020-11-27T05:56:24-07:00"
tags = ["azure","website","cdn","content delivery network","blog","static","hugo","custom domain"]
categories = ["Azure","Hugo","GitHub"]
comments = false
removeBlur = false
[[images]]
  src = "img/main/keyboard-glow.jpg"
  alt = ""
  stretch = ""
draft = "true"
#<a href="" target="_blank"></a>
+++

Previous article | [**Intro: Serverless Static Websites on Azure**](../2020-11-26-serverless-static-websites-on-azure-intro)

## Getting Started with <a href="https://gohugo.io/about/what-is-hugo/" target="_blank">Hugo</a>

Hugo is a general-purpose website framework. Technically speaking, Hugo is a <a href="https://gohugo.io/about/benefits/" target="_blank">static site generator</a>. Unlike systems that dynamically build a page with each visitor request, Hugo builds pages when you create or update your content. Since websites are viewed far more often than they are edited, Hugo is designed to provide an optimal viewing experience for your website’s end users and an ideal writing experience for website authors.

> This article is targeted to Windows users, Hugo can be used on <a href="https://gohugo.io/getting-started/installing/#quick-install" target="_blank">Linux and MacOS</a> too!

### Install Hugo (Windows)

Step-by-setp instructions for installing Hugo on Windows, as found on the Hugo <a href="https://gohugo.io/getting-started/installing/#windows" target="_blank">getting started</a> site.

#### Set up Your Directories

You’ll need a place to store the Hugo executable, your content, and the generated Hugo website:

1. Open Windows Explorer.
2. Create a new folder: `C:\Hugo`, assuming you want Hugo on your C drive, although this can go anywhere
3. Create a subfolder in the Hugo folder: `C:\Hugo\bin`
4. Create another subfolder in Hugo: `C:\Hugo\Sites`

#### Download and Install

> Be sure to download the Extended version

1. Go to the <a href="https://github.com/gohugoio/hugo/releases" target="_blank">Hugo Releases</a> page.
2. The latest release is announced on top. Scroll to the bottom of the release announcement to see the downloads. They’re all ZIP files.
3. Find the Windows files near the bottom (they’re in alphabetical order, so Windows is last) – download either the 32-bit or 64-bit file depending on whether you have 32-bit or 64-bit Windows.
    - Right click Start -> System

      [<img src="../../img/screenshots/2020-11-26-windows-system-type.png" width="248" height="227" />](../../img/screenshots/2020-11-26-windows-system-type.png)

4. Move the ZIP file into your `C:\Hugo\bin folder`.
5. Double-click on the ZIP file and extract its contents. Be sure to extract the contents into the same C:\Hugo\bin folder
6. Windows will do this by default unless you tell it to extract somewhere else.
7. You should now have three new files: The hugo executable (hugo.exe), LICENSE, and README.md.

Now you need to add Hugo to your Windows PATH settings:

#### Update PATH Settings in Windows 10

1. Right click on the **Start** button.
2. Click on **System**.
3. Click on **Advanced System Settings** on the right under *Related Settings*
4. Click on the **Environment Variables** button on the bottom
5. In the User variables section, find the row called **PATH**
6. Double-click on **PATH**.
7. Click the **New** button.
8. Type in the folder where `hugo.exe` was extracted, which is `C:\Hugo\bin` if you went by the instructions above.

  > The PATH entry should be the folder where Hugo lives and not the binary.

9. Press **Enter** when you’re done typing.

    [<img src="../../img/screenshots/2020-11-26-windows-path.png" width="264" height="251" />](../../img/screenshots/2020-11-26-windows-path.png)

10. Click **OK** at every window to exit.

### Testing / Running Hugo with a test website

1. Open PowerShell or your preferred console and type `hugo version`

    ```
    PS C:\Users\Jason> hugo version
    Hugo Static Site Generator v0.78.2/extended windows/amd64 BuildDate: unknown
    ```

2. Navigate to the Hugo sites folder

    ```
    PS C:\Users\Jason> cd C:\Hugo\sites
    PS C:\Hugo\sites>
    ```

3. Create a new site called **HelloWorld**

    ```
    PS C:\Hugo\sites> hugo new site HelloWorld
    ```

4. Navigate to your new site `C:\Hugo\sites\HelloWorld`

    ```
    PS C:\Hugo\sites> cd HelloWorld
    PS C:\Hugo\sites\HelloWorld>
    ```

5. Initialize the directory as a Git Repository
    > This will create / setup the folder as a local git repository. Later we'll get this uploaded to GitHub.

    ```
    PS C:\Hugo\sites\HelloWorld> git init
    Initialized empty Git repository in C:/Hugo/sites/HelloWorld/.git/
    ```

6. Add a simple theme, <a href="https://themes.gohugo.io/gohugo-theme-ananke/" target="_blank">Ananke Gohugo Theme</a>
    > Adding the theme as a submodule using `git` will allow you to pull down any theme updates provided by the author using `git submodule update --remote --merge` from the themes directory.

    ```
    PS C:\Hugo\sites\HelloWorld> cd .\themes\
    PS C:\Hugo\sites\HelloWorld\themes> git submodule add https://github.com/budparr/gohugo-theme-ananke.git
    Cloning into 'C:/Hugo/sites/HelloWorld/themes/gohugo-theme-ananke'...
    remote: Enumerating objects: 9, done.
    remote: Counting objects: 100% (9/9), done.
    Receiving objects:   1% (26/2567) (8/8), done.R
    Receiving objects: 100% (2567/2567),

    Resolving deltas: 100% (1425/1425), done.
    PS C:\Hugo\sites\HelloWorld\themes> cd..
    PS C:\Hugo\sites\HelloWorld>
    ```

7. Create the default configuration file for our Hugo site `config.toml` with our theme
    > The theme name must match the directory name

    ```
    PS C:\Hugo\sites\HelloWorld> notepad config.toml
    ```

    - The file does not exist and notepad will prompt you to create the file, Click Yes.
    - Copy and paste the default settings into notepad, the save and close the file

      ```
      baseurl	= "https://helloworld.com"
      title	= "Hello World"
      theme   = "gohugo-theme-ananke"
      ```

8. Start the Hugo built-in webserver (include drafts)
    > This is the primary way to test your content before you decide to publish it to the Azure storage account. The built-in webserver is very fast and will watch for changes to files in the directories in real-time. Check out the other commands by typing `hugo server --help`

    ```
    PS C:\Hugo\sites\HelloWorld> hugo server --buildDrafts
    Start building sites …

                      | EN
    -------------------+-----
      Pages            |  7
      Paginator pages  |  0
      Non-page files   |  0
      Static files     |  6
      Processed images |  0
      Aliases          |  0
      Sitemaps         |  1
      Cleaned          |  0

    Built in 36 ms
    Watching for changes in C:\Hugo\sites\HelloWorld\{archetypes,content,data,layouts,static,themes}
    Watching for config changes in C:\Hugo\sites\HelloWorld\config.toml
    Environment: "development"
    Serving pages from memory
    Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
    Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
    Press Ctrl+C to stop
    ```

9. Open your browser and go to <a href="http://localhost:1313/" target="_blank">http://localhost:1313/</a> to see our Hello World website

    [<img src="../../img/screenshots/2020-11-26-helloworld-site-test.png" width="562" height="426" />](../../img/screenshots/2020-11-26-helloworld-site-test.png)

### Create our first post

While the Hugo server is running, we can make changes to the folder and files of our *HelloWorld* site and the browser will update with these new changes. We are going to create our first post using the `hugo new` command and edit the post using VS Code.

1. Open a new PowerShell or console windows (*the other console is running the Hugo server*)
2. Navigate to our `C:\Hugo\sites\HelloWorld` folder

    ```
    PS C:\Users\Jason> cd C:\Hugo\sites\HelloWorld
    PS C:\Hugo\sites\HelloWorld>
    ```

3. Create a new post called **My First Post**

    ```
    PS C:\Hugo\sites\HelloWorld> hugo new posts/my-first-post.md
    C:\Hugo\sites\HelloWorld\content\posts\my-first-post.md created
    ```

4. Open Windows File Explorer (*Right-click Start -> File Explorer or Win + E*)
5. Navitate to `C:\Hugo\sites` folder
6. Right-click `HelloWorld` and select Open with Code from the context menu

    ![open-vscode.png](../../img/screenshots/2020-11-26-open-vscode.png)

7. From the left explorer window, expand content and double-click **my-first-post.md** to edit the file

    [<img src="../../img/screenshots/2020-11-26-vs-code-first-post.png" width="451" height="269" />](../../img/screenshots/2020-11-26-vs-code-first-post.png)

    The default Hugo configuration will create a '*metadata*' section at the top of the file which is used by Hugo to generate the page for the site. Next, we'll edit the file using markdown.
    
    > If you are not familiar with markdown syntax, go checkout these resources:
    > - [Markdown Guide](https://www.markdownguide.org)
    > - [Markdown Wikipedia](https://en.wikipedia.org/wiki/Markdown)

8. Edit the `my-first-post.md` file with a simple heading and some text like below

    ```
    ---
    title: "My First Post"
    date: 2020-11-27T09:29:01-07:00
    draft: true
    ---

    ## Welcome to My First Post

    This is my first post using Hugo and markdown!
    ```

9. Save the file and go back to your browser with the <a href="http://localhost:1313/" target="_blank">http://localhost:1313/</a> page open to see your updates!

    [<img src="../../img/screenshots/2020-11-26-helloworld-site-test-1.png" width="534" height="423" />](../../img/screenshots/2020-11-26-helloworld-site-test-1.png)

&nbsp;

---

&nbsp;

![Congrats](../../img/main/congrats-large.jpg)

In **Part 1** of this series, you should have been able to:

- Install Hugo
- Create a new site
- Add a theme
- Create the default configuration file
- Start the Hugo test server
- View your test site
- Create a new post
- Edit the post in VS Code using markdown

Follow along for **Part 2** of this series we'll we focus on a few more complex concepts like, getting our content uploaded to a GitHub repository, creating our Azure storage account with Static Website and automating our site creation.

---

## Series Table of Contents

[**Intro: Serverless Static Websites on Azure**](../2020-11-26-serverless-static-websites-on-azure-intro)

Part 1: Getting Started with Hugo

[**Part 2: Working with GitHub Repositories**](../2020-11-28-serverless-static-websites-on-azure-part-2)
