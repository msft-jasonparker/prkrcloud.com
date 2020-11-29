+++
title = "Serverless Static Websites on Azure (Part 2)"
description = "Hosting your website or blog for a fraction of the cost of traditional hosting"
author = "Jason Parker"
date = "2020-11-28T05:56:24-07:00"
tags = ["azure","website","cdn","content delivery network","blog","static","hugo","custom domain"]
categories = ["Azure","Hugo","GitHub"]
comments = false
removeBlur = false
[[images]]
  src = "img/main/github-header.jpg"
  alt = ""
  stretch = ""
draft = "false"
#<a href="" target="_blank"></a>
+++

Previous article | [**Part 1: Getting Started with Hugo**]({{< ref "../blog/2020-11-27-serverless-static-websites-on-azure-part-1.md" >}})

Continuing this series, but adding some *spice* as we move into GitHub repositories, actions, and Azure resources...oh, my!

## Working with GitHub Repositories

I've taken the liberty of creating a new site for demonstration purposes, along with a different Hugo theme. It's good to remember that Hugo themes are unique in and of themselves, so pick one that suits your needs and be sure to read all the documentation.

### Getting the demo site ready

Going forward, we'll be using our demo site called, `prkrlabs.com`. This site will be using the <a href="https://themes.gohugo.io/gohugo-theme-ananke/" target="_blank">Ananke Gohugo Theme</a> theme and for simplicity sake, we'll copy the contents of the exampleSite into our main site directory.

1. Navigate to your demo site folder (*in the previous article, we used C:\Hugo\sites\HelloWorld*)
2. Go to the **themes\gohugo-theme-ananke\exampleSite** folder
3. Select all (*ctrl+A*) and Copy (*ctrl+C*)
4. Rename the config.toml file in the root of your site directory, if it exists, then paste the contents (replacing any files).

  Select All and Copy | Rename config.toml and Paste
  ---|---
  [<img src="../../img/screenshots/2020-11-28-copy-example-site.png" width="242" height="273" />](../../img/screenshots/2020-11-28-copy-example-site.png) | [<img src="../../img/screenshots/2020-11-28-paste-example-site.png" width="242" height="273" />](../../img/screenshots/2020-11-28-paste-example-site.png)

Now, let's fire up the Hugo server just to be sure our site is running with the example theme configuration.

