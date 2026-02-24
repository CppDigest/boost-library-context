# #426 - Comment skipper not working in current Boost Spirit [Closed]

> Username: mwpowellhtx  
> Created at: 2018-12-17 20:23:20 UTC  
> Updated at: 2018-12-19 16:33:20 UTC  
> Closed at: 2018-12-18 12:48:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/426  

I am getting these kinds of errors following some of the online examples, on version ``Boost v1.68.0``:  
  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2664	'bool boost::function4<R,T0,T1,T2,T3>::operator ()(T0,T1,T2,T3) const': cannot convert argument 4 from 'const Skipper' to 'T3'	Kingdom.Protobuf.Agents	d:\dev\boost.org\boost_1_68_0\boost\spirit\home\qi\nonterminal\rule.hpp	304	  
```  
  
Such a skipper is not a complex issue, but something is not right, I think:  
  
```C++  
template<typename It>  
struct ProtoCommentSkipper : qi::grammar<It> {  
  
    ProtoCommentSkipper() : ProtoCommentSkipper::base_type(skip) {  
  
        using qi::char_;  
        using qi::eol;  
        using qi::ascii::space;  
  
        skip %= space  
            | ("//" >> *(char_ - eol) >> eol)  
            | ("/*" >> *(char_ - "*/") >> "*/")  
            ;  
    }  
  
private:  
    qi::rule<It> skip;  
};  
```  
  
And in usage:  
  
```C++  
template<typename It, typename Skipper = qi::space_type>  
struct ProtoGrammar : qi::grammar<It, ast::proto_t(), Skipper> {  
    // ...  
    using skipper_type = Skipper;  
  
