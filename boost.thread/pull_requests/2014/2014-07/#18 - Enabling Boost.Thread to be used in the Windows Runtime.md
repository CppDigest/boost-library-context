# #18 [winrt support] Enabling Boost.Thread to be used in the Windows Runtime. [Merged]

> Username: stgates  
> Created at: 2014-07-02 21:28:51 UTC  
> Updated at: 2015-04-27 17:09:18 UTC  
> Merged at: 2014-08-30 17:24:57 UTC  
> Closed at: 2014-08-30 17:24:57 UTC  
> Url: https://github.com/boostorg/thread/pull/18  

This involves basically 3 changes:  
1. Using __declspec(thread) instead of the Tls APIs.  
2. Using Windows::System::Threading since Win32 Threading APIs aren't allowed.  
3. Updating or replacing some banned APIs like WaitForSingleObject with WaitForSingleObjectEx.

---

## Comment 1

> Username: ned14  
> Created_at: 2014-07-03 10:38:01 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-47891204  

I'll try to review this this weekend.

---

## Comment 2

> Username: ned14  
> Created_at: 2014-07-03 10:51:11 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-47892230  

BTW do you have a method for building on WinRT? i.e. have you patched Boost.Build for it?

---

## Comment 3

> Username: stgates  
> Created_at: 2014-07-03 16:32:54 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-47953180  

