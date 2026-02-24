# #370 X3: Pass container attribute through sequence [Merged]

> Username: Kojoley  
> Created at: 2018-02-25 01:09:56 UTC  
> Updated at: 2018-03-02 23:33:52 UTC  
> Merged at: 2018-03-01 10:44:27 UTC  
> Closed at: 2018-03-01 10:44:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/370  

If underlying parser of sequence is sequence or unary ending down  
to a sequence the container attribute should be passed through.  
  
TODO:  
  - I have a naming problem, hope to receive a feedback.  
  - I don't like SFINAE, but other solutions will have more boilerplate.  
    Any suggestions?  
  
Fixes https://svn.boost.org/trac10/ticket/12085

---

## Comment 1

> Username: djowel  
> Created_at: 2018-02-25 22:02:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-368349108  

This is in my radar screen. I don't have a good answer yet, nor a good name. Still thinking about it.

---

## Comment 2

> Username: djowel  
> Created_at: 2018-02-26 22:13:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-368669813  

Barring a better name, let's have this a go. As for SFINAE alternative, you can probably use overloading and pass in an MPL bool_ to differentiate the two cases?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-02-26 23:10:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-368684564  

> As for SFINAE alternative, you can probably use overloading and pass in an MPL bool_ to differentiate the two cases?  
  
It is only one alternative I know and it looks ugly https://github.com/Kojoley/spirit/commit/8b76132c8ded0c63b8ecc7d1689627ae49d1fde9,

---

## Comment 4

> Username: djowel  
> Created_at: 2018-02-26 23:17:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-368686156  

I'm OK either way. Implementation details.

---

## Comment 5

> Username: cppljevans  
> Created_at: 2018-02-28 01:24:33 UTC  
> Updated_at: 2018-02-28 15:31:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369087912  

With regard to the naming issue, I'd suggest the following renames:  
  
- parse_into_container_or_proxy -> parse_into_container_or_parser  
- should_sequence_proxy -> should_into_container  
  
Then the calling code would be:  
```c++  
parse_into_container_or_parser(parser.left, first, last, context, rcontext, attr, should_into_container<Parser::left_type, Context>{})  
```  
and the called code would be one of:  
```c++  
    template <typename Parser, typename Iterator, typename Context  
      , typename RContext, typename Attribute>  
    bool parse_into_container_or_parser(  
        Parser const& parser  
      , Iterator& first, Iterator const& last, Context const& context  
      , RContext& rcontext, Attribute& attr, mpl::true_)  
    {  
        return parser.parse(first, last, context, rcontext, attr);  
    }  
  
    template <typename Parser, typename Iterator, typename Context  
      , typename RContext, typename Attribute>  
    bool parse_into_container_or_parser(  
        Parser const& parser  
      , Iterator& first, Iterator const& last, Context const& context  
      , RContext& rcontext, Attribute& attr, mpl::false_)  
    {  
        return parse_into_container(parser, first, last, context, rcontext, attr);  
    }  
```  
This is a better naming because parse_into_container_or_parser, when  
separated by the _or_, gives 2 names:  
  
- parse_into_container  
- parser  
  
which match the 2 differences in leading string of the called  
functions in the bodies of the 2 different overloads:  
  
- parse_into_container(parser, first, last, context, rcontext, attr);  
- parser.parse(first, last, context, rcontext, attr);  
  
  
Hmmm...  The should_into_container, when true, causes the parser.parse  
to be called.  Should that be the other way around?  
  
