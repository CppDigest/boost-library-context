# #461 - X3: Recursive grammar: alternative container parses substitution issue [Closed]

> Username: Xeverous  
> Created at: 2019-02-13 21:42:21 UTC  
> Updated at: 2020-06-06 18:38:26 UTC  
> Closed at: 2020-06-06 18:38:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/461  

I would like to parse such text:  
  
```  
config {  
	foo: 0  
	bar: 1 {  
		bar_child: 11  
		bar_child2: 12  
	}  
	quux: 2  
}  
```  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
	struct config_param  
	{  
		std::string name;  
		int value;  
		std::vector<config_param> child_params;  
	};  
  
	struct config  
	{  
		std::vector<config_param> params;  
	};  
}  
  
BOOST_FUSION_ADAPT_STRUCT(  
	ast::config_param,  
	name, value, child_params)  
  
BOOST_FUSION_ADAPT_STRUCT(  
	ast::config,  
	params)  
  
using comment_type = x3::rule<class comment_class>;  
BOOST_SPIRIT_DECLARE(comment_type)  
  
using identifier_type = x3::rule<class identifier_class, std::string>;  
BOOST_SPIRIT_DECLARE(identifier_type)  
  
using whitespace_type = x3::rule<class whitespace_class>;  
BOOST_SPIRIT_DECLARE(whitespace_type)  
  
using config_param_type = x3::rule<class config_param_class, ast::config_param>;  
BOOST_SPIRIT_DECLARE(config_param_type)  
  
using config_type = x3::rule<class config_class, ast::config>;  
BOOST_SPIRIT_DECLARE(config_type)  
  
using grammar_type = x3::rule<class grammar_class, ast::config>;  
BOOST_SPIRIT_DECLARE(grammar_type)  
  
using skipper_type = whitespace_type;  
using iterator_type = std::string::const_iterator;  
using context_type = x3::phrase_parse_context<skipper_type>::type;  
  
const comment_type comment = "comment";  
const auto comment_def = x3::lexeme['#' >> *(x3::char_ - '\n')];  
BOOST_SPIRIT_DEFINE(comment)  
  
const identifier_type identifier = "identifier";  
const auto identifier_def = x3::lexeme[(x3::alpha | '_') >> *(x3::alnum | '_')];  
BOOST_SPIRIT_DEFINE(identifier)  
  
const whitespace_type whitespace = "whitespace";  
const auto whitespace_def = x3::space | comment;  
BOOST_SPIRIT_DEFINE(whitespace)  
  
const config_param_type config_param = "config param";  
const auto config_param_def = identifier > ':' > x3::int_ > (('{' > *config_param > '}') /* | x3::attr(std::vector<ast::config_param>()) */ );  
BOOST_SPIRIT_DEFINE(config_param)  
  
const config_type config = "config";  
const auto config_def = x3::lit("config") > '{' > *config_param > '}';  
BOOST_SPIRIT_DEFINE(config)  
  
const grammar_type grammar = "code";  
const auto grammar_def = config;  
BOOST_SPIRIT_DEFINE(grammar)  
  
