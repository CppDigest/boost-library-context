# #703 - X3: Rules no longer roll back iterator when forced to fail in an `on_success` handler [Closed]

> Username: syyyr  
> Created at: 2021-11-02 18:57:58 UTC  
> Updated at: 2022-01-15 18:32:40 UTC  
> Closed at: 2022-01-15 17:28:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/703  

Hello,  
since https://github.com/boostorg/spirit/commit/2db3fde0d02b9a48877147c595289934525882a0 my parser started working differently. I'm parsing a commands for my CLI. Example input of a "set" command is [here](https://github.com/CESNET/netconf-cli/blob/master/tests/leaf_editing.cpp#L86). The grammar for the command is [here](https://github.com/CESNET/netconf-cli/blob/master/src/grammars.hpp#L103).  
Since the aforementioned commit, the parser no longer parses the test input. What exactly does the commit change? Can you please help me solve this? I'm thinking something is wrong with the `space_separator` parser, but I'm not sure.  
  
Thanks.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-11-02 19:20:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-958096080  

I am sorry to hear that, the correct grammars should not behave differently after the mentioned commit. Do you intentionally rely on rules to backtrack the input iterator on failure? It could be a bug in Spirit, but your grammar also uses custom parsers which could rely on said behavior. I would need a standalone reproducer if you want me to debug the issue.

---

## Comment 2

> Username: syyyr  
> Created at: 2021-11-03 08:03:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-958725093  

Thanks for the hint, I really appreciate the help.  
  
