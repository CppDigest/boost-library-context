# #71 Fix undefined behavior in detail::copy [Merged]

> Username: rcombs  
> Created at: 2018-04-06 08:24:53 UTC  
> Updated at: 2018-11-06 15:57:44 UTC  
> Merged at: 2018-09-06 01:27:20 UTC  
> Closed at: 2018-09-06 01:27:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71  

Call the detail versions, which take pointers, rather than converting the pointers to references and back (which is UB).

---

## Comment 1

> Username: grembo  
> Created_at: 2018-07-19 14:29:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-406296375  

Could you please merge this? This is causing segfaults in production. Thanks!

---

## Comment 2

> Username: rcombs  
> Created_at: 2018-07-19 15:44:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-406323083  

Yeah I should've been more clear, this causes crashes.

---

## Comment 3

> Username: grembo  
> Created_at: 2018-07-19 21:22:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-406418632  

The code segment below demonstrates the undefined behaviour (in my case leading to segfaults):  
  
```  
  #include <boost/filesystem.hpp>  
  int main()  
  {  
    boost::filesystem::copy("/tmp/a", "/tmp/b");  
  }  
```  
  
See here for a more detailed description of the problem (adding the patch to the freebsd ports tree): https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=229888

---

## Comment 4

> Username: pokey909  
> Created_at: 2018-07-25 11:45:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-407726354  

@Beman could you please take a look and merge this patch if there are no concerns with the code?

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-06 14:06:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-436264189  

Perhaps the CI suite should be running UBSAN and static code analyses?

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-11-06 15:19:42 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-436289870  

It is running UBSAN.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-11-06 15:57:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/71#issuecomment-436304558  

Apologies, I looked at the build results for the PR which is 7 months old.

---
