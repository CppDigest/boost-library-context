# #51 - strange stdcerr file in 1.67 rc2 [Closed]

> Username: jeffreyscottgraham  
> Created at: 2018-03-19 22:56:42 UTC  
> Updated at: 2018-03-19 23:47:36 UTC  
> Closed at: 2018-03-19 23:45:02 UTC  
> Url: https://github.com/boostorg/log/issues/51  

In the 1.67rc2 candidate an empty file called stdcerr is created at runtime in my unit tests.  
Using boost log in 1.64 this file is not there.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-03-19 23:36:21 UTC  
> Url: https://github.com/boostorg/log/issues/51#issuecomment-374421889  

Can you provide a test case to reproduce the problem?

---

## Comment 2

> Username: Lastique  
> Created at: 2018-03-19 23:45:02 UTC  
> Url: https://github.com/boostorg/log/issues/51#issuecomment-374423525  

BTW, a Boost-wide search only found one "stdcerr" string in Boost.Test:  
  
https://github.com/boostorg/test/blob/master/include/boost/test/impl/framework.ipp#L916  
  
Boost.Log does not create log files on its own, so I'm pretty sure this is where the file comes from. The string is probably a typo and should be "stderr", in which case the output should be written to the console.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-03-19 23:47:36 UTC  
> Url: https://github.com/boostorg/log/issues/51#issuecomment-374423977  

Please, report the bug to [Boost.Test](https://github.com/boostorg/test/issues/new).
