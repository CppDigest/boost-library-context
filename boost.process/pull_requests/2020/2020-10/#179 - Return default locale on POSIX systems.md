# #179 Return default locale on POSIX systems [Merged]

> Username: nre-ableton  
> Created at: 2020-10-16 08:07:39 UTC  
> Updated at: 2020-10-29 17:24:16 UTC  
> Merged at: 2020-10-29 17:17:35 UTC  
> Closed at: 2020-10-29 17:17:35 UTC  
> Url: https://github.com/boostorg/process/pull/179  

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
> Created_at: 2020-10-16 08:07:49 UTC  
> Url: https://github.com/boostorg/process/pull/179#issuecomment-709895198  

See also: https://github.com/boostorg/filesystem/pull/163

---
