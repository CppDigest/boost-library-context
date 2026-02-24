# #1780 - Crash on a simple sync HTTP using particular Windows configuration. [Closed]

> Username: JunielKatarn  
> Created at: 2019-12-14 04:40:04 UTC  
> Updated at: 2019-12-18 03:08:48 UTC  
> Closed at: 2019-12-18 03:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1780  

### Version of Beast  
  
266 (Boost 1.71).  
  
### Steps necessary to reproduce the problem  
  
Clone repo https://github.com/jurocha-ms/BeastHttpSyncCrash  
Build `Release|x86`  
Run the MSVC 14.1 / Boost 1.71 project (Vc141Boost171)  
  
### All relevant compiler information  
  
CL.EXE/LINK.EXE 14.16.27023  
  
See the README in the sample repository file for specific instructions.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-14 15:12:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-565725959  

First of all thank you for the detailed report, I was able to build the program on VS2017 and VS2019 with no problems. Unfortunately I cannot reproduce the defect, and without being able to reproduce it there is little I can do other than look at the source code. In this case I don't see a problem with the implementation. `buffers_cat` is one of the oldest and most stable components of Beast, there hasn't been a bug in it for years so I feel strongly that there is something else at play here.  
  
The best I can do is ask you to try to debug this to figure out why it is happening, since it crashes for you reliably. I can help by answering any questions you have regarding the code.

---

## Comment 2

> Username: JunielKatarn  
> Created at: 2019-12-17 01:12:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566330200  

Thanks for looking at it.  
It does surprise me that you couldn't reproduce the bug. I have tried this in many different machine configurations (all of them Windows 10, though).  
  
Can you please share some details about the machine you used to run this sample?  
Also, just to double check: Is it project *Vc141Boost171* on flavor `Release|x86` the one you ran without any issues?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-17 01:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566336717  

Yeah, no issues

---

## Comment 4

> Username: JunielKatarn  
> Created at: 2019-12-17 01:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566343032  

> Yeah, no issues  
  
Noted. Are you willing to share some machine configuration details, so I can try and reproduce the non-crashing case?  
  
If so, but prefer to take that conversation offline, please reach me at:  
julio at rochsquadron dot net.  
  
Thanks again.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-12-17 02:59:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566358033  

> Are you willing to share some machine configuration details  
  
Yeah, I"m not sure how to get that info though. My System information says:  
  
Windows 10 Pro  
Intel(R) Core(TM) i7-6950X  
64GB RAM  
64-bit OS, x64-based CPU  
  
Anything else?

---

## Comment 6

> Username: JunielKatarn  
> Created at: 2019-12-17 03:10:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566360299  

>   
> Anything else?  
  
 The easiest way is to run this command from Windows Powershell:  
```  
Get-ComputerInfo  
```  
  
Please share its output. Feel free to redact anything you see fit.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-17 12:46:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566526930  

