+++
title = "Dynamically create Index HTML files"
description = "Easily create index.html files for Azure Storage Static Websites"
author = "Jason Parker"
date = "2020-12-11T16:58:14-07:00"
tags = [""]
categories = ["Azure","PowerShell"]
comments = false
removeBlur = false
[[images]]
  src = "img/main/file-cabinets.jpg"
  alt = ""
  stretch = ""
draft = "false"
+++

[**TL;DR** | Take me to the code](https://github.com/msft-jasonparker/CodeToCloud)

---

In a previous post, I outlined how you can create an Azure Storage Account and then enable it as a Static Website. You may be hosting a static website as an image gallery, a small business website, or even a blog like Code To Cloud and Beyond. If you venture down this path, you will quickly realize that enabling the <a href="https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website" target="_blank">static website</a> feature for your Azure Storage Account does not give you all the benefits of a traditional webserver. Azure does provide you with alternatives if you need a more features besides HTML, CSS, JavaScript or Image files.

- <a href="https://azure.microsoft.com/services/app-service/static/" target="_blank">Azure Service Static Web App</a>
  - Provide a fully managed continuous integration and continuous delivery (CI/CD) workflow from GitHub source to global deployment.
- <a href="https://azure.microsoft.com/services/app-service/" target="_blank">Azure App Service</a>
  - Full featured mangaged platform web service with built-in maintenance, security, patching, scaling, pipelines, etc.

## So who needs an index HTML file?

If you plan to host files from your Azure Storage static website, then you will be sad when you realize that the static website feature does not provide **native directory browsing**. Depending on what files you are hosting, creating a static *index.html* may not be a big deal.  However, if the content you are hosting changes with any level of frequency, updating the *index.html* files will become quite the chore.

## Create the Demo Data Set

I didn't really have a data set to test this with, so I whipped up **(6 hours later)** this handy little function to create some demo data. I put a bunch of comments in the code so you can review it and see how I created the logic. Feel free to use and tweak it to suite your needs.

### Function New-DemoData

[**Download from GitHub**](https://github.com/msft-jasonparker/CodeToCloud/blob/main/Functions/New-DemoData.ps1)

```PowerShell
Function New-DemoData {
    Param (
        $Path,
        $FolderPrefix = "demo_fldr_",
        $FolderCount = 3,
        $MaxFolderDepth = 4,
        [Switch]$IsRoot
    )
    BEGIN {
        Function _CreateDemoFiles {
            Param (
                $Path,
                $FilePrefix = "demo_file_",
                $FileCount = (Get-Random -Minimum 1 -Maximum 4)
            )
        
            Write-Output "Generating $FileCount files in $path"
            # loop to create files based on $fileCount
            For ($i = 0; $i -lt $FileCount; $i++) {
                # create uniquestring for files from 1st section of a guid
                $uniqueString = [guid]::NewGuid().Guid.Split("-")[0]
                # create new file
                $filetemp = New-Item -ItemType File -Path $Path -Name ($FilePrefix + $uniqueString + ".txt")
                # create random content size
                $size = Get-Random 1024
                $data = $null
                # loop to create data based on $size
                For ($x = 0; $x -lt $size; $x++){ $data = $data+[char][byte]((Get-Random 64)+32) }
                # add content to file
                Add-Content -LiteralPath $filetemp.FullName -Value $data
            }
        }
    }
    PROCESS {
        # check the path
        If (Test-Path $Path -ErrorAction SilentlyContinue) {
            # is this path the root folder of the demo data to create
            If ($IsRoot) {
                Write-Output "Creating folders and files in Root of $path"
                # capturing the root path to determine folder depth
                $rootPath = $path
                # call function to create files in the path
                _CreateDemoFiles -Path $Path
                # loop based on $foldercount to create folders and recall function to continue the folder creation
                For ( $fldr_count = 0; $fldr_count -lt $FolderCount; $fldr_count++ ) {
                    # folder name using a uniquestring suffix from 2nd section of a guid
                    $folderName = $FolderPrefix + [guid]::NewGuid().Guid.Split("-")[1]
                    # create the folder
                    $folder = New-Item -ItemType Directory -Path $Path -Name $folderName -ErrorAction SilentlyContinue
                    # if folder created - call function to created random number of folders in the new path
                    # uses $maxdepth to prevent infinite looping
                    If ($folder) { New-DemoData -Path $folder.FullName -FolderCount (Get-Random -Minimum 0 -Maximum 5) -MaxFolderDepth $MaxFolderDepth }
                }
            }
            Else {
                # checks for 0 in $foldercount and only creates demo files
                If ($folderCount -eq 0) {
                    Write-Output "No more folders to create in $path"
                    # call function to create files in the path
                    _CreateDemoFiles -Path $Path
                    Return
                }
                Else {
                    # call function to create files in the path
                    _CreateDemoFiles -Path $Path
                    # get folder depth
                    $folderDepth = $Path.Substring($rootPath.Length).Split("\").Count
                    Write-Output "[Depth: $folderDepth] Path: $path"
                    If ($folderDepth -lt $MaxFolderDepth) {
                        # loop based on $foldercount to create folders and recall function to continue the folder creation
                        For ( $fldr_count = 0; $fldr_count -lt $FolderCount; $fldr_count++ ) {
                            # folder name using a uniquestring suffix from 2nd section of a guid
                            $folderName = $FolderPrefix + [guid]::NewGuid().Guid.Split("-")[1]
                            # create the folder
                            $folder = New-Item -ItemType Directory -Path $Path -Name $folderName -ErrorAction SilentlyContinue
                            # if folder created - call function to created random number of folders in the new path
                            # uses $maxdepth to prevent infinite looping
                            If ($folder) { New-DemoData -Path $folder.FullName -FolderCount $FolderCount -MaxFolderDepth $MaxFolderDepth }
                        }
                    }
                    Else {
                        # exit if $maxfolderdepth is reached for the folder
                        Write-Output "Max Folder depth reached!"
                        Return
                    }
                }
            }
        }
    }
}
```

Running the function should look like this...

{{< figure src="../../img/screenshots/2020-12-12-new-demo-data.png" caption="Function: New-DemoData" >}}

---

## Generate the index.html Files

Now that we have some demo data, we need to generate our **index.html** files for each of the directories. Ideally, you would be executing this with the intention of hosting the files in an Azure Storage Account setup as a Static Website. Once the files are generated, the pathing and links will not work by just using Edge or Chrome from your desktop.

### Function: New-DirectoryWebBrowsing

[**Download from GitHub**](https://github.com/msft-jasonparker/CodeToCloud/blob/main/Functions/New-DirectoryWebBrowsing.ps1)

```PowerShell
Function New-DirectoryWebBrowsing {
    [CmdletBinding()]
    Param (
        [Parameter(Mandatory=$true)]
        [System.String]$Path,
        [Parameter(Mandatory=$false)]
        [System.String]$Website = "example.com",
        [Switch]$Recurse,
        [Switch]$IsRoot
    )
    BEGIN {
        Function _CreateDocumentIndex {
            [CmdletBinding()]
            Param (
                [Parameter(Mandatory=$true)]
                [Object[]]$InputObject,
                [Switch]$IsRoot
            )
            # clear any previous index reference
            $index = $null
          #If ($InputObject[0].RootPath.StartsWith(".")) { $InputObject[0].RootPath = (Resolve-Path $InputObject[0].RootPath).Path }
            # creates and index based on the parent path
            $parentPathIndex = $InputObject[0].PSParentPath.IndexOf($InputObject[0].RootPath.TrimStart("."))
            # creates output file path based on the parent path
            $outputPath = $InputObject[0].PSParentPath.SubString($parentPathIndex)
            $outputFile = ("{0}\index.html" -f $outputPath)
            # html header and breadcrump HTML string builders
            $htmltop = "<html><head><title>{0} - {1}</title></head><body><H1>{0} - {1}</H1><hr>`n<div style='width:1024px;overflow:auto'>`n<pre>`n"
            $htmlBreadCrumb = "<A HREF='{0}'>[To Parent Directory]</A><br><br>`n"
        
            Write-Verbose ("Creating index.html for {0}" -f $outputPath)
            # creates index variable based on if folder is root
            If ($IsRoot) { $index = $htmltop -f $InputObject[0].SiteFQDN,"/" }
            Else {
                # find the index value for the root path from parent path
                $i = $InputObject[0].PSParentPath.IndexOf($InputObject[0].RootPath)

                [System.Collections.Generic.List[Object]]$parentArray = @()
                # finds parent path based on index PLUS root path length
                $parentPath = $InputObject[0].PSParentPath.Substring($i + $InputObject[0].RootPath.Length)
                # splits the path by the separator, then loops to add each of the paths to an array
                $parentPath.Split("\") | Foreach-Object { $parentArray.Add($_) }
                # removes the last item of the array
                $parentArray.Remove($parentArray[-1]) | Out-Null
                # if the array exists, join the items using '/'
                If ($parentArray) { $parentHREF = $parentArray -Join "/" }
                Else { $parentHREF = "/" }
                # create the index variable
                $index = $htmltop -f $InputObject[0].SiteFQDN,$parentPath.Replace("\","/")
                $index += $htmlBreadCrumb -f $parentHREF
            }
        
            # create hash table based on folders
            $ObjectHash = $InputObject | Group-Object PSIsContainer -AsHashTable -AsString
        
            # loop through hash table
            Foreach ($Key in $ObjectHash.Keys) {
                Switch ($Key) {
                    "False" {
                        # loops through each file and generates html links to those files
                        Foreach ($Object in $ObjectHash[$Key]) {
                            Write-Verbose ("[FILE] Creating HTML Links: {0}" -f $Object.Name)
                            $fileHREF = $Object.FullName.Substring($Object.RootPath.Length).Replace("\","/")
                            #based on the character length of the file, the spaces in the strings need to be adjusted
                            If ($Object.Length.ToString().Length -ge 8) { $index += ("{0}		{1}	<A HREF='{2}'>{3}</A>`n" -f $Object.LastWriteTimeUtc,$Object.Length,$fileHREF,$Object.Name) }
                            ElseIf ($Object.Length.ToString().Length -ge 4) { $index += ("{0}		{1}		<A HREF='{2}'>{3}</A>`n" -f $Object.LastWriteTimeUtc,$Object.Length,$fileHREF,$Object.Name) }
                            Else { $index += ("{0}		{1}		<A HREF='{2}'>{3}</A>`n" -f $Object.LastWriteTimeUtc,$Object.Length,$fileHREF,$Object.Name) }
                        }
                    }
                    "True" {
                        # loops through each folder and generates html links to those folders
                        Foreach ($Object in $ObjectHash[$key]) {
                            Write-Verbose ("[FOLDER] Creating HTML Links: {0}" -f $Object.Name)
                            $folderHREF = $Object.FullName.Substring($Object.RootPath.Length).Replace("\","/")
                            $folderDepth = $folderHREF.TrimStart("/").Split("/").Count
                            If ($folderDepth -eq 1) { $index += ("{0}		&lt;dir&gt;		<A HREF='/{1}/'>{1}</A>`n" -f $Object.LastWriteTimeUtc,$Object.Name) }
                            ElseIf ($folderDepth -gt 1) { $index += ("{0}		&lt;dir&gt;		<A HREF='{1}'>{2}</A>`n" -f $Object.LastWriteTimeUtc,$folderHREF,$Object.Name) }
                        }
                    }
                }        
            }
        
            Write-Verbose ("Finished Processing Folders and Files in {0}" -f $outputPath)
            # writes html footer
            $index += "</div></pre>`n<hr></body></html>"
            # creates the index.html file in the directory
            $index | Out-File -FilePath $outputFile -Force
        }
    }
    PROCESS {
        # check to ensure path exists
        If (Test-Path -Path $Path -ErrorAction SilentlyContinue) {
            Write-Verbose ("Analyzing Path: {0}" -f $Path)
            # checks that the $path does not have trailing '\', trims the separator if found
            If ([System.IO.Path]::EndsInDirectorySeparator($Path)) { $Path = [System.IO.Path]::TrimEndingDirectorySeparator($Path) }
        }
        Else {
            # function exits if path not found
            Write-Warning ("The provided path, {0}, was not found" -f $Path)
            Return
        }
        
        # get folder items excluding common web files
        $FolderItems = Get-ChildItem -Path $Path -Exclude "index.html","web.config"
        # checks for folder items
        If ($FolderItems) {
            # check for root folder
            If ($IsRoot) {
                # check if using $PSDrive and sets global variable for root path - needed for html links and pathing
                If ($PSDrive) { $Global:RootPath = $PSDrive.Root }
                Else { $Global:RootPath = $Path }

                # resolves the root path if specified with .\ notation
                If ($Global:RootPath.StartsWith(".")) {$Global:RootPath = (Resolve-Path $Global:RootPath).Path }
                Write-Verbose ("Processing Root Folder Items: {0}" -f $RootPath)
                # adds root path and site fqdn to folder item array
                $FolderItems | Add-Member -MemberType NoteProperty -Name RootPath -Value $Global:RootPath
                $FolderItems | Add-Member -MemberType NoteProperty -Name SiteFQDN -Value $Website
                # calls function to create html files in the path
                _CreateDocumentIndex -InputObject $FolderItems -IsRoot
            }
            Else {
                Write-Verbose ("Processing Folder Items: {0}" -f $RootPath)
                # adds root path and site fqdn to folder item array
                $FolderItems | Add-Member -MemberType NoteProperty -Name RootPath -Value $Global:RootPath
                $FolderItems | Add-Member -MemberType NoteProperty -Name SiteFQDN -Value $Website
                # calls function to create html files in the path
                _CreateDocumentIndex -InputObject $FolderItems
            }
            
            # checks to recurse the child folders
            If ($Recurse) {
                # loops through each child folder
                Foreach ($Folder in $FolderItems.Where{$_.PSIsContainer -eq $true}) {
                    Write-Verbose ("Processing Child Folder: {0}" -f $Folder.fullname)
                    # if the folder name does not equal the incoming path, call the function again for the new folder with recursion
                    If ($Folder.FullName -ne $Path) { New-DirectoryWebBrowsing -Path $Folder.FullName -Website $Website -Recurse }
                }
            }
        }
        Else {
            # function ends if no folder items found
            Write-Warning ("No files found in path: {0}" -f $path)
            Return
        }
    }
}
```

After creating the demo data, I ran the `New-DirectoryWebBrowsing` function against the directory and the results are below.

[<img src="../../img/screenshots/2020-12-12-new-directory-web-browsing.png" width=80% />](../../img/screenshots/2020-12-12-new-directory-web-browsing.png)

---

## Final Thoughts

While this may not seem completely useful, it found it to be a great exercising in recursive functions and how to work with folder and files. This solution really came to be as part of a solution I created for my customer who is using the Azure Storage Account Static Website to host their CERTIFICATE and CRL files. The Static Website didn't offer directory browsing so we needed a way to ensure users to see the files and check their dates and sizes.

