# #679 - boost::spirit::x3 problem with any ast class representing a std::string [Closed]

> Username: Bockeman  
> Created at: 2021-05-20 20:55:41 UTC  
> Updated at: 2021-10-20 12:49:14 UTC  
> Closed at: 2021-10-20 12:49:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/679  

I have two, what I presume to be legitimate, ways of defining a class in my ast that represents a string, illustrated here:  
```  
  struct white : std::string {};  
```  
or  
```  
  struct white {std::string text;};  
```  
which I use in several places for things like literals, identifiers, comments, or even white space, which I want to capture in my ast.  
  
In some places I have to use the first form, and in other places I have to use the second form, otherwise I get compile errors with horrendous error messages:  
```  
  ...  
  no matching function for call to ??minimal::ast::rules::rules(__gnu_cxx::__normal_iterator<minimal::ast::gap_item*,  
    std::vector<minimal::ast::gap_item> >&,  
    __gnu_cxx::__normal_iterator<minimal::ast::gap_item*, std::vector<minimal::ast::gap_item> >&)??  
  ...  
```  
embedded in several pages of error output with no clue regarding the line of source code which might be at fault.  
  
How should I interpret this type of error message?  
Why do I have to use a different form/style of class in different places?  
  
This minimal example illustrates the problem (when the #defines are [un]commented)  
[rgw29_minimal.cpp.txt](https://github.com/boostorg/spirit/files/6518713/rgw29_minimal.cpp.txt)

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-21 01:47:32 UTC  
> Updated at: 2021-05-24 17:01:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-845593160  

Could you please make a smaller reproducer. There is a lot of code and that means many places for all kind of errors. It is also much easier for me to debug with a minimal reproducer.  
  
> In some places I have to use the first form, and in other places I have to use the second form  
  
What exactly prevents you from using the first form everywhere?  
  
---  
  
```cpp  
  struct white  
  {  
    string			text;  
  };  
...  
BOOST_FUSION_ADAPT_STRUCT(minimal::ast::white,			text)  
...  
  auto const white_def			= +space;					// single space as string  
```  
This might be unsupported. See #463.  
  
---  
  
```cpp  
  struct white  
  : string  
  {  
  };  
...  
  struct gap  
  : vector<gap_item>  
  {  
  };  
...  
  struct c_identifier  
  : string  
  {  
  };  
...  
  struct rule  
  : vector<rule_item>  
  {  
  };    
  struct rules  
  : vector<rule>  
  {  
  };  
...  
  struct defs  
  : vector<def_item>  
  {  
  };  
```  
All these do not bring the additional constructors from the base classes. At least one compilation error is about that.

---

## Comment 2

> Username: Bockeman  
> Created at: 2021-05-21 12:55:08 UTC  
> Updated at: 2021-05-24 17:01:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-845930304  

@Kojoley thank you, your response has been very helpful.  Let me answer each of your points.  
  
> ---  
> _By way of explanation: an experienced user would probably never think of creating "white" as above, but I did so because:_  
> 1. _I want to capture spaces/comments because comments contain important information that I want in my ast (hence no_skip)._  
> 2. _I want to be able to translate any syntax/grammer definition into X3, possibly automatically, but certainly which is able to show a close likeness to the original (with comments)._  
> 3. _I am using this to become proficient in X3_  
> ---  
  
You asked:  
> Could you please make a smaller reproducer. There is a lot of code and that means many places for all kind of errors. It is also much easier for me to debug with a minimal reproducer.  
  
The minimal code I posted was considerably reduced from where I started and is what I thought to be the minimal case which demonstrated a failure when the first form was used for white **and** the second form was used for literal or c_identifier.  Also, only by running this code does anyone get to see the horrendous undecipheral extent of the error messaging, which is many many times larger than the source code.  
  
---  
  
You also asked:  
>  
> >   In some places I have to use the first form, and in other places I have to use the second form  
>  
> What exactly prevents you from using the first form everywhere?  
  
If rgw29_minimal is compiled with  
```  
//#define WITH_INSTANCED_STRING_WHITE  
```  
or any combination with either/both these defines  
```  
#define WITH_INSTANCED_STRING_LITERAL  
#define WITH_INSTANCED_STRING_C_IDENTIFIER  
```  
I get the horrendous undecipheral error messages (only a snippet of which I included above).  
  
So the only way to get a successful compile is with the combination in the code supplied:  
```  
#define WITH_INSTANCED_STRING_WHITE  
//#define WITH_INSTANCED_STRING_LITERAL  
//#define WITH_INSTANCED_STRING_C_IDENTIFIER  
```  
I cannot successfully compile with all being of the same form.  
  
---  
  
However, what is particularly revealing is  
> This might be unsupported. See #463.  
  
It would appear that the problem I was trying to solve was associated with  
```  
  auto const white_def  = +space;                       // single space as string  
  auto const gap_def    = +(white | comment);    // spaces/comments  
```  
which is solved by eliminating white altogether  
```  
  auto const gap_def    = +(space | comment);    // spaces/comments  
```  
My conjecture is that  
  
1. white is a single item and therefore could fall foul of the single element tuple attribute problem #463   
2. white could have an attribute of a single ```char``` or multiple ```char```s as ```vector<char>``` or ```string```  
  
---  
  
But there still remains the problem that the error messaging is horrendous.  If a single element tuple is not supported, then it would be really helpful if the error message stated that **and** pointed to the line of user code (not deep inside the X3 code).  
  
Likewise , if it is impossible to determine or synthesize the type of an attribute, or automatically convert a single char to a string, then again  it would be really helpful if the error message stated that **and** pointed to the line of user code (not deep inside the X3 code).  
  
And as already stated elsewhere, the X3 documentation really should alert the user to such limitations.  
  
Given I am a novice user of X3, rather than a developer, would it be possible for you to rephrase my requests, for both user orientated error messaging and document refinements, such that the appropriate team members might be able to easily grasp the issues and tackle them expediently.  Thank you, in advance.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-05-21 16:14:02 UTC  
> Updated at: 2021-05-24 17:01:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846073371  

> The minimal code I posted was considerably reduced from where I started  
  
However, it is not minimal. The minimal would be something I could put in the test suite to prevent further regressions. Please read https://stackoverflow.com/help/minimal-reproducible-example  
  
> > > In some places I have to use the first form, and in other places I have to use the second form  
> >  
> > What exactly prevents you from using the first form everywhere?  
>  
> I get the horrendous undecipheral error messages (only a snippet of which I included above).  
  
That explains why you were *unable to*, but not why you *must*.  
  
> But there still remains the problem that the error messaging is horrendous.  
  
I see your frustration, but you should also learn what the compiler error tells you.  
  
> If a single element tuple is not supported, then it would be really helpful if the error message stated that and pointed to the line of user code (not deep inside the X3 code).  
  
That is something people do not try to understand in the first place and make judgments. It is not supported as you wish it might be, because it does something else. Please read what `BOOST_FUSION_ADAPT_STRUCT` actually do (TLDR: it makes the struct behave like a tuple). I also described that in https://github.com/boostorg/spirit/issues/463#issuecomment-596018980.  
  
> Likewise , if it is impossible to determine or synthesize the type of an attribute, or automatically convert a single char to a string, then again it would be really helpful if the error message stated that and pointed to the line of user code (not deep inside the X3 code).  
  
I do not think a single char should be automatically converted into a string, but if you show me a good example where it justified I will likely to change my opinion. Anyway, you should be able to do it with `repeat(1)[char_]` if you really wish.  
  
> Given I am a novice user of X3, rather than a developer, would it be possible for you to rephrase my requests  
  
This is a bug tracker where bugs are reported in Spirit, not in your code. That's why I asked about a minimal reproducer. You could also open an feature request, if you think something is missing and would benefit a lot of users. Currently this report is more like 'please fix my code', and while I already said what you should fix in it to continue -- you did not, or at least did not updated the reproducer.

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-05-21 21:27:39 UTC  
> Updated at: 2021-05-24 17:11:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846272130  

MCVE:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
namespace minimal { namespace ast  
{    
  struct gap_item : x3::variant<std::string>  
  {  
    using base_type::base_type;  
    using base_type::operator=;  
  };  
  struct gap : std::vector<gap_item>  
  {  
    using std::vector<gap_item>::vector;  
    using std::vector<gap_item>::operator=;  
  };  
  struct def_item : x3::variant<gap, std::string>  
  {  
    using base_type::base_type;  
    using base_type::operator=;  
  };  
  struct defs : std::vector<def_item>  
  {  
    using std::vector<def_item>::vector;  
    using std::vector<def_item>::operator=;  
  };  
}}  
  
namespace minimal { namespace parser  
{  
  x3::rule<struct gap_class, ast::gap> const gap = "gap";  
  x3::rule<struct defs_class, ast::defs> const defs = "defs";  
  
  auto const gap_def = +x3::attr(ast::gap{});  
  auto const defs_def = +gap;  
  
  BOOST_SPIRIT_DEFINE(gap, defs)  
}}  
  
int main()  
{  
    char const* s = "";  
    minimal::ast::defs x;  
    parse(s, s, minimal::parser::defs, x);  
}  
```

---

## Comment 5

> Username: Bockeman  
> Created at: 2021-05-22 20:12:45 UTC  
> Updated at: 2021-05-24 17:11:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846457972  

@Kojoley again thank you.  
  
I would like to stress that the copious information supplied above has indeed helped me better undestand the various issues.  
1. I recognise that single element tuples are not supported.  And this is easily fixed by not using such.  
2. I recognise that derived classes/structs do not inherit the constructable/copyable methods, so these must be added.  
3. I understand the need for MCVE, and the example provided has shown where things can really be paired down to essentials.  
4. Although suspecting a possible bug in X3, I was more refrained and expressed this as a percieved _problem_ which looked to me like a contradiction in possible ways of coding.  An unsupported construct comes pretty close to a bug when as a user I was oblivious of this.  It was never my intention to make this look like a "please fix my code", sorry.  If nothing else, I hope that this issue serves to help others who might fall into the same trap as I did.  
5. There does remain the issue of how a novice X3 user might interpret the extensive error messages spewed out when the code does not compile.  I recognise that this is a feature of heavily templated libraries like boost::spirit.  These error messages are related to problems caused by trivial mistakes in user code but are reported relating to deep in the inner workings of the library that cause the compiler to stumble.  It would be **so nice** if, just like X3 is a simple to use, elegant, compact and efficient library (a modular wrapper for a lot of hidden wonders under the hood), that the error reporting could also be "wrapped" to provide user orientated hints rather than being directed at the hidden inner workings.  This is an entirely separate issue from the topic of this issue and deserves a new issue in its on right.  Let's not dwell on this further here.  
6. My MCVE, which was already heading in the direction of your example, is now paired down to something similar.  But I am still getting error messages which I do not know how to interpret.  I shall raise a separate issue for this.  
  
Thank you again for your help.

---

## Comment 6

> Username: Bockeman  
> Created at: 2021-05-23 15:27:48 UTC  
> Updated at: 2021-05-24 17:11:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846580834  

@Kojoley I naively assumed that the MCVE you provided above was a working/compilable example of correct coding.  
  
However, when I attempt to compile this MCVE I get:  
```  
  src/rgw29_nikita_mcve.cpp:58:41:   required from here  
  /usr/include/boost/spirit/home/x3/support/ast/variant.hpp:151:39: error: no matching function for call to ??boost::variant<minimal::ast::gap, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >::variant(minimal::ast::gap_item&)??  
    151 |             : var(std::forward<T>(rhs)) {}  
        |                                       ^  
```  
Perhaps this is what you intended.  But since this MCVE does not contain any single element structs (unsupported single element tuple) and each derived struct does have constructors and assignment methods, I cannot see what is wrong.  
  
Please could you provide a novice x3 user level explanation for why this code fails to compile and what would be an appropriate fix.

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-05-23 21:58:27 UTC  
> Updated at: 2021-05-24 17:11:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846630187  

It is a code that I think should work but does not. You indeed seems to find a bug. From what I see your #681 is a duplicate of this, and it already shows a workaround I was thinking to suggest you to use.

---

## Comment 8

> Username: Bockeman  
> Created at: 2021-05-23 23:47:12 UTC  
> Updated at: 2021-05-24 17:12:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846645312  

That is interesting (that you think there may be a bug here).  
  
Please could you expand on this and explain more precisely what you think the bug might be.  
  
If you think #681 is a duplicate, then you may wish to mark this one or that one as the duplicate, possibly based on the more appropriate title.  However, I would personally appreciate a little more information as to why you think there is a duplicate.  
  
Please see my recently added comment to #681 which explains why I do not see the alternative coding as a viable workaround.

---

## Comment 9

> Username: Bockeman  
> Created at: 2021-05-24 00:02:41 UTC  
> Updated at: 2021-05-24 17:12:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846647862  

1. I have several cases where single element structures do work successfully.  I note above that you say that these **might be** unsupported.  I get the impression that they are supported in general, but there are some cases [bugs?] which are not.  
2. Likewise I have several cases of derived structures, e.g. ```: vector<item>```, that also work without needing to add:  
```  
    using std::vector<item>::vector;  
    using std::vector<item>::operator=;  
```  
So again I am puzzled as to whether these also are _supported_ (without the explicit methods) or there are some corner cases [bugs?] where they don't work.  
  
As a novice X3 user, I'd really appreciate your help in understanding what I (and others) should and should not be using, or help in understanding the error messages such that a viable alternative can be deployed where needed.  Thanks in advance.

---

## Comment 10

> Username: cppljevans  
> Created at: 2021-05-24 01:44:54 UTC  
> Updated at: 2021-05-24 17:12:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-846676230  

@Kojoley said [here](https://github.com/boostorg/spirit/issues/679#issuecomment-846272130)  
[snip]  
>   
>   auto const gap_def = +x3::attr(ast::gap{});  
>   auto const defs_def = +gap;  
>   
>   BOOST_SPIRIT_DEFINE(gap, defs)  
> }}  
>   
> int main()  
> {  
>     char const* s = "";  
>     minimal::ast::defs x;  
>     parse(s, s, minimal::parser::defs, x);  
> }  
> ```  
  
How will this do anything but infinitely copy ast::gap{} to the attribute  
of gap?  I say this because:  
  
  x3::attr  
  
consumes no input and just assigns to gap, and the + in front of  
x3::attr just says, do the same until false is returned, but  the   
attr_parser::parse **ALWAYS** returns true; hence, the parse  
never ends.

---

## Comment 11

> Username: Kojoley  
> Created at: 2021-05-24 16:19:45 UTC  
> Updated at: 2021-05-24 17:12:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847158654  

@Bockeman the answers for your questions about single element tuples are already in #463, I have pointed you to there, please read it, and if you have a valuable comment about single element tuples please leave it #463, not in this issue.  
  
@cppljevans it does not meant to run, because it is a compile test.

---

## Comment 12

> Username: Kojoley  
> Created at: 2021-05-24 17:11:19 UTC  
> Updated at: 2021-05-24 18:18:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847201554  

Further reduced to:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    char const* s = "abaabb", *e = s + std::strlen(s);  
    using Foo = std::vector<x3::variant<int>>;  
    using Bar = std::vector<x3::variant<Foo, int>>;  
    Bar x;  
    parse(s, e, +('a' >> x3::attr(Foo{}) | 'b' >> x3::attr(int{})), x);  
}  
```  
`parse_into_container` shortcut defers the whole attribute (due to `parser_accepts_container` finds `Foo` and `Bar` substitutable) while it should unpack it.

---

## Comment 13

> Username: Kojoley  
> Created at: 2021-05-24 17:16:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847204803  

Interesting, if I replace `x3::variant` with `boost::variant` -- it works.

---

## Comment 14

> Username: Kojoley  
> Created at: 2021-05-24 17:48:20 UTC  
> Updated at: 2021-05-24 18:18:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847222274  

It works because `boost::variant` can be implicitly constructed, adding another layer brakes with `boost::variant` too:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/variant.hpp>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    char const* s = "abaabb", *e = s + std::strlen(s);  
    using Qaz = std::vector<boost::variant<int>>;  
    using Foo = std::vector<boost::variant<Qaz, int>>;  
    using Bar = std::vector<boost::variant<Foo, int>>;  
    Bar x;  
    parse(s, e, +('a' >> x3::attr(Foo{}) | 'b' >> x3::attr(int{})), x);  
}  
```

---

## Comment 15

> Username: Bockeman  
> Created at: 2021-05-24 22:47:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847399228  

That looks like an amazing reduction from the original problem.  Thanks again for your work in trying to resolve this.  
  
Would you be able to explain, in X3 user terms, what you have concluded, what might be the underlying problem and if this would require a code fix, or is there a user level workaround that might be deployed?  
  
From a user level pespective, it looks like we have nested pairs of vectors and variants.  You seem to be dropping some hints that it is the variants that interest you.  However, I have been thinking about nesting of vectors.  Please could you permit me to put forward some ideas; not that I ever expect to come any where near your expertise but in the hope that my ideas, however misguided, might just trigger a line of thought that could lead you to a solution.  
  
If the attribute (type) of the element of a nested vector is compatible with the attribute (type) of the parent vector, then, according to my very limited understanding, attribute collapsing occurs.  This is an essential behaviour for synthesizing attributes, and permits, for instance a mixed sequence of chars and strings to be collapsed into a single string.  
  
Very crudely, this can be illustrated by this example:  
```  
#include <boost/spirit/home/x3.hpp>  
#include <vector>  
namespace x3 = boost::spirit::x3;  
int main()  
{  
  char const* s		= "abaabbcc", *e = s + std::strlen(s);  
  using Foo		= std::vector<char>;  
  //using Foo		= char;  
  using Bar		= std::vector<Foo>;  
  Bar x;  
  parse(s, e, +((x3::char_ > x3::char_) > (x3::char_ > x3::char_)), x);  
}  
```  
which obviously fails to compile because the attribute is collapsed into a single (unnested) ```vector<char>```.  (and, of course, this does compile if the commented alternative definition of Foo is used).  
  
By extension, I surmise that the attribute of nested vectors of _compatible variants_ would also get collapsed.  If I apply this theory to your example:  
```  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <vector>  
namespace x3 = boost::spirit::x3;  
int main()  
{  
  char const* s		= "abaabb", *e = s + std::strlen(s);  
  using Yik		= std::vector<x3::variant<int>>;  
  using Qaz		= x3::variant<Yik, int>;  
  using Foo		= x3::variant<Qaz, int>;  
  using Bar		= std::vector<x3::variant<Foo, int>>;  
  Bar x;  
  parse(s, e, +('a' >> x3::attr(Qaz{}) | 'b' >> x3::attr(int{})), x);  
}  
```  
which does compile.  
  
Therefore it would appear to me that what is needed is some mechanism to break this collapsing.  Is there a directive or user level construct that would break this collapsing?

---

## Comment 16

> Username: Bockeman  
> Created at: 2021-05-24 23:16:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-847415218  

I have another example, which I believe to be suffering from the same problem.  
_But I am sure you will direct me to place this in a separate issue, if not._  
  
```  
#define BOOST_SPIRIT_X3_DEBUG	// Provide some meaningful reassuring output.  
#define WITH_TERMINATOR		//   
#define WITH_GAP_ITEM		//  
  
#include <string>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3		= boost::spirit::x3;  
  
namespace minimal { namespace ast  
{  
  struct braced						{char open; std::string	text; char close;};  
  struct gap_item	: x3::variant<braced,char>	{using base_type::operator=;};  
  struct gap 		: std::vector<gap_item>		{};  
  
  struct start_item	: x3::variant< std::string  
  #ifdef WITH_GAP_ITEM  
  , gap_item  
  #else  
  , gap  
  #endif  
  #ifdef WITH_TERMINATOR  
  , char  
  #endif  
						>	{using base_type::base_type; using base_type::operator=;};  
  struct start_rule	: std::vector<start_item>	{using std::vector<start_item>::vector;};  
}}  
BOOST_FUSION_ADAPT_STRUCT(minimal::ast::braced, open, text, close)  
  
namespace minimal { namespace parser  
{  
  using x3::char_;  
  using x3::space;  
    
  x3::rule<struct braced,	ast::braced>	 const	braced		= "braced";  
  x3::rule<struct gap,		ast::gap>	 const	gap		= "gap";  
  x3::rule<struct start_rule,	ast::start_rule> const	start_rule	= "start_rule";  
    
  auto const braced_def		= char_('{') >> *(char_ -'}') >> char_('}');	// { ... }  
  auto const gap_def		= +((space) | braced);				// spaces etc  
  
  auto const start_rule_def	= +(gap | +(char_ -'{' -space -';'))		// gap or string  
    #ifdef WITH_TERMINATOR  
    > char_(';')  
    #endif  
    ;  
    
  BOOST_SPIRIT_DEFINE(gap, braced, start_rule);  
}}  
  
int  
main()  
{  
  char const*			iter	= "? {;};", * const end	= iter + std::strlen(iter);  
  minimal::ast::start_rule	ast;  
  return !parse(iter, end, minimal::parser::start_rule, ast) || iter!=end;  
}  
```  
The starting point should be with these two defines commented:  
```  
//#define WITH_TERMINATOR		//   
//#define WITH_GAP_ITEM		//  
```  
Which gives a working parser which compiles, but for the input supplied, exits with a non-zero status because the end of statement ';' is missing from the grammar.  
  
So I now add that terminator to the grammar  
```  
> char_(';')  
```  
and of course, I have to change the contents of the variant by adding ```char```.  This is done by uncommenting  
```  
#define WITH_TERMINATOR		//   
```  
but this fails to compile, with the guts of the error message being:  
```  
src/rgw29_terminator.cpp:63:60:   required from here  
/usr/include/boost/spirit/home/x3/support/ast/variant.hpp:151:39: error: no matching function for call to ??boost::variant<std::__cxx11::basic_string<char>, minimal::ast::gap, char>::variant(minimal::ast::gap_item&)??  
  151 |             : var(std::forward<T>(rhs)) {}  
      |                                       ^  
```  
This was a total surprise to me.  Why did adding something extra to the end of my start_rule break things so dramatically?  
  
But I am becoming used to these error messages, and on a hunch, changed the collective ```gap``` (a ```vector<gap_item>```) to ```gap item```, acheived by uncommenting:  
```  
#define WITH_GAP_ITEM		//  
```  
  
Can you possibly provide an explanation for this X3 behaviour?  
  
There's a hint of the collapsing attributes, mentioned above, with the workaround being similar: drop one level of vector nesting.  
However this is *not* a viable workaround for any real world parser.

---

## Comment 17

> Username: Bockeman  
> Created at: 2021-05-26 16:16:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-848909658  

With all of the workarounds suggested so far, I am still unable to get my real world parser to compile.  
  
 If a gvien  rule can be ligitimately collapsed to, say, a single char, then that is what is passed, obviously failing higher levels which only expect that rule, not what it might be collapsed to.  
  
I think I need some sort of trick/workaround/fix that stops attribute collapsing at a given rule, whether a variant or vector.  
  
Can anyone help?

---

## Comment 18

> Username: Kojoley  
> Created at: 2021-05-26 17:43:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-848986678  

> but this fails to compile, with the guts of the error message being  
  
It works for me: https://wandbox.org/permlink/joueNN8PK1wmyMl7  
  
> I think I need some sort of trick/workaround/fix that stops attribute collapsing at a given rule, whether a variant or vector.  
  
I do not know what you meant, but a rule is a barrier for any trickery.

---

## Comment 19

> Username: cppljevans  
> Created at: 2021-05-28 13:25:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-850417744  

>   
> > but this fails to compile, with the guts of the error message being  
>   
> It works for me: https://wandbox.org/permlink/joueNN8PK1wmyMl7  
>   
> > I think I need some sort of trick/workaround/fix that stops attribute collapsing at a given rule, whether a variant or vector.  
>   
> I do not know what you meant, but a rule is a barrier for any trickery.  
  
Nikita, I'm pretty sure what Borkeman meant by "stop attribute  
collapsing" can be illustrate by using:  
  
  [collapse_seq_rules](https://www.boost.org/doc/libs/1_75_0/libs/spirit/doc/html/spirit/qi/reference/operator/sequence.html#spirit.qi.reference.operator.sequence.attributes)  
    
which uses several specialized rules for:  
````  
  a >> b  
````    
where the specialized rules are:  
````  
  a: A, b: Unused --> (a >> b): A  
  a: Unused, b: B --> (a >> b): B  
  a: Unused, b: Unused --> (a >> b): Unused  
    
  a: A, b: A --> (a >> b): vector<A>  
  a: vector<A>, b: A --> (a >> b): vector<A>  
  a: A, b: vector<A> --> (a >> b): vector<A>  
  a: vector<A>, b: vector<A> --> (a >> b): vector<A>  
````  
By "stop attribute collapsing", I believe Borkeman   
means to **NOT** use these specilalized rules but   
only use the general rule:  
````  
  a: A, b: B --> (a >> b): tuple<A, B>  
````    
More specifically, even if either A or B were Unused or the same,  
still, the composite attribute would be tuple<A,B>.  An analogous "not  
collapsing" rule would apply for variants.  
  
The obvious advantage of "not-collapsing" from the end-user's  
prospective is that it would be much easier to infer what the structure of the  
attribute is from just looking at the grammar expression.  
  
Obviously, using only the "not-collapsing" rules would require  
changing how attributes from the RHS of a rule are propagated to the  
LHS of the rule, but that should be pretty simple using a semantic  
actions although requiring more run-time because of all the run-time  
copying and more memory because an "Unused" type, although empty,  
still uses a small amount of memory.  
  
But run-time or memory cost is insignificant complared to developement  
cost and that's obviously Borkeman's main concern, at least, at the  
moment.  
  
-regards,  
Larry

---

## Comment 20

> Username: Kojoley  
> Created at: 2021-05-28 15:41:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-850506888  

Attribute collapsing is something that does not exist. Sequence parser has two (three) modes: parse as container or tuple (or fusion map). It decides which one will be use solely by the actual attribute provided. When it parses a container, each sequence parser subject is invoked in "parse to container mode". If you want some part of the sequence to behave like a completely separate parser -- hide it behind a rule or some other non-passthrough parser (like https://github.com/boostorg/spirit/issues/352#issuecomment-368338439).  
  
> More specifically, even if either A or B were Unused or the same,  
> still, the composite attribute would be tuple<A,B>.  
  
"if either A or B were Unused or the same" then "tuple<A,B>" would be simply A or B, according to the cited rules.  
  
> The obvious advantage of "not-collapsing" from the end-user's  
> prospective is that it would be much easier to infer what the structure of the  
> attribute is from just looking at the grammar expression.  
  
It is simply impossible. You need an actual attribute.  
  
> Obviously, using only the "not-collapsing" rules would require  
> changing how attributes from the RHS of a rule are propagated to the  
> LHS of the rule, but that should be pretty simple using a semantic  
> actions although requiring more run-time because of all the run-time  
> copying and more memory because an "Unused" type, although empty,  
> still uses a small amount of memory.  
>   
> But run-time or memory cost is insignificant complared to developement  
> cost and that's obviously Borkeman's main concern, at least, at the  
> moment.  
  
I have no idea what does it mean.

---

## Comment 21

> Username: Bockeman  
> Created at: 2021-05-30 23:19:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851077160  

I picked up the notion of _attribute_collapsing_ from the [employee tutorial](https://www.boost.org/doc/libs/1_75_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/employee.html#spirit_x3.tutorials.employee.attribute_collapsing),  
but @cppljevans has a much better reference above.  
Therefore I do not understand you @Kojoley when you say  
> Attribute collapsing is something that does not exist.   
  
You may be correct, that it does not exist per se under the hood, but the documentation uses _attribute_collapsing,_ so it only seems fair to reference that and continue use terms that are in the documentation.  
I was trying to help identify a possible area for investigation, but it seems only to have added confusion.  
Please put aside my interjection relating to _attribute_collapsing._

---

## Comment 22

> Username: Kojoley  
> Created at: 2021-05-30 23:51:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851081727  

That does not help me to understand what you want in any way. I feel like this off-topic discussion could continue forever. I have to repeat myself: If you have a future request, please, create an example - a code (as short as possible) you would like to compile and run.

---

## Comment 23

> Username: Bockeman  
> Created at: 2021-05-30 23:52:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851081797  

There is, I believe, a genuine problem here.  Let me illustrate with another example.  
  
My starting point is  
```  
#include <string>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3		= boost::spirit::x3;  
  
namespace minimal { namespace ast  
{  
  struct braced						{char open; std::string	text; char close;};  
  struct gap_item	: x3::variant<char,braced>	{using base_type::operator=;};  
  struct gap 		: std::vector<gap_item>		{};  
  struct start_item	: x3::variant<gap,std::string>	{using base_type::operator=;};  
  struct start	: std::vector<start_item>		{using std::vector<start_item>::vector;};  
}}  
BOOST_FUSION_ADAPT_STRUCT(minimal::ast::braced, open, text, close)  
  
namespace minimal { namespace parser  
{  
  using x3::char_;  
  using x3::space;  
  using x3::raw;  
    
  x3::rule<struct white,	ast::white>	const	white		= "white";  
  x3::rule<struct braced,	ast::braced>	const	braced		= "braced";  
  x3::rule<struct gap,		ast::gap>	const	gap		= "gap";  
  x3::rule<struct start,	ast::start>	const	start		= "start";  
    
  auto const white_def		= raw[+space];  
  auto const braced_def		= char_('{') >> *(char_ -'}') >> char_('}');	// { ... }  
  auto const gap_def		= +(space | braced);				// spaces etc  
  auto const start_def		= +(gap | +(char_ -'{' -space));		// gap=container or string  
    
  BOOST_SPIRIT_DEFINE(white, gap, braced, start);  
}}  
  
int  
main()  
{  
  char const*			iter	= "? {;};", * const end	= iter + std::strlen(iter);  
  minimal::ast::start	ast;  
  return !parse(iter, end, minimal::parser::start, ast) || iter!=end;  
}  
```  
which compiles and runs without issue.  
  
Next I add ```white``` for the collective white space, rather than just a single ```space``` character.  
```  
namespace minimal { namespace ast  
{  
  struct white		: std::string			{using std::string::string;};  
  struct braced						{char open; std::string	text; char close;};  
  struct gap_item	: x3::variant<white,braced>	{using base_type::operator=;};  
  struct gap 		: std::vector<gap_item>		{};  
  struct start_item	: x3::variant<gap,std::string>	{using base_type::operator=;};  
  struct start	: std::vector<start_item>		{using std::vector<start_item>::vector;};  
}}  
BOOST_FUSION_ADAPT_STRUCT(minimal::ast::braced, open, text, close)  
  
namespace minimal { namespace parser  
{  
  using x3::char_;  
  using x3::space;  
  using x3::raw;  
    
  x3::rule<struct white,	ast::white>	const	white		= "white";  
  x3::rule<struct braced,	ast::braced>	const	braced		= "braced";  
  x3::rule<struct gap,		ast::gap>	const	gap		= "gap";  
  x3::rule<struct start,	ast::start>	const	start		= "start";  
    
  auto const white_def		= raw[+space];  
  auto const braced_def		= char_('{') >> *(char_ -'}') >> char_('}');	// { ... }  
  auto const gap_def		= +(white | braced);				// spaces etc  
  auto const start_def		= +(gap | +(char_ -'{' -space));		// gap=container or string  
    
  BOOST_SPIRIT_DEFINE(white, gap, braced, start);  
}}  
```  
However, this fails to compile giving  
  
  
```  
src/rgw29_gap_item.cpp:64:55:   required from here  
/usr/include/c++/10/bits/stl_uninitialized.h:137:72: error: static assertion failed: result type must be  
constructible from value type of input range  
  137 |       static_assert(is_constructible<_ValueType2, decltype(*__first)>::value,  
      |                                                                        ^~~~~  
src/rgw29_gap_item.cpp:64:55:   required from here  
/usr/include/boost/spirit/home/x3/support/ast/variant.hpp:184:17: error: no match for ??operator=??  
(operand types are ??boost::spirit::x3::variant<minimal::ast::gap, std::__cxx11::basic_string<char,  
std::char_traits<char>, std::allocator<char> > >::variant_type?? {aka ??boost::variant<minimal::ast::gap,  
std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >??} and ??const char??)  
  184 |             var = std::forward<T>(rhs);  
      |             ~~~~^~~~~~~~~~~~~~~~~~~~~~  
```  
essentially telling me to add a ```char``` to my start_item variant.  And when I do that I get  
```  
src/rgw29_gap_item.cpp:64:55:   required from here  
/usr/include/c++/10/bits/stl_uninitialized.h:137:72: error: static assertion failed: result type must be  
constructible from value type of input range  
  137 |       static_assert(is_constructible<_ValueType2, decltype(*__first)>::value,  
      |                                                                        ^~~~~  
  
src/rgw29_gap_item.cpp:64:55:   required from here  
/usr/include/boost/spirit/home/x3/support/ast/variant.hpp:184:17: error: no match for ??operator=??  
(operand types are ??boost::spirit::x3::variant<minimal::ast::gap, std::__cxx11::basic_string<char,  
std::char_traits<char>, std::allocator<char> >, char>::variant_type?? {aka ??boost::variant<minimal::ast::gap,  
std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, char>??} and  
??minimal::ast::gap_item??)  
  184 |             var = std::forward<T>(rhs);  
      |             ~~~~^~~~~~~~~~~~~~~~~~~~~~  
```  
essentially telling me to add a ```gap_item``` to my start_item variant.  And when I do that I get it does compile parse ok.  
  
But this is utter madness.  Why do I have to add ```char``` and ```gap_item``` to my variant, when that already contains ```gap``` and ```gap``` is a container which contains one or more ```char``` or ```gap_item```.  And to be clear, although this does make this minimal code compile and parse, in my real world code the ```gap``` in the ast has been bypassed and the code therein is not executed (for instance, the gap is not printed).  This is unnacceptable.  
  
This also breaks the notion you @Kojoley suggested that  
> a rule is a barrier for any trickery.  
  
What is going wrong with X3, or what am I doing wrong?

---

## Comment 24

> Username: Kojoley  
> Created at: 2021-05-31 00:11:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851085801  

I feel your frustration, but you are posting the very similar code multiple times. I cannot say for sure it is the same bug (https://github.com/boostorg/spirit/issues/679#issuecomment-847201554), but I am not going to investigate it right now either because there is a high probability that it is. When I or somebody else fix the MCVE above I will recheck your examples. You can use a workaround (as you did in #681) until the bug is fixed.

---

## Comment 25

> Username: Bockeman  
> Created at: 2021-05-31 00:23:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851089403  

Given you have stated  
> until the bug is fixed.  
  
Please could you explain to me what the bug is so that I might devise (or you might suggest) a workaround.  
  
None of the workarounds that I have used, _to get the code to compile,_ is acceptable because I end up with code that does not perform the inteded task.  ATM I don't care how clumsy a workaround might be, as long as it can be applied consistently and scaled to my real world code.  
  
To be clear, I am not interested in any prolonged discussion about possible causes or ways to fix.  I just want  X3 to do the job it is supposed to.  At present, I am blocked and cannot get X3 to work in several applications.

---

## Comment 26

> Username: Kojoley  
> Created at: 2021-05-31 02:19:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851121853  

> Please could you explain to me what the bug is so that I might devise (or you might suggest) a workaround.  
  
https://github.com/boostorg/spirit/issues/679#issuecomment-847201554  
  
> None of the workarounds that I have used, _to get the code to compile,_ is acceptable because I end up with code that does not perform the inteded task. ATM I don't care how clumsy a workaround might be, as long as it can be applied consistently and scaled to my real world code.  
  
You code that does not compile from https://github.com/boostorg/spirit/issues/679#issuecomment-851081797 with an applied workaround:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
namespace minimal { namespace ast  
{  
  struct white		: std::string			{using std::string::string;};  
  struct braced						{char open; std::string	text; char close;};  
  struct gap_item	: x3::variant<white,braced>	{using base_type::operator=;};  
  struct gap 		: std::vector<gap_item>		{};  
  struct start_item	: x3::variant<gap,std::string>	{using base_type::operator=;};  
  struct start	: std::vector<start_item>		{using std::vector<start_item>::vector;};  
}}  
BOOST_FUSION_ADAPT_STRUCT(minimal::ast::braced, open, text, close)  
  
namespace minimal { namespace parser  
{  
  using x3::char_;  
  using x3::space;  
  using x3::raw;  
    
  x3::rule<struct white,	ast::white>	const	white		= "white";  
  x3::rule<struct braced,	ast::braced>	const	braced		= "braced";  
  x3::rule<struct gap,		ast::gap>	const	gap		= "gap";  
  x3::rule<struct start_item,	ast::start_item>	const	start_item = "start_item";  
  x3::rule<struct start,	ast::start>	const	start		= "start";  
    
  auto const white_def		= raw[+space];  
  auto const braced_def		= char_('{') >> *(char_ -'}') >> char_('}');	// { ... }  
  auto const gap_def		= +(white | braced);				// spaces etc  
  auto const start_item_def = gap | +(char_ - '{' - space);  
  auto const start_def		= +start_item;		// gap=container or string  
    
  BOOST_SPIRIT_DEFINE(white, gap, braced, start_item, start);  
}}  
  
int main()  
{  
  char const*			iter	= "? {;};", * const end	= iter + std::strlen(iter);  
  minimal::ast::start	ast;  
  return !parse(iter, end, minimal::parser::start, ast) || iter!=end;  
}  
```

---

## Comment 27

> Username: Bockeman  
> Created at: 2021-05-31 19:38:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851649568  

Thank you very much for that code you supplied with a workaround applied.  That works for me.  
  
I want a workaround that is **consistent** and **scaleable** to real world X3 applications.  
  
Your _workaround_ appears to be: "for each instance of a container<variant>, add an additional rule in the parser specifically for the variant".  This is clumsy (which I said I did not mind, in order to get me going), and by applying it everywhere, it is **consistent** and it has the _possibility_ of being **scalable** (I can apply it through out my X3 applications, but not yet tried).  
  
However, you did not explain what is the nature of the bug and why this solution is a viable workaround.  Please could you explain.  
  
Independently, and with suggestions from others, I discovered another workaround.  All of the inherited classes are replaced by structures containing a single item.  This means that single item tuples are created.  You have warned that these might not be supported in all cases.  I prefer this workaround because it changes the AST  (it is just a different way of defining the class) rather than the change to the parser (which adds extra clutter and breaks the spirit of X3 [pun!]).  
  
```  
// Purpose:	Working example using single element *** TUPLE *** consistently, instead of class inheritance  
  
#define BOOST_SPIRIT_X3_DEBUG		// Provide some meaningful reassuring output.  
  
#include <string>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3		= boost::spirit::x3;  
  
namespace ast {  
  struct white						{std::string			value;};    // *** TUPLE ***  
  struct braced						{char open; std::string	text; char close;};  
  struct gap_item	: x3::variant<white,braced>	{using base_type::operator=;};  
  struct gap 						{std::vector<gap_item>		value;};    // *** TUPLE ***  
  struct start_item	: x3::variant<gap,std::string>	{using base_type::operator=;};  
  struct start						{std::vector<start_item>	value;};    // *** TUPLE ***  
}  
BOOST_FUSION_ADAPT_STRUCT(ast::white,		value)						    // *** TUPLE ***  
BOOST_FUSION_ADAPT_STRUCT(ast::gap,		value)						    // *** TUPLE ***  
BOOST_FUSION_ADAPT_STRUCT(ast::start,		value)						    // *** TUPLE ***  
BOOST_FUSION_ADAPT_STRUCT(ast::braced,		open, text, close)  
  
namespace parser {  
  using x3::char_; using x3::space; using x3::raw; using x3::graph;  
    
  x3::rule<struct white,	ast::white>	const	white		= "white";  
  x3::rule<struct braced,	ast::braced>	const	braced		= "braced";  
  x3::rule<struct gap,		ast::gap>	const	gap		= "gap";  
  x3::rule<struct start,	ast::start>	const	start		= "start";  
    
  static auto const white_def		= raw[+space];  
  static auto const braced_def		= char_('{') >> *~char_('}') >> char_('}');	// { ... }  
  static auto const gap_def		= +(white | braced);				// spaces etc  
  static auto const start_def		= +(gap | +(graph -'{'));			// gap or body  
    
  BOOST_SPIRIT_DEFINE(white, braced, gap, start);  
}  
  
int main()  
{  
  char const*		iter	= "? {;};", * const end	= iter + std::strlen(iter);  
  ast::start		ast;  
  return !parse(iter, end, parser::start, ast) || iter!=end;  
}  
```  
  
Please could you comment on this alternative workaround compared to yours.  I need your expertise to advise me, and others, given your understanding of the underlying problem (which I would appreciate you explaining to me).

---

## Comment 28

> Username: Kojoley  
> Created at: 2021-05-31 22:33:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-851705046  

All your questions were already answered. I have nothing more to say, and I am not going to repeat myself over and over and over and over again.

---

## Comment 29

> Username: Kojoley  
> Created at: 2021-10-19 00:56:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/679#issuecomment-946278138  

#702 will fix the main problem and https://github.com/boostorg/spirit/issues/679#issuecomment-851081797. The https://github.com/boostorg/spirit/issues/679#issuecomment-851649568 as well as other code with single element tuples still gives compile errors, most likely because of https://github.com/boostorg/spirit/pull/178.
