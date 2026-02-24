# #50 - Feature request: optimization for types with common base class [Closed]

> Username: HDembinski  
> Created at: 2018-07-15 19:50:09 UTC  
> Updated at: 2018-08-28 09:15:54 UTC  
> Closed at: 2018-08-10 19:12:03 UTC  
> Url: https://github.com/boostorg/variant/issues/50  

It would be great if the variant could skip the visitation loop when all bound types have a common base type, and the user tries to cast the variant to the common base type.  
  
Pseudo-code:  
```  
struct A {};  
struct B : A {};  
struct C : A {};  
  
boost::variant<B, C> v(B());  
A& a = boost::get<A>(v); // no visitation needed, because A is a common base type of all bounded types  
```  
I am using boost::variant as a backend in [histogram](https://github.com/hdembinski/histogram) and this would allow me to speed up a part of the code. I think it is a useful feature for others as well.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-18 08:11:46 UTC  
> Updated at: 2018-07-18 08:12:10 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-405847845  

I looked into the code, I think this can work and would merely require some changes to `boost::get`. TMP can figure out whether the requested type is a common base of all bounded types. Based on that static decision, either the fast implementation with a direct `static_cast` can be used or the slow visitation version.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-07-28 15:40:41 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-408616237  

That's an interesting idea for the compiler developers rather than for the Boost.Variant.  
  
I'll try to simplify your example and fill an issue for the Clang and GCC.

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-07-28 18:13:33 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-408625761  

Looks like modern compilers already do that for simple cases: https://godbolt.org/g/FdZjmT  
For more complex cases GCC fails to eliminate the checks: https://godbolt.org/g/rpXEvD

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-07-30 09:54:54 UTC  
> Updated at: 2018-07-30 09:55:35 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-408810655  

Yeah, you are right. I had a discussion with @mattcalabrese and others on Slack on this, and they convinced me that my original naive idea to just reinterpret cast the aligned storage holder in boost::variant to the base is UB.  
  
Then I found that - at least for my case and with gcc - the visitation in boost::variant is optimized away if I make a visitor that just casts to the base.  
https://godbolt.org/g/qNDWme  
  
I don't fully understand the assembler, but I look like the visitation is skipped and there is only one error check at the end. When you increase the number of derived classes, the assembler code has the same length. So that's good. It is not as efficient as your first example, but I am still pretty impressed.  
  
So, feel free to close this request.

---

## Comment 5

> Username: apolukhin  
> Created at: 2018-08-10 19:12:03 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-412178513  

Yes, everything is fine with `boost::variant`, however GCC and Clang fail to optimize the `std::variant` from libstdc++ in the same way https://godbolt.org/g/UaHt7Q . So some day, after the migration to C++17, you may get hit by this issue.  
  
I've minimized the example and filled bugs on GCC and Clang  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86909  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86912  
* https://bugs.llvm.org//show_bug.cgi?id=34801 (Actually I've filled this one a long time ago)  
  
And only now I'm OK with closing this request :)  
  
P.S.: Subscribing to the bugs may improve the chances of the bug to be fixed sooner. :)

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-08-13 08:00:05 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-412438197  

Cool, this is great. Sorry, I wasn't trying to tell you what you should do and what not or when, I just wanted to be friendly :)

---

## Comment 7

> Username: apolukhin  
> Created at: 2018-08-28 09:15:54 UTC  
> Url: https://github.com/boostorg/variant/issues/50#issuecomment-416511497  

Do not apologize. I've just wanted to look cool :)  
  
BTW, here's another related issue https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78113
