# #201 - strong typdef operator== can compile into infinite loop in clang10 with -std=c++2a [Closed]

> Username: LumaGhost  
> Created at: 2020-05-05 13:53:49 UTC  
> Updated at: 2020-07-17 21:59:21 UTC  
> Closed at: 2020-07-17 21:59:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/201  

```  
#include <boost/serialization/strong_typedef.hpp>  
  
BOOST_STRONG_TYPEDEF(double, Deg);  
  
int main() {  
    auto d = Deg{};  
    return d == double{};  
}  
```  
  
expected behavior: return 1  
  
actual behavior: infinite loop  
  
https://godbolt.org/z/6jDXqs  
  
seems to only happen on clang10/trunk with the std=c++2a or std=c++20 flag. other compilers i've tried (gcc9, and clang9) seem to work as expected. this code also compiles as expected with clang10 if the -std=c++17 flag is used instead of 2a.  
  
let me know if any additional information is required. thank you.  
  
edit: forgot to mention the boost versions i tried are 1.72 and 1.73 and both seem to produce the same behavior

---

## Comment 1

> Username: robertramey  
> Created at: 2020-05-05 17:20:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-624193134  

A couple of comments.  
  
a) for a number of reasons I don't use BOOST_STRONG_TYPEDEF in the serialization library anymore.  I eventually concluded that it's usage was a mistake - at least in my context.  
b) I left it in in order not to break peoples code which might be relying it on.  
c) I'm not a big fan of auto as it prevents the compiler from flagging type errors.  
d) The C++ initialization rules have become so complex that I don't know what something like double{} means any more.  
  
So I would simplify (for me) your program:  
`  
#include <boost/serialization/strong_typedef.hpp>  
BOOST_STRONG_TYPEDEF(double, Deg); // is a type which models a complete integer  
int main() {  
    Deg d(0); // or Deg d = 0;  
    return ! (d == (double)0) ; // compares a Deg with value zero to a double with value zero. Should return 0 to caller of main  
}`  
  
Indeed this throws recent versions of clang (and gcc) into an infinite loop.  So I'd have to dig into BOOST_STRONG_TYPEDEF code - which I can't justify the time to do.  If you want to do this, and post a PR I'd be happy to review it, and if I think it's ok, merge it.  
  
Another interesting little fact.  You've used the -O3 switch - entirely reasonable.  But If I change this to -O1 - the program works as expected.  So this is might be a case of overzealous optimization.   
  
Thanks for spending the time on this so far.  Good luck if you decide to pursue it.  
  
Robert Ramey

---

## Comment 2

> Username: LumaGhost  
> Created at: 2020-05-05 18:23:16 UTC  
> Updated at: 2020-05-05 18:42:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-624226021  

I am glad you were able to reproduce the issue. I understand that the effort to look into a solution is not justified at this time. Thank you for your insight and detailed response. I will also note that the optimization level changes the behavior.  
  
I can certainly try to further narrow down the issue. for what its worth, it seems the issue can be further narrowed down to the use of ` boost::equality_comparable2` for example:  
  
```  
#include <boost/operators.hpp>  
  
struct my_type : boost::equality_comparable2<my_type, double> {  
    explicit my_type(double mem) : mem_{mem}{}  
    double mem_{0};  
    operator double() {  
        return mem_;  
    }  
    bool operator==(my_type l) {  
        return l.mem_ == mem_;  
    }  
};   
  
int main() {  
    my_type x{0};  
    return x == double{0};  
}  
```  
  
so perhaps opening an issue on boost/utility is the next step for me.  
  
for others facing this issue, [type_safe](https://github.com/foonathan/type_safe/blob/master/example/strong_typedef.cpp) may be a suitable replacement for strong typedef functionality.  
  
anyway, with that said I have nothing further to add at this time. thank you again for your input!   
  
edit: had to add the double conversion operator to get the example building with c++17

---

## Comment 3

> Username: LumaGhost  
> Created at: 2020-05-05 21:39:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-624320757  

per the discussion here https://github.com/boostorg/utility/issues/65 it seems the fix is to add operator==(double). for example  
  
```  
#include <boost/operators.hpp>  
  
struct my_type : boost::equality_comparable2<my_type, double> {  
    explicit my_type(double mem) : mem_{mem}{}  
    double mem_{0};  
    operator double() {  
        return mem_;  
    }  
    bool operator==(my_type l) {  
        return l.mem_ == mem_;  
    }  
    bool operator==(double l) {  
        return l == mem_;  
    }  
};   
  
  
int main() {  
    my_type x{0};  
    return x == double{0};  
}  
```  
  
so I believe adding operator==(T) to BOOST_STRONG_TYPEDEF will fix the issue.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-07-17 19:10:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-660290668  

I looked at this discussion and its references.  All I can say is wow - this is complicated.  The question is should I merge in the pull request.  As it seems not, I shouldn't change anything.  How about giving me some final guidance.

---

## Comment 5

> Username: LumaGhost  
> Created at: 2020-07-17 20:03:37 UTC  
> Updated at: 2020-07-17 20:07:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-660312993  

it has been my understanding that regardless of the more involved c++20 discussion, the operator== for T is correct to have per the contract of using boost::totally_ordered. I am saying that based on these comments in particular https://github.com/boostorg/utility/issues/65#issuecomment-624304082 https://github.com/boostorg/utility/issues/65#issuecomment-624308338 https://github.com/boostorg/utility/issues/65#issuecomment-624672525  
  
from what I see, a lot of the complexities of that discussion come from the fact that something like `boost::equality_comparable<T,U>` now overlaps with functionality provided by the language in c++20 and therefor is potentially no longer needed in c++20, but there are concerns on both sides. Also, it looks like there are potential talks of changing the language rules https://github.com/boostorg/utility/issues/65#issuecomment-636073901 which of course adds to the complexities of the discussion.  
  
that said, i would not claim to fully understand the nuance of this discussion especially as i take a closer look. my overall opinion would be to wait and see what, if anything, Boost.Operators changes and hope that at that point the discussion is a bit more tangible. at this point i see a lot of moving parts between potential language changes and potential changes to Boost.Operators so my best recommendation would be to wait, if that's acceptable.  
  
I dont mind keeping an eye on the discussion over at Boost.Operators and updating the proposed change here based on their final decision when it arrives. it seems a change of some sort may end up being required to avoid breaking in c++20, but i agree its too soon to commit to this particular solution.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-07-17 21:59:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/201#issuecomment-660354906  

And don't forget ... I'm interested in keeping backwards compatibility with C++03 as long as is practicable.  And also compatibility with serialization files created under older versions.
