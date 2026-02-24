# #708 - Missing boost includes [Closed]

> Username: ghost  
> Created at: 2017-08-01 10:09:15 UTC  
> Updated at: 2017-08-01 13:44:09 UTC  
> Closed at: 2017-08-01 12:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/708  

Hi, maybe I missed something, but i'm getting no boost/beast setup working anymore.  
  
First boost master has no beast sources so far.  
And second boost develop contains only an empty beast folder.  
  
Thus I wanted to use the old beast git repository (Version 85),  
resulting in missing boost includes.  
  
Next I used the lastest beast sources from the boost git repository and moved them manually to the boost/libs/beast folder resulting in missing boost includes again.  
  
Could you please provide some instructions how to get beast working again with boost and which versions of boost/beast are needed for this?  
  
E.g.:  
> Error	C1083	Cannot open include file: 'boost/utility/string_view.hpp': No such file or directory	...\beast\include\boost\beast\core\string.hpp	26  
  
As stated here it is only working with an snapshot branch?  
  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/quick_start.html  
  
If so I think it was a bit to early for moving the beast repository into the boost one.

---

## Comment 1

> Username: ghost  
> Created at: 2017-08-01 12:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/708#issuecomment-319353396  

Ok, it could be compiled with the last official release 1.64.0. I expected to need the latest boost sources having beast included.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-01 13:00:01 UTC  
> Url: https://github.com/boostorg/beast/issues/708#issuecomment-319362825  

Hmm...I'm not sure why you got that error. Beast checks the boost version and includes either string_view.hpp or string_ref.hpp to keep things working:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/string.hpp#L15  
  
>And second boost develop contains only an empty beast folder.  
  
Did you init the submodules?  
  
If you had Beast working before the move to boost (version 85) then it should also work for you after the move to boost, the only change is that you need to put `boost::` in front of all your beast namespace qualifiers.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-01 13:43:58 UTC  
> Updated at: 2017-08-01 13:44:09 UTC  
> Url: https://github.com/boostorg/beast/issues/708#issuecomment-319373844  

>I think it was a bit to early for moving the beast repository into the boost one.  
  
I had to move the repository right away, in order to get as many  builds through the boost test infrastructure as possible before the October 25th cut-off for the 1.66.0 release. It takes several days for the tests to cycle through all the boost libraries, and the more builds I can get the more errors I can fix before the release:  
http://www.boost.org/development/tests/develop/developer/beast.html
