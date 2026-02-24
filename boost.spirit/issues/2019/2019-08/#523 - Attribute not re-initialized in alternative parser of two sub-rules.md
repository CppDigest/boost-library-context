# #523 - Attribute not re-initialized in alternative parser of two sub-rules [Closed]

> Username: rubenvb  
> Created at: 2019-08-03 17:18:26 UTC  
> Updated at: 2020-06-04 19:17:19 UTC  
> Closed at: 2020-06-04 19:17:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/523  

The following used to work as expected in Boost 1.69, but broke from 1.70 (and is still broken in 1.71b1rc1):  
```  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
template<typename T>  
inline auto as = [](auto&& p) { return x3::rule<struct _, T>{} = x3::as_parser(p); };  
  
const auto one_two = x3::rule<struct one_two, std::vector<int>>{"one two"}  
                   = as<std::vector<int>>(x3::int_ >> x3::int_)  
                   | as<std::vector<int>>(x3::int_ >> x3::attr(3));  
  
int main()  
{  
    const std::string input = "1";  
      
    std::vector<int> result;  
    x3::phrase_parse(input.begin(), input.end(),  
                     one_two,  
                     x3::space,  
                     result);  
      
    std::cout << BOOST_LIB_VERSION << ": "  << result.size() << '\n';  
}  
```  
  