BOOST_SPIRIT_INSTANTIATE(grammar_type, iterator_type, context_type)  
```  
  
With comment enabled, every config param must have empty `{}` after it's value. I tried to remove this requirement by providing alternative that results in the same attribute (empty vector of childs). However, if you uncomment code, it does not compile.  
  
Is this the same bug as #434 ? Any workaround?

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-13 21:59:25 UTC  
> Updated at: 2019-02-14 16:48:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463392124  

Hmm, did not see such an awful backtrace in a long time. Cannot say where the problems comes from, but for some reason `is_substitute` instantiated with the same types and we do not have such a specialization.  
  
Try to workaround the problem by adding the specialization:  
```cpp  
template <typename T>  
struct boost::spirit::x3::traits::is_substitute<T, T> : boost::mpl::true_ {};  
```

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-02-13 22:25:16 UTC  
> Updated at: 2019-02-14 16:48:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463400553  

I haven't thoroughly looked through template backtrace but `error: invalid use of incomplete type 'boost::mpl::bind3< [...] >::type' {aka 'struct boost::spirit::x3::traits::is_substitute<std::vector<ast::config_param>, std::vector<ast::config_param>, void>'}` seemed almost ok for me as  `(('{' > *config_param > '}') | x3::attr(std::vector<ast::config_param>()))` does look like it would instantiate an alternative with exactly the same types ... incomplete type error was quite misleading, didn't thought it could result from failed SFINAE of `is_substitute`.  
  
I tried to workaround it differently:   
```cpp  
	struct config_param  
	{  
		std::string name;  
		int value;  
		std::optional<std::vector<config_param>> child_params;  
	};  
  
const auto config_param_def = identifier > ':' > x3::int_ > -('{' > *config_param > '}');  
```  
But optional vector does't compile either:  
```  
boost/spirit/home/x3/support/traits/container_traits.hpp:125:15: error: 'class std::optional<std::vector<ast::config_param> >' has no member named 'insert'  
             c.insert(c.end(), std::move(val));  
             ~~^~~~~~  
```  
  
Something causes it to treat `optional<vector>` like just `vector`

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-13 22:36:57 UTC  
> Updated at: 2019-02-14 16:48:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463404101  

Please open a separate issue for optional with container attributes. While the idea that optional should handle containers seems odd for me, it is justified in your example and if it works in Qi it possibly should be fixed in X3.

---

## Comment 4

> Username: Xeverous  
> Created at: 2019-02-13 22:47:54 UTC  
> Updated at: 2019-02-14 16:48:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463407229  

I had never used Qi, and I think optional containers can collapse to containers because empty container holds the same amount of data (nothing) as no container.  
  
I saw there are some `is_same` traits used in specializations of `is_substitute`, perhaps there is a bug in that causes it to SFINAE all of them.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-13 22:54:49 UTC  
> Updated at: 2019-02-14 16:48:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463409301  

> I had never used Qi, and I think optional containers can collapse to containers because empty container holds the same amount of data (nothing) as no container.  
  
I do not want even to think about this, because there are a lot of questionable usages. In general - no, an optional container is not the same thing as an empty container.  
  
> I saw there are some `is_same` traits used in specializations of `is_substitute`, perhaps there is a bug in that causes it to SFINAE all of them.  
  
The problem is before `is_substitute`, it should not be called with the same parameters, most likely variant simplification does not work for some reason.

---

## Comment 6

> Username: Xeverous  
> Created at: 2019-02-13 23:03:58 UTC  
> Updated at: 2019-02-14 16:48:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463411850  

> I do not want even to think about this, because there are a lot of questionable usages. In general - no, an optional container is not the same thing as an empty container.  
  
This. [This](https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/compound_attribute_rules.html) needs more examples. And better syntax - it's hard to read and understand. Also most expressions in the left column are duplicated.  
  
Compound attributes could be written much simplier, just look at this:  
```  
A | B         => variant<A, B>  
A | B | A     => variant<A, B>  
A | A         => A  
A | unused    => optional<A>  
  
