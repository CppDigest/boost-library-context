# #511 - X3: attribute of rule cannot be ignored in boost 1.70.0 [Closed]

> Username: wanghan02  
> Created at: 2019-06-03 11:13:35 UTC  
> Updated at: 2020-12-26 20:58:05 UTC  
> Closed at: 2020-12-26 13:55:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/511  

This simple code below triggers `static_assert(!has_attribute, "The rule requires an input attribute. Check your parser.")` in rule.hpp since [commit afe763b](https://github.com/boostorg/spirit/commit/afe763b61ece1a9fea5aee419920e8526e6dfcef) @Kojoley.  Let's say we have defined a rule with an attribute. We may use this rule in many places. In some places we may want to use this attribute, but in other places we may just want the boolean match result. This static_assert forces us to use the attribute every time (or attach a dummy semantic action as a workaround?...). Is it possible to remove this static_assert? Thanks!  
  
```  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
x3::rule<struct dummy1, std::string> ruleName("name");  
  
auto const ruleName_def = +x3::alpha;  
  
BOOST_SPIRIT_DEFINE(ruleName);  
  
int main() {  
	std::string str("test");  
	bool match = x3::parse(str.begin(), str.end(), x3::omit[ruleName]);  
	std::cout << (match ? "match" : "not match") << std::endl;  
    return 0;  
}  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-06-03 11:58:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498227250  

> This static_assert forces us to use the attribute every time (or attach a dummy semantic action as a workaround?...).  
  
It is what the assertion is about. To call a rule you have to have an attribute of the type the rule was defined with, which previously was silently synthesized imposing full attribute manipulation overhead. The same situation is with semantic actions, when an outer parser does not have an attribute and you do not need it in the semantic action.  
  
> Is it possible to remove this static_assert?  
  
I would like to not do it. It needs to return back attribute synchronization in rules that was removed.  
  
P.S. If rules were not strictly typed, you instead have to instantiate the rule with the all used attribute types - the different side of the coin, more flexible, but less user friendly.

---

## Comment 2

> Username: wanghan02  
> Created at: 2019-06-03 12:34:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498237730  

Let's say we have to define a very complex rule with an attribute. Do you mean we cannot use this rule in a simple match or not parser?

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-06-03 16:27:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498328995  

Without synthesizing a temporary attribute - no. The assertion unrevealed a performance problem in your parser, I understand that the easiest way is to ignore it, but is not better to fix it?

---

## Comment 4

> Username: djowel  
> Created at: 2019-06-03 21:56:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498441380  

> which previously was silently synthesized imposing full attribute manipulation overhead.  
  
What do you mean by that? Can you explain a bit more? I didn't realize the static_assert was there until now. We might have to rethink the situation and the use-case. I too sometimes use parsers with or without passing attributes. It is a valid use-case, IMO.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-06-03 22:46:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498453961  

> > which previously was silently synthesized imposing full attribute manipulation overhead.  
>  
> What do you mean by that? Can you explain a bit more?  
  
You have a rule that has an attribute, to call it you have to supply one. If you do not need the result you still have to create a temporary to supply to the rule. The rule parser will create a result (an AST for example) in that temporary just to check if the parser matches.  
  
In other words (in code):  
```cpp  
bool can_parse(char const* s, char const* e)  
{  
    return x3::parse(s, e, x3::int % ';');  
}  
```  
```cpp  
bool can_parse(char const* s, char const* e)  
{  
    std::vector<int> tmp;  
    return x3::parse(s, e, x3::int % ';', tmp);  
}  
```

---

## Comment 6

> Username: wanghan02  
> Created at: 2019-06-04 09:15:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498591236  

I still don't get the benefit here. To use a rule with an attribute in a match or not parser,  
  
- before we silently synthesize an attribute  
- now we have to explicitly provide a dummy attribute to the rule  
  
The performance is the same. But now we need to write more code. It also breaks old parsers that used to work. At least it should be controlled by a macro and the default option should make all old parsers work.

---

## Comment 7

> Username: OBorce  
> Created at: 2019-06-04 10:29:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498615561  

I am using it in my tests where I pass my rule as a template, any quick fix?  
  
https://github.com/OBorce/zero-preprocessor/blob/5fbcb72b72066207ef34f1f44a9ff7ab387dc4ec/tests/test_std_rules.cpp#L12-L38

---

## Comment 8

> Username: djowel  
> Created at: 2019-06-04 22:43:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498870205  

> You have a rule that has an attribute, to call it you have to supply one. If you do not need the result you still have to create a temporary to supply to the rule. The rule parser will create a result (an AST for example) in that temporary just to check if the parser matches.  
  
Instead of the static_assert, what we need to do is *not* create a temporary if the attribute is unused_type. If that's not already what's happening, then it is a bug (although the temporary would most probably be optimized out by the compiler anyway).

---

## Comment 9

> Username: djowel  
> Created at: 2019-06-04 22:48:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498871317  

> I still don't get the benefit here. To use a rule with an attribute in a match or not parser,  
>   
> * before we silently synthesize an attribute  
> * now we have to explicitly provide a dummy attribute to the rule  
>   
> The performance is the same. But now we need to write more code. It also breaks old parsers that used to work. At least it should be controlled by a macro and the default option should make all old parsers work.  
  
I agree. Actually we can do better by detecting unused_type and not generating a temporary.

---

## Comment 10

> Username: wanghan02  
> Created at: 2019-06-05 07:35:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-498972702  

> I agree. Actually we can do better by detecting unused_type and not generating a temporary.  
  
But then any operation on x3::val_(ctx) inside semantic actions in that rule will fail...

---

## Comment 11

> Username: djowel  
> Created at: 2019-06-06 00:12:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-499301079  

> But then any operation on x3::val_(ctx) inside semantic actions in that rule will fail...  
  
Sure. You can't have everything. Very good point though. That leads me to think that the best option is to simply remove the static_assert. The temporary would most probably be optimized out by the compiler anyway.

---

## Comment 12

> Username: wanghan02  
> Created at: 2019-06-07 06:45:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-499776587  

> Sure. You can't have everything. Very good point though. That leads me to think that the best option is to simply remove the static_assert. The temporary would most probably be optimized out by the compiler anyway.  
  
I agree.

---

## Comment 13

> Username: Kojoley  
> Created at: 2019-06-07 15:56:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-499940899  

> The performance is the same. But now we need to write more code. It also breaks old parsers that used to work. At least it should be controlled by a macro and the default option should make all old parsers work.  
  
Of course there will be no benefits if you are going for a workaround instead of a fix.  
  
> I am using it in my tests where I pass my rule as a template, any quick fix?  
  
Supply an output value:  
```cpp  
Rule::attribute_type unused_attr{};  
bool r = x3::parse(begin, end, rule[w], unused_attr);  
```  
  
> the temporary would most probably be optimized out by the compiler anyway  
  
It requires a full inline up to the temporary (which cannot happen if there is a recursion), and even in that case not a simple optimization as you can imagine. For example in the snippet above the temporary is not optimized out by GCC 10 (https://godbolt.org/z/rhvkz6) or Clang 9 (https://godbolt.org/z/bPEKCH).

---

## Comment 14

> Username: djowel  
> Created at: 2019-06-07 23:48:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500072213  

> Supply an output value:  
>   
> ```c++  
> Rule::attribute_type unused_attr{};  
> bool r = x3::parse(begin, end, rule[w], unused_attr);  
> ```  
  
That should be the default behavior if an attribute is not supplied. If not, it is a bug. To be clear, by design, this:  
  
```x3::parse(begin, end, r);```  
  
should be equivalent to this:  
  
```x3::parse(begin, end, r, x3::unused);```  
  
Am I misunderstanding the essence of this issue? I fear I might be.

---

## Comment 15

> Username: Kojoley  
> Created at: 2019-06-08 00:11:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500074909  

It is a dirty workaround that has significant performance cost (as I showed it in the previous post), that is why it must not be performed silently behind the scene. A possible right way to solve it is to introduce a new parse_rule instantiation macro that deals with this case (but it has a downside of non-customizable linker error if a user forgot to use the macro). That is my opinion, you may have other.

---

## Comment 16

> Username: djowel  
> Created at: 2019-06-08 00:16:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500075531  

> It is a dirty workaround that has significant performance cost (as I showed it in the previous post), that is why it must not be performed silently behind the scene. A possible right way to solve it is to introduce a new parse_rule instantiation macro that deals with this case (but it has a downside of non-customizable linker error if a user forgot to use the macro). That is my opinion, you may have other.  
  
For now, please remove the static_assert. If you, or @wanghan02 can make another "issue" about this significant performance cost, preferably with some code and assign it to me, I'd like to find a solution.

---

## Comment 17

> Username: Kojoley  
> Created at: 2019-06-08 11:57:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500118571  

I am not going myself legalize a thing that was previously possible only in the same translation unit, worked due to a bug and a copypaste from Qi.

---

## Comment 18

> Username: djowel  
> Created at: 2019-06-08 12:20:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500119990  

> I am not going myself legalize a thing that was previously possible only in the same translation unit, worked due to a bug and a copypaste from Qi.  
  
I do not even know what you mean. But here's what I know: it's a freakin' PAIN to work with you!

---

## Comment 19

> Username: Kojoley  
> Created at: 2019-06-08 14:13:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500127347  

> I do not even know what you mean.  
  
I mean this https://wandbox.org/permlink/narFEvZFzyigJt5d. It was failing at linking, now during compiling at the static assert. It was working only when the rule was used in the same TU where it was defined because of a bug.  
  
> But here's what I know: it's a freakin' PAIN to work with you!  
  
Removing the static assert is a no way back point. You would prefer if I did not highlight the problem?

---

## Comment 20

> Username: cppljevans  
> Created at: 2019-06-08 15:27:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500132718  

On 6/8/19 9:13 AM, Nikita Kniazev wrote:  
>     I do not even know what you mean.  
>   
> I mean this https://wandbox.org/permlink/narFEvZFzyigJt5d. It was  
> failing at linking, now during compiling at the static assert.   
  
This would have been clearer if it had said:  
  
  With boost 1.69.0, it fails at link time.  
  With boost 1.70.0, it fails at compile time.  
  
This can be seen by switching the boost version in the  
left-hand column of the wandbox.org location.  
  
> It was working only when the rule was used in the same TU  
> where it was defined because of a bug.  
  
Could you please show the actual code where this occurs?  
It **really** helps when you provide concrete examples!  
  
What's immediately obvious to you (because it's fresh in  
your mind) is probably not to most people :(  Please keep  
that in mind when you're tempted to write terse answers.  
  
[snip]

---

## Comment 21

> Username: djowel  
> Created at: 2019-06-08 23:05:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-500171898  

> I mean this https://wandbox.org/permlink/narFEvZFzyigJt5d. It was failing at linking, now during compiling at the static assert. It was working only when the rule was used in the same TU where it was defined because of a bug.  
>   
  
First you say 'The assertion unrevealed a performance problem'. I said remove the assert and start another issue about this performance problem and I will attempt a fix. Now you are saying a totally different thing: linker error due to a copy paste bug that you did not even bother to elaborate.   
  
Anyway, in the interest of moving forward, I'll file two tickets with these two separate issues.

---

## Comment 22

> Username: benstadin  
> Created at: 2019-07-01 14:39:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507293260  

Any update on this? I'm having difficulties to build mapnik on OS X due to this bug.

---

## Comment 23

> Username: djowel  
> Created at: 2019-07-01 22:19:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507446170  

> Any update on this? I'm having difficulties to build mapnik on OS X due to this bug.  
  
I'll have a look later today.

---

## Comment 24

> Username: djowel  
> Created at: 2019-07-02 12:47:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507661367  

> I'll have a look later today.  
  
Alright, I removed the static_assert and added @Kojoley's  test (https://wandbox.org/permlink/narFEvZFzyigJt5d). Guess what? The test is passing, no linker errors (develop branch). I can confirm the linker error in Boost 1.70 and also Boost 1.69, but for some reason (and I need to know why), something in develop seems to have fixed the linker error. Anyway, for now, here's the relevant commit:  
  
https://github.com/boostorg/spirit/commit/885d3ac0135a8b1348f14d52d783723b0069383b

---

## Comment 25

> Username: Kojoley  
> Created at: 2019-07-02 23:09:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507877011  

> I removed the static_assert  
  
I am sure it is a bad thing to support hacks/workarounds in the user code. For example, mentioned above mapnik uses bunch of them ([1](https://github.com/mapnik/mapnik/blob/master/include/mapnik/boost_spirit_instantiate.hpp) [2](https://github.com/mapnik/mapnik/blob/70fbdd21c7879d9d46afe0fbdacbfecbb7fed37e/src/css_color_grammar_x3.cpp#L33-L34)).  
  
> and added @Kojoley's test Guess what? The test is passing, no linker errors (develop branch). I can confirm the linker error in Boost 1.70 and also Boost 1.69, but for some reason (and I need to know why), something in develop seems to have fixed the linker error.  
  
It was fixed by series of patches targeted parse_rule instantiation problems. #437 #456

---

## Comment 26

> Username: djowel  
> Created at: 2019-07-02 23:22:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507879659  

> > I removed the static_assert  
>   
> I am sure it is a bad thing to support hacks/workarounds in the user code. For example, mentioned above mapnik uses bunch of them ([1](https://github.com/mapnik/mapnik/blob/master/include/mapnik/boost_spirit_instantiate.hpp) [2](https://github.com/mapnik/mapnik/blob/70fbdd21c7879d9d46afe0fbdacbfecbb7fed37e/src/css_color_grammar_x3.cpp#L33-L34)).  
>   
> > and added @Kojoley's test Guess what? The test is passing, no linker errors (develop branch). I can confirm the linker error in Boost 1.70 and also Boost 1.69, but for some reason (and I need to know why), something in develop seems to have fixed the linker error.  
>   
> It was fixed by series of patches targeted parse_rule instantiation problems. #437 #456  
  
So if it was fixed, what's the big deal? Yes, I agree that it is a bad thing to support hacks/workarounds in the user code. Is that workaround still needed with the #437 and #456 fixes?

---

## Comment 27

> Username: Kojoley  
> Created at: 2019-07-02 23:35:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507882338  

> So if it was fixed, what's the big deal?  
  
The fix was targeted other problem but opened the way to shoot yourself in a foot by using non-attributeless rule in no attribute context, and static assert was preventing exactly what people are complaining. You just let more people make a mistake.

---

## Comment 28

> Username: djowel  
> Created at: 2019-07-02 23:58:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-507886630  

> The fix was targeted other problem but opened the way to shoot yourself in a foot by using non-attributeless rule in no attribute context, and static assert was preventing exactly what people are complaining. You just let more people make a mistake.  
  
If you can provide code that clearly "shoots myself in the foot", I can work on something reasonable. I'm sorry if I am not getting it. Passing in no attributes to an attributed rule has been and should always be a valid use case.

---

## Comment 29

> Username: cppljevans  
> Created at: 2019-07-05 21:49:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-508868660  

> > I'll have a look later today.  
>   
> Alright, I removed the static_assert and added @Kojoley's test (https://wandbox.org/permlink/narFEvZFzyigJt5d). Guess what? The test is passing, no linker errors (develop branch). I can confirm the linker error in Boost 1.70 and also Boost 1.69, but for some reason (and I need to know why), something in develop seems to have fixed the linker error. Anyway, for now, here's the relevant commit:  
>   
> [885d3ac](https://github.com/boostorg/spirit/commit/885d3ac0135a8b1348f14d52d783723b0069383b)  
  
I think maybe the reason the link error is no longer occurring is because of the   
recently added rule<...>::parse overload where attribute=unused:  
  
  https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/rule.hpp#L131  
  
This creates the dummy no_attr which is used to call the parse_rule overloaded on rule<...>::attribute_type&, and all the BOOST_SPIRIT_{DECLARE,DEFINE,INSTANTIATE}  
all use that overload; hence, there's no link error.  
  
AFAICT, that's why there's no longer any link error.

---

## Comment 30

> Username: djowel  
> Created at: 2019-07-06 13:25:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-508926408  

> This creates the dummy no_attr which is used to call the parse_rule overloaded on rule<...>::attribute_type&, and all the BOOST_SPIRIT_{DECLARE,DEFINE,INSTANTIATE}  
> all use that overload; hence, there's no link error.  
>   
> AFAICT, that's why there's no longer any link error.  
  
That was my intent actually, by first adding the linker error test code that I assumed will fail, then work on an overloaded call like that. I was surprised it went through. I'll close this one now. @Kojoley, feel free to open another ticket based on your objections.

---

## Comment 31

> Username: K-ballo  
> Created at: 2020-12-25 15:22:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751262529  

Any use of the `raw[]` directive over a rule is triggering this unfortunate assertion for Boost 1.70 (see https://wandbox.org/permlink/qvePP1eSEkmspzoK). Is there any known workaround, or should projects intended to support 1.70 ship an entire replacement of the directive?

---

## Comment 32

> Username: Kojoley  
> Created at: 2020-12-25 17:05:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751272211  

> Is there any known workaround, or should projects intended to support 1.70 ship an entire replacement of the directive?  
  
Fix your rules signature, or use attribute-less duplicates of these rules. In your example it means either `x3::rule<class p, int> int_;` ->`x3::rule<class p> int_;` or defining `x3::rule<class p> lit_int_;` and using it with `raw[]` directive.

---

## Comment 33

> Username: K-ballo  
> Created at: 2020-12-25 23:23:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751299241  

> > Is there any known workaround, or should projects intended to support 1.70 ship an entire replacement of the directive?  
>   
> Fix your rules signature, or use attribute-less duplicates of these rules. In your example it means either `x3::rule<class p, int> int_;` ->`x3::rule<class p> int_;` or defining `x3::rule<class p> lit_int_;` and using it with `raw[]` directive.  
  
@Kojoley Introducing an attribute-less duplicate of the rather complex rule wouldn't be at all reasonable. I figured I'd just do `raw[omit[p]]` but somehow that didn't work either... How would we go about fixing our rules signatures?

---

## Comment 34

> Username: Kojoley  
> Created at: 2020-12-26 01:03:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751305832  

> @Kojoley Introducing an attribute-less duplicate of the rather complex rule wouldn't be at all reasonable.  
  
1) You asked for a workaround.   
2) I do not see a much problem in doing `x3::rule<class p> lit_int_; auto const lit_int__def = int__def;`, especially when you are elimination unneeded attribute construction+manipulation+destruction.  
  
> I figured I'd just do raw[omit[p]] but somehow that didn't work either...  
  
Yup, that's because `omit` relies on a rule to drop an attribute. This implementation will work:  
  
```cpp  
template <typename Subject>  
struct omit2_directive : x3::unary_parser<Subject, omit2_directive<Subject>>  
{  
    typedef x3::unary_parser<Subject, omit2_directive<Subject> > base_type;  
    typedef x3::unused_type attribute_type;  
    static bool const has_attribute = false;  
  
    typedef Subject subject_type;  
    constexpr omit2_directive(Subject const& subject)  
      : base_type(subject) {}  
  
    template <typename Iterator, typename Context, typename RContext>  
    bool parse(Iterator& first, Iterator const& last  
      , Context const& context, RContext& rcontext, x3::unused_type) const  
    {  
        typename x3::traits::attribute_of<Subject, Context>::type unused_attr;  
        return this->subject.parse(first, last, context, rcontext, unused_attr);  
    }  
};  
  
struct omit2_gen  
{  
    template <typename Subject>  
    constexpr omit2_directive<typename x3::extension::as_parser<Subject>::value_type>  
    operator[](Subject const& subject) const  
    {  
        return { x3::as_parser(subject) };  
    }  
};  
  
omit2_gen const omit2{};  
```  
  
> How would we go about fixing our rules signatures?  
  
Remove attribute type from rule signatures where you are not using attribute values.

---

## Comment 35

> Username: K-ballo  
> Created at: 2020-12-26 11:55:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751348633  

By duplicate I understood a complete new rule that duplicates the entire parser, not one that simply drops the attribute and defers to the original (that much we can afford). The fact that the attribute dropping `omit[]` directive fails to drop the attribute of the rule made me suspect simply dropping the attribute was not an option.  
  
It's rather surprising that x3 itself would prevent its own `raw[]` and `omit[]` directives from performing their job. Is this a bug in `omit[]` as well? and has it been fixed since 1.70.0?

---

## Comment 36

> Username: K-ballo  
> Created at: 2020-12-26 12:46:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751352658  

> By duplicate I understood a complete new rule that duplicates the entire parser, not one that simply drops the attribute and defers to the original (that much we can afford).  
  
Unfortunately this doesn't work either. The rule in question references other rules, and now is these rules complain about their attributes. For it to work we would have to duplicate the entire tree. This is a rather complex recursive grammar, and we don't need/want to store the entire tree, we only need to destructure the top-level elements and everything else we want only as a string.  
  
Let me know if you can think of a reasonable workaround. We may just have to wait a few years until 1.70 is no longer supported.

---

## Comment 37

> Username: djowel  
> Created at: 2020-12-26 13:28:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751355681  

Reopened this issue for discussion. Is this still an issue post 1.70?

---

## Comment 38

> Username: Kojoley  
> Created at: 2020-12-26 13:55:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751357872  

> Is this still an issue post 1.70?  
  
No. It is not even possible.

---

## Comment 39

> Username: K-ballo  
> Created at: 2020-12-26 14:26:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751361327  

> Reopened this issue for discussion. Is this still an issue post 1.70?  
  
We've only hit this regression on 1.70, but there appears to be no workaround other than duplicating entire grammars.  
  
It would be good to add tests to ensure that `raw[]` and `omit[]` are behaving as expected, including in complex situations like grammars in which one rule refers to another rule.

---

## Comment 40

> Username: djowel  
> Created at: 2020-12-26 20:58:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/511#issuecomment-751395496  

> It would be good to add tests to ensure that `raw[]` and `omit[]` are behaving as expected, including in complex situations like grammars in which one rule refers to another rule.  
  
Would you care to do a PR for that?
