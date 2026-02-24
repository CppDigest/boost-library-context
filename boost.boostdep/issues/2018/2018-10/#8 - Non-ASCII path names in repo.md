# #8 - Non-ASCII path names in repo [Closed]

> Username: teeks99  
> Created at: 2018-10-17 00:21:22 UTC  
> Updated at: 2018-10-18 01:43:08 UTC  
> Closed at: 2018-10-17 14:00:44 UTC  
> Url: https://github.com/boostorg/boostdep/issues/8  

I know this was intentional, but the addition of non-ascii path names caused scripts I had running (python 2 shutil.copytree on windows) to fail. Others may experience other issues with this.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-17 00:29:32 UTC  
> Url: https://github.com/boostorg/boostdep/issues/8#issuecomment-430447589  

Right. The point of the path names was to check that my `depinst` script didn't fail. :-) I realize this might cause problems; any suggestions as to how I can fix that while retaining the test?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-17 14:00:44 UTC  
> Url: https://github.com/boostorg/boostdep/issues/8#issuecomment-430640194  

Fixed.

---

## Comment 3

> Username: teeks99  
> Created at: 2018-10-18 01:43:08 UTC  
> Url: https://github.com/boostorg/boostdep/issues/8#issuecomment-430846233  

Thanks.   
  
I also added this thread on the mailing list for broader discussion:  
https://lists.boost.org/Archives/boost/2018/10/243784.php
