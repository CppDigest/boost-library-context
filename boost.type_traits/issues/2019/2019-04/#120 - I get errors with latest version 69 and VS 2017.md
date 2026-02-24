# #120 - I get errors with latest version 69 and VS 2017 [Open]

> Username: BatshevaR  
> Created at: 2019-04-10 12:58:08 UTC  
> Updated at: 2019-04-16 05:29:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120  

a lot of errors in packages\boost.1.69.0.0\lib\native\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2764	'C': template parameter not used or deducible in partial specialization 'boost::is_member_function_pointer<Ret(Args...) volatile const &>'	packages\boost.1.69.0.0\lib\native\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	220	  
  
another error:  
  Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2059	syntax error: 'generic'	\packages\boost.1.69.0.0\lib\native\include\boost\filesystem\path.hpp	579	  
  
can someone help, thanks

---

## Comment 1

> Username: BatshevaR  
> Created at: 2019-04-11 10:14:56 UTC  
> Updated at: 2019-04-11 10:16:28 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-482055815  

I get a bunch of issues in **path.hpp**  
Severity Code Description Project File Line Suppression State Error C2334 unexpected token(s) preceding '{'; skipping apparent function body packages\boost.1.69.0.0\lib\native\include\boost\filesystem\path.hpp 579  
  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2059	syntax error: 'generic'	Wrapper	\packages\boost.1.69.0.0\lib\native\include\boost\filesystem\path.hpp	579	  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error (active)	E0145	member "boost::filesystem::path::preferred_separator" may not be initialized		\packages\boost.1.69.0.0\lib\native\include\boost\filesystem\path.hpp	66

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-04-11 11:38:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-482079442  

Can you please provide the command line used to generate the type_traits errors as 1.69 is known to work with VS2017.

---

## Comment 3

> Username: BatshevaR  
> Created at: 2019-04-11 11:50:29 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-482082638  

![ScreenHunter_02 Apr  11 14 49](https://user-images.githubusercontent.com/24564864/55955088-19761000-5c69-11e9-8303-0bb041bb5534.gif)

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-04-11 17:12:23 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-482205647  

Your screen shot is no help - it just repeats what you're already posted.  As I said, our tests for that compiler are all passing, so I need:  
  
* A self contained test case (.cpp file) which reproduces the error.  
* The actual command line required to reproduce the issue.  
  
Without that no one here can help, sorry!

---

## Comment 5

> Username: BatshevaR  
> Created at: 2019-04-15 05:13:53 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-483109927  

i see it falls with some of my cpp files,  
is it will help to add them here, here is some of them:  
[EthernetDeviceDetector.zip](https://github.com/boostorg/type_traits/files/3078507/EthernetDeviceDetector.zip)  
  
  
and what do you mean here: "The actual **command line** required to reproduce the issue."???

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-04-15 17:15:44 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-483340270  

>i see it falls with some of my cpp files, is it will help to add them here, here is some of them:  
  
Those files include lots of your other headers so aren't much use.  I need a test case I can reproduce *here*.  
  
But before that, Boost-1.70 is now out, can you please test with that?

---

## Comment 7

> Username: BatshevaR  
> Created at: 2019-04-16 05:06:45 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-483512704  

ho, I waited for 70 to come out, I think I sew a Beta version of this before,  
but not the real version

---

## Comment 8

> Username: BatshevaR  
> Created at: 2019-04-16 05:29:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/120#issuecomment-483516607  

can you add the new boost version to NuGet VS?
