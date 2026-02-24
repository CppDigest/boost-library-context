# #658 - X3: seek parser goes past the end of input [Closed]

> Username: Kojoley  
> Created at: 2021-03-07 00:15:14 UTC  
> Updated at: 2021-05-31 22:49:48 UTC  
> Closed at: 2021-05-29 13:34:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/658  

```cpp  
#include <boost/spirit/home/x3.hpp>  
namespace x3 = boost::spirit::x3;  
int main() {  
    char const* x = " ";  
    x3::phrase_parse(x, x+1, x3::seek['!'], x3::space);  
}  
```  
from https://sourceforge.net/p/spirit/mailman/spirit-general/?viewmonth=202004  
  
`seek` seems to be missing the fact that a parser can advance an iterator (are they allowed to actually a good question), what happens all the time with skippers.  
  
The https://github.com/boostorg/spirit/pull/30 seems to be responsible for this regression, and I have no idea what it was fixing.

---

## Comment 1

> Username: vtnerd  
> Created at: 2021-05-31 22:04:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851699179  

The change permitted seeking to end of input. Reverting #30 no longer permits this case because the subject will never return true. And shouldn't the subject check for end of input first, or is it always assumed? I don't remember the semantics now.

---

## Comment 2

> Username: vtnerd  
> Created at: 2021-05-31 22:06:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851699617  

Oh nevermind, I see the problem. Yes, re-implement as a do-while actually.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-05-31 22:08:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851700026  

> The change permitted seeking to end of input.  
  
There are tests for that from the beginning, even before this PR: https://github.com/boostorg/spirit/blob/e8b4b10d1e47c1b1b7fe8efee88fdadbd56e6af8/test/x3/seek.cpp#L34-L37  
  
Could you please show an example of what you talking about maybe?

---

## Comment 4

> Username: vtnerd  
> Created at: 2021-05-31 22:27:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851703749  

The code should still have UB, there's no `return` at the end. This error was copied from the original qi ("repository") code. `return true` is insufficient without checking the subject first and `return false` is insufficient for the eoi of case. The tests likely pass because of the UB. I think a `do-while` is what I was trying to do here.

---

## Comment 5

> Username: vtnerd  
> Created at: 2021-05-31 22:30:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851704529  

Ah, wait, forever loop, damn.

---

## Comment 6

> Username: vtnerd  
> Created at: 2021-05-31 22:38:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851705937  

Ok, now I remember. Its in the comment. I'll add a test but it may not be immediate.  
  
The problem is `seek[eol | eoi]`. If the input string is `\n`, it matches `eol`, hits the forever loop and increments, then does an `eoi` correctly but misses the last check since its one-past the last character.  
  
And how does the change create a one past end of input case? That happened in the subject ?

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-05-31 22:49:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/658#issuecomment-851707855  

> Ok, now I remember. Its in the comment. I'll add a test but it may not be immediate.  
>   
> The problem is `seek[eol | eoi]`. If the input string is `\n`, it matches `eol`, hits the forever loop and increments, then does an `eoi` correctly but misses the last check since its one-past the last character.  
  
Could you please write a reproducer? This work just fine:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    char const* s = "\n", * e = s + std::strlen(s);  
    bool r = parse(s, e, x3::seek[x3::eol | x3::eoi]);  
    if (r) std::cout << (s == e ? "Match\n" : "Partly parsed:" + std::string(s, e) + "\n");  
    else std::cout << "Fail\n";  
}  
```  
  
As I already said I tried to reproduce an issue without any success.  
  
> And how does the change create a one past end of input case? That happened in the subject ?  
  
Quoting myself from the first post:  
  
> ```c++  
> #include <boost/spirit/home/x3.hpp>  
> namespace x3 = boost::spirit::x3;  
> int main() {  
>     char const* x = " ";  
>     x3::phrase_parse(x, x+1, x3::seek['!'], x3::space);  
> }  
> ```  
>   
> from [sourceforge.net/p/spirit/mailman/spirit-general/?viewmonth=202004](https://sourceforge.net/p/spirit/mailman/spirit-general/?viewmonth=202004)  
>   
> `seek` seems to be missing the fact that a parser can advance an iterator (are they allowed to actually a good question), what happens all the time with skippers.
