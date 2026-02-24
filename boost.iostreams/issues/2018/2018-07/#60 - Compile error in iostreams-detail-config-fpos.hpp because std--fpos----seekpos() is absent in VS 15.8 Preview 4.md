# #60 - Compile error in iostreams\detail\config\fpos.hpp because std::fpos<>::seekpos() is absent in VS 15.8 Preview 4 [Closed]

> Username: k15tfu  
> Created at: 2018-07-17 11:49:52 UTC  
> Updated at: 2022-04-25 15:51:33 UTC  
> Closed at: 2018-08-30 08:35:49 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60  

```  
packages\jetbrains.boost.include.1.66.0.1\build\native\include\boost\iostreams\positioning.hpp(96): error C2039: 'seekpos': is not a member of 'std::fpos<_Mbstatet>'  
1>c:\program files (x86)\microsoft visual studio\preview\enterprise\vc\tools\msvc\14.15.26629\include\iosfwd(132): note: see declaration of 'std::fpos<_Mbstatet>'  
```  
  
p.s. BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS is defined

---

## Comment 1

> Username: jeking3  
> Created at: 2018-07-30 21:58:22 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-409025318  

Yes, the MSVC C++ RTL is Dinkumware so that makes sense.  
Perhaps seekpos was deprecated (I haven't checked) and since removed?  
Or is this a defect in the RTL?

---

## Comment 2

> Username: Wedge009  
> Created at: 2018-08-17 03:17:11 UTC  
> Updated at: 2018-08-17 03:27:20 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-413746059  

MSVS 15.8 was released a couple of days ago. The updated MSVC tools now deprecates seekpos with the following message:  
  
> warning STL4019: The member std::fpos::seekpos() is non-Standard, and is preserved only for compatibility with workarounds for old versions of Visual C++. It will be removed in a future release, and in this release always returns 0. Please use standards-conforming mechanisms to manipulate fpos, such as conversions to and from streamoff, or an integral type, instead. If you are receiving this message while compiling Boost.IOStreams, a fix has been submitted upstream to make Boost use standards-conforming mechanisms, as it does for other compilers. You can define _SILENCE_FPOS_SEEKPOS_DEPRECATION_WARNING to acknowledge that you have received this warning, or define _REMOVE_FPOS_SEEKPOS to remove std::fpos::seekpos entirely.  
  
~~I don't know where this fix submission is right now, I'd be interested to see what Microsoft's correction is.~~  
  
Edit: It looks like this is resolved with #57, but it's not yet included in a boost release.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-08-19 14:48:18 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-414132731  

It seems worse... when I examine the history I can't find the merged commit in develop.  
  
jking@ubuntu:~/boost/libs/date_time$ git show a0c32babd2f491163b9876a35d2f778bb43e5e01  
fatal: bad object a0c32babd2f491163b9876a35d2f778bb43e5e01  
  
I will have to investigate..

---

## Comment 4

> Username: k15tfu  
> Created at: 2018-08-19 15:10:10 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-414134102  

James, check in the iostreams directory, it's a different repo.  
  
On Sun, Aug 19, 2018, 17:48 James E. King III <notifications@github.com>  
wrote:  
  
> It seems worse... when I examine the history I can't find the merged  
> commit in develop.  
>  
> jking@ubuntu:~/boost/libs/date_time$ git show a0c32ba  
> <https://github.com/boostorg/iostreams/commit/a0c32babd2f491163b9876a35d2f778bb43e5e01>  
> fatal: bad object a0c32ba  
> <https://github.com/boostorg/iostreams/commit/a0c32babd2f491163b9876a35d2f778bb43e5e01>  
>  
> I will have to investigate..  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/iostreams/issues/60#issuecomment-414132731>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA7womyVUeHxDnneM9rp5nJcfZI0Leahks5uSXqygaJpZM4VSsu4>  
> .  
>

---

## Comment 5

> Username: k15tfu  
> Created at: 2018-08-24 09:05:48 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-415700016  

> Edit: It looks like this is resolved with #57, but it's not yet included in a boost release.  
  
Does anybody know why the April's fix wasn't included into release in August?

---

## Comment 6

> Username: jeking3  
> Created at: 2018-08-29 02:31:28 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-416803189  

@k15tfu this library is maintained by the Community Maintenance Team and nobody merged develop into master for the release cycle.  I can say there's a combination of lack of people to do the work and lack of tools to track the work that needs doing.  Given we have 15+ repositories to monitor, and no tools to show when master is out of sync with respect to develop, sometimes things like this fall through the cracks.  It will be in the next release as it's been merged to master.

---

## Comment 7

> Username: mwpowellhtx  
> Created at: 2018-10-30 23:33:03 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-434508965  

Just bumped into this nasty bug myself... Is there a workaround? I'll examine the history here... Thanks!

---

## Comment 8

> Username: mwpowellhtx  
> Created at: 2018-10-30 23:37:44 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-434509913  

Just an aside, since y'all adopted Git and did your thing to separate out the monolithic repositories, it's been one snafu after another it seems, missed updates, etc. It would seem to me that y'all should make it a top priority to make sure the repos are always in a presentable state for the build maintainers.

---

## Comment 9

> Username: mwpowellhtx  
> Created at: 2018-10-30 23:54:19 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-434512996  

See comment re: [iostreams/positioning.hpp](/boostorg/iostreams/pull/57#issuecomment-434512644).

---

## Comment 10

> Username: StevePanda  
> Created at: 2022-04-25 15:51:33 UTC  
> Url: https://github.com/boostorg/iostreams/issues/60#issuecomment-1108748904  

This bug is still present when building using the Intel compilers on Windows (which use the MSVC runtimes). It looks like BOOST_MSVC gets unset when using these, so the check in https://github.com/boostorg/iostreams/pull/57 still goes the wrong way. Probably need to add BOOST_INTEL_WIN in the same way BOOST_CLANG was added in https://github.com/boostorg/iostreams/pull/77.