```
PS D:\..\_Website\prkrlabs.com> hugo server -D
Start building sites …

                   | EN
-------------------+-----
  Pages            | 21
  Paginator pages  |  1
  Non-page files   |  0
  Static files     | 10
  Processed images |  0
  Aliases          |  2
  Sitemaps         |  1
  Cleaned          |  0

Built in 49 ms
Watching for changes in D:\OneDrive\_Website\prkrlabs.com\{archetypes,content,data,layouts,static,themes}
Watching for config changes in D:\OneDrive\_Website\prkrlabs.com\config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

And...voila!

[<img src="../../img/screenshots/2020-11-28-prkrlabs-demo-site.png" width="480" height="380" />](../../img/screenshots/2020-11-28-prkrlabs-demo-site.png)

### Local git repository to GitHub repository

We've done most of the hard work already. Creating a directory for our repository, initializing the repository using `git init`, and adding content to the repository. Next, let's pop out to our GitHub account and create a repository for our site.

1. Open your browser and navigate to <a href="https://www.github.com" target="_blank">GitHub</a> and **login** to your account
2. On the left side of the screen locate the green **New** button and let's create a new *GitHub* repository

    ![New Repo](../../img/screenshots/2020-11-28-new-github-repo-button.png)

3. Type the name of our new GitHub repository, set to *Public*, leave the initialization options **unchecked**, Click **Create Repository**

    [<img src="../../img/screenshots/2020-11-28-github-new-repo.png" width="450" height="420" />](../../img/screenshots/2020-11-28-github-new-repo.png)

    Now we've got a *shiny* new GitHub repo, so we'll add our content, commit the changes and push them to GitHub

4. Open PowerShell or any console and navigate to our site folder, `PS D:\..\_Website\prkrlabs>`
5. Run the following commands:
    - **git add .** This adds all the changed files to the repository since being initialized
    - **git commit -m 'inital commit'** Commits the changes to the repository with a message
    - **git branch -M main** Creates our *main* working branch
    - **git remote add origin https://github.com/msft-jasonparker/prkrlabs.com.git** Specifies the location of our remote GitHub repository
    - **git push --set-upstream origin main** Pushes the content from our to our origin from the main branch

    ```
    PS D:\..\_Website\prkrlabs> git add .
    PS D:\..\_Website\prkrlabs> git commit -m 'inital commit'
    [main cc49d68] inital commit
    20 files changed, 2095 insertions(+), 1 deletion(-)
    create mode 100644 archetypes/default.md
    create mode 100644 config.toml
    create mode 100644 config.toml.old
    create mode 100644 content/_index.md
    create mode 100644 content/about/_index.md
    create mode 100644 content/contact.md
    create mode 100644 content/post/_index.md
    create mode 100644 content/post/chapter-1.md
    create mode 100644 content/post/chapter-2.md
    create mode 100644 content/post/chapter-3.md
    create mode 100644 content/post/chapter-4.md
    create mode 100644 content/post/chapter-5.md
    create mode 100644 content/post/chapter-6.md
    create mode 100644 resources/_gen/assets/scss/scss/main.scss_f300667da4f5b5f84e1a9e0702b2fdde.content
    create mode 100644 resources/_gen/assets/scss/scss/main.scss_f300667da4f5b5f84e1a9e0702b2fdde.json
    create mode 100644 static/images/Pope-Edouard-de-Beaumont-1844.jpg
    create mode 100644 static/images/Victor_Hugo-Hunchback.jpg
    create mode 100644 static/images/esmeralda.jpg
    create mode 100644 static/images/notebook.jpg
    PS D:\..\_Website\prkrlabs> git branch -M main
    PS D:\..\_Website\prkrlabs> git remote add origin https://github.com/msft-jasonparker/prkrlabs.com.git
    PS D:\..\_Website\prkrlabs> git push --set-upstream origin main
    Enumerating objects: 35, done.
    Counting objects: 100% (35/35), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (25/25), done.
    Writing objects: 100% (33/33), 1.74 MiB | 1.33 MiB/s, done.
    Total 33 (delta 0), reused 0 (delta 0), pack-reused 0
    To https://github.com/msft-jasonparker/prkrlabs.com.git
      9cce67e..cc49d68  main -> main
    ```

  Our local repository has now been pushed to our GitHub repository!

  [<img src="../../img/screenshots/2020-11-28-github-repo-review.png" width="419" height="389" />](../../img/screenshots/2020-11-28-github-repo-review.png)

## Azure Storage Accounts

If you are familiar with cloud storage concepts like <a href="https://www.onedrive.com/" target="_blank">OneDrive</a>, <a href="https://drive.google.com" target="_blank">Google Drive</a>, or <a href="https://www.icloud.com" target="_blank">iCloud</a>, then Azure storage accounts are the next teir of cloud storage services. To get a more detailed overview, <a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview" target="_blank">Microsoft docs</a> has it all there.

### Create Storage Account

Creating resources, like storage accounts, in Azure is pretty simple. In case this is your first time creating any type of resource, I'll walk you through each step.

1. Open your browser and **login** to the <a href="https://portal.azure.com" target="_blank">Azure Portal</a>
2. On the left Click **Storage Accounts**, then Click **Add** at the top

  **Azure Storage Accounts** | **Create Storage Account**
  --- | ---
  [<img src="../../img/screenshots/2020-11-28-add-storage-account.png" width="90%" />](../../img/screenshots/2020-11-28-add-storage-account.png) | [<img src="../../img/screenshots/2020-11-28-new-azure-stg-acct.png" width="90%" />](../../img/screenshots/2020-11-28-new-azure-stg-acct.png)

  > If you didn't already have a resource group for the storage account resource, Click the **new** link and type the name of resource group where the storage account should be created
  >
  > ![Create RG](../../img/screenshots/2020-11-28-new-azure-rg.png)

3. After reviewing each section and accepting the defaults, Click **Create**

    [<img src="../../img/screenshots/2020-11-28-stg-acct-create.png" width="50%" />](../../img/screenshots/2020-11-28-stg-acct-create.png)

4. Azure will start deploying (creating) your resource and should complete in less than 2 minutes, Click **Go to Resource** when it's complete

    **Storage Account Deploying** | **Storage Account Complete**
    --- | ---
    [<img src="../../img/screenshots/2020-11-28-stg-acct-deploying.png"/>](../../img/screenshots/2020-11-28-stg-acct-deploying.png) | [<img src="../../img/screenshots/2020-11-28-stg-acct-created.png" />](../../img/screenshots/2020-11-28-stg-acct-created.png)

### Enable Storage Account Static Website

Now that we have a *new* storage account, let's enable the **Static Website** feature

1. Click **Static Website** from the left pane (left image)
2. Click **Enable** (right image)
3. Enter **index.html** for the index document name (right image)
4. Enter **404.html** for the error document path (right image)
5. Click **Save** (right image)

**Azure Storage Account** | **Enable Static Website**
--- | ---
[<img src="../../img/screenshots/2020-11-28-stg-acct-static-website.png" width="92%" />](../../img/screenshots/2020-11-28-stg-acct-static-website.png) | [<img src="../../img/screenshots/2020-11-28-stg-acct-static-website-enabled.png" />](../../img/screenshots/2020-11-28-stg-acct-static-website-enabled.png)

6. Enabling the **Static Website** creates a container for the storage account called, *$web*, which is used to store our static website content

> Notice the primary and secondary URL(s) for the static website. **Copy** the primary URL and save it for later. You can always come back to the **Static Website** page and find it later.

### Test / Validate Storage Account Static Website

Let's make sure our static website is working by creating a very simple *index.html* page and upload it to the *$web* container of the storage account.

1. Open **File Explorer** and navigate to our website directory
2. Right-click the site name (*prkrlabs.com in this example*) and **Open with Code**
3. Right-click at the bottom of the left pane and Click **New File**

    [<img src="../../img/screenshots/2020-11-28-vscode-new-index-file.png" width="50%" />](../../img/screenshots/2020-11-28-vscode-new-index-file.png)

4. Type **index.html** and press **Enter**
5. Copy and paste the following code into the file and Click **Save**

    ```html
    <html>
        <head>
            <title>Hello World</title>
        </head>
        <body>
            <h1>Hello World Static Website</h1>
        </body>
    </html>
    ```

6. Go back to the Azure Portal in your browser where we just enabled the static website and Click **Overview** from the top left
7. From the *Overview* page, Click on **Containers** in the main page

    [<img src="../../img/screenshots/2020-11-28-stg-acct-containers.png" width="35%" />](../../img/screenshots/2020-11-28-stg-acct-containers.png)

8. Click **$web** from the list of containers and then Click **Upload**

    [<img src="../../img/screenshots/2020-11-28-stg-acct-web-container.png" width="50%" />](../../img/screenshots/2020-11-28-stg-acct-web-container.png)

9. Click the folder icon and locate the *index.html* file we created in the previous section and Click **Upload** (if you have an older file, be sure to check the box to overwrite files)

    [<img src="../../img/screenshots/2020-11-28-stg-acct-index-upload.png" width="50%" />](../../img/screenshots/2020-11-28-stg-acct-index-upload.png)

10. Now that our upload is complete, we can test the site using the primary URL we save from the previous step

    [<img src="../../img/screenshots/2020-11-28-stg-acct-static-website-test.png" width="50%" />](../../img/screenshots/2020-11-28-stg-acct-static-website-test.png)

### Updating the config.toml (baseURL)

1. Before continuing, we'll need to update the **baseURL** setting in the config.toml file
2. From the VS Code window, Double-click the **config.toml** file to edit it
3. Edit line 2 and insert the URL for the **Static Website**

    [<img src="../../img/screenshots/2020-11-28-config-baseurl.png" width="50%" />](../../img/screenshots/2020-11-28-config-baseurl.png)

4. Click **File -> Save**, we now have an **uncommited** save into our repository
5. Type a commit message like, '**updated baseURL**' and click the *checkmark* just above

    [<img src="../../img/screenshots/2020-11-28-config-baseurl-commit.png" width="50%" />](../../img/screenshots/2020-11-28-config-baseurl-commit.png)

6. We now have a commit that needs to be **pushed** to our GitHub repository
7. Click the **arrows icons** to push this 1 commit to your GitHub repository

    [<img src="../../img/screenshots/2020-11-28-config-baseurl-push.png" />](../../img/screenshots/2020-11-28-config-baseurl-push.png)

---

Glad you've made it this far! Just a few more steps and we'll wrap this section up with a bow.

{{< figure src="../../img/main/hang-in-there-kitten.png" caption="You got this!" >}}

## Automation using GitHub Actions

I don't plan to cover all the in's and out's of GitHub Actions, but I would recommend some *light* reading on their <a href="https://docs.github.com/en/free-pro-team@latest/actions" target="_blank">docs</a> site. Before we create our first action we'll need to grab some information about our storage account and create a few GitHub <a href="https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets#about-encrypted-secrets" target="_blank">secrets</a>.

### Storage Account Connection String and Secrets

We'll need to go back to our browser where we've been doing work in the Azure portal. Navigate to your storage account and find the section on the left called **Access Keys**. When you create a storage account, Azure generates two 512-bit storage account access keys. These keys can be used to authorize access to data in your storage account via Shared Key authorization. Read more about storage account keys on Microsoft docs.

1. Click on **Access Keys** from the storage account page in the Azure portal
2. Under key1, copy the connection string

    [<img src="../../img/screenshots/2020-11-28-stg-acct-access-keys.png" width="50%" />](../../img/screenshots/2020-11-28-stg-acct-access-keys.png)

3. In a separate tab, login to your GitHub account and navigate to your website repository we created [in the previous section]({{< ref "blog/2020-11-28-serverless-static-websites-on-azure-part-2.md#local-git-repository-to-gitHub-repository" >}}).
4. Click on **Settings**
5. Click on **Secrets**
6. Click on **New repository secret**

    [<img src="../../img/screenshots/2020-11-28-github-new-secret.png" width="50%" />](../../img/screenshots/2020-11-28-github-new-secret.png)

7. Enter a name for this secret, like, *AZURE_STORAGE_CONNECTION_STRING* and paste the connection string from your clipboard
8. Click **Add secret**

    [<img src="../../img/screenshots/2020-11-28-github-secret.png" width="50%" />](../../img/screenshots/2020-11-28-github-secret.png)

    [<img src="../../img/screenshots/2020-11-28-github-secret-added.png" width="70%" />](../../img/screenshots/2020-11-28-github-secret-added.png)

> Once you add the secret, you can no longer view it, but you can update it.

### Creating the GitHub Workflow

<a href="https://docs.github.com/en/free-pro-team@latest/actions" target="_blank">GitHub Actions</a> can be complicated to learn at first, especially if you are not familiar with YAML (Yet Another Markup Language). There are loads of marketplace actions to use or you can create your own, if you're feeling *spicy*.

1. From the browser where we've been working in GitHub, Click on **Actions**

  Repository Actions | New Workflow File
  --- | ---
  [<img src="../../img/screenshots/2020-11-28-github-actions.png" />](../../img/screenshots/2020-11-28-github-actions.png) | [<img src="../../img/screenshots/2020-11-28-github-actions-new-workflow.png" />](../../img/screenshots/2020-11-28-github-actions-new-workflow.png)

2. Copy and paste this into your new workflow file

    ```YAML
    name: Publish Website to Azure
    on:
      # this is the trigger event
      # when you push a new commit to your repository in the 'main' branch
      # this workflow will run
      push:
        branches:
          - main
    jobs:
      upload:
        # linux is required for the hugo-actions step
        runs-on: ubuntu-latest
        steps:
          # this action will checkout our repository to the github action runner
          - name: Checkout Repository
            uses: actions/checkout@master
            with:
              # includes our theme submodule
              submodules: true

          # this is the hugo action that builds our site into a folder called 'public'
          # the public folder only exists on the github action runner while its running
          # it won't appear in your repository
          - name: Build Hugo Site into Public
            uses: chabad360/hugo-actions@master
            with:
              # Use if you have a need to build the site to another directory
              buildPath: 'public'

              # Use if your site requires a specific version of Hugo.
              # Append "extended_" to the begining to use Hugo Extended
              hugoVersion: 'extended_0.78.2'

              # Use if you want to pass some custom arguments to Hugo
              args: --gc --minify --cleanDestinationDir --forceSyncStatic

          # this action uses the Azure CLI built into the github action runner
          - name: Azure CLI - Upload Batch to Storage Account
            # not required, but better to use variables for some things instead of hard coded
            env:
              VERB: 'upload-batch' # uploads the content as a batch job
              CONTAINER_NAME: '$web' # the name of our website container
              HUGO_BUILDPATH: 'public' # the hugo build directory
            run: |
              # batch upload to azure storage account - update env params above to match your environment
              # the quotes around the secet variable is required
              az storage blob $VERB --connection-string "${{ secrets.AZURE_STORAGE_CONNECTION_STRING }}" --source $HUGO_BUILDPATH --destination $CONTAINER_NAME
    ```

3. Click the **Commit** button and type a commit message

    ![GitHub-Commit-Message](../../img/screenshots/2020-11-28-github-actions-new-workflow-commit.png)

    > Once you commit this file, the Action will run since we configured it to run anytime there is a push (*commit*) to the main branch

4. Click on the **Actions** section again for your repository
5. Click the name of your commit message

    [<img src="../../img/screenshots/2020-11-28-github-actions-workflows.png" width="50%" />](../../img/screenshots/2020-11-28-github-actions-workflows.png)

6. While reviewing the workflow, Click **upload** to watch the job in real-time

  Workflow Running | Workflow Complete
  --- | ---
  [<img src="../../img/screenshots/2020-11-28-github-actions-workflow-running.png" />](../../img/screenshots/2020-11-28-github-actions-workflow-running.png) | [<img src="../../img/screenshots/2020-11-28-github-actions-workflow-complete.png" />](../../img/screenshots/2020-11-28-github-actions-workflow-complete.png)

  7. Finally, review your published website using the URL we copied from the storage account when we enabled the **Static Website**
  
      [<img src="../../img/screenshots/2020-11-28-static-website-in-azure-storage-account.png" width="80%" />](../../img/screenshots/2020-11-28-static-website-in-azure-storage-account.png)

---

![Fireworks](../../img/main/fireworks-large.jpg)

Wow, What a journey! This was quite a bit longer than I anticipated, but hopefully, you it was worth the read.  In this section you should have completed the following:

  - Setting up a demo website with a theme using Hugo
  - Configuring our local git repository and pushing it to a GitHub repository
  - Create an Azure Storage Account
  - Enable the Static Website (testing too!)
  - Created our first GitHub Action Workflow using secrets

There will be a **Part 3**, which will cover optional setup items like custom domains, Azure Service Principals, Azure CDN and HTTPS redirection.

&nbsp;

---

&nbsp;

## Series Table of Contents

[**Intro: Serverless Static Websites on Azure**](../2020-11-26-serverless-static-websites-on-azure-intro)

[**Part 1: Getting Started with Hugo**](../2020-11-26-serverless-static-websites-on-azure-part-1)

Part 2: Working with GitHub Repositories