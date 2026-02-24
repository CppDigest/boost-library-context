# #205 Fix MinGW vsnprintf compile errors and warnings [Closed]

> Username: Lastique  
> Created at: 2019-02-10 15:59:18 UTC  
> Updated at: 2019-02-18 21:21:16 UTC  
> Closed at: 2019-02-14 07:29:26 UTC  
> Url: https://github.com/boostorg/test/pull/205  

This PR:  
  
- Adds Appveyor CI config file.  
- Fixes compile errors on MinGW about missing declaration of `vsnprintf`. The attempted fix in https://github.com/boostorg/test/pull/195, although correct, did not fix the error.  
- Silences gcc warnings about unused global variables.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2019-02-10 18:05:41 UTC  
> Url: https://github.com/boostorg/test/pull/205#issuecomment-462156751  

There is already a PR about AppVeyor, see #185 . For some reason it is not active yet. It would be good if you can split the PR on those orthogonal issues. Thanks!

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-02-10 18:25:26 UTC  
> Url: https://github.com/boostorg/test/pull/205#issuecomment-462158330  

That PR sets up MinGW differently than my config, I'm not sure it is equivalent. Also, it doesn't enable CI for `topic` branches. I've included the config because I had to ensure the problem reproduces and test the change in Appveyor since I couldn't reproduce it locally.   
  
The config is in a separate commit, you can skip merging it. If you want, I can remove this commit from this PR. Please, confirm.  
  
> For some reason it is not active yet. It would be good if you can split the PR on those orthogonal issues.  
  
You have to add the GitHub project to your Appveyor project list in order to get it running. You should have admin rights for the GitHub project though.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-02-12 00:20:31 UTC  
> Url: https://github.com/boostorg/test/pull/205#issuecomment-462551606  

I split the PR in 3 branches:  
  
Concerning the `vsnprintf` issue (that is the purpose of the PR), it is currently running the tests in the branch `topic/PR-205-Fix-MinGW-vsnprintf-compile-errors-warnings`. I'll merge that once the tests have cycled.  
  
The GCC warnings is on branch `topic/silence-gcc-warnings` and the AppVeyor one is in the branch `topic/CI-appveyor`. I need to merge the Appveyor work with the other running PR on CI. Concerning the gcc warning, I am not sure: I can see some of the warnings, but all those symbols are used at some point. Since I do not know what `BOOST_ATTRIBUTE_UNUSED` does and since this is just about warnings (lesser priority), I suggest we wait a bit. The other option for silencing the warnings would be to add the proper pragmas in the `suppress_warnings.hpp` file.

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-02-12 01:32:44 UTC  
> Url: https://github.com/boostorg/test/pull/205#issuecomment-462574870  

> Since I do not know what `BOOST_ATTRIBUTE_UNUSED` does...  
  
This is [`__attribute__((unused))`](https://gcc.gnu.org/onlinedocs/gcc-8.2.0/gcc/Common-Variable-Attributes.html#index-unused-variable-attribute) for gcc, which explicitly indicates that the variable may appear unused in the program. Disabling the warning with a pragma may work, but it may also disable the warning for genuinely leftover unused variables, so I prefer the attribute.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2019-02-14 07:29:26 UTC  
> Url: https://github.com/boostorg/test/pull/205#issuecomment-463521964  

Changes merged to next except for the AppVeyor (tried to enable the thing from Appveyor but still waiting for some approval by boost.org).  
  
Closing, thanks!

---
