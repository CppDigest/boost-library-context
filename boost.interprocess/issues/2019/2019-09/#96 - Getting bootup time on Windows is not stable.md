# #96 - Getting bootup time on Windows is not stable [Open]

> Username: kkumar45  
> Created at: 2019-09-27 04:56:36 UTC  
> Updated at: 2020-06-04 03:42:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/96  

Bootup time is needed in boost::interprocess for creating a unique shared directory which remains identical for all processes started since last reboots, but different between reboots. Currently people have choice to get the bootup time by define following macros:  
```  
1. BOOST_INTERPROCESS_BOOTSTAMP_IS_LASTBOOTUPTIME (not recommended due to instability with time synchronization and hibernation and unusable in practice)  
2. BOOST_INTERPROCESS_BOOTSTAMP_IS_EVENTLOG_BASED (It's a default method, if none of the macro is defined, fetches bootup time from system event log, Id 6005)  
3. BOOST_INTERPROCESS_BOOTSTAMP_IS_SESSION_MANAGER_BASED (fetches bootup time from registry setting, path  : HKEY_LOCAL_MACHINE→ SYSTEM → CurrentControlSet → Control → Session Manager → Memory Management → PrefetchParameters → BootID)  
```  
  
The problem with 2 & 3 is that if the Windows system event log with ID 6005 and registry key BootID gets deleted then the application throws runtime exception.  We have already been notified of these issues by different users of our application and hence the idea is to propose a new method which can be used on Windows OS that supports of:  
  
```  
* Minimum supported client -> Windows Vista  
* Minimum supported server -> Windows server 2008  
```  
Since this issue is in existence from long time and for those who are using newer Windows OS, provide them the flexibility to use GetTickCount64 for calculating bootup time by define a new preprocessor macro  `BOOST_INTERPROCESS_BOOTSTAMP_IS_GETTICKCOUNT64_BASED `in `boost/interprocess/detail/win32_api.hpp`  
  
I have added a patch for having a better view of suggested change. Please provide your feedback if the change can be pulled into Boost.  If so I can submit a PR for this.  
  
![my_patch1](https://user-images.githubusercontent.com/26009849/65743138-f05f1400-e110-11e9-9324-402a8847db2f.JPG)

---

## Comment 1

> Username: assarbad  
> Created at: 2020-05-11 15:19:54 UTC  
> Updated at: 2020-05-11 15:36:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/96#issuecomment-626770511  

Hi @kkumar45,  
  
I have recently reported - and today diagnosed - this issue as well over in [SourceTrail issue 1002](https://github.com/CoatiSoftware/Sourcetrail/issues/1002).  
  
I have to admit that using `GetTickCount64` would be able to work around this. Depends entirely what the minimum Windows version supported by Boost is at the moment.  
  
Another possibility would be to use what I proposed over in that SourceTrail ticket. Quoting myself:  
  
> Still, it would be much more sensible to query the creation time of `\REGISTRY` or `\SystemRoot` in the Object Manager namespace. My tool [NT Objects](https://bitbucket.org/assarbad/ntobjx) demonstrates this. And yes, these can be queried as totally unprivileged user. I just verified it _after a reboot_ and I have the following time stamps (times in UTC):  
>   
> * `\REGISTRY`: 2020-05-11 14:29:45.644  
> * `\SystemRoot`: 2020-05-11 14:29:45.555  
>   
> (as a side-note, these values are actually roughly _nine seconds_ before the timestamp of the event 6005 "The Event log service was started.")  
>   
> I didn't check what [PSTools](https://docs.microsoft.com/en-us/sysinternals/downloads/pstools) (or `psinfo` in particular) use, but using event ID 6005 really doesn't strike me as a very prudent course of action.  
>   
> And the error being thrown is also rather generic for an error that could _reasonably_ occur weeks or months into a given boot session. And what's more, running a program with such a widely used library may succeed now, but after the event log cycles or I clear it, the program just starts to fail with a rather unspecific error message.  
>   
  
The big question is, and I don't know what the Boost project aims for in that regard, whether this should work across projects using different settings. ~~Because as nice as `GetTickCount64` is, it doesn't give you the number of seconds since Unix epoch.~~ (edit: just noticed your subtraction!)

---

## Comment 2

> Username: kkumar45  
> Created at: 2020-06-04 03:42:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/96#issuecomment-638584022  

@assarbad Thanks..   
Recently I ran into another problem with Boost interprocess. Boost provides 3 different MACRO's which can be used to calculate the bootup time on Windows (needed to create the shared folder).   
  
Let's consider a situation of two different application using different macros, their bootup time is different and thus it results in the deletions of shared folder created by the first application since the bootup time of second application is different from first.   
  
There is a logic in Boost which checks and deletes all other folder if they don't match the most recently calculated bootup time.   
  
File : shared_dir_helpers.hpp  
  
```  
// Get the shared directory name by calculating bootup time  
get_shared_dir(shared_dir);      
  
//This function erases all the subdirectories of a directory except the one pointed by "subdir.c_str()"  
delete_subdirectories(root_shared_dir, subdir.c_str());     
```   
  
Thus it deletes the shared dir created by the first application which looks to be a bug here.