PS C:\Users\vinnie> Get-ComputerInfo                                                                                      
```  
WindowsBuildLabEx                                       : 18362.1.amd64fre.19h1_release.190318-1202  
WindowsCurrentVersion                                   : 6.3  
WindowsEditionId                                        : Professional  
WindowsInstallationType                                 : Client  
WindowsInstallDateFromRegistry                          : 9/17/2019 9:07:35 AM  
WindowsProductId                                        : 00330-80006-53400-AA060  
WindowsProductName                                      : Windows 10 Pro  
WindowsRegisteredOrganization                           :  
WindowsRegisteredOwner                                  : Windows User  
WindowsSystemRoot                                       : C:\WINDOWS  
WindowsVersion                                          : 1903  
BiosCharacteristics                                     : {7, 10, 11, 12...}  
BiosBIOSVersion                                         : {ALASKA - 1072009, 1502, American Megatrends - 5000B}  
BiosBuildNumber                                         :  
BiosCaption                                             : 1502  
BiosCodeSet                                             :  
BiosCurrentLanguage                                     : en|US|iso8859-1  
BiosDescription                                         : 1502  
BiosEmbeddedControllerMajorVersion                      : 255  
BiosEmbeddedControllerMinorVersion                      : 255  
BiosFirmwareType                                        : Uefi  
BiosIdentificationCode                                  :  
BiosInstallableLanguages                                : 3  
BiosInstallDate                                         :  
BiosLanguageEdition                                     :  
BiosListOfLanguages                                     : {en|US|iso8859-1, fr|FR|iso8859-1, zh|CN|unicode}  
BiosManufacturer                                        : American Megatrends Inc.  
BiosName                                                : 1502  
BiosOtherTargetOS                                       :  
BiosPrimaryBIOS                                         : True  
BiosReleaseDate                                         : 12/15/2016 4:00:00 PM  
BiosSeralNumber                                         : System Serial Number  
BiosSMBIOSBIOSVersion                                   : 1502  
BiosSMBIOSMajorVersion                                  : 3  
BiosSMBIOSMinorVersion                                  : 0  
BiosSMBIOSPresent                                       : True  
BiosSoftwareElementState                                : Running  
BiosStatus                                              : OK  
BiosSystemBiosMajorVersion                              : 5  
BiosSystemBiosMinorVersion                              : 11  
BiosTargetOperatingSystem                               : 0  
BiosVersion                                             : ALASKA - 1072009  
CsAdminPasswordStatus                                   : Unknown  
CsAutomaticManagedPagefile                              : True  
CsAutomaticResetBootOption                              : True  
CsAutomaticResetCapability                              : True  
CsBootOptionOnLimit                                     :  
CsBootOptionOnWatchDog                                  :  
CsBootROMSupported                                      : True  
CsBootStatus                                            : {0, 0, 0, 0...}  
CsBootupState                                           : Normal boot  
CsCaption                                               : VF-CORSAIR  
CsChassisBootupState                                    : Safe  
CsChassisSKUNumber                                      : Default string  
CsCurrentTimeZone                                       : -480  
CsDaylightInEffect                                      : False  
CsDescription                                           : AT/AT COMPATIBLE  
CsDNSHostName                                           : VF-Corsair  
CsDomain                                                : WORKGROUP  
CsDomainRole                                            : StandaloneWorkstation  
CsEnableDaylightSavingsTime                             : True  
CsFrontPanelResetStatus                                 : Unknown  
CsHypervisorPresent                                     : False  
CsInfraredSupported                                     : False  
CsInitialLoadInfo                                       :  
CsInstallDate                                           :  
CsKeyboardPasswordStatus                                : Unknown  
CsLastLoadInfo                                          :  
CsManufacturer                                          : ASUS  
CsModel                                                 : All Series  
CsName                                                  : VF-CORSAIR  
CsNetworkAdapters                                       : {VirtualBox Host-Only Network, vEthernet (Default Switch),  
                                                          Ethernet, Ethernet 2...}  
CsNetworkServerModeEnabled                              : True  
CsNumberOfLogicalProcessors                             : 20  
CsNumberOfProcessors                                    : 1  
CsProcessors                                            : {Intel(R) Core(TM) i7-6950X CPU @ 3.00GHz}  
CsOEMStringArray                                        : {Default string, Default string, ERDINGER, Default string}  
CsPartOfDomain                                          : False  
CsPauseAfterReset                                       : -1  
CsPCSystemType                                          : Desktop  
CsPCSystemTypeEx                                        : Desktop  
CsPowerManagementCapabilities                           :  
CsPowerManagementSupported                              :  
CsPowerOnPasswordStatus                                 : Unknown  
CsPowerState                                            : Unknown  
CsPowerSupplyState                                      : Safe  
CsPrimaryOwnerContact                                   :  
CsPrimaryOwnerName                                      : Windows User  
CsResetCapability                                       : Other  
CsResetCount                                            : -1  
CsResetLimit                                            : -1  
CsRoles                                                 : {LM_Workstation, LM_Server, NT, Potential_Browser...}  
CsStatus                                                : OK  
CsSupportContactDescription                             :  
CsSystemFamily                                          : ASUS MB  
CsSystemSKUNumber                                       : All  
CsSystemType                                            : x64-based PC  
CsThermalState                                          : Safe  
CsTotalPhysicalMemory                                   : 68622888960  
CsPhyicallyInstalledMemory                              : 67108864  
CsUserName                                              : VF-CORSAIR\vinnie  
CsWakeUpType                                            : PowerSwitch  
CsWorkgroup                                             : WORKGROUP  
OsName                                                  : Microsoft Windows 10 Pro  
OsType                                                  : WINNT  
OsOperatingSystemSKU                                    : 48  
OsVersion                                               : 10.0.18362  
OsCSDVersion                                            :  
OsBuildNumber                                           : 18362  
OsHotFixes                                              : {KB4533002, KB4515383, KB4516115, KB4520390...}  
OsBootDevice                                            : \Device\HarddiskVolume2  
OsSystemDevice                                          : \Device\HarddiskVolume4  
OsSystemDirectory                                       : C:\WINDOWS\system32  
OsSystemDrive                                           : C:  
OsWindowsDirectory                                      : C:\WINDOWS  
OsCountryCode                                           : 1  
OsCurrentTimeZone                                       : -480  
OsLocaleID                                              : 0409  
OsLocale                                                : en-US  
OsLocalDateTime                                         : 12/17/2019 4:45:52 AM  
OsLastBootUpTime                                        : 12/14/2019 1:34:58 PM  
OsUptime                                                : 2.15:10:53.6744864  
OsBuildType                                             : Multiprocessor Free  
OsCodeSet                                               : 1252  
OsDataExecutionPreventionAvailable                      : True  
OsDataExecutionPrevention32BitApplications              : True  
OsDataExecutionPreventionDrivers                        : True  
OsDataExecutionPreventionSupportPolicy                  : OptIn  
OsDebug                                                 : False  
OsDistributed                                           : False  
OsEncryptionLevel                                       : 256  
OsForegroundApplicationBoost                            : Maximum  
OsTotalVisibleMemorySize                                : 67014540  
OsFreePhysicalMemory                                    : 52324396  
OsTotalVirtualMemorySize                                : 76976012  
OsFreeVirtualMemory                                     : 60065892  
OsInUseVirtualMemory                                    : 16910120  
OsTotalSwapSpaceSize                                    :  
OsSizeStoredInPagingFiles                               : 9961472  
OsFreeSpaceInPagingFiles                                : 9961472  
OsPagingFiles                                           : {C:\pagefile.sys}  
OsHardwareAbstractionLayer                              : 10.0.18362.387  
OsInstallDate                                           : 9/17/2019 2:07:35 AM  
OsManufacturer                                          : Microsoft Corporation  
OsMaxNumberOfProcesses                                  : 4294967295  
OsMaxProcessMemorySize                                  : 137438953344  
OsMuiLanguages                                          : {en-US}  
OsNumberOfLicensedUsers                                 :  
OsNumberOfProcesses                                     : 262  
OsNumberOfUsers                                         : 2  
OsOrganization                                          :  
OsArchitecture                                          : 64-bit  
OsLanguage                                              : en-US  
OsProductSuites                                         : {TerminalServicesSingleSession}  
OsOtherTypeDescription                                  :  
OsPAEEnabled                                            :  
OsPortableOperatingSystem                               : False  
OsPrimary                                               : True  
OsProductType                                           : WorkStation  
OsRegisteredUser                                        : Windows User  
OsSerialNumber                                          : 00330-80006-53400-AA060  
OsServicePackMajorVersion                               : 0  
OsServicePackMinorVersion                               : 0  
OsStatus                                                : OK  
OsSuites                                                : {TerminalServices, TerminalServicesSingleSession}  
OsServerLevel                                           :  
KeyboardLayout                                          : en-US  
TimeZone                                                : (UTC-08:00) Pacific Time (US & Canada)  
LogonServer                                             : \\VF-CORSAIR  
PowerPlatformRole                                       : Desktop  
HyperVisorPresent                                       : False  
HyperVRequirementDataExecutionPreventionAvailable       : True  
HyperVRequirementSecondLevelAddressTranslation          : True  
HyperVRequirementVirtualizationFirmwareEnabled          : False  
HyperVRequirementVMMonitorModeExtensions                : True  
DeviceGuardSmartStatus                                  : Off  
DeviceGuardRequiredSecurityProperties                   :  
DeviceGuardAvailableSecurityProperties                  :  
DeviceGuardSecurityServicesConfigured                   :  
DeviceGuardSecurityServicesRunning                      :  
DeviceGuardCodeIntegrityPolicyEnforcementStatus         :  
DeviceGuardUserModeCodeIntegrityPolicyEnforcementStatus :  
  
```

---

## Comment 8

> Username: JunielKatarn  
> Created at: 2019-12-18 03:08:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1780#issuecomment-566847059  

Thanks. That should help our investigation.  
Closing for now, for this seems more likely like a compiler/linker issue.