Yes I have a pending pull request (https://github.com/boostorg/build/pull/14) on Boost.Build for targeting Windows Store and Windows Phone.  
  
Thanks for taking a look,  
Steve

---

## Comment 4

> Username: ned14  
> Created_at: 2014-07-05 14:06:47 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571282  

The (C) Microsoft may cause problems for the steering committee. As you will note throughout all Boost code, we don't copyright non-individuals, what happens is that the individual is working for whatever corporate employer but gets approval to personally take the copyright. Would Microsoft be able to give you personally the copyright? I know Eric Niebler used to work for Microsoft, he may be able to advise on what Microsoft channels to use.

---

## Comment 5

> Username: ned14  
> Created_at: 2014-07-05 14:09:08 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571294  

All BOOST_PLAT_WINDOWS_RUNTIME ifdefs need to be removed in favour of adjusting the code to support all platforms. I personally don't want to see a BOOST_PLAT_WINDOWS_RUNTIME unless there is really no possible choice. Here, for example, you can safely have a single start_thread_noexcept() with attributes parameter.

---

## Comment 6

> Username: ned14  
> Created_at: 2014-07-05 14:12:54 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571307  

This isn't how we support WinXP in Boost.Thread. Have a look at include/boost/thread/win32/thread_primitives.hpp where you'll see we use GetProcAddress to query KERNEL32.dll for whether some API is present.

---

## Comment 7

> Username: ned14  
> Created_at: 2014-07-05 14:14:09 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571310  

Actually I've just realised you're testing for before XP. You don't need to, you can assume a minimum of WinXP.

---

## Comment 8

> Username: ned14  
> Created_at: 2014-07-05 14:19:08 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571320  

I'd _really_ prefer to see these ifdef's go away. Would it be possible that on WinRT you create emulations of CreateEventX using CreateEventExX? Put them into that platform abstraction header. That removes a lot of the source file churn.

---

## Comment 9

> Username: ned14  
> Created_at: 2014-07-05 14:21:24 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571330  

You've just reminded me about a patch implementing GetTickCount64 on WinXP using atomics which I have forgotten about. It lives at https://github.com/boostorg/thread/pull/8 and I'll get it fixed up after I finish this review, hopefully we'll have it into HEAD soon so you can work from it.

---

## Comment 10

> Username: ned14  
> Created_at: 2014-07-05 14:27:14 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571345  

I am right in thinking that the WRL works in normal desktop Win8 too? If so, can you replace the ifdefs for this stuff with an environment variable based runtime enabler? The reason why is because I'd like the WRL functionality being regularly unit tested, and my Windows CI here is normal Win8.1 desktop, and we already dump out enough alternative binaries for Boost.Thread on Windows to the point it already confuses end users.

---

## Comment 11

> Username: ned14  
> Created_at: 2014-07-05 14:28:09 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14571348  

Again, an emulation here would be nicer.

---

## Comment 12

> Username: ned14  
> Created_at: 2014-07-05 14:30:27 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-48087761  

If you can clean up the ifdef splosh significantly, this patch looks to be of good enough quality it can be merged. Thanks for taking the effort to submit it.

---

## Comment 13

> Username: stgates  
> Created_at: 2014-07-09 05:12:57 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14693817  

Yes the WRL threading done here could be used on Windows 8 desktop as well, however there still would have to be different binaries produced for Windows store and phone. Reasons being different libs, like the CRT, are linked against and the /APPCONTAINER requirement. Also environment variables aren't available in the Windows Runtime.  
  
The best that I can think of is using a macro that can be used to turn on/off to utilize WRL instead of the Win32 APIs. This would always be on for Windows store and phone, and optionally could be turned on for desktop (only will work on Windows 8.0 and later).

---

## Comment 14

> Username: ned14  
> Created_at: 2014-07-09 10:07:28 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14702004  

Yet more binary variants for Windows! :( We actually have a magic build flag now for building all the variants, we normally only produce a subset. It should be easy enough to fold the RT binaries in those though, assuming Boost.Build merges RT build support sometime soon (probably after the 1.56 release).

---

## Comment 15

> Username: stgates  
> Created_at: 2014-07-09 22:52:05 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741266  

I talked again with my legal group and they've agreed to allow the contributions without the Microsoft copyright :). So I removed them from all the source files.

---

## Comment 16

> Username: stgates  
> Created_at: 2014-07-09 22:52:50 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741292  

I updated to not remove the attributes overload version, it is now just silently ignored if you use on the Windows Runtime.

---

## Comment 17

> Username: stgates  
> Created_at: 2014-07-09 22:53:38 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741326  

Yes this is awesome to hear! Wish I had known before I went editing them all originally :). All the 'before XP' locations have just had their APIs upgraded.

---

## Comment 18

> Username: stgates  
> Created_at: 2014-07-09 22:54:05 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741343  

Done. I also put a platform abstraction for some of the other APIs like Sleep.

---

## Comment 19

> Username: stgates  
> Created_at: 2014-07-09 22:54:53 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741369  

I created an abstraction for GetSystemInfo and GetNativeSystemInfo.

---

## Comment 20

> Username: stgates  
> Created_at: 2014-07-09 22:56:37 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-48545791  

Hi Niall,  
  
I think I've addressed all of our comments now. Let me know if you have any additional feedback.  
  
Thanks,  
Steve

---

## Comment 21

> Username: ned14  
> Created_at: 2014-07-09 23:07:30 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741852  

I assume this reorder is due to a bad find in files and replace?

---

## Comment 22

> Username: stgates  
> Created_at: 2014-07-09 23:09:14 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14741919  

I'm not exactly sure where from but I can fix it back up.

---

## Comment 23

> Username: ned14  
> Created_at: 2014-07-09 23:13:30 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742064  

That patch is now at #20 and once Vicente reviews it should be into HEAD. You may need to adjust this slightly for it.

---

## Comment 24

> Username: ned14  
> Created_at: 2014-07-09 23:16:02 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742165  

Is there any good reason why we don't use the WinRT implementation here as standard? I assume the non use of __declspec(thread) is because of older MSVCs, but we now have a minimum compiler of I think VS2005? I think VS2013 support has been retired anyway.

---

## Comment 25

> Username: ned14  
> Created_at: 2014-07-09 23:22:56 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742449  

A number of copyright headers seem to have become reordered. Weird. Anyway, apart from that this patch looks good enough for me to try building. Unfortunately for you we have the first beta of Boost v1.56 in the works, and this Saturday will be lost on getting my CI soak testing code for 24 hours and other such fun. If no nasty surprises turn up, I'll get to this the following Saturday, but if I miss that date I'm on a business trip the following weekend so it could potentially be a month out before I get back to this. Is this okay?

---

## Comment 26

> Username: stgates  
> Created_at: 2014-07-09 23:27:46 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742654  

Ok when it is in, I'll see if a merge is necessary.

---

## Comment 27

> Username: stgates  
> Created_at: 2014-07-09 23:30:31 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742740  

I was just being careful, but yes if the minimum is VS 2005 we can entirely use __declspec(thread) instead of the TLS APIs directly. I will make this fix.

---

## Comment 28

> Username: stgates  
> Created_at: 2014-07-09 23:33:46 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14742852  

I'll fix up the copyright header ordering.  
  
Greedily from my side the sooner this is in the better :), but I understand. Let me know if I can do anything or you need more information.