A >> B         => tuple<A, B>  
vector<A> >> A => vector<A>  
A >> unused    => A  
```

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-02-13 23:11:52 UTC  
> Updated at: 2019-02-14 16:48:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463413988  

> This. [This](https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/compound_attribute_rules.html) needs more examples. And better syntax - it's hard to read and understand.  
  
It is complete grammar. There cannot be more 'examples'. If you have a better syntax in mind - please open a PR.  
  
> Also most expressions in the left column are duplicated.  
  
This is #363.  
  
Though repetition of the expression in the **right** column is incomprehensible to me.

---

## Comment 8

> Username: Xeverous  
> Created at: 2019-02-13 23:24:06 UTC  
> Updated at: 2019-02-14 16:48:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463417305  

> There cannot be more 'examples'  
  
There can be because users may be confused which rules apply in more complex (nested) grammars - for example, can `-(-(-A))` collapse to `optional<A>`  or must it be `optional<optional<optional<A>>>`?

---

## Comment 9

> Username: Xeverous  
> Created at: 2019-02-13 23:28:36 UTC  
> Updated at: 2019-02-14 16:48:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463418378  

Tried example with `boost::optional<std::vector<ast::config>>` instead of `std::optional` - doesn't compile either but has a different error:  
  
```  
boost/fusion/support/is_view.hpp:31:45: error: no type named 'is_view' in 'class boost::optional<std::vector<ast::config_param> >'  
                 typedef typename T::is_view type;  
                                             ^~~~  
```

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-02-13 23:57:50 UTC  
> Updated at: 2019-02-14 16:48:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463425902  

> There can be because users may be confused which rules apply in more complex (nested) grammars - for example, can `-(-(-A))` collapse to `optional<A>` or must it be `optional<optional<optional<A>>>`?  
  
Oh god, please stop. There is also containers in containers, and if all this would collapse it will explode. Also a note - the grammar on that page is not a holy truth, otherwise there could not be sequence to container collapsing, plain into optional parsing and other stuff.  
  
> Tried example with `boost::optional<std::vector<ast::config>>` instead of `std::optional` - doesn't compile either but has a different error:  
  
I do not get what you tried to do.

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-02-14 00:12:25 UTC  
> Updated at: 2019-02-14 16:48:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463429439  

I have spotted that you are using single-element sequences, when I fixed it (see https://github.com/boostorg/spirit/issues/408#issuecomment-432717410) - your parser compiles (with either optional or variant with `attr` or `eps`). It is a know pitfall, and most likely this will not be fixed (previously I was thinking that it is a legitimate use and fixed some usages, but then I turned my mind) as it is abusive usage of Fusion. The generic answer: if you want you attribute to be treated as a container - implement the STL container API for it or use inheritance from a container.

---

## Comment 12

> Username: djowel  
> Created at: 2019-02-14 02:04:20 UTC  
> Updated at: 2019-02-14 16:48:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463455560  

@Xeverous Here's my suggestion: don't fight the attributes. Try your best to make the grammar attribute oriented in such a way that 'magic' transformations are avoided as much as possible.

---

## Comment 13

> Username: Xeverous  
> Created at: 2019-02-14 09:47:09 UTC  
> Updated at: 2019-02-14 16:48:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463562266  

> using single-element sequences [...] is abusive usage of Fusion  
  
I still don't get what's wrong with this/why it is abusive. Is this an implementation problem or a AST design issue because spirit was never supposed to work with 1-element fusions?  
  
> There is also containers in containers, and if all this would collapse it will explode. Also a note - the grammar on that page is not a holy truth, otherwise there could not be sequence to container collapsing, plain into optional parsing and other stuff.  
  
I do not want to propose any additional collapsing. IMO the library needs more examples to guide the users what is and what is not the correct usage. Examples of the form: grammar + recommended attribute for that grammar. **It's very easy to misuse spirit and hard to tell whether grammar X with attribute of type Y is correct use of the library.**  
  
___  
  
Regarding the example: what is the correct grammar and attribute type for such text?  
  
```  
	foo: 0  
	bar: 1 {  
		bar_child: 11  
		bar_child2: 12  
	}  
	quux: 2 { quux_child: 22 }  
```  
  
The grammar `identifier > ':' > x3::int_ > ('{' > *config_param > '}')` requires `{}` to be always present so the text would need to be like this:  
  
```  
	foo: 0 {}  
	bar: 1 {  
		bar_child: 11 {}  
		bar_child2: 12 {}  
	}  
	quux: 2 { quux_child: 22 {} }  
