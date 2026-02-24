# #618 - Type safety of Boost Qi/X3 parse [Closed]

> Username: cppljevans  
> Created at: 2020-07-21 22:48:02 UTC  
> Updated at: 2025-05-10 00:32:57 UTC  
> Closed at: 2025-05-10 00:32:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/618  

This simply echoes the issue raised in the stackoverflow post here:  
  
  https://stackoverflow.com/questions/62986317/type-safety-of-boost-qi-x3-parse  
  
about the parse returning the attribute value of "hello world" when it really  
should not compile.  
  
Test case was provided by sehe in that stackoverflow thread.

---

## Comment 1

> Username: djowel  
> Created at: 2020-07-22 00:54:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662180310  

Do you want to disallow parsing `double_` into `int` or `char`? In c++ this is allowed : `char x = 66.0;`... granted you'll get a warning.

---

## Comment 2

> Username: cppljevans  
> Created at: 2020-07-22 11:47:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662407786  

@djowel, I had a hard time understanding your point until I changed the value declaration to:  
```c++  
  #define ATTR_TYPE 2  
  #if ATTR_TYPE==0  
    std::string value="dummy init string.";  
  #elif ATTR_TYPE==1  
    std::vector<int> value;    
  #elif ATTR_TYPE==2  
    std::vector<char> value;    
  #else  
    std::vector<double> value;  
  #endif  
```  
and observed the output:  
  
  h e l l o   w o r l d   
  
and realized that the `std::vector<double>` from  *x3::_double was being converted to `std::vector<char>`.  
Now I understand sehe's point, but sehe could have made it clearer if he would have replaced the comment:  
  
`  //std::string command = "f: hello world"`  
  
with:  
  
`//the numbers in the following string, when interpreted as char's and then to string are "hello world"`  
  
So, maybe this is not a bug, but also it seems it's very counter-intuitive :(  
I don't know what's best; so, I'll understand if you just close this an issue  
as "won't fix because not **really** a bug".

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-07-22 12:16:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662419631  

The whole story is about the other thing. `double_` parser with container attribute does not compile, but `'x' >> double_` does. In other words, sequence collapsing activates for sequence length of one.

---

## Comment 4

> Username: djowel  
> Created at: 2020-07-22 22:18:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662725908  

> So, maybe this is not a bug, but also it seems it's very counter-intuitive :(  
> I don't know what's best; so, I'll understand if you just close this an issue  
> as "won't fix because not **really** a bug".  
  
I don't know. Your thoughts, @Kojoley ?  
  
> and realized that the `std::vector<double>` from *x3::_double was being converted to `std::vector<char>`.  
> Now I understand sehe's point, but sehe could have made it clearer if he would have replaced the comment:  
>   
> ` //std::string command = "f: hello world"`  
>   
> with:  
>   
> `//the numbers in the following string, when interpreted as char's and then to string are "hello world"`  
  
Yes indeed. Maybe you can comment about this there. I wanted to ping Sehe here, but I can't seem to find his handle. I'd love to hear a consensus.

---

## Comment 5

> Username: Kojoley  
> Created at: 2020-07-22 22:37:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662731964  

`*double_` into `std::string` is not a bug, but we can turn it into a compile error by disallowing `double_` into integral types, what I find a reasonable requirement.

---

## Comment 6

> Username: sehe  
> Created at: 2020-07-23 00:15:46 UTC  
> Updated at: 2020-07-23 00:16:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662760125  

