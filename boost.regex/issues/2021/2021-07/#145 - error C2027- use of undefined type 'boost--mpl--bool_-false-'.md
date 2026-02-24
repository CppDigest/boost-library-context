# #145 - error C2027: use of undefined type 'boost::mpl::bool_<false>' [Closed]

> Username: PhoebeHui  
> Created at: 2021-07-27 10:45:25 UTC  
> Updated at: 2021-07-28 01:58:00 UTC  
> Closed at: 2021-07-28 01:58:00 UTC  
> Url: https://github.com/boostorg/regex/issues/145  

Hi All,  
  
We encountered 'error C2027: use of undefined type 'boost::mpl::bool_<false>'' when use '#include <boost/regex/v4/regex.hpp>', it looks a source issue, could you please take a look? Thanks!  
  
**Environment**: boost 1.76 +  VS2019 + C++17  
  
**Repro code:**  
#include <boost/regex/v4/regex.hpp>  
#include <iostream>  
  
int main()  
{  
    std::cout << "Hello World!\n";  
}  
  
  
**Failures**:  
```  
.\boost\regex\v4\basic_regex_parser.hpp(59,116): error C2027: use of undefined type 'boost::mpl::bool_<false>'  
```  
  
Related to https://github.com/microsoft/vcpkg/issues/19153

---

## Comment 1

> Username: jdx-john  
> Created at: 2021-07-27 10:51:15 UTC  
> Url: https://github.com/boostorg/regex/issues/145#issuecomment-887411713  

I'm the original reporter, just for further information this is an old code-base and honestly, I don't know _why_ we are using v4 rather than just boost/regex.hpp. Using the latter, compilation succeeds but I do not understand what difference it makes.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-07-27 12:16:17 UTC  
> Url: https://github.com/boostorg/regex/issues/145#issuecomment-887462502  

What you're doing isn't (and never has been) supported.  In short please include boost/regex.hpp.

---

## Comment 3

> Username: jdx-john  
> Created at: 2021-07-27 12:51:13 UTC  
> Url: https://github.com/boostorg/regex/issues/145#issuecomment-887484955  

Thanks @jzmaddock. I wondered as much although it's been in our codebase a dozen years as far as I know.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-07-27 16:26:36 UTC  
> Url: https://github.com/boostorg/regex/issues/145#issuecomment-887655446  

> Thanks @jzmaddock. I wondered as much although it's been in our codebase a dozen years as far as I know.  
  
I realise that, however the "V4" code is all C++03, and has now been deprecated, and will most likely be removed entirely sometime next year.  At that point your code really will break!  In general, please don't use internal/private/undocumented headers as they will almost certainly break at some point I'm afraid :(

---

## Comment 5

> Username: PhoebeHui  
> Created at: 2021-07-28 01:58:00 UTC  
> Url: https://github.com/boostorg/regex/issues/145#issuecomment-887950181  

@jzmaddock, thanks for your response!
