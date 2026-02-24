# #43 - Feature Request: support for stat? [Closed]

> Username: thehans  
> Created at: 2019-02-21 03:11:49 UTC  
> Updated at: 2020-07-06 15:12:06 UTC  
> Closed at: 2020-06-18 15:58:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/43  

Do you think it would be within scope of this project to support stat such that utf8 strings can be passed to it and it would call wstat with the proper utf8->utf16 conversion underneath?

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-12-13 18:51:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-565562788  

AFAIK `stat` is a POSIX-only function and I don't know of any `wstat` function. Can you point me to the documentation of it if there is one? I think it does not apply to Windows as the return value of `stat` is very POSIX specific.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-12-13 19:00:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-565566461  

`_wstat`, https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/stat-functions?view=vs-2019

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-12-13 19:16:49 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-565572485  

This is mainly MSVC only, isn't it? What to do for others? I guess it will be a trial-and-error. Well, if we put it in an extra header, why not?

---

## Comment 4

> Username: Berrysoft  
> Created at: 2020-05-22 14:58:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-632737779  

> This is mainly MSVC only, isn't it?  
  
No, it's part of CRT of Windows, which means MinGW can also get use of it.

---

## Comment 5

> Username: Flamefire  
> Created at: 2020-06-16 07:22:38 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-644583376  

I looked into this and found the following which needs to be addressed:  
- `stat` is available on non-Windows even with `-std=c++11` set -> OK  
- As with other `w*` functions in cygwin/mingw I expect them to be not available for `-std=c++xx` which means they need to be defined in a CPP file  
- `stat` on non-Windows may report sizes in 32bit types on 32bit systems and set an error on overflow  
- `stat` on Windows uses a 64 or 32 bit time field depending on a macro and a 32bit file size. There is no indication that an error will be set for large files  
- minor: `wstat` has an issue with Windows Vista and symbolic links which is not the case for `stat` --> WTF?  
  
So in conclusion:  
- On Linux we may or may not get 64bit types but have an error set when a to-large-file is `stat`ed -> OK  
- On Windows `wstat` needs to be called in a cpp file but the stat-struct may change size depending on defines  
  
--> Proposal: Define a typedef `boost::nowide::_stat` which points to `struct _stat` on Linux and `_stat64` on Windows and use `_wstat64` to query the information. This way the type doesn't change the size.  
  
It is also possible to provide an overload which accepts a `struct _stat` and internally passes it's size to the cpp file and return an error if the size mismatches. This allows to detect differing defines (`_USE_32BIT_TIME_T`) but may silently report wrong sizes for large files.   
  
PS: Does anyone else want a different struct name for the data returned which does not clash with the function name?

---

## Comment 6

> Username: Flamefire  
> Created at: 2020-06-16 18:00:21 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-644920098  

@thehans I'd like to invite you (and everyone else) to take a look at #108 which implements this. Check if it suits your needs.  
  
Thanks!

---

## Comment 7

> Username: thehans  
> Created at: 2020-06-19 00:19:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-646368013  

@Flamefire Thank you very much for implementing this, and sorry I wasn't able to provide any timely feedback.    
When I opened this feature request, I was working on adding nowide to an existing project, and never quite finished that PR (only partly because of wanting `stat`).  I'm still interested to try it out, and that PR is still open and waiting for me to update and finalize, just not sure when I'll have time to get back into it.  
  
And congratulations on being added to official Boost releases! :tada:

---

## Comment 8

> Username: Flamefire  
> Created at: 2020-07-06 15:12:06 UTC  
> Url: https://github.com/boostorg/nowide/issues/43#issuecomment-654298099  

@thehans All good. The upcoming Boost release 1.74 will contain the feature and as usual you can try it out already by using this git repository directly with and without boost. So feel free to test it and if you notice any problems please open a new issue here