---

## Comment 29

> Username: stgates  
> Created_at: 2014-07-10 00:00:09 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14743799  

Actually I took a further look and I don't think __declspec(thread) should be used in all cases. According to msdn on pre-Vista machines a protection fault or corruption can occur.  
  
http://msdn.microsoft.com/en-us/library/2s9wt68x.aspx  
  
I'm also not certain if CE supports or not, I can't seem to find confirmation on msdn. I'm going to leave this as is.

---

## Comment 30

> Username: stgates  
> Created_at: 2014-07-10 00:07:34 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-48551089  

Copyright reordering has been fixed. There is no action on my side now until the GetTickCount64 merges.

---

## Comment 31

> Username: viboes  
> Created_at: 2014-07-10 06:43:14 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14751441  

Thanks Niall for this deep review. Niall, you could merge both pull request once Boost 1.56 will be released.

---

## Comment 32

> Username: ned14  
> Created_at: 2014-07-10 19:13:33 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14786517  

Yep, that's exactly why we don't use __declspec(thread). Roll on the day we can drop XP support - I'm sure your colleagues entirely agree with this sentiment :)

---

## Comment 33

> Username: stgates  
> Created_at: 2014-07-10 20:45:25 UTC  
> Updated_at: 2014-07-18 19:20:30 UTC  
> Url: https://github.com/boostorg/thread/pull/18#discussion_r14792072  

You have no idea :)....

---

## Comment 34

> Username: stgates  
> Created_at: 2014-07-18 19:22:58 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-49469787  

Ok I merged with the GetTickCount changes, basically there is no choice but to use GetTickCount64 for the Windows Runtime.

---

## Comment 35

> Username: ned14  
> Created_at: 2014-07-19 10:40:24 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-49506000  

Yup seems reasonable. Thanks.

---

## Comment 36

> Username: viboes  
> Created_at: 2014-08-23 17:20:53 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-53159334  

Niall, could this PR be merged?

---

## Comment 37

> Username: ned14  
> Created_at: 2014-08-23 17:50:53 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-53160241  

I have testing it scheduled for the coming Saturday, so one week from today. Today was spent upgrading website software for security bugs, and probably so will tomorrow.

---

## Comment 38

> Username: ned14  
> Created_at: 2014-08-30 17:22:10 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-53964736  

I've got this patch compiling, but the tests won't run on my Win8.1 machine (x86). Am I missing something?  
  
```  
b2 libs/thread/test windows-api=store  
  
Performing configuration checks  
  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 12368 targets...  
...updating 1409 targets...  
testing.capture-output bin.v2\libs\thread\test\packaged_task__move_asign_p.test\msvc-12.0\debug\threading-multi\windows-api-store\packaged_task__move_asign_p.run  
====== BEGIN OUTPUT ======  
The system cannot execute the specified program.  
  
EXIT STATUS: 9020   
====== END OUTPUT ======  
  
```

---

## Comment 39

> Username: stgates  
> Created_at: 2014-09-02 17:59:35 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-54191874  

Thanks for merging.  
  
Yes the tests for WinRT can't run yet because they require the pending changes I have on Boost.Build and Boost.Test. With both of those pull requests the tests can be run.  
  
https://github.com/boostorg/build/pull/26  
https://github.com/boostorg/test/pull/9

---

## Comment 40

> Username: ned14  
> Created_at: 2014-09-03 11:31:25 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-54283521  

@stgates I merged the Build changes locally, so the above reflects that. Also, I believe Thread mostly uses the lightweight Boost.Test alternative, and therefore doesn't need Boost.Test.  
  
If you look closely at my build log above, it looks to me that the build outputs binaries which cannot run on a 32-bit Windows 8 machine. Perhaps I need x64?

---

## Comment 41

> Username: jgleitsmann  
> Created_at: 2015-04-24 18:06:55 UTC  
> Updated_at: 2015-04-24 18:25:59 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-96017329  

Would these versions be compatible with or useable for developing an Xbox app, using the DurangoADK, that has legacy C++ code that needs Boost for threads, signals, ...?  
  
If not, are there any alternatives?

---

## Comment 42

> Username: stgates  
> Created_at: 2015-04-27 17:09:18 UTC  
> Url: https://github.com/boostorg/thread/pull/18#issuecomment-96743085  

@jgleitsmann I'm not very familiar for developing for the Xbox, but potentially it could work.

---