```  
  
How should I edit the grammar the make that `{}` not required?  
  
- `identifier > ':' > x3::int_ > (('{' > *config_param > '}') | x3::attr(std::vector<ast::config_param>()))` - is this valid use of spirit? fusion of `string, int, vector<config_param>` correct AST for this? (this currently does not compile)  
- `identifier > ':' > x3::int_ > -('{' > *config_param > '}')` - is this correct? What is the recommended AST for this? (this currently does not compile for fusion of `string, int, optional<vector<config_param>>`)

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-02-14 12:40:14 UTC  
> Updated at: 2019-02-14 16:49:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463612699  

> I still don't get what's wrong with this/why it is abusive. Is this an implementation problem or a AST design issue because spirit was never supposed to work with 1-element fusions?  
  
Because it makes the attribute to belong to multiple mutually exclusive categories.  
  
```cpp  
namespace ast  
{  
	struct config  
	{  
		std::vector<config_param> params;  
	};  
}  
BOOST_FUSION_ADAPT_STRUCT(  
	ast::config,  
	params)  
```  
  
In this declaration `ast::config` is not a container.  
  
  
> How should I edit the grammar the make that `{}` not required?  
>  * `identifier > ':' > x3::int_ > (('{' > *config_param > '}') | x3::attr(std::vector<ast::config_param>()))` - is this valid use of spirit? fusion of `string, int, vector<config_param>` correct AST for this? (this currently does not compile)  
>  * `identifier > ':' > x3::int_ > -('{' > *config_param > '}')` - is this correct? What is the recommended AST for this? (this currently does not compile for fusion of `string, int, optional<vector<config_param>>`)  
  
Either works, the third variant is `identifier > ':' > x3::int_ > (('{' > *config_param > '}') | x3::eps)`  
  
The problem is in `ast::config`

---

## Comment 15

> Username: djowel  
> Created at: 2019-02-14 12:56:28 UTC  
> Updated at: 2019-02-14 16:49:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463617287  

> IMO the library needs more examples to guide the users what is and what is not the correct usage. Examples of the form: grammar + recommended attribute for that grammar. **It's very easy to misuse spirit and hard to tell whether grammar X with attribute of type Y is correct use of the library.**  
  
Contributions are always very welcome :-)

---

## Comment 16

> Username: Xeverous  
> Created at: 2019-02-14 13:29:53 UTC  
> Updated at: 2019-02-14 16:49:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463626958  

@djowel I would like to contribute but I must know what is the correct usage in the first place.  
  
I will try to make a PR soon that IMO significantly simplifies compound attribute rules doc page, then I will ask questions what is correct and what is not.

---

## Comment 17

> Username: Kojoley  
> Created at: 2019-02-14 16:20:56 UTC  
> Updated at: 2019-02-14 16:23:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-463690897  

I rechecked your example and found that I was wrong, I am sorry. Please provide MWE next time.  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
struct Foo  
{  
    int value;  
    std::vector<Foo> children;  
};  
BOOST_FUSION_ADAPT_STRUCT(Foo,  
    value, children)  
  
x3::rule<class foo_r, Foo> foo;  
auto const foo_def = x3::int_ >> ('{' >> *foo >> '}' | '[' >> *foo >> ']');  
BOOST_SPIRIT_DEFINE(foo)  
  
BOOST_SPIRIT_INSTANTIATE(decltype(foo), char const*, x3::unused_type)  
```

---

## Comment 18

> Username: Frkymhn  
> Created at: 2020-01-09 12:36:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-572543008  

i want to work on this issue please guide me

---

## Comment 19

> Username: Kojoley  
> Created at: 2020-01-09 13:26:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/461#issuecomment-572560372  

> i want to work on this issue please guide me  
  
For some reason, the specialization https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/include/boost/spirit/home/x3/support/traits/is_substitute.hpp#L51-L60 is offending, if I comment it out - the repro from my previous post will compile. Try to identify the reason.