Or, for more encapsulated code, move the specializations and the using  
into a separate struct with static calls such as:  
```c++  
    struct parse_into_container_or_parser  
    {  
    private:  
        template <typename Parser, typename Context>  
        using which = mpl::bool_<(sequence_size<Parser, Context>::value > 1)>;  
        
        template <typename Parser, typename Iterator, typename Context  
          , typename RContext, typename Attribute>  
        static bool call(  
            Parser const& parser  
          , Iterator& first, Iterator const& last, Context const& context  
          , RContext& rcontext, Attribute& attr, mpl::true_)  
        {  
            return parser.parse(first, last, context, rcontext, attr);  
        }  
      
        template <typename Parser, typename Iterator, typename Context  
          , typename RContext, typename Attribute>  
        static bool call(  
            Parser const& parser  
          , Iterator& first, Iterator const& last, Context const& context  
          , RContext& rcontext, Attribute& attr, mpl::false_)  
        {  
            return parse_into_container(parser, first, last, context, rcontext, attr);  
        }  
    public:  
        template <typename Parser, typename Iterator, typename Context  
          , typename RContext, typename Attribute>  
        static bool call(  
            Parser const& parser  
          , Iterator& first, Iterator const& last, Context const& context  
          , RContext& rcontext, Attribute& attr)  
        {  
            return call(parser, first, last, context, rcontext, attr, which<Parser, Context>{});  
        }  
    };  
  
    template <typename Parser, typename Iterator, typename Context  
      , typename RContext, typename Attribute>  
    bool parse_sequence(  
        Parser const& parser , Iterator& first, Iterator const& last  
      , Context const& context, RContext& rcontext, Attribute& attr  
      , traits::container_attribute)  
    {  
        Iterator save = first;  
        if (parse_into_container_or_parser::call(parser.left, first, last, context, rcontext, attr)  
            && parse_into_container_or_parser::call(parser.right, first, last, context, rcontext, attr))  
            return true;  
        first = save;  
        return false;  
    }  
```  
As you can see, the alias template has been renamed to which and privatized  
into the new struct parse_into_container_or_parser.  Furthermore, both the  
specializations have been renamed to call and also privatized into the new  
struct.  This also makes the calling code cleaner.  
  
The renaming of the alias template to which is meant to suggest a similarity  
of the above to variant visitor's.  If you think about it, the new parse_into_container_or_parser  
a certain similarities to boost's static_visitor:  
  
http://www.boost.org/doc/libs/1_64_0/doc/html/variant/reference.html#variant.concepts.static-visitor.examples  
  
where, instead of the member functions, operator()(...)'s, there's the static call(...)'s.  
Maybe that would suggest renaming the new struct to contain visitor in it's name  
somewhere to further suggest the similarity with variant's static_visitor.  All this is  
to somehow make it easier to understand what the code is doing.  
  
Also, it might be a good idea to, within the new struct, supply a short comment  
about what problem is solved there.  This would also make the code easier to  
understand.  
  
OR, simply use an if statement with the compile time value of the "discriminator":  
  
using which = mpl::bool_<(sequence_size<_Parser, Context>::value > 1)>  
  
Since which::value is a compile time value, the compiler, I'd hope, would just  
generate code for the branch of the if actually needed.  Here's that code:  
```c++  
    template <typename Parser, typename Iterator, typename Context  
      , typename RContext, typename Attribute>  
    bool parse_sequence(  
        Parser const& parser , Iterator& first, Iterator const& last  
      , Context const& context, RContext& rcontext, Attribute& attr  
      , traits::container_attribute)  
    {  
        Iterator save = first;  
        auto parse_into_container_or_parser=  
          [&](auto const& _parser)  
          {  
              using _Parser=decltype(_parser);  
              using which = mpl::bool_<(sequence_size<_Parser, Context>::value > 1)>;  
              if(which::value)  
              {  
                  return parse_into_container(_parser,first,last,context,rcontext,attr);  
              }  
              else  
              {  
                  return _parser.parse(first,last,context,rcontext,attr);  
              }  
          };  
        if (parse_into_container_or_parser(parser.left)  
            && parse_into_container_or_parser(parser.right))  
            return true;  
        first = save;  
        return false;  
    }  
```  
This seems simplest and most modular.  It's most modular because there's  
no external overloaded functions used.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-02-28 20:26:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369371381  

I like how it looks with lambda, but unfortunately it will not work without constexpr if.  
  
For some reason on VS 2015/2017 the if behaves like it is constexpr even without `/std:c++17`. GCC and Clang does not have this bugfeature.

---

## Comment 7

> Username: djowel  
> Created_at: 2018-02-28 22:48:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369410876  

constexpr if can solve a lot of the verbosity indeed. should we us it or not is another question.

---

## Comment 8

> Username: cppljevans  
> Created_at: 2018-03-01 08:48:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369518788  

On 02/28/2018 04:48 PM, Joel de Guzman wrote:  
> constexpr if can solve a lot of the verbosity indeed. should we us it or not is another question.  
>   
The just uploaded gist:  
   https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29  
hopefully helps answer that question.  The gist shows that the  
constexpr if lambda method doesn't work as expected :(  
However, the alternative, using the struct with static call method  
does work.  
  
-regards,  
Larry

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-03-01 10:44:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369552387  

> The gist shows that the constexpr if lambda method doesn't work as expected :(  
  
