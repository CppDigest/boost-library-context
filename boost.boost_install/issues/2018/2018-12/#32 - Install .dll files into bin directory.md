# #32 - Install .dll files into bin directory [Open]

> Username: markand  
> Created at: 2018-12-06 08:03:57 UTC  
> Updated at: 2020-03-01 18:07:11 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32  

Hello,  
  
On Windows, .dll files are installed in the lib/ directory under the prefix. This is quite awkward and should be installed into bin/ directory as many other projects and conventions do.  
  
Thus, user that adds PATH to a common prefix + bin/ will have runtime dependencies correctly.

---

## Comment 1

> Username: komalbharadiya  
> Created at: 2018-12-18 12:02:40 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448197713  

Hey @markand , I am Komal from Pune, India. I'm studying 4th year of engineering. I really want to work on this issue. But since, I am totally novice to open source, could you help me? How do I go about this issue?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-12-19 00:40:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448426117  

@swatanabe What do you think about this one? I noticed that under Cygwin the DLLs are actually installed in the --bindir and did wonder briefly why we don't do this for "ordinary" Windows.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-12-19 01:14:48 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448432927  

AMDG  
  
On 12/18/2018 05:40 PM, Peter Dimov wrote:  
> @swatanabe What do you think about this one? I noticed that under Cygwin the DLLs are actually installed in the --bindir and did wonder briefly why we don't do this for "ordinary" Windows.  
>   
  
bin/ is probably better, but I'm uncertain  
whether it's worth a breaking change at this  
point.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created at: 2018-12-19 03:40:05 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448459011  

I looked at the history, and Cygwin was changed in https://github.com/boostorg/build/commit/fe12dc59bb8627eeb33a66ad9efc61cff520fae0 by @vprus, but it's not clear why Windows hasn't been. It definitely sounds like the right thing to do, while we're at it.

---

## Comment 5

> Username: markand  
> Created at: 2018-12-19 15:34:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448637573  

I'm not sure if this is a real breaking change. The .dll files are never implied in build process, only users are responsible of updating their PATH or copy .dll once packaging. Thus this is not a big deal IMHO.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-12-19 16:08:30 UTC  
> Url: https://github.com/boostorg/boost_install/issues/32#issuecomment-448650064  

We could in principle add `--dlldir` that defaults to `--bindir` so that there's a way to get the old behavior.
