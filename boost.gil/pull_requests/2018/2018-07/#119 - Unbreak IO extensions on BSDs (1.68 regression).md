# #119 Unbreak IO extensions on BSDs (1.68 regression) [Merged]

> Username: jbeich  
> Created at: 2018-07-20 14:20:30 UTC  
> Updated at: 2018-07-24 22:15:21 UTC  
> Merged at: 2018-07-24 08:59:46 UTC  
> Closed at: 2018-07-24 08:59:46 UTC  
> Url: https://github.com/boostorg/gil/pull/119  

`alloca` is defined in `<stdlib.h>` even on [Linux](http://man7.org/linux/man-pages/man3/alloca.3.html) (see **Notes on the GNU version**). [FreeBSD](https://github.com/freebsd/freebsd/commit/2ffad81b1757554bf9413d11af0e92c93a94575b), [DragonFly](https://github.com/DragonFlyBSD/DragonFlyBSD/commit/02b66c54cac986a0bf93435b8d5ae1b17521515b), [OpenBSD](https://github.com/openbsd/src/commit/d88f57029e5acaaaf028633c7fa15c5d7325c5cc) don't support `<malloc.h>` while GNU libc uses `<malloc.h>` for non-standard extensions: `malloc_usable_size`, `mallinfo`, `memalign`, `pvalloc`, etc. FreeBSD (but not other BSDs) also supports non-standard extensions: `malloc_usable_size`, `mallctl*`, `*allocx`, etc.  
  
```c++  
$ echo '#include <boost/gil/extension/io/png/read.hpp>' | c++ -xc++ - -isystem/usr/local/include  
In file included from <stdin>:1:  
In file included from /usr/local/include/boost/gil/extension/io/png/read.hpp:29:  
In file included from /usr/local/include/boost/gil/io/get_reader.hpp:22:  
In file included from /usr/local/include/boost/gil/io/get_read_device.hpp:26:  
In file included from /usr/local/include/boost/gil/io/path_spec.hpp:23:  
/usr/include/malloc.h:3:2: error: "<malloc.h> has been replaced by <stdlib.h>"  
#error "<malloc.h> has been replaced by <stdlib.h>"  
 ^  
1 error generated.  
```

---

## Comment 1

> Username: jbeich  
> Created_at: 2018-07-20 14:29:08 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-406617650  

Windows [appears](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/alloca) to have `_alloca` in `<malloc.h>`. I wonder if `alloca` is a compiler builtin instead.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2018-07-23 21:22:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/119#pullrequestreview-139659426  

LGTM

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-07-24 09:00:46 UTC  
> Updated_at: 2018-07-24 09:01:19 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-407334491  

@jbeich Thanks!  
  
Next, we shall watch the regression tests if this does not cause any issues  
www.boost.org/development/tests/develop/developer/gil.html  
  
Once that's confirmed, we may still have time to merge it into master for release with Boost 1.68 (/cc @stefanseefeld )

---

## Comment 4

> Username: jbeich  
> Created_at: 2018-07-24 18:29:03 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-407506723  

> http://www.boost.org/development/tests/develop/developer/gil.html  
  
How to filter out expected (i.e., pre-existing) fallout? Some `cerr` links even go nowhere: "Error extracting file: Error code 11 - No matching files were found." And it doesn't look like `develop` can be replaced with arbitrary commit in order to track when the fallout was introduced.

---

## Comment 5

> Username: jbeich  
> Created_at: 2018-07-24 18:51:04 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-407513575  

Ah, this is hilarious. https://www.boost.org/development/tests/develop/developer/gil-io.html has been showing bustage on FreeBSD but no one payed an attention.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-07-24 18:52:53 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-407514154  

or to be a little less harsh: no-one with either FreeBSD experience or a suitable system to test locally is available. Read: your contributions would be very much appreciated ! :-)

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-07-24 22:15:21 UTC  
> Url: https://github.com/boostorg/gil/pull/119#issuecomment-407569765  

Indeed, in a team of three, there is never enough man power!  
  
@jbeich I confess, proposing to watch the regression tests, I failed to check them myself.

---
