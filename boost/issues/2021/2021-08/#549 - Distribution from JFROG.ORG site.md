# #549 - Distribution from JFROG.ORG site [Closed]

> Username: hamlibdk  
> Created at: 2021-08-26 09:03:46 UTC  
> Updated at: 2021-09-13 18:16:21 UTC  
> Closed at: 2021-08-26 23:45:08 UTC  
> Url: https://github.com/boostorg/boost/issues/549  

Hi,  
  
As the coordinating maintainer for the HAMLIB SDK (a software developers' kit based around WSJT-X and its derivatives and the Hamlib libraries) we maintain a script - **Deploy-Boost.ps1** - that utilises the PowerShell command **Invoke-WebRequest** to download a defined version of Boost (i.e. v 1.77.0). See the script below this message as scripts cannot be uploaded.  
  
The supplied script has worked very well until recently.  
  
JFROG, your download maintainer, seems to have put in an "advertising" layer that prevents the **Invoke-WebRequest** function from operating properly. See   
![Untitled](https://user-images.githubusercontent.com/71536383/130931611-fa9af5cb-487e-46f3-9e4c-4238f7380a43.jpg)  
  
A small "corrupted .ZIP" file is now returned to browsers as shown in that image. This is not a problem for most web browsers - but it is a problem for scripted environs that automate the process of pulling in source.  
  
Note that the intent of the Powershell **Invoke-WebRequest** is that function should behave exactly as if it was a browser.   
  
This layer now employed by your delivery site prevents this standard function from operating properly.  
  
It is fully reasonable to have techniques that support and simplify user learning to auto download tools and libraries from repositories.  
  
Can you please look into this?   
  
If your ISP is unable to resolve the issue can you please provide a location of long-term authorised mirrors that do not not utilise "advertising foulers" so that this JFROG URL can be bypassed in our code?  
  
Can you please advise by email - hamlibdk@outlook.com ?  
  
Thanks, and keep up the AMAZING work that has set so many standards (and has itself had functionality incorporated into language standards).  
  
HamlibDK  
Coordinator for the Qt/MinGW-based HAMLIB SDK - The Hamlib and JT-ware Software Developers' Kit !  
E; hamlibdk@outlook.com  
........................................  
  
```  
#-----------------------------------------------------------------------------#  
# Name .........: Download-Boost.ps1  
# Project ......: Part of the JTSDK64 Tools Project  
# Version ......: 3.2.0 Beta  
# Description ..: Downloads selected Boost deployment specified in Versions.ini  
# Usage ........: Call this file directly from the command line  
#  
# Concept ......: Greg, Beam, KI7MT, <ki7mt@yahoo.com>  
# Author .......: Hamlib SDK Contributors <hamlibdk@hotmail.com>  
# Copyright ....: Copyright (C) 2021 Hamlib SDK Contributors  
# License ......: GPL-3  
#  
#-----------------------------------------------------------------------------#  
  
Set-Location -Path $env:JTSDK_HOME  
  
# These are here temporary for Development purposes only  
#  
# Set-Location -Path $PSScriptRoot  
#  
#$env:JTSDK_HOME = "C:\JTSDK64-Tools"  
#$env:JTSDK_CONFIG = $env:JTSDK_HOME + "\config"  
#$env:JTSDK_DATA= $env:JTSDK_HOME + "\data"  
#$env:JTSDK_SRC = $env:JTSDK_HOME + "\src"  
#$env:JTSDK_TMP = $env:JTSDK_HOME + "\tmp"  
#$env:JTSDK_TOOLS = $env:JTSDK_HOME + "\tools"  
#$env:JTSDK_SCRIPTS = $env:JTSDK_TOOLS + "\scripts"  
  
# Describe Action  
  
Clear-Host  
Write-Host " "  
Write-Host "* Download Boost"  
Write-Host " "  
  
# Get hash table of configuration variables from Versions.ini  
  
$env:jtsdk64VersionConfig = "$env:JTSDK_CONFIG\Versions.ini"  
Get-Content $env:jtsdk64VersionConfig | foreach-object -begin {$configTable=@{}} -process { $k = [regex]::split($_,'='); if(($k[0].CompareTo("") -ne 0) -and ($k[0].StartsWith("[") -ne $True)) { $configTable.Add($k[0], $k[1]) } }  
  
# Retrieve Boost Version  
  
$boostv = $configTable.Get_Item("boostv")  
  
# Place into file format for Boost distribution  
# Note: Multi stage here - can be simplified.  
  
$dlFile = "boost_$boostv.zip"  
$dlFile = $dlFile.replace(".","_")  
$dlFile = $dlfile.replace("_zip",".zip")	# Formatted as the filename found on the Web  
$dLoc = $dlFile.Replace(".zip","")			# Remove the .zip extenstion [ For decompression ]  
  
# Boost Distribution URL  
  
#$dlPath = "https://dl.bintray.com/boostorg/release/$boostv/source/$dlFile"  
$dlPath = "https://boostorg.jfrog.io/artifactory/main/release/$boostv/source/$dlFile"  
  
Write-Host "  --> Requested Source: $dlPath"  
  
# Download  
  
if (!(Test-Path("$env:JTSDK_SRC\$dlFile"))) {  
	Write-Host "  --> Downloading $env:JTSDK_SRC\$dlFile"   
	Write-Host "      `[Note: this will be *** slow ***`]"  
	try { Invoke-WebRequest $dlPath -OutFile "$env:JTSDK_SRC\$dlFile" } catch { exit(1) }  
	Write-Host "  --> Download Complete"  
} else {  
	Write-Host "  --> Souce already downloaded"  
	Write-Host "  --> File: $env:JTSDK_SRC\$dlFile"  
	Write-Host "  --> To refresh: Delete $env:JTSDK_SRC\$dlFile and re-run `'Download-Boost.ps1`'"  
}  
  
Write-Host ""  
  
# Decompression  
  
if (!(Test-Path("$env:JTSDK_SRC\$dLoc"))){  
	Write-Host "  --> Decompressing to: $env:JTSDK_SRC"  
	Write-Host "      `[Note: This will be *** very slow ***`]"  
	try { Expand-Archive "$env:JTSDK_SRC\$dlFile" -DestinationPath $env:JTSDK_SRC -Force } catch { exit(2) }  
		Write-Host "  --> Decompression complete."   
} else {	  
	Write-Host "  --> Source already decompressed"  
	Write-Host "  --> To refresh: Delete source directory in $env:JTSDK_SRC and re-run `'Download-Boost.ps1`'"  
}  
  
Write-Host "  --> Source in $env:JTSDK_SRC\$dLoc"	  
Write-Host " "  
  
exit(0)  
```

---

## Comment 1

> Username: sdarwin  
> Created at: 2021-08-26 11:47:47 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906337171  

@hamlibdk ,   
  
Unfortunately, artifactory does seem to have recently inserted some sort of "advertising" layer into the URL scheme.  
  
As a reference, the links shown at https://www.boost.org/users/history/version_1_77_0.html are correct.     
  
https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.bz2  
  
What seems to be happening, if you visit https://boostorg.jfrog.io/ in a browser, it ends up showing modified links that contain "/native/" or "/ui/native/" in the path.   Downloading "/native/" or "/ui/native/" from the command line, or in an automated script, is failing.     
  
Switch that part of the string to "/artifactory/".   
  
Could you add this sort of (pseudo)code in your script?:  
  
```  
if URL matches jfrog.io:  
    replace "/ui/native/" with "/artifactory/"  
```

---

## Comment 2

> Username: hamlibdk  
> Created at: 2021-08-26 15:21:43 UTC  
> Updated at: 2021-08-26 15:25:35 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906507910  

Hi,  
  
The script - hence URL's - are already in a format as you describe.  
  
i.e.:    
```  
$boostv contains the version i.e. 1.77.0  
$dlFile contains the filename i.e. boost_1_77_0.zip  
  
$dlPath = "https://boostorg.jfrog.io/artifactory/main/release/$boostv/source/$dlFile"  
```  
  
My apologies earlier with the code post - I ACCIDENTALLY POSTED THE WRING SCRIPT. I also forgot that directly posting some of the script would lead to these being interpreted as markdown. Its been a busy day here.   
  
I have fixed the display so this should become clearer.

---

## Comment 3

> Username: mclow  
> Created at: 2021-08-26 16:10:16 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906547695  

In my testing, I tried:  
```  
% curl -I "https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz"  
  
HTTP/1.1 200 OK  
Date: Thu, 26 Aug 2021 16:01:28 GMT  
Content-Type: application/x-gzip  
Content-Length: 130620992  
Connection: keep-alive  
X-JFrog-Version: Artifactory/7.23.3 72303900  
X-Artifactory-Id: d9b0a1e2fcb154275fc224430d682a48c0902701  
X-Artifactory-Node-Id: boostorg-artifactory-primary-0  
Last-Modified: Thu, 05 Aug 2021 10:19:31 GMT  
ETag: 7f906921bffea1a84b45e39c092c317dcc5794f4  
X-Checksum-Sha1: 7f906921bffea1a84b45e39c092c317dcc5794f4  
X-Checksum-Sha256: 5347464af5b14ac54bb945dc68f1dd7c56f0dad7262816b956138fc53bcc0131  
X-Checksum-Md5: 5e07d6aa387ba780e4e4de85b5ba3c70  
Accept-Ranges: bytes  
X-Artifactory-Filename: boost_1_77_0.tar.gz  
Content-Disposition: attachment; filename="boost_1_77_0.tar.gz"; filename*=UTF-8''boost_1_77_0.tar.gz  
Strict-Transport-Security: max-age=15724800; includeSubDomains  
X-Request-ID: bb4deb7e2134e06020256750b730f702  
```  
all of which look reasonable.  
  
But when I tried:  
```  
% curl -o junk.tar.gz  "https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz"  
```  
  
I got a zero byte file.

---

## Comment 4

> Username: mclow  
> Created at: 2021-08-26 16:19:01 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906554784  

Another data point - this python code downloads things correctly:  
```  
def downloadAFile(url, destFile):  
	with requests.get(url, stream=True) as r:  
		with open(destFile, 'wb') as f:  
			shutil.copyfileobj(r.raw, f)  
```

---

## Comment 5

> Username: memsharded  
> Created at: 2021-08-26 16:53:25 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906578577  

So far I think that all typical downloads, curl, python, etc have been working without issues.  
It would be useful to reduce the above .ps to a single line (or just a few lines), even if hardcoded values, that can reproduce the issue, just calling ``Invoke-WebRequest `` with some hardcoded values would be great.

---

## Comment 6

> Username: mclow  
> Created at: 2021-08-26 16:57:11 UTC  
> Updated at: 2021-08-26 16:57:47 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906581055  

the `curl` download is NOT working.(at least not for me)

---

## Comment 7

> Username: memsharded  
> Created at: 2021-08-26 17:12:28 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906591912  

> the curl download is NOT working.(at least not for me)  
  
Oh, yes, you are right, this is unexpected, I'd say this worked in the past, I'll try to have a look.

---

## Comment 8

> Username: sdarwin  
> Created at: 2021-08-26 17:43:19 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906612168  

> the curl download is NOT working.(at least not for me)  
  
curl -L (follow redirects)

---

## Comment 9

> Username: memsharded  
> Created at: 2021-08-26 17:45:55 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906613837  

```  
curl -L -o boost_1_77_0.7z https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.7z  
```  
works, indeed

---

## Comment 10

> Username: mclow  
> Created at: 2021-08-26 17:46:13 UTC  
> Updated at: 2021-08-26 17:46:34 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906614059  

> > the curl download is NOT working.(at least not for me)  
>   
> Oh, yes, you are right, this is unexpected, I'd say this worked in the past, I'll try to have a look.  
  
This *definitely* worked in the past.  (`curl` w/o the `-L`)

---

## Comment 11

> Username: mclow  
> Created at: 2021-08-26 17:52:55 UTC  
> Updated at: 2021-09-13 18:16:21 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906618504  

Interesting note:  
`curl -I` (does an http `HEAD` and fetches just the headers) gives you a status "HTTP/1.1 200 OK" - and the rest of the stuff that I posted above.  
  
`curl` (w/o the `-I` does an HTTP `GET`) gets you a COMPLETELY different set of headers, including a status of "HTTP/1.1 302 Found"

---

## Comment 12

> Username: sdarwin  
> Created at: 2021-08-26 21:47:19 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906765535  

@hamlibdk ,  
  
Setting an empty user agent worked.    
  
-UserAgent ""  
  
```  
Invoke-WebRequest -URI https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.bz2 -OutFile file.bz2 -UserAgent ""  
```  
  
Jira ticket https://www.jfrog.com/jira/browse/RTFACT-26216

---

## Comment 13

> Username: hamlibdk  
> Created at: 2021-08-26 23:45:08 UTC  
> Url: https://github.com/boostorg/boost/issues/549#issuecomment-906815617  

"Houston ... We've had a problem" <== The real line.  
  
**Sdarwin** your suggestion works ! Thanks very much for this, and its been highly informative (and constructive for the general programming community) to see the discussion that this is generating.  
  
Fix in my case:  
  
```  
try { Invoke-WebRequest $dlPath -UserAgent "" -OutFile "$env:JTSDK_SRC\$dlFile" } catch { exit(1) }  
```  
  
For those using CURL (that I deprecated in favor of PowerShell) it is:  
  
``  
curl -A "" [URL]  
``  
  
[ Preliminary testing on old codebase suggests this works ]  
  
Can I please request that you people keep an eye on this issue that has emerged to ensure that it does not balloon? There are also security implications with this technique used by your SP.  
  
Again I need to convey my sincerest personal thanks and also that from our Amateur community.
