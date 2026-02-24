# #78 - What's a problem with Visual Studio? [Closed]

> Username: denzor200  
> Created at: 2021-04-25 20:47:13 UTC  
> Updated at: 2021-04-30 09:35:03 UTC  
> Closed at: 2021-04-30 09:35:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/78  

I tried to compile this example with my VS 2017 (C++14, boost 1.76):  
https://github.com/boostorg/pfr/blob/develop/example/get.cpp  
And i seen error, like `Boost.PFR requires /std:c++latest or /std:c++17 flags on your compiler.`  
Then i just remove the line with this error from config.hpp, and this example works normally.  
Is there any reason why pfr is prohibited from working with VS and C++14? Can I make a PR that would remove this error for VS 2017? It would help me in promoting the PFR in fusion.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-04-26 18:56:04 UTC  
> Updated at: 2021-04-30 09:34:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/78#issuecomment-827070342  

Visual Studio may work in C++14 mode on simple cases and fail on a more complex ones. If MSVC fails on complex cases - the whole C++14 support is disabled.  
  
The current rules for detecting a required support of C++14 are quite complex https://github.com/boostorg/pfr/blob/8aa4e0712a0167996c288b52963dbdaddb65b83b/include/boost/pfr/detail/config.hpp#L26-L55 and may not be perfect.  
  
PRs are welcome! Do not forget to add C++14 runs to CI here https://github.com/boostorg/pfr/blob/33250407dbb2c4cd9683f5099ca4e2fb8e633821/.github/workflows/ci.yml#L143 and drop this line https://github.com/boostorg/pfr/blob/b14939376d27c65dd95a9352a4ecc6a989b25200/test/Jamfile.v2#L15
