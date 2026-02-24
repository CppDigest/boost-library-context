# #30 - Boost Log will not compile on Android [Closed]

> Username: toonetown  
> Created at: 2016-09-29 20:52:21 UTC  
> Updated at: 2016-09-29 23:18:40 UTC  
> Closed at: 2016-09-29 23:18:40 UTC  
> Url: https://github.com/boostorg/log/issues/30  

Version 1.62.0 will not compile on android due to the fact that the android bionic runtime does not have a `getsid` function.  This is referenced at https://github.com/boostorg/log/blob/boost-1.62.0/src/posix/object_name.cpp#L71   
  
I am willing to work up a PR and submit it - but am wondering what the best approach to do for android would be.  Should I `#ifndef` just the `format_id` call there - and use something else instead of `getsid`?  Or should I `#ifndef` the access to `object_name::session` altogether so that scope isn't even available?  Or is there some other approach that would be better (I don't believe there is an alternative to `getsid` on android - you just aren't allowed to access it at all).

---

## Comment 1

> Username: Lastique  
> Created at: 2016-09-29 21:08:07 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250591835  

On 09/29/16 23:52, Nathan Toone wrote:  
  
> Version 1.62.0 will not compile on android due to the fact that the  
> android bionic runtime does not have a |getsid| function. This is  
> referenced at  
> https://github.com/boostorg/log/blob/boost-1.62.0/src/posix/object_name.cpp#L71  
>   
> I am willing to work up a PR and submit it - but am wondering what the  
> best approach to do for android would be. Should I |#ifndef| just the  
> |format_id| call there - and use something else instead of |getsid|? Or  
> should I |#ifndef| the access to |object_name::session| altogether so  
> that scope isn't even available? Or is there some other approach that  
> would be better (I don't believe there is an alternative to |getsid| on  
> android - you just aren't allowed to access it at all).  
  
I don't know Android well enough to pick the correct solution. Ideally,   
`getsid` should be replaced with some other API on that platform having   
a similar effect. I know at least some Android versions support multiple   
user logins - there must be a way to discern one session from the other.   
If not, then we can only pretend there is only one session system-wide   
and all processes are its members.  
  
PS: A quick search shows that there is the __NR_getsid syscall[1](https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/arch-x86/syscalls/getsid.S). I   
suspect, it's the libc wrapper that is missing on some SDK versions.   
Which one are you using?

---

## Comment 2

> Username: toonetown  
> Created at: 2016-09-29 21:19:08 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250594546  

> PS: A quick search shows that there is the __NR_getsid syscall[1](https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/include/unistd.h#195). I   
> suspect, it's the libc wrapper that is missing on some SDK versions.   
> Which one are you using?  
>   
> [1](https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/include/unistd.h#195):   
> https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/arch-x86/syscalls/getsid.S  
  
And I don't know enough about syscalls to know if that will work or not. All I know is that the header explicitly mentions some functions that are disabled in bionic[1](https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/include/unistd.h#195).  The version I am using is `android-15`, but a quick `grep` through the android headers seems to show the same functions disabled in all the other versions as well.   
  
I don't know what alternate function would be useful either...what with not being super familiar with `getsid` in the first place.   
  
What about writing an inline function that just returns `-1`, like described in the man page[2](https://www.linux.com/manpage/man2/getsid.2.html)?

---

## Comment 3

> Username: Lastique  
> Created at: 2016-09-29 22:21:04 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250608514  

On 09/30/16 00:19, Nathan Toone wrote:  
  
> > PS: A quick search shows that there is the __NR_getsid syscall[1]. I  
> > suspect, it's the libc wrapper that is missing on some SDK versions.  
> > Which one are you using?  
> >   
> > [1]:  
> >   
> > https://android.googlesource.com/platform/bionic/+/1ad10a5/libc/arch-x86/syscalls/getsid.S  
>   
> And I don't know enough about syscalls to know if that will work or not.  
> All I know is that the header explicitly mentions some functions that  
> are disabled in bionic[1]. The version I am using is `android-15`, but a  
> quick `grep` through the android headers seems to show the same  
> functions disabled in all the other versions as well.  
  
I've committed a workaround (93a063c) but I can't test it. Could you try   
it on your setup?

---

## Comment 4

> Username: toonetown  
> Created at: 2016-09-29 22:41:43 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250612968  

Getting there...now the same thing is happening with `getpwuid_r` (it appears in the headers in android API 21 as well, but is commented out prior to that version).  
  
Maybe I'll just build against android-21.  😄

---

## Comment 5

> Username: toonetown  
> Created at: 2016-09-29 22:55:30 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250615470  

It will build against android-21 - however, it would be good to support a few versions back than that.  
  
`getpwuid_r` doesn't exist as a syscall either...

---

## Comment 6

> Username: Lastique  
> Created at: 2016-09-29 22:59:45 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250616257  

On 09/30/16 01:55, Nathan Toone wrote:  
  
> It will build against android-21 - however, it would be good to support  
> a few versions back than that.  
>   
> |getpwuid_r| doesn't exist as a syscall either...  
  
Yes, `getpwuid_r` is not that easy to work around. There is `getpwuid`,   
but it's not thread-safe, at least not according to POSIX. I don't know   
how safe it is on Android.

---

## Comment 7

> Username: toonetown  
> Created at: 2016-09-29 23:02:46 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250616713  

for `getpwuid_r`, couldn't we add a `#define BOOST_LOG_ANDROID_SINGLE_USER` in the block you just added, and then only do `getpwuid_r` when not defined (falling back to only running the `else` block at https://github.com/boostorg/log/blob/develop/src/posix/object_name.cpp#L105 when `BOOST_LOG_ANDROID_SINGLE_USER` is defined)?

---

## Comment 8

> Username: toonetown  
> Created at: 2016-09-29 23:11:21 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250618007  

An example patch which actually will compile and appears to work can be found at https://github.com/toonetown/boostorg-log/commit/4d41ff0829fcae5488b6aac9e5935589832aba57

---

## Comment 9

> Username: Lastique  
> Created at: 2016-09-29 23:13:30 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250618377  

On 09/30/16 01:59, Andrey Semashev wrote:  
  
> On 09/30/16 01:55, Nathan Toone wrote:  
>   
> > It will build against android-21 - however, it would be good to support  
> > a few versions back than that.  
> >   
> > |getpwuid_r| doesn't exist as a syscall either...  
>   
> Yes, `getpwuid_r` is not that easy to work around. There is `getpwuid`,  
> but it's not thread-safe, at least not according to POSIX. I don't know  
> how safe it is on Android.  
  
I've added another workaround in (d13e6cb). Please, try if it helps.

---

## Comment 10

> Username: Lastique  
> Created at: 2016-09-29 23:17:47 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250619081  

On 09/30/16 02:11, Nathan Toone wrote:  
  
> An example patch which actually will compile and appears to work can be  
> found at toonetown@4d41ff0  
> https://github.com/toonetown/boostorg-log/commit/4d41ff0829fcae5488b6aac9e5935589832aba57  
  
Yes, that's basically what I did as well.

---

## Comment 11

> Username: toonetown  
> Created at: 2016-09-29 23:18:40 UTC  
> Url: https://github.com/boostorg/log/issues/30#issuecomment-250619241  

Yes - your fix appears to work now.  Thanks!  Sorry I wasn't more help... 😉