    ProtoGrammar() : ProtoGrammar::base_type(start) {  
        // ...  
    }  
  
private:  
    // ...  
    qi::rule<It, ast::proto_t(), skipper_type> start;  
};  
```  
  
The only difference is that I sub in the ``ProtoCommentSkipper`` and I get the errors. Instead of:  
  
```C++  
using iterator = std::string::const_iterator;  
using proto_grammar = ProtoGrammar<std::string::const_iterator>;  
// Where s is std::string ...  
iterator begin = s.begin(), end = s.end();  
const auto parse_tried = qi::parse(begin, end, proto_grammar{}, x);  
```  
  
I am trying this:  
  
```C++  
using iterator = std::string::const_iterator;  
using proto_comment_skipper = ProtoCommentSkipper<std::string::const_iterator>;  
using proto_grammar = ProtoGrammar<std::string::const_iterator, ProtoCommentSkipper<std::string::const_iterator>>;  
iterator begin = s.begin(), end = s.end();  
const auto parse_tried = qi::parse(begin, end, proto_grammar{}, proto_comment_skipper{}, x);  
```  
  
Seems like a possible duplicate of [Boost library spirit failed to build](/boostorg/spirit/issues/399), however, do you happen to have a simple example of a comment parser? Using a *Spirit Qi Confix*, for instance?

---

## Comment 1

> Username: mwpowellhtx  
> Created at: 2018-12-17 20:34:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-447989284  

The obvious answer it seems is that I should migrate to ``1.69.0`` to include the [PR](/boostorg/spirit/pull/397), I think. I'll stand by for a little feedback, however.  
  
Ultimately, I'm trying to fold the *Comment Skipper* in with a minimum of footprint. If I could simply specify the template type in the *Parent Grammar*, for instance, that would be great, notwithstanding that this [may not be possible](http://stackoverflow.com/questions/8521314/custom-skip-parser-with-boostspirit), at least not without actually furnishing the skipper instance itself.

---

## Comment 2

> Username: mwpowellhtx  
> Created at: 2018-12-18 17:38:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448305551  

@Kojoley Please explain yourself with [these comments](/boostorg/spirit/pull/427#issue-239304824). The illustrated code above shows you the exact opposite of that concern. In other words, specifically, the *Skipper* type is the ***SAME***, furnished to my grammar, and I've tried with or without furnishing my own *Skipper* instance. Same "assertion" occurs.  
  
For my part, I will check whether I am running against ``Boost 1.68`` or ``1.69``, considering the [PR](/boostorg/spirit/pull/397) probably fixes the issue.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-12-18 18:11:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448316036  

Your bug report is invalid.  
  
There is no MCVE in it. The code snippets you have provided works for me https://wandbox.org/permlink/R3V31gHIQ6Te7lco. The PR #397 cannot be related to anything as it targeted a deprecation warning that came from a change in other Boost library (https://github.com/boostorg/predef/commit/32d4581c1689370444f2e565cfbb8421d5071807).

---

## Comment 4

> Username: mwpowellhtx  
> Created at: 2018-12-18 18:11:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448316129  

@Kojoley We need an example how to furnish a custom skipper type, say comment skipper, because when I replace ``qi::space_type`` with my ``ProtoCommentSkipper<std::string::const_iterator>`` I get this assertion/"error".

---

## Comment 5

> Username: mwpowellhtx  
> Created at: 2018-12-18 18:13:52 UTC  
> Updated at: 2018-12-18 18:14:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448316861  

@Kojoley Is it a compiler difference? That "works" under *GCC*. It is breaking under *MSVC*.

---

## Comment 6

> Username: Kojoley  
> Created at: 2018-12-18 18:18:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448318170  

It works for me on MSVC (VS 15.9.4).

---

## Comment 7

> Username: mwpowellhtx  
> Created at: 2018-12-18 19:12:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448335530  

@Kojoley Thank you for that. It's a head-scratcher to me. I spun up an academic test case locally as well. I even fleshed in the integer parser bits. That does "work", and in a variety of actual tests, with or without comments leading or trailing comments, same line, preceding line, etc. I need to investigate the other bits of my production code why that is not working. Unfortunately I do not have a "minimum" example for that; the code is the code, but I will give it some thought.  
  
Just off the cuff, one thought is that some of the rules are using no skipper at all (default, ``space``?), whereas the head or start rules would use the comment skipper. Could that be the issue? Masked by the "default" to ``space`` skipper? In which case, that is intentional; I would rather default to a true "default" or "none expected" or "unused" skipper if possible. Or does it matter? In other words, there is a reason why I do not want the skipper involved; the phrase ought not to involve anything skips. But to this end, I should use something like ``no_skip`` or other Qi directives?  
  
Thanks for any insights...

---

## Comment 8

> Username: mwpowellhtx  
> Created at: 2018-12-18 19:23:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448338968  

@Kojoley Okay, so preliminary thoughts, I should avoid ``parse`` and instead focus on ``phrase_parse``, in which case I can provide the *skipper* instance. I will look into that further. Thanks!

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-12-18 19:23:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448339069  

There is no default skipper. The `parse` function uses your grammar without a skipper (internally it passes `unused_type` as a skipper to simplify implementation). The `phrase_parse` function expects a skipper to be provided as a fourth argument, you cannot omit it. If you are using `grammar`/`rule`s with `phrase_parse`, they must be instantiated without skipper type (again, internally it will be `unused_type`) or with a skipper type that the passed skipper (fourth argument to `phrase_parse`) must be convertible to.

---

## Comment 10

> Username: Kojoley  
> Created at: 2018-12-18 19:26:38 UTC  
> Updated at: 2018-12-18 19:39:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448340065  

If you need a skipper, but you do not want to use `phrase_parse` and/or instantiate outer `rule`/`grammar` with a skipper type, you can use [`skip` directive](  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/html/spirit/qi/reference/directive/skip.html).  
  
But be aware that `foo(first, last, r, s)` is equal to `parse(first, last, r)`  
  
```cpp  
template <template Iterator, template Skipper>  
bool foo(Iterator& first, Iterator last, rule<Iterator> const& r, Skipper const& s)  
{  
    return parse(first, last, skip(s)[r]);  
}  
```  
  
In other words:  
```cpp  
skip(s)[r];       // <-- this and  
r;                // <-- this are equal if `r` has no skipper type because  
rule<Iterator> r; // that way it explicitly states as it does not need skipping  
```

---

## Comment 11

> Username: mwpowellhtx  
> Created at: 2018-12-19 02:12:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448443989  

Ah, I think I see the problem in my code. I did actually have a rule in which I was defining it in terms of ``qi::skip(qi::space)[...] >> qi::eoi``, which I think was some of the conflict when I specified the *Skipper* type. Originally there was "agreement" with the ``space_type``, but when I changed the *Skipper*, then suddenly things broken down along the asserted lines. Makes sense.

---

## Comment 12

> Username: Kojoley  
> Created at: 2018-12-19 12:01:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448570370  

I suggest to switch to X3. It does not have those limitations.

---

## Comment 13

> Username: mwpowellhtx  
> Created at: 2018-12-19 16:23:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448655883  

@Kojoley Re: X3, I appreciate that, but unless the MSVC limitations have been resolved, it's a non-starter at the moment. At least the last time I checked.

---

## Comment 14

> Username: Kojoley  
> Created at: 2018-12-19 16:29:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448657806  

I am not sure what you are talking about, X3 test suit passes on VS2015U3+.

---

## Comment 15

> Username: mwpowellhtx  
> Created at: 2018-12-19 16:33:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/426#issuecomment-448659279  

@Kojoley I do not recall the precise set of circumstances, but I think it had something to do with available *C++14* or *17* support at the time.