You forgot to strip const ref.

---

## Comment 10

> Username: djowel  
> Created_at: 2018-03-01 21:51:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369743467  

Let's keep this in line with what the current implementation does (either sfinae or static bool_). When constexpr if is more prevalent, then we can use it.

---

## Comment 11

> Username: cppljevans  
> Created_at: 2018-03-02 10:52:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369889691  

On 03/01/2018 03:51 PM, Joel de Guzman wrote:  
> Let's keep this in line with what the current implementation does (either sfinae or static bool_). When constexpr if is more prevalent, then we can use it.  
>   
But doesn't the Merged#370 code use sfinae.  I say this because the code   
here:  
  
https://github.com/boostorg/spirit/blob/a0df3c098ff4e42c0958796c4f47d4d72a20c164/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L400  
  
uses enable_if_c and, according to:  
  
http://www.boost.org/doc/libs/1_66_0/libs/core/doc/html/core/enable_if.html  
  
enable_if_c uses sfinae.  
  
====================  
However, the Merged#370 code is not as modular as that here:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L95  
  
***WHEN*** !defined(USE_CONSTEXPR_IF_LAMBDA),  
because the Merged#370 code is distributed over several function  
declarations and definitions from:  
  
https://github.com/boostorg/spirit/blob/a0df3c098ff4e42c0958796c4f47d4d72a20c164/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L386  
  
to:  
  
https://github.com/boostorg/spirit/blob/a0df3c098ff4e42c0958796c4f47d4d72a20c164/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L420  
  
In contrast the #file-parse_sequence_container-cpp-L95 code just  
involves the struct:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L95  
  
and the function:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L135  
  
So, wouldn't the more modular #file-parse_sequence_container-cpp-L95  
code be preferable?  
  
Of course the #file-parse_sequence_container-cpp-L95 code is not:  
  
   in line with what the current implementation  
  
OTOH, the current implementation is not as modular ;)  
  
-regards,  
Larry  
  
BTW,  IMHO, it would be nice to have these design considerations  
and their pros/cons, such as those outlined here:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L12  
  
were documented somewhere, say in a design directory, so that  
future maintainers wouldn't have to search the past PR comments  
to figure out why the code is structured the way it is.  
  
Just my 2cents worth ;)

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-03-02 13:54:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369926532  

> But doesn't the Merged#370 code use sfinae.  
  
Yes it is a sfinae, I've even written about it in the PR description.  
  
> However, the Merged#370 code is not as modular as that here:  
> So, wouldn't the more modular #file-parse_sequence_container-cpp-L95 code be preferable?  
> OTOH, the current implementation is not as modular ;)  
  
I do not understand what are you meaning under 'modular' here. If you meant customization point -- it is not needed here, and can't be here as it is in `detail` namespace.  
  
> OTOH, the current implementation is not as modular ;)  
  
Current implementation is less code, it does the same thing as yours (not less, not more). Compilation speed is the same in the all cases:  
  
MSVC 14.1 (15.5.7)  
  
~         | compilation timings  
--------- | ------  
before pr | 5 loops, best of 3: 3.14 sec per loop  
after pr  | 5 loops, best of 3: 3.15 sec per loop  
"modular" | 5 loops, best of 3: 3.15 sec per loop  
  
