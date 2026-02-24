# #887 - No ZIP Package release for Boost 1.85.0 [Open]

> Username: hamlibdk  
> Created at: 2024-04-16 14:58:49 UTC  
> Updated at: 2024-05-02 01:26:47 UTC  
> Url: https://github.com/boostorg/boost/issues/887  

**There is no release (of everything) in .zip package form.**  
  
Many SDK's such as our Software Developers Kit for JT-ware and Hamlib (The JTSDK) for legacy support reasons rely on such packages.  
  
i.e. https://hamlib-sdk.sourceforge.io/ and https://sourceforge.net/projects/hamlib-sdk/files/Windows/JTSDK-3.4-Stream/ (current release)  
  
Can a .ZIP package release - that maintains legacy nomenclature - please be made available ASAP on the GITHUB release Repo?  
  
i.e. https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.zip <== in this case boost-1.85.0.zip ???  
  
Cna .ZIP package releases please be continued for future releases?

---

## Comment 1

> Username: mclow  
> Created at: 2024-04-16 15:02:48 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2059312032  

The official download site for boost is https://boostorg.jfrog.io/artifactory/main/release/1.85.0/source/, and there is a zip package there: https://boostorg.jfrog.io/artifactory/main/release/1.85.0/source/boost_1_85_0.zip

---

## Comment 2

> Username: hamlibdk  
> Created at: 2024-04-16 15:07:59 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2059323608  

mcclow ... Thanks for the response. There has been long standing issues with JFrog that we do not want to go into heavily (i.e. the links can insert advertising etc.) that make scripting solutions difficult. We were recommended to go here by maintainers in the past - and the GitHUB release repo has served us well as a result.   
  
Its a simple ask ... can the ZIP file please also be moved into the GitHUB release stream in this repo?  
  
Thanks.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-04-16 15:24:41 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2059359608  

>  long standing issues with JFrog  
  
@hamlibdk also, another download location https://archives.boost.io/release/1.85.0/source/boost_1_85_0.zip .  There are plans to shift away from JFrog fairly soon.

---

## Comment 4

> Username: hamlibdk  
> Created at: 2024-04-18 03:57:22 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2062944849  

Attached below this main text is most of a PowerShell script that has been used since the 1.7x.0 range of Boost releases.  
  
We run a configuration key in a file called Versions.ini that allows users - if they want - to select their prefererred release of Boost  
  
i.e.  
  
**File: Versions.ini**  
_...  
boostv=1.84.0  
..._  
  
Yes we could edit our code... **Yet that BREAKS compatibility with PREVIOUS Boost versions that users prefer** (some of our user group actually are Nobel Laureates).  
  
Yet it is the change in nomenclature that has affected us.  
  
Yes we have "obtuse" ways around this ! But that then defeats the purpose of a SDK - which is to bundle EVERYTHING into one simply understood package set.  
  
The ask is simple ... **Please just maintain past nomenclature and standard(s) and manually push this file up to GitHUB !**  
  
We are happy to do this if offered appropriate access :-)  
  
As you will see we have had to move AWAY from JFrog - on the advice of developers in here in the past - - due to issues that can be experienced when advertising links etc. at JFrog are tried to be inserted ! You will see commented efforts even in here !  
  
.............................  
  
  
```  
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
  
$dlFile = "boost-$boostv.zip"  
$dLoc = $dlFile.Replace(".zip","")			# Remove the .zip extenstion [ For decompression ]  
  
# Boost Distribution URL  
  
#$dlPath = "https://boostorg.jfrog.io/artifactory/main/release/$boostv/source/$dlFile"  
# URL used for Development: https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.zip  
$dlPath = "https://github.com/boostorg/boost/releases/download/$dLoc/$dlFile"  
  
Write-Host "  --> Requested Source: $dlPath"  
  
# Download  
# Note: -UserAgent "" <== so can download from JFROG.ORG  
  
if (!(Test-Path("$env:JTSDK_SRC\$dlFile"))) {  
	Write-Host "  --> Downloading $env:JTSDK_SRC\$dlFile"   
	Write-Host ""  
	Write-Host "      `[Note: This could be *** SLOW *** depending on link speed and net congestion.`]"  
	Write-Host ""  
	# try { Invoke-WebRequest $dlPath -UserAgent "" -OutFile "$env:JTSDK_SRC\$dlFile" }  
	try { Invoke-WebRequest $dlPath -OutFile "$env:JTSDK_SRC\$dlFile" }	  
	catch {   
		Write-Host -ForegroundColor Red "  *** ERROR DOWNLOADING FILE ***"   
		Write-Host ""  
		exit(1)   
	}  
	Write-Host "  --> Download Complete"  
} else {  
	Write-Host -ForegroundColor Yellow "  --> Source already downloaded"  
	Write-Host "  --> File: $env:JTSDK_SRC\$dlFile"  
	Write-Host "  --> To refresh: Delete $env:JTSDK_SRC\$dlFile and re-run `'Download-Boost.ps1`'"  
}  
  
