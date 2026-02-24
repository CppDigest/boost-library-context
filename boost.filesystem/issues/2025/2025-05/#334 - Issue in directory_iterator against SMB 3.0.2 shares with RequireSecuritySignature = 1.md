# #334 - Issue in directory_iterator against SMB 3.0.2 shares with RequireSecuritySignature = 1 [Closed]

> Username: vvekariya  
> Created at: 2025-05-27 14:56:50 UTC  
> Updated at: 2025-11-07 14:08:45 UTC  
> Closed at: 2025-11-07 14:07:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334  

Affected Boost version: 1.79.0 and newer  
Working Boost version: 1.78.0 and earlier  
Platform: Windows 2019 Data center (with RequireSecuritySignature = 1)  
  
Network Share (Linux): SMB 3.0.2   
  
After upgrading Boost.Filesystem from 1.78 to 1.79 or newer, calls to boost::filesystem::directory_iterator fail when accessing directories on an SMB network share that has RequireSecuritySignature = 1 enabled.  
  
Error message:  
`boost::filesystem::directory_iterator::construct: Invalid Signature [system:-2146893818]:`  
  
  
Windows event log (SMB Client security)  
```  
The signing validation failed.  
  
Error:An invalid parameter was passed to a service or function.  
  
Server name: \xxxxxxxxxxxxxx.com  
Session ID:0xAC33E23  
Tree ID:0x18645  
Message ID:0x129C4  
Command: Query directory  
  
Guidance:  
This error indicates that SMB messages are being modified in transit across the network from the server to the client. This may be due to the session ending on the server, a problem with the network, a problem with a third-party SMB server, or a "adversary-in-the-middle" compromise attempt.  
  
PacketFragment:0  
```  
  
  
Procmon logs provided:  
[Boost178.CSV](https://github.com/user-attachments/files/20481015/Boost178.CSV) -> Working  
[Boost179.CSV](https://github.com/user-attachments/files/20481017/Boost179.CSV) -> Not working  
  
  
Test application code: [Testapp.txt](https://github.com/user-attachments/files/20481016/Testapp.txt)

---

## Comment 1

> Username: Lastique  
> Created at: 2025-05-27 17:37:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-2913401483  

I have no knowledge about SMB signing and I'm unlikely to be able to look into this any time soon. Patches are welcome.

---

## Comment 2

> Username: vvekariya  
> Created at: 2025-06-02 11:35:50 UTC  
> Updated at: 2025-06-02 11:36:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-2930184545  

> I have no knowledge about SMB signing and I'm unlikely to be able to look into this any time soon. Patches are welcome.  
  
After seeing this logging out:  
```  
20:44:49,4429999	test178.exe	14448	QueryDirectory	\\mytestServer.com\myshare\Share\*	SUCCESS	FileInformationClass: FileBothDirectoryInformation, Filter: *, 2: .  
  
20:47:32,7579872	test179.exe	7392	QueryDirectory	\\mytestServer.com\myshare\Share	0xC000A000	FileInformationClass: FileIdExtdDirectoryInformation  
```  
  
I would suggest to catch error code for STATUS_INVALID_SIGNATURE 0xC000A000  
then try to make use of FileBothDirectoryInformation as fallback instead, similar to: https://github.com/boostorg/filesystem/blob/f4bb6d0f3ebe9f8b90243d8a98989191925d49d2/src/directory.cpp#L942  
  
I believe this problem occurring due to how the GPO is setup on this environment. Unfortunately I am unable to get that information or logs from SMB server to be able to help this case forward. And this is the only work around I can find at the moment.  
  
I will run a test on this fallback if it resolves the problem or not.

---

## Comment 3

> Username: Lastique  
> Created at: 2025-06-02 12:06:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-2930359069  

Thanks for the lead. A few notes:  
  
1. `STATUS_INVALID_SIGNATURE` is an `NTSTATUS` error code, it is most certainly not something that is returned by `GetLastError()` after `GetFileInformationByHandleEx`. We need an `ERROR_*` family error code that is returned in this case.  
2. The check you referenced sets `g_extra_data_format`, which prevents this version of `GetFileInformationByHandleEx` from being used ever again. This shouldn't be done for problems that are only specific to SMB, especially in a very specialized configuration. The fallback should only apply to this specific iterator instance (i.e. don't set `g_extra_data_format`, just try the next branch).  
3. We need to know which branch actually works in your use case. Then, knowing the error code returned in each of the preceding branches, we can add fallback `goto`s in them.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-06-15 15:06:24 UTC  
> Updated at: 2025-06-15 15:10:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-2974031769  

I have committed a fix in https://github.com/boostorg/filesystem/commit/152d384c5045ba153a94c1dc21e69d2df5e751e0, please test if it works for you.  
  
I could not reproduce the problem locally, but hopefully I got the error code right. I suspect, the issue may be specific to your client or server software versions (specifically, Samba seems very old). Otherwise, it could be something else specific to your setup that I can't reproduce.

---

## Comment 5

> Username: Lastique  
> Created at: 2025-06-15 15:22:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-2974067818  

In fact, Samba 3.0.2 was released in 2004, and Samba 3.0.3 contains fixes for ["SMB signing errors"](https://www.samba.org/samba/history/samba-3.0.3.html). I'm tempted to consider Samba 3.0.2 too old to support, as it surely contains lots of issues and vulnerabilities at this point. Can you test if your issue reproduces with a more up-to-date Samba version?

---

## Comment 6

> Username: vvekariya  
> Created at: 2025-11-06 12:17:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-3496976157  

> I have committed a fix in [152d384](https://github.com/boostorg/filesystem/commit/152d384c5045ba153a94c1dc21e69d2df5e751e0), please test if it works for you.  
>   
> I could not reproduce the problem locally, but hopefully I got the error code right. I suspect, the issue may be specific to your client or server software versions (specifically, Samba seems very old). Otherwise, it could be something else specific to your setup that I can't reproduce.  
  
Apologies.. I will get it tested. Thanks

---

## Comment 7

> Username: vvekariya  
> Created at: 2025-11-07 12:39:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-3502327481  

> In fact, Samba 3.0.2 was released in 2004, and Samba 3.0.3 contains fixes for ["SMB signing errors"](https://www.samba.org/samba/history/samba-3.0.3.html). I'm tempted to consider Samba 3.0.2 too old to support, as it surely contains lots of issues and vulnerabilities at this point. Can you test if your issue reproduces with a more up-to-date Samba version?  
  
Hi,  
In regards of upgrading to SM 3.0.3 unfortunately we are unable to do that because its a company wide system that is currently using 3.0.2. So this would significantly affect other part of the system that is dependent on this version.  
  
We would be glad if we can get this change in, even if it's with a specific flag to enable this fix is enough.

---

## Comment 8

> Username: vvekariya  
> Created at: 2025-11-07 12:40:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-3502330869  

@Lastique We have now tested the change you did. And this solution works perfect!  
When will you be able to deliver this to a version that we can implement?

---

## Comment 9

> Username: Lastique  
> Created at: 2025-11-07 14:08:28 UTC  
> Updated at: 2025-11-07 14:08:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/334#issuecomment-3502771435  

Should be included in the next Boost release (1.90.0).
