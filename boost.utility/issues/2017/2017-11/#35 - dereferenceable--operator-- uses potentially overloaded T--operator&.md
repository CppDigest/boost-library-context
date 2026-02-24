# #35 - dereferenceable::operator-> uses potentially overloaded T::operator& [Closed]

> Username: joaquintides  
> Created at: 2017-11-23 15:28:25 UTC  
> Updated at: 2017-11-23 20:14:46 UTC  
> Closed at: 2017-11-23 20:14:46 UTC  
> Url: https://github.com/boostorg/utility/issues/35  

In line 305 of `boost/operators.hpp`:  
  
    return &*static_cast<const T&>(*this);  
  
This will break if `T` overloads `operator&`. I suggest using `boost::addressof` to protect us from that.

---

## Comment 1

> Username: d-frey  
> Created at: 2017-11-23 17:35:54 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346672566  

Looking at the documentation at http://www.boost.org/doc/libs/1_65_1/libs/utility/operators.htm#deref, it is documented to return `&*i`, which must be convertible to `P`. Given that the library is quite old and it has been like that for >=15 years, I wonder if there really is a problem that needs to be fixed. Plus we would add a dependency. What are your thoughts on this? Or do you have a specific problem that needs fixing?

---

## Comment 2

> Username: joaquintides  
> Created at: 2017-11-23 19:14:36 UTC  
> Updated at: 2017-11-23 19:16:12 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346685143  

Hi Daniel,  
  
It's a real problem reported by a user of Boost.MultiIndex —which library is BTW 13 years old :-)  
  
https://stackoverflow.com/questions/47416304/boostmulti-index-container-crashes-with-com-ptr-t-objects  
  
Basically, Boost.MultiIndex does not work with elements whose `operator&` is overloaded. The problem is scattered through the code, one of the locations being its dependency on `boost/operators.hpp`.  
  
I understand changing this on your side requires that you update the documentation from  
  
**Requirements:** `(&*i`). Return convertible to `P`.  
  
to  
  
**Requirements:** `*i`. Return convertible to `R`.  
  
which is not, strictly speaking, a relaxation of the former condition —`*i` could in principle be anything as long as `&*i` returns a pointer. So it is up to you; if you decide to not do the change I can fix the issue on my side, although it's more verbose in terms of LOCs touched and the root problem might hit other code using  `boost/operators.hpp`.  FWIW commercial implementations of std containers guard themselves against abuses of `operator&` overloading via constructs similar to `boost::addressof`.  
  
Thank you

---

## Comment 3

> Username: d-frey  
> Created at: 2017-11-23 19:41:31 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346688340  

If Boost.MultiIndex is only 13 years old, you loose ;-P  
  
Joking aside, I am not trying to ignore the problem, I was just curious where this was coming from. I think it is likely enough that people are not relying on the current implementation to allow them to "abuse" an overloaded `operator&`, so fixing it by using `boost::addressof` seems reasonable.  
  
For the documentation part, well, it's not correct that the "return" is directly convertible to `R`. Maybe this would work:  
  
**Requirements:** `*i`. Pointer to the return convertible to `R`.  
  
or "...returned value..." instead of "...return..."?

---

## Comment 4

> Username: joaquintides  
> Created at: 2017-11-23 19:55:37 UTC  
> Updated at: 2017-11-23 19:55:59 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346689931  

> **Requirements:** `*i`. Pointer to the return convertible to `R`.  
  
I think this not correct either... What about  
  
**Requirements:** `*i`. The address of the returned value convertible to `P`.  
  
"Address of a value" is a well-defined concept that does not depend on `operator&`, so I think this holds water.

---

## Comment 5

> Username: d-frey  
> Created at: 2017-11-23 19:57:29 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346690132  

OK, agreed. I'll commit a fix soon.

---

## Comment 6

> Username: joaquintides  
> Created at: 2017-11-23 19:59:17 UTC  
> Url: https://github.com/boostorg/utility/issues/35#issuecomment-346690332  

Thank you! Some Russian guy is going to be grateful for this, even if he is not aware of the behind-the-scenes conversation.