[Code on coliru](http://coliru.stacked-crooked.com/a/1cf5f0043da8ae79)  
  
Expected is that the sub-rule introduced through `as` starts with an empty `vector`. Instead, somewhere post-1.69, it does not, and reuses the alternative's attribute resulting in a vector size of 3.  
  
This is an alternative implementation of  
```  
inline const auto reset_value = [](auto& context)  
{  
  x3::_val(context) = {};  
};  
const auto one_or_two = x3::rule<struct one_or_two, std::vector<int>>{"one or two"}  
                     %= int_ >> int_  
                      | x3::eps[reset_value] >> int_ >> attr(1)  
                      ;  
```  
But without semantic action, so I really prefer the sub-rule variant, which was broken in recent releases.

---

## Comment 1

> Username: djowel  
> Created at: 2019-08-03 22:41:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-517959435  

Could you help git bisect this to find the commit that broke this one? Thanks!

---

## Comment 2

> Username: rubenvb  
> Created at: 2019-08-04 10:06:44 UTC  
> Updated at: 2019-08-04 10:09:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-517990239  

Bisecting between 1.69.0 and 1.70.0 with the above test program gives me the following commit:  
c915ac4170d5664fd6cee96399d999c00bef3ebc  
  
If I revert that commit on 1.70.0 (and comment out the conflict in `test/x3/rule3.cpp`), the test case triggers a `static_assert`:  
  
```  
In file included from spirit/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:17,  
                 from spirit/include/boost/spirit/home/x3/nonterminal/rule.hpp:10,  
                 from spirit/include/boost/spirit/home/x3/nonterminal.hpp:10,  
                 from spirit/include/boost/spirit/home/x3.hpp:17,  
                 from main.c++:1:  
spirit/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp: In instantiation of 'struct boost::spirit::x3::traits::transform_attribute<std::vector<int>&, std::vector<int>, boost::spirit::x3::parser_id, void>':  
spirit/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:315:46:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >, std::vector<int>, false>, boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >, std::vector<int>, false> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type>; ActualAttribute = std::vector<int>; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = std::vector<int>; ID = one_two]'  
spirit/include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type>; Attribute_ = std::vector<int>; ID = one_two; RHS = boost::spirit::x3::alternative<boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >, std::vector<int>, false>, boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >, std::vector<int>, false> >; Attribute = std::vector<int>; bool force_attribute_ = false]'  
spirit/include/boost/spirit/home/x3/core/parse.hpp:119:14:   required from 'bool boost::spirit::x3::phrase_parse_main(Iterator&, Iterator, const Parser&, const Skipper&, Attribute&, boost::spirit::x3::skip_flag) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = boost::spirit::x3::rule_definition<one_two, boost::spirit::x3::alternative<boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >, std::vector<int>, false>, boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >, std::vector<int>, false> >, std::vector<int>, false>; Skipper = boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>; Attribute = std::vector<int>]'  
spirit/include/boost/spirit/home/x3/core/parse.hpp:151:33:   required from 'bool boost::spirit::x3::phrase_parse(const Iterator&, Iterator, const Parser&, const Skipper&, Attribute&, boost::spirit::x3::skip_flag) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = boost::spirit::x3::rule_definition<one_two, boost::spirit::x3::alternative<boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::int_parser<int> >, std::vector<int>, false>, boost::spirit::x3::rule_definition<<lambda(auto:1&&)> [with auto:1 = boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >]::_, boost::spirit::x3::sequence<boost::spirit::x3::int_parser<int>, boost::spirit::x3::attr_parser<int> >, std::vector<int>, false> >, std::vector<int>, false>; Skipper = boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>; Attribute = std::vector<int>]'  
main.c++:23:28:   required from here  
spirit/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp:85:23: error: static assertion failed: Exposed cannot be a reference type  
   85 |         static_assert(!std::is_reference<Exposed>::value,  
      |       
```  
  
Hmm reading the commit message it seems the commit purposely removes attribute construction from `rule` to make `rule`s "free" to use. This, however, constitutes quite the change in behaviour, seeing that a `rule` also provided "attribute scope" so to speak, in all previous releases.

---

## Comment 3

> Username: djowel  
> Created at: 2019-08-04 14:44:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-518009394  

Thanks! OK, I'll see what I can do.

---

## Comment 4

> Username: djowel  
> Created at: 2019-08-10 01:25:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520106683  

I'm looking at this now. I'd like to put this code snippet in a test to make sure it does not happen again (assuming it is indeed a bug; i'll need to investigate more). Is it possible for you to simplify the code more (preferably without the 'as' decorations), while still exhibiting the problem?

---

## Comment 5

> Username: rubenvb  
> Created at: 2019-08-10 10:17:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520137247  

The core issue is two rules in an alternative, which both result in a container:  
```  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
const auto alternative_one = x3::rule<struct alternative_one, std::vector<int>>{"alternative one"}  
                           = x3::int_ >> x3::int_;  
const auto alternative_two = x3::rule<struct alternative_two, std::vector<int>>{"alternative two"}  
                           = x3::int_ >> x3::attr(3);  
  
const auto one_two = x3::rule<struct one_two, std::vector<int>>{"one two"}  
                   = alternative_one  
                   | alternative_two;  
  
int main()  
{  
    const std::string input = "1";  
      
    std::vector<int> result;  
    x3::phrase_parse(input.begin(), input.end(),  
                     one_two,  
                     x3::space,  
                     result);  
      
    std::cout << BOOST_LIB_VERSION << ": " << result.size() << '\n';  
}  
```  
The `one_two` rule matches 1 or two ints, and it only one is specified, the second is always 3.  
From Boost 1.70 onwards, this rule results in a 3 element vector for single integer input.  
  
Currently the code above has the same result as the code below (without the subrules):  
```  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
const auto alternative_one //= x3::rule<struct alternative_one, std::vector<int>>{"alternative one"}  
                           = x3::int_ >> x3::int_;  
const auto alternative_two //= x3::rule<struct alternative_two, std::vector<int>>{"alternative two"}  
                           = x3::int_ >> x3::attr(3);  
  
const auto one_two = x3::rule<struct one_two, std::vector<int>>{"one two"}  
                   = alternative_one  
                   | alternative_two;  
  
int main()  
{  
    const std::string input = "1";  
      
    std::vector<int> result;  
    x3::phrase_parse(input.begin(), input.end(),  
                     one_two,  
                     x3::space,  
                     result);  
      
    std::cout << BOOST_LIB_VERSION << ": " << result.size() << '\n';  
}  
```  
Which seems wrong to me, at least in how I understood `x3::rule` and `operator|` as defined in Spirit.

---

## Comment 6

> Username: djowel  
> Created at: 2019-08-11 10:52:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520218543  

@Kojoley, do you have any thoughts on this one before I proceed? Seems like a bug alright.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-08-11 15:09:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520235570  

It does not seem like a bug to me. Spirit does not do roll back attributes on a backtrack and I see no reasons why rules should make any difference, so as for me 1.70 actually fixed inconsistency, moreover Qi behaves like post 1.70 X3 https://wandbox.org/permlink/BE67JBuhY3lDRGBq

---

## Comment 8

> Username: djowel  
> Created at: 2019-08-11 22:04:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520264867  

> It does not seem like a bug to me. Spirit does not do roll back attributes on a backtrack and I see no reasons why rules should make any difference, so as for me 1.70 actually fixed inconsistency, moreover Qi behaves like post 1.70 X3 https://wandbox.org/permlink/BE67JBuhY3lDRGBq  
  
Very good point. And that is one reason for the existence of the [hold directive](https://www.boost.org/doc/libs/1_55_0/libs/spirit/doc/html/spirit/qi/reference/directive/hold.html). When in doubt, I always refer to Qi. @rubenvb, hold documents this no-back-track-on-alternatives behaviour ("Alternative parsers normally do not rollback changes made to the outer attribute by a failed alternative") and provides the solution:  
  
```  
rule_t const one_two = qi::hold[alternative_one] | alternative_two;  
```

---

## Comment 9

> Username: rubenvb  
> Created at: 2019-08-12 15:47:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520481517  

There is no hold directive in X3 so that is not really a solution though...  
  
  
Op ma 12 aug. 2019 00:04 schreef Joel de Guzman <notifications@github.com>:  
  
> It does not seem like a bug to me. Spirit does not do roll back attributes  
> on a backtrack and I see no reasons why rules should make any difference,  
> so as for me 1.70 actually fixed inconsistency, moreover Qi behaves like  
> post 1.70 X3 https://wandbox.org/permlink/BE67JBuhY3lDRGBq  
>  
> Very good point. And that is one reason for the existence of the hold  
> directive  
> <https://www.boost.org/doc/libs/1_55_0/libs/spirit/doc/html/spirit/qi/reference/directive/hold.html>.  
> When in doubt, I always refer to Qi. @rubenvb <https://github.com/rubenvb>,  
> hold documents this no-back-track-on-alternatives behaviour ("Alternative  
> parsers normally do not rollback changes made to the outer attribute by a  
> failed alternative") and provides the solution:  
>  
> rule_t const one_two = qi::hold[alternative_one] | alternative_two;  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/spirit/issues/523?email_source=notifications&email_token=AACLLPWDIWBXSFWURELNEITQECEFJA5CNFSM4IJDPQMKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOD4BJZIY#issuecomment-520264867>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AACLLPQB7EQ6Z2CA4H472ULQECEFJANCNFSM4IJDPQMA>  
> .  
>

---

## Comment 10

> Username: djowel  
> Created at: 2019-08-12 21:37:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520605311  

> There is no hold directive in X3 so that is not really a solution though...  
  
Oh. Duh! So we should provide one for you quickly!

---

## Comment 11

> Username: rubenvb  
> Created at: 2019-08-13 14:33:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520860855  

> Oh. Duh! So we should provide one for you quickly!  
  
I can't tell if you're being sarcastic or not. Writing a proper `hold` seems a meticulous task given the unknown number of edge cases I'd need to handle to make it behave correctly.  
  
In any case, I feel like I'm running into too many obscure issues or unexpected behaviours when using Spirit(.X3) (of which the current issue is clearly one). Even though I thought I grasped most basic concepts, it seems simple things still elude me completely, and often the solutions I can come up with given the limited functional coverage of Spirit.X3 seem suboptimal to me in the end. I'm starting to give up on Spirit due to its inherent possibility to actually make clear what is actually going wrong. Maybe it's Spirit, maybe it's me being inexperienced with formal parsers and grammars. The bottom line is I do not feel like I am ending up with maintainable code at all, let alone that I can explain it to someone not experienced with Spirit at all, which should for all (my) intents and purposes be possible.

---

## Comment 12

> Username: cppljevans  
> Created at: 2019-08-13 15:05:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520874718  

On 8/13/19 9:33 AM, Ruben Van Boxem wrote:  
>     Oh. Duh! So we should provide one for you quickly!  
>   
> I can't tell if you're being sarcastic or not.   
  
I'm pretty sure Joel had no intention to be sarcastic.  
  
> Writing a proper |hold|  
> seems a meticulous task given the unknown number of edge cases I'd need  
> to handle to make it behave correctly.  
>   
> In any case, I feel like I'm running into too many obscure issues or  
> unexpected behaviours when using Spirit(.X3) (of which the current issue  
> is clearly one). Even though I thought I grasped most basic concepts, it  
> seems simple things still elude me completely, and often the solutions I  
> can come up with given the limited functional coverage of Spirit.X3 seem  
> suboptimal to me in the end.   
[snip]  
  
I sympathize.  I have difficulty following some of the code myself.  
  
I'd guess Joel would welcome suggestions for simplifying the code,  
although he couldn't guarantee following such suggestions.  
  
-regards,  
Larry

---

## Comment 13

> Username: djowel  
> Created at: 2019-08-13 17:36:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520932744  

> I can't tell if you're being sarcastic or not. Writing a proper `hold` seems a meticulous task given the unknown number of edge cases I'd need to handle to make it behave correctly.  
  
I am not being sarcastic. I'm sorry if I sounded that way. Hold is easy to implement, esp. with X3! It's a lot easier compared to Qi in fact. And it is the right thing to do. While it is possible to do what you want, 1) It deviates from Qi and 2) You always pay for it in terms of efficiency by having to synthesize an attribute all the time you hit an alternative.  
  
I've been using x3 for a long time now, and TBH, I've no need for hold. One easy rule to follow is not to fight with the attributes. I simply use variants in alternatives, instead of reusing the attribute in the alternative branches.

---

## Comment 14

> Username: djowel  
> Created at: 2019-08-13 17:56:49 UTC  
> Updated at: 2019-08-13 17:59:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-520940195  

> I've been using x3 for a long time now, and TBH, I've no need for hold. One easy rule to follow is not to fight with the attributes. I simply use variants in alternatives, instead of reusing the attribute in the alternative branches.  
  
Oh, and one more easy rule to follow is to avoid ambiguity. Ambiguity leads to backtracking in cases like yours. When I design a language, I try my best to make it LL1. So for example, I'd refactor your grammar as:  
  
```  
int_ >> (int_ | attr(3))  
```

---

## Comment 15

> Username: cppljevans  
> Created at: 2019-08-13 23:27:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-521046189  

On 8/13/19 12:56 PM, Joel de Guzman wrote:  
>     I've been using x3 for a long time now, and TBH, I've no need for  
>     hold. One easy rule to follow is not to fight with the attributes. I  
>     simply use variants in alternatives, instead of reusing the  
>     attribute in the alternative branches.  
>   
> Oh, and one more easy rule to follow is to avoid ambiguity. Ambiguity  
> leads to backtracking in cases like yours. When I design a language, I  
> try my best to make it LL1.  
>   
Joel,  
  
do you mean something like:  
  
  const auto one_two  
    = x3::rule<struct one_two, std::vector<int>>{"one two"}  
    =  x3::int_  
   >> ( x3::int_  
      | x3::attr(3)  
      )  
    ;  
  
?  WARNING, I've not tried this; hence, I don't know if it will even  
compile.  
  
-regards,  
Larry

---

## Comment 16

> Username: cppljevans  
> Created at: 2019-08-13 23:47:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-521050248  

> On 8/13/19 12:56 PM, Joel de Guzman wrote: I've been using x3 for a long time now, and TBH, I've no need for hold. One easy rule to follow is not to fight with the attributes. I simply use variants in alternatives, instead of reusing the attribute in the alternative branches. Oh, and one more easy rule to follow is to avoid ambiguity. Ambiguity leads to backtracking in cases like yours. When I design a language, I try my best to make it LL1.  
> Joel, do you mean something like: const auto one_two = x3::rule<struct one_two, std::vector<int>>{"one two"} = x3::int_ > ( x3::int_ | x3::attr(3) ) ; ? WARNING, I've not tried this; hence, I don't know if it will even compile.  
> […](#)  
> -regards, Larry  
  
OOPS.  Sorry Joel.  I see you've already answered my question.  I was responding from my  
thunderbird mail reader which apparently does not keep up-to-date with this webpage :(  
  
Sorry for noise :(

---

## Comment 17

> Username: Kojoley  
> Created at: 2020-06-04 19:17:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/523#issuecomment-639063871  

There is no point in fixing only this without implementing full-blown container rollback on backtracking.  
  
Closed as a duplicate/subset of boostorg/spirit#835.
