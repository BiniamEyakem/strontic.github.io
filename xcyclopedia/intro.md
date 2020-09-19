---
title: "xCyclopedia | Introduction"
breadcrumbs_nested: true
breadcrumbs_title: Introduction
layout: "splash"
classes: wide
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/digital2.jpg
  headline: "xCyclopedia"
  logo: "/assets/images/strontic-xcyclopedia-logo-min-ocr.png"
  logo_description: "xCyclopedia Logo"
  actions:
    - label: "Download"
      url: "https://github.com/strontic/xcyclopedia/archive/master.zip"
    - label: "View"
      url: "/xcyclopedia/#index"
  caption: "Source: [**shutterstock**](https://www.shutterstock.com/image-illustration/network-connection-structure-perspective-grid-technology-1814625266)"
excerpt: "The Encyclopedia of Executables"
---

## What is xCyclopedia?
The xCyclopedia project attempts to document all executable binaries (and eventually scripts) that reside on a typical operating system. It provides a [web page](https://strontic.github.io/xcyclopedia) to view the data as well as a machine-readable format ([JSON](https://github.com/strontic/xcyclopedia/blob/master/output/strontic-xcyclopedia.json.zip) and [CSV](https://github.com/strontic/xcyclopedia/blob/master/output/strontic-xcyclopedia.csv.zip)) that can be immediately usable in other systems such as SIEMs to enrich observed executions with contextual data.

## What data points are available?

* Runtime data (Standard Out, Standard Error, Children Processes, Screenshots, Open Handles, Loaded Modules, Window Title)
* File metadata (File Description, Original File Name, Product Name, Comments, Company Name, File Version, Product Version, Copyright)
* Digital signature validity and associated metadata (Serial, Thumbprint, Issuer, Subject)
* File hashes (MD5, SHA1, SHA256, SHA384, SHA512)
* Fuzzy file hash (ssdeep)
* Similar files* (available on [xCyclopedia web page](https://strontic.github.io/xcyclopedia) only)
* External References* (available on [xCyclopedia web page](https://strontic.github.io/xcyclopedia) only)
  * Examples of misuse (e.g. malicious use of legitimate executable)
  * Microsoft Documentation

## How is this done?
A powershell script iterates recursively through all directories and starts any executables found. It then gathers a multitude of artifacts (which is slowly being improved). For example, it grabs the command line output, in search of helpful syntax messages. And if a window is visible, it will take a screenshot.

## Where is this data stored?

#### JSON/CSV
For the machine-readable data (JSON & CSV): 
* [strontic-xcyclopedia.json.zip](https://github.com/strontic/xcyclopedia/blob/master/output/strontic-xcyclopedia.json.zip)
* [strontic-xcyclopedia.csv.zip](https://github.com/strontic/xcyclopedia/blob/master/output/strontic-xcyclopedia.csv.zip)

#### Web Page (Markdown)
For a web-based view of the data click here: [strontic.github.io/xcyclopedia](https://strontic.github.io/xcyclopedia). *Note: the web view includes a few bonus features that the JSON/CSV files do not currently include; namely the following:*
* Examples of known malicious use of a given executable (current sources: [atomic-red-team](https://github.com/redcanaryco/atomic-red-team), [LOLBAS](https://github.com/LOLBAS-Project/LOLBAS), [malware-ioc](https://github.com/eset/malware-ioc), [Sigma](https://github.com/Neo23x0/sigma), and [Signature-Base](https://github.com/Neo23x0/signature-base))
* File comparisons/similarities (using [ssdeep](https://github.com/ssdeep-project/ssdeep/releases/tag/release-2.14.1))
* relevant [Microsoft documentation](https://github.com/MicrosoftDocs/windowsserverdocs).

## Can I collect this data myself?

Sure! The powershell scripts are [here](https://github.com/strontic/xcyclopedia/blob/master/script)! See syntax/usage section below.

## Collector Script Usage

### Syntax

 ```powershell
  Get-Xcyclopedia
  #Synopsis: Iterate through all executable files in a specified directory (default target is .EXE). Gather CLI usage/syntax, screenshots, file hashes, file metadata, signature validity, and child processes.
    -save_path                  #path to save output
    -target_path                #target path for enumerating files (non-recursive). Comma-delimited for multiple paths.
    -target_path_recursive      #target path for enumerating files (recursive). Comma-delimited for multiple paths.
    -target_file_extension      #File extension to target (default = ".exe")
    -execute_files    [bool]    #Execute each for gathering syntax/usage info (stdout/stderr)
    -take_screenshots [bool]    #Take a screenshot if a given process has a window visible. This requires execute_files to be enabled.
    -minimize_windows [bool]    #Minimizing windows helps with screenshots, so that other windows do not get in the way. This only takes effect if execute_files and $take_screenshots are both enabled.
    -xcyclopedia_verbose [bool] #Verbose Output
    -transcript_file  [bool]    #Write console output to a file (job.txt)

  Coalesce-Json
    #Synopsis: Combine JSON files into a single file. Only works with PowerShell-compatible JSON files.
    -target_files          #List of JSON files (comma-delimited) to combine.
    -save_path             #Path to save the combined JSON file.
    -verbose_output [bool]
    -save_json      [bool] #Save file as JSON
    -save_csv       [bool] #Save file as CSV
````

### Example
```powershell
Get-Xcyclopedia -save_path "c:\temp\strontic-xcyclopedia" -target_path "$env:windir\system32" -target_file_extension ".exe"
````

### **Optional** Dependencies:
* *ssdeep*: For obtaining ssdeep fuzzy hashes (useful for finding similar files). You must extract the ssdeep ZIP file (available [here](https://github.com/ssdeep-project/ssdeep/releases/download/release-2.14.1/ssdeep-2.14.1-win32-binary.zip)) into a subfolder called "bin/ssdeep-2.14.1".
* *Sysinternals Handle*: For obtaining the open handles of a given process. You must place `handle64.exe` (available [here](https://docs.microsoft.com/en-us/sysinternals/downloads/handle)) in a subfolder called "bin/sysinternals/handle".

## How can I contribute?
* Share it with friends
* Provide feedback

## TODO
- Use a more reliable method for determining children processes (and for stopping them)
- Add other hashing algorithms (e.g. Imphash, vHash, Authentihash)
- Use Logman.exe (or equivalent) to determine which ETW providers are being populated by a given process.
- Use SilkETW (or equivalent) for vastly improved runtime metadata gathering. 
- Identify runtime deltas in different executable versions. (e.g. when a new command-line switch is added to the standard output)
