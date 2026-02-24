# #47 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-17 12:43:01 UTC  
> Updated at: 2020-08-25 18:49:17 UTC  
> Merged at: 2020-08-25 10:08:05 UTC  
> Closed at: 2020-08-25 10:08:05 UTC  
> Url: https://github.com/boostorg/units/pull/47  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 12:41:02 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-624625382  

Some coverity scan test failed with:  
  
```  
The command "echo -n | openssl s_client -connect scan.coverity.com:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' | sudo tee -a /etc/ssl/certs/ca-" exited with 0.  
  
0.21s$ libs/$SELF/covscan.sh  
  
+pushd /tmp  
  
/tmp ~/build/boostorg/boost-root  
  
+rm -rf coverity_tool.tgz 'cov-analysis*'  
  
+wget -nv https://scan.coverity.com/download/linux64 --post-data 'token=&project=boostorg/units' -O coverity_tool.tgz  
  
Username/Password Authentication Failed.  
  
The command "libs/$SELF/covscan.sh" exited with 6.  
cache.2  
  
store build cache  
```  
This has nothing to do with this PR. Can this please be merged ?

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-07-16 23:01:27 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-659722341  

Steven,  
  
Is there any reason you do not want to merge this PR  ? It has been 3 months since submitting this simple PR and I do not understand why it has not been merged to develop. If it does not get into the 1.74 release that is not significant , but the reason that you have not merged the PR  eludes me.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-08-24 09:59:12 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-679032559  

I don't like the `bu::information::byte` changes. First, you should use a using declaration instead of qualifying everything. Second, there should be a comment explaining why it's needed (where does a global `byte` come from?). Third, it should probably be ifdef-ed for the specific compiler needing it.

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-08-24 15:26:11 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-679195710  

The mnemonic 'byte' is too common to be unqualified. It occurs in certain Windows header files and we all know there are no namespaces there nor has Microsoft ever cared about common mnemonic names themselves, so it is not a compiler issue other than that certain compilers include certain Windows header files while others do not. I do not understand why a 'using' declaration solves the problem. Do you mean as a way of typedefing the type and then using that name instead ? I really think that not hiding behind a typedef is better, but if you insist I can do that.

---

## Comment 5

> Username: pdimov  
> Created_at: 2020-08-24 15:34:59 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-679200970  

I mean add `using bu::information::byte;`

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-08-24 18:40:47 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-679298008  

How does adding `using bu::information::byte;` solve the problem when `byte` is also an unjqualified type in a Windows header file ?

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-08-24 18:56:55 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-679305508  

Did you try it? Immediately after `using namespace bu::information;`.

---

## Comment 8

> Username: eldiener  
> Created_at: 2020-08-25 18:24:32 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-680194547  

Peter: Thanks for tolerating my stupidity and helping out getting the Embarcadero C++ clang-based changes merged for the remaining libaries.

---

## Comment 9

> Username: pdimov  
> Created_at: 2020-08-25 18:49:17 UTC  
> Url: https://github.com/boostorg/units/pull/47#issuecomment-680206852  

No problem. :-)

---