I managed to fix one thing: I am parsing "paths", which are basically "simplified XPaths". These paths can also have trailing slash. The grammar for the trailing slash is [here](https://github.com/CESNET/netconf-cli/blob/master/src/path_parser.hpp#L400). The problem was that this rule would eat spaces and would not return them back if it couldn't find a slash. Easy fix, just wrap this with `x3::no_skip`. I think this is probably a bug in my grammar.  
  
I encountered one more problem. When parsing these paths I sometimes use parsers that have additional requirements. These reuiqrements are handled inside the `on_success` handlers of `x3::rule`. Basically I let the parser pass, then check the attribute inside the handler and use `_pass(context) = false` to fail the parser if I don't like the attribute. Before the Spirit change, this would rollback the parser iterator back to the start.  
In the following example code, older Spirit would successfully parse the input. New Spirit does not.  
```cpp  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct checkNumber_class {  
    template <typename T, typename Iterator, typename Context>  
    void on_success(Iterator const&, Iterator const&, T& ast, Context const& context)  
    {  
        if (ast > 100) { // Number is too big, so let's fail the parser.  
            _pass(context) = false;  
        }  
    }  
};  
  
// This parser should reject numbers bigger than 100.  
const auto numberRule = x3::rule<checkNumber_class, int>{"my_rule"} = x3::int32;  
// This parser should parse all numbers.  
const auto numberRule2 = x3::rule<class checkNumber2_class, int>{"my_rule2"} = x3::int32;  
const auto grammar = numberRule | numberRule2;  
  
int main(int argc, char* argv[])  
{  
    auto input = std::string{"2000"};  
    auto begin = input.begin();  
    int attr;  
    auto ret = x3::parse(begin, input.end(), grammar, attr);  
  
    std::cerr << "ret" << " = " << ret << "\n";  
    std::cerr << "attr" << " = " << attr << "\n";  
}  
```  
The code above is really just an example, in my actual parser, the second rule would be constrained by some other condition, so the parser could fail completely. These are the relevant grammars:  
- [the top-level rule where the alternatives are](https://github.com/CESNET/netconf-cli/blob/master/src/grammars.hpp#L98)  
- [definition of one of the alternatives](https://github.com/CESNET/netconf-cli/blob/master/src/path_parser.hpp#L20)  
- [the `on_success` handler that fails the parser](https://github.com/CESNET/netconf-cli/blob/master/src/ast_handlers.hpp#L156)  
  
So, the problem in my code is this part:  
```  
(presenceContainerPath | listInstancePath | leafListElementPath | writableLeafPath)  
```  
The parser tries `presenceContainerPath`, it passes, then the `on_success` handler says no, so the parser tries `listInstancePath`, but there's no more input to parse, because `presenceContainerPath` didn't give it back.  
  
Now, I am not sure if this behavior is intentional or not. Probably yes? If so, then what do you suggest I should do? I read somewhere, that you shouldn't create alternative parsers where the alternatives are the same (like `int32 | int32`), so maybe that's the problem here?  
  
Also, I thought about rolling back the iterator inside `presenceContainerPath_class` (and others), but I would have to save it somewhere first and I don't how I would do that.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-11-03 18:14:19 UTC  
> Updated at: 2021-11-05 22:31:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-959797022  

Well, that's unfortunate, I have no idea why Spirit does not roll back the iterator in alternative parser (but roll backs in sequence parser!), I think we need to fix that, and it will also pave the road to boostorg/spirit#835.  
  
> I read somewhere, that you shouldn't create alternative parsers where the alternatives are the same (like `int32 | int32`), so maybe that's the problem here?  
  
That should not cause issues, having the same branches is simply redundant (clever optimization compiler should even be able to fold them). Your case is also different because branches in `numberRule | numberRule2` are **not** same.  
  
> Also, I thought about rolling back the iterator inside `presenceContainerPath_class` (and others), but I would have to save it somewhere first and I don't how I would do that.  
  
It is already there, in the first parameter, and the current iterator in the second parameter.  
  
```cpp  
    void on_success(Iterator const& was, Iterator& will, T& ast, Context const& context)  
    {  
        if (ast > 100) { // Number is too big, so let's fail the parser.  
            _pass(context) = false;  
            will = was; // roll back the iterator  
        }  
    }  
```

---

## Comment 4

> Username: djowel  
> Created at: 2021-11-04 00:35:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960332918  

As mentioned here: https://github.com/boostorg/spirit/issues/704  
  
The fundamental rules for parsing are as follows ([https://bit.ly/3GSjalg](https://bit.ly/3GSjalg)):  
  
- The parser returns true if successful, false otherwise.  
- If successful, first is incremented N number of times, where N is the number of characters parsed. N can be zero --an empty (epsilon) match.  
- If successful, the parsed attribute is assigned to attr  
- If unsuccessful, first is reset to its position before entering the parser function. attr is untouched.  
  
If this is not applied consistently, then it is a bug

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-11-04 01:04:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960351227  

In the example above `int32` succeeds, and so does `numberRule`, but then the `on_success` callback changes success status to failure, so the `x3::parse` function fails -- who should have been reset the iterator? And how we could achieve `attr is untouched.` without always parsing into a temporary?

---

## Comment 6

> Username: djowel  
> Created at: 2021-11-04 01:18:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960356714  

> And how we could achieve `attr is untouched.` without always parsing into a temporary?  
  
Good question. But that is another matter, no?

---

## Comment 7

> Username: djowel  
> Created at: 2021-11-04 01:21:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960358037  

> but then the `on_success` callback changes success status to failure  
  
  
  
> callback changes success status to failure, so the `x3::parse` function fails -- who should have been reset the iterator?  
  
Another good question. Hmmm...

---

## Comment 8

> Username: Kojoley  
> Created at: 2021-11-04 01:26:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960359859  

> > And how we could achieve `attr is untouched.` without always parsing into a temporary?  
>   
> Good question. But that is another matter, no?  
  
This statement is a part of the same rule, if we do not comply with any of either statements -- we not comply with the whole rule...

---

## Comment 9

> Username: djowel  
> Created at: 2021-11-04 01:31:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960361636  

> This statement is a part of the same rule, if we do not comply with any of either statements -- we not comply with the whole rule...  
  
To be pedantic, yes. But the rule could have been separated into two parts. They are orthogonal, as far as I can tell. Our task now is to refine these rules. They were designed some 20 years ago.

---

## Comment 10

> Username: djowel  
> Created at: 2021-11-04 01:39:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960364220  

Ah, don't get me wrong. I am not questioning your reasoning. I am actually questioning the validity of the rules and how they can be refined.

---

## Comment 11

> Username: Kojoley  
> Created at: 2021-11-04 01:51:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960368921  

The original issue will be fixed by doing iterator roll back in alternative parser, that's also what I propose in boostorg/spirit#704. My fear to doing that was performance impact, but I think we can agree that obeying to the rules in the documentation as they are written imply a way higher performance hit.

---

## Comment 12

> Username: djowel  
> Created at: 2021-11-04 01:55:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960370301  

Keep in mind that we also need to determine the impact of any change to the rules, WRT to back ward compatibility and making sure all parsers written prior function as expected.

---

## Comment 13

> Username: djowel  
> Created at: 2021-11-04 02:50:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-960407237  

> The original issue will be fixed by doing iterator roll back in alternative parser, that's also what I propose in boostorg/spirit#704. My fear to doing that was performance impact, but I think we can agree that obeying to the rules in the documentation as they are written imply a way higher performance hit.  
  
Let's do it then.

---

## Comment 14

> Username: syyyr  
> Created at: 2021-11-04 14:09:42 UTC  
> Updated at: 2021-11-04 14:09:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-961013667  

Thank you very much for solving my issue, I tried the `x3::eps` trick and it worked.  
  
I'll keep this issue open, since you have a discussion here, but feel free to close, if you want.

---

## Comment 15

> Username: Kojoley  
> Created at: 2021-11-05 22:28:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-962257914  

> > but then the `on_success` callback changes success status to failure  
>   
> > callback changes success status to failure, so the `x3::parse` function fails -- who should have been reset the iterator?  
>   
> Another good question. Hmmm...  
  
And I think I know the answer: it is should be done by that callback. It can actually be done even now:  
  
```cpp  
    void on_success(Iterator const& was, Iterator& will, T& ast, Context const& context)  
    {  
        if (ast > 100) { // Number is too big, so let's fail the parser.  
            _pass(context) = false;  
            will = was; // roll back the iterator  
        }  
    }  
```  
  
@syyyr I suggest you to use this approach instead of the workaround.

---

## Comment 16

> Username: syyyr  
> Created at: 2021-11-06 09:54:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-962427076  

Thanks, I didn't realize that I could accept the `will` argument by non-const reference!

---

## Comment 17

> Username: syyyr  
> Created at: 2022-01-12 13:01:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-1011021773  

Hi again. I have now updated to Boost 1.78 and it seems that I now need to implement the iterator rollback in the on_success callback. I am referring to this (the code you suggested):  
```cpp  
    void on_success(Iterator const& was, Iterator& will, T& ast, Context const& context)  
    {  
        if (ast > 100) { // Number is too big, so let's fail the parser.  
            _pass(context) = false;  
            will = was; // roll back the iterator  
        }  
    }  
```  
There seems to be a problem here: taking the `will` argument by non-const reference makes Spirit totally disregard the function However, to implement your suggestion, I need to take it by non-const reference, otherwise I won't be able to rollback the iterator. Here is the code which shows, that it isn't called:  
```cpp  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct my_class {  
    template <typename T, typename Iterator, typename Context>  
    void on_success(Iterator const&, Iterator &, T& ast, Context const& context)  
    {  
        std::cerr << "on_success\n";  
    }  
};  
  
const auto numberRule = x3::rule<my_class, int>{"my_rule"} = x3::int32;  
const auto grammar = numberRule;  
  
int main(int argc, char* argv[])  
{  
    auto input = std::string{"2000"};  
    auto begin = input.begin();  
    int attr;  
    auto ret = x3::parse(begin, input.end(), grammar, attr);  
  
    std::cerr << "ret" << " = " << ret << "\n";  
    std::cerr << "attr" << " = " << attr << "\n";  
}  
```  
In the example, `my_class::on_success` isn't called. If I add const to its second argument, it gets called. Is this correct behavior? Or maybe I'm doing something wrong?

---

## Comment 18

> Username: Kojoley  
> Created at: 2022-01-13 23:22:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-1012608974  

> In the example, `my_class::on_success` isn't called. If I add const to its second argument, it gets called. Is this correct behavior? Or maybe I'm doing something wrong?  
  
boostorg/spirit#711 will fix that.

---

## Comment 19

> Username: syyyr  
> Created at: 2022-01-15 18:32:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/703#issuecomment-1013731958  

Thanks!
