# #123 Add support for FreeBSD 10 or later. [Closed]

> Username: Flast  
> Created at: 2016-02-29 14:01:37 UTC  
> Updated at: 2021-10-02 22:20:06 UTC  
> Closed at: 2016-03-10 06:30:35 UTC  
> Url: https://github.com/boostorg/build/pull/123  

Since FreeBSD 10, it uses clang as a system default compiler.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2016-02-29 15:37:26 UTC  
> Updated_at: 2016-02-29 23:02:49 UTC  
> Url: https://github.com/boostorg/build/pull/123#discussion_r54425998  

Is this is for FreeBSD >=10.. Shouldn't the version also be checked?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2016-02-29 15:46:37 UTC  
> Updated_at: 2016-02-29 23:02:49 UTC  
> Url: https://github.com/boostorg/build/pull/123#discussion_r54427534  

AMDG  
  
On 02/29/2016 08:37 AM, Rene Rivera wrote:  
  
> > @@ -77,6 +77,7 @@ Guess_Toolset ()  
> >            BOOST_JAM_TOOLSET=vacpp  
> >         fi  
> >      elif test_uname AIX && test_path xlc; then BOOST_JAM_TOOLSET=vacpp      
> > -    elif test_uname FreeBSD && test_path freebsd-version; then BOOST_JAM_TOOLSET=clang  
>   
> Is this is for FreeBSD >=10.. Shouldn't the version also be checked?  
  
  Also, shouldn't this be testing whether clang exists?  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: Flast  
> Created_at: 2016-02-29 22:59:46 UTC  
> Updated_at: 2016-03-10 05:35:06 UTC  
> Url: https://github.com/boostorg/build/pull/123#discussion_r54494897  

> > Is this is for FreeBSD >=10.. Shouldn't the version also be checked?  
  
`freebsd-version` is new command which is provided only if FreeBSD >= 10.  
  
> Also, shouldn't this be testing whether clang exists?  
  
OK, I'll check `test_path clang`.

---

## Comment 4

> Username: vprus  
> Created_at: 2016-03-09 07:07:45 UTC  
> Url: https://github.com/boostorg/build/pull/123#issuecomment-194145522  

Is the current version of the patch fine with everybody? I'd rather have it in 1.61 if so.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2016-03-09 13:01:15 UTC  
> Url: https://github.com/boostorg/build/pull/123#issuecomment-194286242  

Yes, it's fine.

---

## Comment 6

> Username: Flast  
> Created_at: 2016-03-10 05:36:22 UTC  
> Url: https://github.com/boostorg/build/pull/123#issuecomment-194682021  

I have no more patch.

---

## Comment 7

> Username: vprus  
> Created_at: 2016-03-10 06:30:35 UTC  
> Url: https://github.com/boostorg/build/pull/123#issuecomment-194694323  

I've cherry-picked this pull request, thanks!

---
