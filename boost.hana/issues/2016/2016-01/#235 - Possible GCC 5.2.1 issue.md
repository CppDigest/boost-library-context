# #235 - Possible GCC 5.2.1 issue? [Closed]

> Username: lucanus81  
> Created at: 2016-01-07 16:02:51 UTC  
> Updated at: 2018-02-16 19:04:03 UTC  
> Closed at: 2016-01-08 03:51:31 UTC  
> Url: https://github.com/boostorg/hana/issues/235  

Hello,  
first of all I tried to check whether a similar bug already exists, but apparently I couldn't find any. We have a gcc 5.2.1 on our redhat 6 machine which -according to Redhat- it should be 100% c++14 compliant. However when compiling the simplest hana example:  
  
```c++  
# include <boost/hana.hpp>  
  
int main() { return 0; }  
```  
  
I'm getting two errors:  
  
```  
include/boost/hana/fwd/optional.hpp:330:29  
error: explicitly defaulted function 'constexpr boost::hana::optional<>& boost::hana::optional<>::operator=(const boost::hana::optional<>&)' cannot be declared as constexpr because the implicit declaration is not constexpr:  
         constexpr optional& operator=(optional const&) = default;  
  
include/boost/hana/fwd/optional.hpp:33129  
error: explicitly defaulted function 'constexpr boost::hana::optional<>& boost::hana::optional<>::operator=(boost::hana::optional<>&&)' cannot be declared as constexpr because the implicit declaration is not constexpr:  
         constexpr optional& operator=(optional&&) = default;  
```  
  
I have tried to comment out those two lines in optional.hpp and finally I was able to compile few hana examples (directly from your documentation).  
  
```  
329         // 5.3.3, Assignment  
330         // constexpr optional& operator=(optional const&) = default;  
331         // constexpr optional& operator=(optional&&) = default;  
```  
  
Is this an already reported GCC issue (couldn't find any reference to something like that )or is this a new one you weren't aware of?  
  
Thanks,  
Luca

---

## Comment 1

> Username: m-j-w  
> Created at: 2016-01-07 18:21:50 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-169762366  

Luca, having had the same problem as a Hana user myself, I'd like to give a quick answer for you to try a few things.   
  
Unfortunately, Redhat is promising a bit too much. GCC has quite a few issues with C++14 compliance. I'd like to refer you to the [Hana Wiki](https://github.com/boostorg/hana/wiki/General-notes-on-compiler-support), where those issues are listed directly below the table.  
  
You may also run the tests provided with Hana and then reduce your include statements to those features which are working. Thus, don't do an `#include <boost/hana.hpp>`, but just include the very minimum you require. Running the tests with `cmake` is well documented in the wiki and the [manual](http://boostorg.github.io/hana/).  
  
Also unfortunately, and as far as I know, Redhat Enterprise 6 doesn't provide an up to date Clang compiler package (Clang >=3.5) out of the box. But Google might help you out there. If you want to dig deep, see the previous issues #167 and #168.  
  
Good luck, Markus.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-08 03:51:31 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-169886388  

Luca,  
  
Markus is right in saying that GCC is not fully C++14 compliant. Being compliant means being able to compile without segfaulting on most non-trivial examples, but unfortunately GCC is not quite there yet.  
  
Regarding the exact error that you encountered; I am aware of it, and I even have a fix for it (it's actually a single thing that triggers two error messages). However, there are so many other problems with GCC that it is not really worth doing any workaround to accommodate that compiler for now.  
  
I regularly monitor GCC's progress in terms of C++14; I compile Hana and take the time to generate useful bug reports to help them reach their goal of C++14 conformance, and enable Hana on GCC. But it seems like they are not there yet. Believe me, we are a couple of people waiting for Hana to work on GCC, which is one of the most widely used compilers, after all.  
  
For now, the best you can do if you really want to use Hana is to use Clang. It should be fairly easy to install Clang through a package manager on Linux. On Ubuntu, for example, I think `apt-get clang` would work out of the box. I understand that using a different compiler might not be an option, though. In all cases, I would suggest putting some pressure by signifying to RedHat that you want the following bugs fixed:  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=67364  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=68754  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=67164  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=69059  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=69060  
  
**Especially** if you are paying for entreprise support (which I don't know if you are), they tend to be much more responsive than to non-paying customers. I'll close this now; good luck!

---

## Comment 3

> Username: lucanus81  
> Created at: 2016-01-08 09:25:43 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-169944995  

First of all thanks, I completely missed that table in the wiki with all the gcc open issues.  
Unfortunately we cannot move our code base to clang, however we do pay for enterprise support so I'll send those bugs to our Redhat support.

---

## Comment 4

> Username: guijoe  
> Created at: 2018-02-16 14:11:49 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-366245448  

Hello Guys,  
  
Please, any fix on this issue ? I use gcc 7.2.0 and I have the same issue.  
  
Thank you.

---

## Comment 5

> Username: ldionne  
> Created at: 2018-02-16 17:57:20 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-366310513  

@guijoe Can you please post a minimal complete reproduction on GCC 7.2.0? I can't reproduce: https://wandbox.org/permlink/wIJlMZNUyLMJ5Fiu

---

## Comment 6

> Username: guijoe  
> Created at: 2018-02-16 19:04:03 UTC  
> Url: https://github.com/boostorg/hana/issues/235#issuecomment-366328915  

Hi Idionne.  
  
I'm sorry. I was using gcc 5.1.x. I found out that Hana did not support gcc versions lower than 6.0.0. I now moved to gcc 7.2.0. The issue is no more. Thanks.