Write-Host ""  
  
# Decompression  
  
if (!(Test-Path("$env:JTSDK_SRC\$dLoc"))){  
	Write-Host "  --> Decompressing to: $env:JTSDK_SRC"  
	Write-Host ""  
	Write-Host "      `[Note: This will be *** SLOW ***`]"  
	Write-Host""  
	try { Expand-Archive "$env:JTSDK_SRC\$dlFile" -DestinationPath $env:JTSDK_SRC -Force }   
	catch {   
		Write-Host -ForegroundColor Red "  *** ERROR DECOMPRESSING FILE ***"   
		exit(2)   
	}  
		Write-Host "  --> Decompression complete."   
} else {	  
	Write-Host -ForegroundColor Yellow "  --> Source already decompressed"  
	Write-Host "  --> To refresh: Delete source directory in $env:JTSDK_SRC and re-run `'Download-Boost.ps1`'"  
}  
  
Write-Host "  --> Source in $env:JTSDK_SRC\$dLoc"	  
Write-Host " "  
  
exit(0)  
```

---

## Comment 5

> Username: Joe-K0OG  
> Created at: 2024-04-19 14:26:16 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2066700478  

Greetings,  
  
It seems that a simple fix would help, so please consider renaming the official posted .zip from "boost_1_85_0.zip" to "boost-1.86.0.zip" and renaming the top folder inside the .zip to "boost-1.85.0".  
  
To make 1.85.0 work with the Hamlib SDK (JTSDK) build system, I set "boostv=1.85.0" in Versions.ini, then downloaded https://archives.boost.io/release/1.85.0/source/boost_1_85_0.zip.  After the download I renamed it to "boost-1.85.0.zip" and within the .zip renamed the top folder to "boost-1.85.0".  After doing that, running the "Deploy-Boost" script works perfectly, and the resulting boost 1.85.0 builds applications fine.  Hopefully the boost folks will do this simple name fix.  
  
Thanks!  
-Joe-

---

## Comment 6

> Username: hamlibdk  
> Created at: 2024-04-21 07:45:04 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2067944766  

Joe,  
  
Thanks. Its not that simple. In this case we are trying to maintain backward compatibility without re-issuing a new JTSDK version and/or patch set. We want to concentrate on Version 4 - which deals with deprecation and the lack of availability from "scripted" sources of Qt 5.15.2 (etc).   
  
Any changes that we would make would break backwards compatibility.  
  
Thanks  
  
HSD

---

## Comment 7

> Username: mrichmon  
> Created at: 2024-05-02 01:13:10 UTC  
> Updated at: 2024-05-02 01:15:37 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2089368662  

Is there some reason why the Boost 1.85.0 release on GitHub at https://github.com/boostorg/boost/releases/tag/boost-1.85.0 does not include a `boost-1.85.0.tar.gz` archive?  
  
This archive was published for previous releases at:  
* https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.tar.gz  
* https://github.com/boostorg/boost/releases/download/boost-1.83.0/boost-1.83.0.tar.gz  
* https://github.com/boostorg/boost/releases/download/boost-1.82.0/boost-1.82.0.tar.gz  
* https://github.com/boostorg/boost/releases/download/boost-1.81.0/boost-1.81.0.tar.gz  
  
Pulling release packages from GitHub rather than the main Boost website is important because our corporate caching has a pass-through mechanism for GitHub.com hosted releases. Updating this caching for individual project sites is complicated.

---

## Comment 8

> Username: sdarwin  
> Created at: 2024-05-02 01:26:46 UTC  
> Url: https://github.com/boostorg/boost/issues/887#issuecomment-2089378025  

@mrichmon the github releases contain the boost source, however they are not quite the "official" releases, posted on jfrog, due to the directory format.   
  
What is available on github:  
  
https://github.com/boostorg/boost/releases/download/boost-1.85.0/boost-1.85.0-b2-nodocs.tar.gz (no rendered documentation)  
https://github.com/boostorg/boost/releases/download/boost-1.85.0/boost-1.85.0-cmake.tar.gz (for cmake)  
  
1.81.0 through 1.84.0 were the cmake version.
