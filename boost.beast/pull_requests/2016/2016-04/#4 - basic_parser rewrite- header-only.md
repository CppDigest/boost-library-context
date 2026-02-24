# #4 basic_parser rewrite: header-only [Merged]

> Username: vinniefalco  
> Created at: 2016-04-26 22:52:32 UTC  
> Updated at: 2016-05-09 15:35:07 UTC  
> Merged at: 2016-04-29 14:42:01 UTC  
> Closed at: 2016-04-29 14:42:01 UTC  
> Url: https://github.com/boostorg/beast/pull/4  

The basic_parser is rewritten to be header-only. The nodejs parser is removed from the include subtree and placed into the test directory. Other changes:  
- Parser specific error codes in parse_error.hpp  
- Add parser-bench performance testing, nodejs vs beast  
- New random message generator for fuzz tests  
- Test for header-only parser using random message generator  
- Augmented some existing message tests to check more cases  
- Add beast::write(): This function performs serialization of a variable list of arguments to a streambuf. It accepts a wide variety of argument types, using boost::asio::buffer and boost::lexical_cast where possible.  
  
First 2 commits already passed review (add WebSocket tests and Beast documentation work). Preview of the documentation changes are here:  
http://vinniefalco.github.io/stage/beast  
  
This commit also already passed review (Add UTF8_checker unit test)  
  
Reviewers: @nbougalis

---

## Comment 1

> Username: miguelportilla  
> Created_at: 2016-04-27 16:23:55 UTC  
> Updated_at: 2016-04-29 09:22:59 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61289709  

Unused local variable 'sig'.

---

## Comment 2

> Username: miguelportilla  
> Created_at: 2016-04-27 16:41:51 UTC  
> Updated_at: 2016-04-29 09:22:59 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61292774  

Non-executing code.

---

## Comment 3

> Username: miguelportilla  
> Created_at: 2016-04-27 19:14:27 UTC  
> Updated_at: 2016-04-29 09:22:59 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61318917  

A bitset may be cache friendlier.  
  
```  
 static std::bitset<256> tab {  
    "11111111111111111111111111111111"  
    "11111111111111111111111111111111"  
    "11111111111111111111111111111111"  
    "11111111111111111111111111111111"  
    "01111111111111111111111111111111"  
    "11111111111111111111111111111111"  
    "11111111111111111111111111111111"  
    "00000000000000000000001000000000"  
};  
```

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-04-27 20:50:14 UTC  
> Updated_at: 2016-04-29 09:22:59 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61333415  

OOH!!! SHINY

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-04-27 20:58:45 UTC  
> Updated_at: 2016-04-29 09:22:59 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61334869  

Can't initialize `constexpr` unfortunately but nice idea.

---

## Comment 6

> Username: nbougalis  
> Created_at: 2016-05-02 07:15:43 UTC  
> Updated_at: 2016-05-03 05:22:15 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61709455  

Just throwing this option out there:  
  
```  
inline  
bool  
is_text(char c)  
{  
    auto const cc = static_cast<std::uint8_t>(c);  
  
    if (cc < 32)  
        return (cc == 9);  
  
    return (cc != 127);  
}  
```  
  
I get that this is adding a lot of comparisons, so it's bound to be slower than the table-based lookup, but it is easier to verify correct.   
  
If you're interested I benchmarked both implementations by classifying 128MB of randomly distributed characters, and the performance is only _marginally_ worse for code compiled with `-O3`: 115 millseconds for the table based approach, vs 150 milliseconds for the conditional version. That works out to around 20 nanoseconds per character.  
  
I'm fine as is - just pointing out the alternative.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-05-02 11:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61727120  

That kills performance

---

## Comment 8

> Username: nbougalis  
> Created_at: 2016-05-03 05:22:44 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61839360  

Not as much as you'd think.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-05-03 10:05:40 UTC  
> Updated_at: 2016-05-03 10:06:11 UTC  
> Url: https://github.com/boostorg/beast/pull/4#discussion_r61860572  

You gotta understand though, its a cut-throat world out there and Beast is fighting for its life. There's a benchmark executable that compares the performance of Beast versus nodejs http-parser and even tiny differences matter:  
https://travis-ci.org/vinniefalco/Beast/jobs/127369018#L448  
  
Patches improving performance welcomed.

---
