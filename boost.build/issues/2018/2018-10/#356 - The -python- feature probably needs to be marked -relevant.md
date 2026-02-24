# #356 - The <python> feature probably needs to be marked "relevant" [Closed]

> Username: pdimov  
> Created at: 2018-10-13 17:05:13 UTC  
> Updated at: 2019-04-01 18:04:25 UTC  
> Closed at: 2019-04-01 18:04:25 UTC  
> Url: https://github.com/boostorg/build/issues/356  

When I build boost_python with `python=3.3`, and then try to build it with `python=2.7`, I get this:  
  
```  
msvc.link.dll ..\..\bin.v2\libs\python\build\msvc-14.1\debug\threading-multi\boost_python27-vc141-mt-gd-x32-1_69.dll  
LINK : fatal error LNK1104: cannot open file 'python33.lib'  
```  
  
The problem here is that the sources aren't recompiled, it goes straight to the link step. `python33.lib` comes from an autolink pragma in `py_config.h`. Since the sources are the previously compiled ones, using the 3.3 pyconfig, they try to autolink to python33.lib.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-13 17:15:49 UTC  
> Url: https://github.com/boostorg/build/issues/356#issuecomment-429559104  

AMDG  
  
On 10/13/2018 11:05 AM, Peter Dimov wrote:  
> When I build boost_python with `python=3.3`, and then try to build it with `python=2.7`, I get this:  
>   
> ```  
> msvc.link.dll ..\..\bin.v2\libs\python\build\msvc-14.1\debug\threading-multi\boost_python27-vc141-mt-gd-x32-1_69.dll  
> LINK : fatal error LNK1104: cannot open file 'python33.lib'  
> ```  
>   
> The problem here is that the sources aren't recompiled, it goes straight to the link step. `python33.lib` comes from an autolink pragma in `py_config.h`. Since the sources are the previously compiled ones, using the 3.3 pyconfig, they try to autolink to python33.lib.  
>   
  
What's supposed to happen is:  
- boost_python depends on /python//python_for_extensions  
- python_for_extensions has one target alternative for each  
  version of python.  
- Any feature used to choose a target alternative is relevant.  
  
So the question is: why isn't it working?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-13 17:21:04 UTC  
> Url: https://github.com/boostorg/build/issues/356#issuecomment-429559499  

It's more complicated than I thought. In fact, when I supply `python=2.7` and then `python=3.3`, it works and the sources go into the subdirectory `python-X.Y` as intended.  
  
When the feature is not supplied though, the sources don't get a `python-X.Y` subdirectory. So when one run autodetects 3.3, and then one changes the configuration and the second run either autodetects 2.7 (or 2.7 is explicitly given but is considered default? not sure), then things seem to get mixed up.  
  
It's not clear if this corner case is worth looking into.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-10-13 17:36:00 UTC  
> Url: https://github.com/boostorg/build/issues/356#issuecomment-429560546  

AMDG  
  
On 10/13/2018 11:21 AM, Peter Dimov wrote:  
> It's more complicated than I thought. In fact, when I supply `python=2.7` and then `python=3.3`, it works and the sources go into the subdirectory `python-X.Y` as intended.  
>   
> When the feature is not supplied though, the sources don't get a `python-X.Y` subdirectory. So when one run autodetects 3.3, and then one changes the configuration and the second run either autodetects 2.7 (or 2.7 is explicitly given but is considered default? not sure), then things seem to get mixed up.  
>   
  
Ah, I see.  
  
> It's not clear if this corner case is worth looking into.  
>   
  
It's easy enough to make it work, if we think it's  
the right behavior:  
- make <python> symmetric so it appears even if it is  
  the default.  
- mark <python> as relevant for the /python//python  
  and /python//python_for_extensions, so it appears  
  even if there is only one alternative.  
  
The result is to make the paths for one python version  
invariant regardless of whether other python versions  
are present.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-13 17:47:25 UTC  
> Url: https://github.com/boostorg/build/issues/356#issuecomment-429561413  

Both of these changes seem entirely correct to me.