<details>  
 <summary>Benchmark code</summary>  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/vector.hpp>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    boost::fusion::vector<  
        std::vector<int>, std::vector<std::string>  
      , std::vector<int>, std::vector<std::string>  
      , std::vector<int>, std::vector<std::string>  
      , std::vector<int>, std::vector<std::string>  
      , std::vector<int>, std::vector<std::string>  
    > attr;  
  
    char *iter = nullptr, *last = nullptr;  
    phrase_parse(iter, last,  
           "ints: " >> (x3::int_ % ',') >> "strings: " >> *x3::lexeme['[' >> +~x3::char_(']') >> ']']  
        >> "ints: " >> (x3::int_ % ',') >> "strings: " >> *x3::lexeme['[' >> +~x3::char_(']') >> ']']  
        >> "ints: " >> (x3::int_ % ',') >> "strings: " >> *x3::lexeme['[' >> +~x3::char_(']') >> ']']  
        >> "ints: " >> (x3::int_ % ',') >> "strings: " >> *x3::lexeme['[' >> +~x3::char_(']') >> ']']  
        >> "ints: " >> (x3::int_ % ',') >> "strings: " >> *x3::lexeme['[' >> +~x3::char_(']') >> ']']  
      , x3::space, attr);  
  
    return 0;  
}  
```  
  
</details>

---

## Comment 13

> Username: cppljevans  
> Created_at: 2018-03-02 16:11:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-369967585  

In the following, by:  
   #file-parse_sequence_container-cpp  
I'll mean:  
   
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp  
On 03/02/2018 07:54 AM, Nikita Kniazev wrote:  
 >> But doesn't the Merged#370 code use sfinae.  
 >  
 > Yes it is a sfinae, I've even written about it in the PR description.  
 >  
 >> However, the Merged#370 code is not as modular as that here:  
 >> So, wouldn't the more modular #file-parse_sequence_container-cpp-L95   
code be preferable?  
 >> OTOH, the current implementation is not as modular ;)  
 >  
 > I do not understand what are you meaning under 'modular'  
 > here.  
[snip]  
Sorry I was unclear.  I'd hoped my comments between here:  
  
   because the Merged#370 code is distributed over several function  
   declarations and definitions from:  
  
and here:  
  
   So, wouldn't the more modular  
   #file-parse_sequence_container-cpp-L95 code be preferable?  
  
would be sufficient.  Since they weren't I'll try again here.  
  
In the comments under //Results:, there was a wikipedia  
reference to Modular_programming, which says a module  
  
   contains everything necessary to execute only one aspect  
   of the desired functionality.  
  
I interpreted that to mean the fewer parts scattered over  
various parts of the source code that are needed to do  
something, the more modular it was.  As mentioned, in my  
comment, Merge#370 has more than 2 parts scattered over the  
detail/sequence.hpp source code.  ( OK, I admit, "scattered"  
is a bit strong, but the parts were not in one logical unit  
like a class or function, they were close together but they  
weren't explicity gathered together).  In addition:  
  
   https://en.wikipedia.org/wiki/Modular_design  
  
claims a module:  
  
   can be independently created and then used in different  
   systems.  
  
In contrast, the pass_sequence_container_attribute constexpr  
and the 2 parse_sequence_container overloaded functions in  
Merge#370 are only useful in the:  
  
   parse_sequence(...,traits::container_attribute)  
  
function; hence, cannot be used in "different systems".  So,  
that constexpr and those overloaded functions really belong  
together, and although they're together in the sense that  
they're in the same detail/sequence.hpp file, there's better  
ways for keeping things together including having them in  
the same class or function.  The best example of that in the  
current situation is when defined(USE_CONSTEXPR_IF_LAMBDA)  
in #file-parse_sequence_container-cpp.  It's best because  
it's only in 1 function.  Slightly worse is when  
!defined(USE_CONSTEXPR_IF_LAMBDA), in which case the code is  
scattered, slightly, over the struct  
parse_sequence_container here:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L95  
  
and the parse_sequence function here:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L135  
  
However, that scattering (or less modularity) is somewhat  
mitigated by there being a friend declaration of the  
function in the struct and all the struct static functions  
being static.  Thus the code reader see's a more explicit  
connection between the struct and the function and doesn't  
have to infer it by first looking at the function and then  
search for the occurrence of the overloaded functions.  The  
friend declaration and private struct static functions also  
increase the encapsulation, as mentioned here:  
  
https://gist.github.com/cppljevans/a74ceee491c6c8fd6ff48311dbf50b29#file-parse_sequence_container-cpp-L98  
  
I hope that clears things up.  
  
 > If you meant customization point -- it is not needed  
 > here, and can't be here as it is in `detail` namespace.  
  
No, I didn't mean customization point.  
  
-regards,  
Larry  
[snip]

---

## Comment 14

> Username: djowel  
> Created_at: 2018-03-02 23:25:47 UTC  
> Updated_at: 2018-03-02 23:33:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/370#issuecomment-370085880  

I really appreciate your concern and effort, @cppljevans. However, there are certain coding idioms that run across the current code base. Understanding the common idioms will give you a good understanding of how the whole library works. I am opposed to a code base using a multitude of idioms when one will do. That will make the code a lot more difficult to read and understand. Right now, the idiom is the use of static bool_ and overloading (and some, but lesser of sfinae actually). There can be a better idiom, sure. But whatever it is, it should be generally applicable to all code and does not rely on some compiler quirks. constexpr if is a good candidate, but until that one becomes more prevalent and practical to use across c++ compilers, I do not think we want to switch yet.

---