I don't consider it a bug either. I'm not fond of disallowing implicit conversions. Like I argue [in my answer](https://stackoverflow.com/a/62986395/85371), very similar situations would benefit from implicit conversions.  
  
The fact that the language will implicitly convert the double into integral types with potential loss of accuracy is a _language issue_ IMO. I suppose people can lint their code, enable `-Wconversion` and friends if they want to catch it it seems to have little to do with Spirit.  
  
Demo https://godbolt.org/z/vKM9E7

---

## Comment 7

> Username: Kojoley  
> Created at: 2020-07-23 00:22:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662761809  

No one on the Earth will parse as double and then convert it to an integer, it is also a dangerous thing because if the value does not fit you get UB.

---

## Comment 8

> Username: sehe  
> Created at: 2020-07-23 02:39:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662791927  

@Kojoley  You say that as if it is a fact.  
  
I can see myself parsing input where fractions are admissable, but I'm not interested in them. Not quite astonishing to want to allow people to enter `2e10` instead of `20000000000` either. Finally, I'm always using `qi::double_` toparse even for `float`¹ I **know** that's slightly different, because it's also floating point, but I maintain it's also the same: implicit, even potentally lossy conversion.  
  
The principle extends to all implicit conversions (including but not limited to constructors and conversion operators). You can special floating point to integrals but I question the gain. It does add complexity, and people can already use static analysis/compiler warnings.  
  
¹  (because at some point I detected accuracy loss when using `qi::float_` https://stackoverflow.com/questions/17391348/boost-spirit-floating-number-parser-precision/17393048#17393048).

---

## Comment 9

> Username: djowel  
> Created at: 2020-07-23 04:12:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662809671  

> No one on the Earth will parse as double and then convert it to an integer, it is also a dangerous thing because if the value does not fit you get UB.  
  
Arguably... But there are other conversions that are just fine, and we can't know beforehand which are fine and which are not. double to float, as @sehe says is one good example, or how about the other way around like int to double? That is perfectly fine. Or how about convertible user defined types? The list goes on. It is unacceptable if we special-case char and strings.  
  
IMO, we trust the programmer that he knows what he is doing and not act like the coding police. At least, perhaps we can invent a static_warning facility for such potentially lossy conversions, but I'm not sure if it's worth the trouble.

---

## Comment 10

> Username: Kojoley  
> Created at: 2020-07-23 12:41:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-662983813  

> I can see myself parsing input where fractions are admissable, but I'm not interested in them.  
  
Then you should use an integer parser and skip the mantissa, it will be faster and without UB.  
  
> Finally, I'm always using `qi::double_` toparse even for `float`  
  
Then your program contains implementation-defined behavior, which at least on one major compiler ([MSVC](https://docs.microsoft.com/en-us/cpp/c-language/conversions-from-floating-point-types?view=vs-2019)) is UB.  
  
> how about the other way around like int to double? That is perfectly fine.  
  
This is also may not be safe, if int does not fit into double it is implementation-defined behavior.  
  
---  
  
Gentlemen, thanks for the discussion, but you gave opinion on everything, except the original poster issue.  
  
I am posting alternative version that does not contain implicit conversions:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <vector>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    std::string command = "123";  
    std::vector<int> value;  
    parse(command.begin(), command.end(), x3::int_, value);            // this does not compile  
    parse(command.begin(), command.end(), x3::eps >> x3::int_, value); // this compiles  
}  
```  
  
I am not a fan of Qi's plain to container parsing, so I think both cases should not compile.

---

## Comment 11

> Username: cppljevans  
> Created at: 2020-07-23 15:16:54 UTC  
> Updated at: 2020-07-23 15:17:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663066558  

[djowel says](https://github.com/boostorg/spirit/issues/618#issuecomment-662809671):  
  
> IMO, we trust the programmer that he knows what he is doing and not act like the coding police. At least, perhaps we can invent a static_warning facility for such potentially lossy conversions, but I'm not sure if it's worth the trouble.  
  
OTOH, spirit shouldn't violate [POLA](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)  
by acting like a "coding nanny" and do all sorts of  
"helpful" things like all possible implicit conversions,  
even ones that are **lossy**, such as `double` -> `char`,  
and leading to violation of `POLA` as evidenced by the OP of  
the [SO post](https://stackoverflow.com/questions/62986317/type-safety-of-boost-qi-x3-parse).  
  
This **no code nanny rule** would not prevent the obviously  
valid **non lossy** conversion to `std::variant<T1,T2,...,Tn>`   
from any of `T1,T2,...,Tn`.  
  
OTOH, if the user actually want's a lossy conversion, then  
he should have to expend some effort to achieve that, such  
as via a semantic action.  IOW, spirit shouldn't presume to  
know too much about what the user wants (and then do  
something that violates `POLA`) and instead obligate the  
user to be explicit about what he wants.  
  
Of course requiring the user to be more explicit requires  
more work from the user, but that's also true of requiring  
the user to `type` all his variables; hence, the qoal is  
not requiring too much effort to be explicit, which is the  
hard part :(

---

## Comment 12

> Username: cppljevans  
> Created at: 2020-07-23 15:19:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663067805  

Mistakenly closed it :(

---

## Comment 13

> Username: djowel  
> Created at: 2020-07-23 22:46:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663268428  

> I am not a fan of Qi's plain to container parsing, so I think both cases should not compile.  
  
For better or worse, it is what it is and doing otherwise would cause a lot of disruptions on existing code. Again, IMO, I'm OK with issuing warnings (I think it is possible to invent a static warning scheme) just as a c++ would when you try to do a lossy conversion. If not, perhaps a strict PP config flag that disallows such things and make them compiler errors would be fine.

---

## Comment 14

> Username: djowel  
> Created at: 2020-07-23 22:54:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663270701  

> OTOH, spirit shouldn't violate [POLA](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)  
> by acting like a "coding nanny" and do all sorts of  
> "helpful" things like all possible implicit conversions,  
> even ones that are **lossy**, such as `double` -> `char`,  
> and leading to violation of `POLA` as evidenced by the OP of  
> the [SO post](https://stackoverflow.com/questions/62986317/type-safety-of-boost-qi-x3-parse).  
  
Making something easy does not equate to being a "coding nanny". Plain C/C++ makes it easy too. Same pattern emerges: allow it, but make it clear that it is UB, trust the programmer that he knows what he is doing, issue a warning. Some people want to make it an error, that is fine too. But offer a choice.  
  
In other words, when in doubt, do as C++ does.

---

## Comment 15

> Username: Kojoley  
> Created at: 2020-07-24 15:27:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663594785  

> In other words, when in doubt, do as C++ does.  
  
And C++ does what C does, which does what it does because fifty years ago no one knew what the hell implicit narrowing conversion leads to.  
  
When you said:  
  
> In c++ this is allowed : char x = 66.0;... granted you'll get a warning.  
  
Have you tried it yourself? GCC and Clang will not yell on you even on the highest warning level. https://godbolt.org/z/T5Wc8v

---

## Comment 16

> Username: jpyllman  
> Created at: 2020-07-24 16:41:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663628521  

-Wall and -Wextra have never been 'the highetst' warning level. But it is a sane set of many warnings. But the conversion is not included. :-( I guess because to many lines of code would fail even if 'correct'. But I normally have -Wconversion, -Wfloat-conversion, and -Wsign-conversion on. But sometime you have to turn them off because of some external dependency. :-(

---

## Comment 17

> Username: Kojoley  
> Created at: 2020-07-24 21:30:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663742588  

I do not think that your remark adds much value. Your meaning of 'warning level' seems to be somewhat different than mine, while I technically lied, Clang has `-Weverything` which could be considered the highest warning level, but taking your approach -- it will still be not the highest as both compilers have integrated static analyzer enabled separately (GCC `-fanalyzer` and Clang `--analyze`) which produces 'warnings' too. As you noticed, `-Wconversion` has not gained much appreciation and popularity to be included in any 'warning level', and at the moment I cannot suggest enabling it because Spirit itself is not clean for it (https://travis-ci.org/github/Kojoley/spirit/builds/711593885). Not to mention that in some places Spirit suppresses this kind of warnings (via `static_cast`) 'because C++ programmers know what they do!'.

---

## Comment 18

> Username: sehe  
> Created at: 2020-07-24 21:59:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663750896  

Unsubscribing from this thread.  
  
I'm with @djowel  :   
> For better or worse, it is what it is and doing otherwise would cause a lot of disruptions on existing code.   
  
I see this behavior as rational though sometimes surprising, but not error-prone. The worst that can be hurt would be a broken expectation. Nothing that wouldn't be caught on the first run. Maybe the documentation could highlight  attribute compatibility w.,r.t. strings being containers.  
  
I'm personally trying to think why we should be changing something so benign, when something similar crops up much more often: people who write this kind of attribute propagation hoping to capture the `raw[]` string representation. I have seen that confusion at least a few _dozen_ times, so much so that I just [included that hint](https://stackoverflow.com/questions/62986317/type-safety-of-boost-qi-x3-parse#comment111383757_62986395) as a comment in case it was the same this time.  
I think time would be better spent adding helpful examples / wording to the docs about applications like that.  
  
Of course it doesn't need to be a zero-sum game. If we can improve the situation without breaking stuff, that's fine. If it involves introducing a compile-time flag, keep in mind that it hurts test-coverage unless all tests run with all combinations of flags.

---

## Comment 19

> Username: djowel  
> Created at: 2020-07-24 22:03:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663752216  

Gents, let's not focus on what warning you will get, or what you won't get. I may be wrong on that but that is besides the point, although it actually reinforces my point if it does not (give you a warning). Spirit may unnecessarily suppress some warnings, that is another matter (sure, bring it up again, but not here). The question is whether we allow conversions on sequences. IMO, the answer is yes, and doing otherwise will break a lot of code. The second question is whether we provide a special case for the OP (double->char ?). IMO, the answer is also no. We'd want to avoid special cases.  
  
IMO, the reasonable approach is to detect this and issue a warning ourselves, if you care about such things.

---

## Comment 20

> Username: Kojoley  
> Created at: 2020-07-24 22:27:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663758677  

> The question is whether we allow conversions on sequences.  
  
It is a subsequent issue, the original one is about the other thing -- plain parser into container attribute. I am telling it the third time here already!  
  
> will break a lot of code  
  
@djowel since you have landed several times such changes I am not sure you are actually much cared about backwards compatibility :-)  
  
> IMO, the reasonable approach is to detect this and issue a warning ourselves, if you care about such things.  
  
I do not know about such mechanism, can you provide an example?  
  
---  
  
Funny enough, I am creating here an extra work for myself, while everybody says that I should not care and rest, so let it be.

---

## Comment 21

> Username: djowel  
> Created at: 2020-07-24 22:46:58 UTC  
> Updated at: 2020-07-24 22:52:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663762919  

> > The question is whether we allow conversions on sequences.  
>   
> It is a subsequent issue, the original one is about the other thing -- plain parser into container attribute. I am telling it the third time here already!  
  
I hear you. I'm just more concerned about the latter.  
  
> > will break a lot of code  
>   
> @djowel since you have landed several times such changes I am not sure you are actually much cared about backwards compatibility :-)  
  
I do! I may have violated that, sure, but that does not mean that I do not. I suspect that there are some cases where there's a balance between moving forward and backward compatibility. There are always exceptions to rules. It's not strictly just about backward compatibility. It's more about widespread code breakage. Some non-backward compatible changes might be justified if such changes do not cause widespread code breakage.   
  
Sure. Bring it up, but not here.  
  
> > IMO, the reasonable approach is to detect this and issue a warning ourselves, if you care about such things.  
>   
> I do not know about such mechanism, can you provide an example?  
  
I haven't looked, and IMO, it's not worth it, but there are some examples here using MPL:  
https://stackoverflow.com/questions/8936063/does-there-exist-a-static-warning  
  
> Funny enough, I am creating here an extra work for myself, while everybody says that I should not care and rest, so let it be.  
  
I am not fanatical about such things. If you are, then go for it.

---

## Comment 22

> Username: cppljevans  
> Created at: 2020-07-24 23:53:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663777003  

[@sehe said:](https://github.com/boostorg/spirit/issues/618#issuecomment-662791927)  
  
>   
> I can see myself parsing input where fractions are admissable, but I'm not interested in them. Not quite astonishing to want to allow people to enter `2e10` instead of `20000000000` either.   
  
But you could fairly easy do that without the implicit conversions:  
  
```c++  
    bool result=  
      x3::phrase_parse(  
        begin,  
        end,  
        double_to_int_parser, x3::blank,  
        value);  
```  
  
where `double_to_int_parser` is a custom parser which parses a  
`double` and converts it to an `int`.  Having to code the custom  
parser is a **little** inconvenient, but it also much clearer, which  
I think is a more important goal.  NOTE, I don't mean there  
was no implicit conversions in the custom parser, I mean there  
was no implicit conversions "hidden" deep inside the x3 code  
waiting to surprise some NB like the SO OP.

---

## Comment 23

> Username: cppljevans  
> Created at: 2020-07-25 11:55:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-663846947  

[@cppljevans said](https://github.com/boostorg/spirit/issues/618#issuecomment-663777003):  
> But you could fairly easy do that without the implicit conversions:  
>   
> ```c++  
>     bool result=  
>       x3::phrase_parse(  
>         begin,  
>         end,  
>         double_to_int_parser, x3::blank,  
>         value);  
> ```  
>   
> where `double_to_int_parser` is a custom parser which parses a  
> `double` and converts it to an `int`.   
  
> I mean there  
> was no implicit conversions "hidden" deep inside the x3 code  
> waiting to surprise some NB like the SO OP.  
  
My use of  the phrase:  
  
> "hidden" deep inside the x3 code  
  
was too harsh since I see it's done around this [real.hpp comment](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/numeric/real.hpp#L41), which is not **that** "hidden".  
  
Nevertheless, I'd suggest using something like the `explicit_convert_parser_type ` from [coilru online](http://coliru.stacked-crooked.com/a/6d411e76f063a746) which solves the SO OP problem; yet preserve's, with a little effort, @sehe's desires.

---

## Comment 24

> Username: Xeverous  
> Created at: 2020-11-29 23:27:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-735475066  

IMO (from quite long X3 user's perspective): I would prefer to have some way to enforce strict parsing that follows the same design as `std::chrono` - that is, all narrowing conditions must be explicit. The situation for numeric type convertions and compiler warning levels has no universally accepted "state" and is usually decided per project so I think something like a parser config type parameter would be the best way to let library user's decide.

---

## Comment 25

> Username: cppljevans  
> Created at: 2020-12-05 23:19:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-739429251  

@Xeverous could you please provide a little more detail on your suggestion.  I've briefly looked at  
https://en.cppreference.com/w/cpp/chrono, and although I see parse, that doesn't seem applicable  
because it would involve too much run-time. clock_cast might be applicable since it does something  
similar to an explicit cast, but I've not idea how this would fit into x3::phrase_parse of something  
similar.   Furthermore, why would this chrono-like proposal be better than the coilru online proposal  
I mentioned in my last comment?  
  
-regards,  
Larry

---

## Comment 26

> Username: Xeverous  
> Created at: 2020-12-08 11:28:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-740563429  

@cppljevans I'm not sure what did you mean - I was only after compile-time decisions, such as which types are allowed by which parsers

---

## Comment 27

> Username: cppljevans  
> Created at: 2020-12-08 18:40:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-740830317  

> @cppljevans I'm not sure what did you mean - I was only after compile-time decisions, such as which types are allowed by which parsers  
  
Yes, but my question was **how** are you proposing to do that.  
The reference to the std::chrono method was unclear.  
  
Also unclear is why you think the code here:  
  
  [coliru Spirit3TypeSafety](http://coliru.stacked-crooked.com/a/6d411e76f063a746)  
  
would not satisfy your desire for a "compile-time decision" to  
restrict "which types are allowed by which parsers".  For example,  
the example runs shows the user has complete control over which  
conversions take place.  For example:  
  
Line 124 shows where only 'double' "is allowed" by the parser.    
Line 102 shows where a 'std::string' "is allowed" by the parser.    
Line 85 shows where an 'int' "is allowed" by the parser.  
  
Is that not what you're wanting?  
  
-Larry

---

## Comment 28

> Username: Xeverous  
> Created at: 2020-12-08 19:58:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-740941292  

> Yes, but my question was how are you proposing to do that.  
  
How - I'm not really sure, I haven't really gotten in the implementation of Spirit.  
  
> The reference to the std::chrono method was unclear.  
  
I meant only 1 specific aspect of its design - that narrowing convertions should be explicit.  
  
> Is that not what you're wanting?  
  
It is. The example does satisfy my compile-time convertion requirements but if such thing gets introduced - what would happen with implicit convertions that are already present in Spirit? I guess `x3::long_` should not be possible to be attributed to `int`  - I'm not sure what's the current state in such scenario.

---

## Comment 29

> Username: saki7  
> Created at: 2025-05-10 00:32:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/618#issuecomment-2868147223  

Closing this issue because it's stale, and I obviously see many Spirit experts on this thread simply disagreeing with the feature request.
