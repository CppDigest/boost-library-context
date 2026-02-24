# #163 Return default locale on POSIX systems [Closed]

> Username: nre-ableton  
> Created at: 2020-10-16 08:04:39 UTC  
> Updated at: 2021-02-26 23:19:37 UTC  
> Closed at: 2021-02-26 17:44:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163  

The comment here is incorrect; an empty locale is not the correct  
behavior here and will cause exceptions to be thrown in other code.  
Instead a locale object with the default constructor should be  
returned, not one created with an empty string. This is a known issue  
which was originally reported here:  
  
https://svn.boost.org/trac10/ticket/4688  
  
The issue claims to have been fixed in 7bb19f9 (see also:  
https://svn.boost.org/trac10/changeset/72855).  
  
However, this only fixes the issue for FreeBSD and not other POSIX  
platforms. This patch is based on the one originally submitted here:  
  
https://svn.boost.org/trac10/attachment/ticket/4688/boost_filesystem.patch

---

## Comment 1

> Username: nre-ableton  
> Created_at: 2020-10-16 08:07:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163#issuecomment-709895262  

See also: https://github.com/boostorg/process/pull/179

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-02-26 12:03:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163#issuecomment-786606428  

The C++ standard explicitly [lists](http://eel.is/c++draft/locale.cons#4) "" as one of the valid locale names. The tests run without errors on Linux systems both locally and the CI. Are you having a problem on a specific system? What system and what compiler?

---

## Comment 3

> Username: nre-ableton  
> Created_at: 2021-02-26 17:16:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163#issuecomment-786778970  

@Lastique it's unfortunately been awhile since I have worked on this code, so I don't remember what the exact issue was. As close as I can remember, it was not a compiler error but a runtime error with the application, which crashed when it was launched.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-02-26 17:44:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163#issuecomment-786794483  

I think the current code is correct. Default-constructed `std::locale` object is not necessarily the environment locale. It is created as a copy of the locale set by `std::locale::global()`, which by default is the same as the environment locale, but can be overridden by the user. The empty locale name explicitly requests the environment locale, not global. Creating such a locale must succeed. If a standard library rejects it then this is either a bug in the standard library or somehow weirdly misconfigured system locale.  
  
If the problem reproduces, a patch that works around the bug for a particular C++ standard library and target system might be useful. I'll emphasize that this *may* be acceptable only as a workaround for a particular C++ standard library bug.

---

## Comment 5

> Username: nre-ableton  
> Created_at: 2021-02-26 23:19:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/163#issuecomment-786945720  

@Lastique fair enough, thanks for looking at this patch!

---
