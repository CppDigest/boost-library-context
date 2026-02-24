# #833 - Returning `error_handler_result::fail` on expectation failure results in false continuation on the surrounding grammar [Closed]

> Username: Xeverous  
> Created at: 2025-09-25 09:53:43 UTC  
> Updated at: 2025-10-06 11:36:45 UTC  
> Closed at: 2025-10-06 11:36:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/833  

This is the bug I was writing about in other issue. It is very bizarre. The reproduction is somewhat large but I'm happy I managed to even recreate it after hours spent of pulling code out of my project without success.  
  
(details at the bottom)  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <boost/spirit/home/x3/support/utility/lambda_visitor.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
	struct identifier : x3::position_tagged  
	{  
		identifier& operator=(std::string str)  
		{  
			value = std::move(str);  
			return *this;  
		}  
  
		const std::string& get_value() const { return value; }  
  
		std::string value;  
	};  
  
	struct string_literal : std::string, x3::position_tagged  
	{  
		const std::string& get_value() const { return *this; }  
	};  
  
	struct integer_literal : x3::position_tagged  
	{  
		integer_literal& operator=(int n)  
		{  
			value = n;  
			return *this;  
		}  
  
		int get_value() const { return value; }  
  
		int value;  
	};  
  
	struct floating_point_literal : x3::position_tagged  
	{  
		floating_point_literal& operator=(double n)  
		{  
			value = n;  
			return *this;  
		}  
  
		double get_value() const { return value; }  
  
		double value;  
	};  
  
	struct literal_expression : x3::variant<floating_point_literal, integer_literal, string_literal>, x3::position_tagged  
	{  
		using base_type::base_type;  
		using base_type::operator=;  
	};  
  
	struct sequence : std::vector<literal_expression>, x3::position_tagged {};  
  
	struct constant_definition : x3::position_tagged  
	{  
		identifier name;  
		sequence seq;  
	};  
} // namespace ast  
  
BOOST_FUSION_ADAPT_STRUCT(ast::constant_definition, name, seq)  
  
using iterator_type = const char*;  
  
struct parse_error  
{  
	iterator_type error_place;  
	iterator_type backtracking_place;  
	std::string what_was_expected;  
};  
  
using error_holder_type = std::vector<parse_error>;  
using position_cache_type = x3::position_cache<std::vector<iterator_type>>;  
  
struct error_holder_tag;  
struct position_cache_tag;  
  
struct annotate_on_success  
{  
	// template <typename Iterator, typename Context, typename... Types>  
	// void on_success(  
	// 	const Iterator& first,  
	// 	const Iterator& last,  
	// 	x3::variant<Types...>& ast,  
	// 	const Context& context)  
	// {  
	// 	ast.apply_visitor(x3::make_lambda_visitor<void>([&](auto& node)  
	// 	{  
	// 		this->on_success(first, last, node, context);  
	// 	}));  
	// }  
  
	// template <typename T, typename Iterator, typename Context>  
	// void on_success(  
	// 	const Iterator& first,  
	// 	const Iterator& last,  
	// 	T& ast,  
	// 	const Context& context)  
	// {  
	// 	position_cache_type& positions = x3::get<position_cache_tag>(context).get();  
	// 	positions.annotate(ast, first, last);  
	// }  
};  
  
struct error_on_error  
{  
	template <typename Iterator, typename Exception, typename Context>  
	x3::error_handler_result on_error(  
		Iterator& first,  
		const Iterator& /* last */,  
		const Exception& ex, // x3::expectation_failure<Iterator> or similar type  
		const Context& context)  
	{  
		error_holder_type& error_holder = x3::get<error_holder_tag>(context).get();  
		error_holder.push_back(parse_error{ex.where(), first, ex.which()});  
		return x3::error_handler_result::fail;  
	}  
};  
  
  
//struct identifier_class                      : error_on_error, annotate_on_success {};  
//struct integer_literal_class                 : error_on_error, annotate_on_success {};  
//struct floating_point_literal_class          : error_on_error, annotate_on_success {};  
struct string_literal_class                  : error_on_error, annotate_on_success {};  
//struct literal_expression_class              : error_on_error, annotate_on_success {};  
//struct sequence_class                        : error_on_error, annotate_on_success {};  
//struct constant_definition_class             : error_on_error, annotate_on_success {};  
  
struct identifier_class                      {};  
struct integer_literal_class                 {};  
struct floating_point_literal_class          {};  
//struct string_literal_class                  {};  
struct literal_expression_class              {};  
struct sequence_class                        {};  
struct constant_definition_class             {};  
  
using floating_point_literal_type = x3::rule<floating_point_literal_class, ast::floating_point_literal>;  
BOOST_SPIRIT_DECLARE(floating_point_literal_type)  
  
using integer_literal_type = x3::rule<integer_literal_class, ast::integer_literal>;  
BOOST_SPIRIT_DECLARE(integer_literal_type)  
  
using string_literal_type = x3::rule<string_literal_class, ast::string_literal>;  
BOOST_SPIRIT_DECLARE(string_literal_type)  
  
using literal_expression_type = x3::rule<literal_expression_class, ast::literal_expression>;  
BOOST_SPIRIT_DECLARE(literal_expression_type)  
  
using sequence_type = x3::rule<sequence_class, ast::sequence>;  
BOOST_SPIRIT_DECLARE(sequence_type)  
  
using identifier_impl_type = x3::rule<struct identifier_impl_class, std::string>;  
BOOST_SPIRIT_DECLARE(identifier_impl_type)  
using identifier_type = x3::rule<identifier_class, ast::identifier>;  
BOOST_SPIRIT_DECLARE(identifier_type)  
  
using constant_definition_type = x3::rule<constant_definition_class, ast::constant_definition>;  
BOOST_SPIRIT_DECLARE(constant_definition_type)  
  
const floating_point_literal_type floating_point_literal = "number (fractional)";  
const auto floating_point_literal_def = x3::real_parser<double, x3::strict_real_policies<double>>{};  
BOOST_SPIRIT_DEFINE(floating_point_literal)  
  
const integer_literal_type integer_literal = "number (integer)";  
const auto integer_literal_def = x3::int_;  
BOOST_SPIRIT_DEFINE(integer_literal)  
  
const string_literal_type string_literal = "string";  
const auto string_literal_def = x3::lexeme['"' > *(x3::char_ - (x3::lit('"') | '\n' | '\r')) > '"'];  
BOOST_SPIRIT_DEFINE(string_literal)  
  
const literal_expression_type literal_expression = "literal";  
const auto literal_expression_def = floating_point_literal | integer_literal | string_literal;  
BOOST_SPIRIT_DEFINE(literal_expression)  
  
const identifier_impl_type identifier_impl = "identifier";  
const auto identifier_impl_def = x3::lexeme[(x3::alpha | x3::char_('_')) > *(x3::alnum | x3::char_('_'))];  
BOOST_SPIRIT_DEFINE(identifier_impl)  
  
const identifier_type identifier = identifier_impl.name;  
const auto identifier_def = identifier_impl;  
BOOST_SPIRIT_DEFINE(identifier)  
  
const sequence_type sequence = "list of values";  
const auto sequence_def = x3::skip(x3::space - x3::eol)[*literal_expression > (x3::eol | &x3::eoi)];  
BOOST_SPIRIT_DEFINE(sequence)  
  
const constant_definition_type constant_definition = "constant definiton";  
const auto constant_definition_def = (identifier >> '=') > sequence;  
BOOST_SPIRIT_DEFINE(constant_definition)  
  
int main()  
{  
	std::string input =  
		"abc = 123 \"abc\n" // intentionally bad input: missing "  
		"xyz = 456 \"xyz\"";  
  
	auto first = input.data();  
	const auto last = input.data() + input.size();  
  
	position_cache_type position_cache(first, last);  
	error_holder_type error_holder;  
	const auto parser = x3::with<position_cache_tag>(std::ref(position_cache))  
	[  
		x3::with<error_holder_tag>(std::ref(error_holder))[*constant_definition > x3::eoi]  
	];  
  
	std::vector<ast::constant_definition> ast;  
	if (!x3::phrase_parse(first, last, parser, x3::space, ast)) {  
		std::cout << "parse failed\n";  
		return 1;  
	}  
  
	if (first != last) {  
		std::cout << last - first << " characters left\n";  
		return 1;  
	}  
  
	std::cout << "success:\n";  
  
	for (const auto& cd : ast) {  
		std::cout << cd.name.value << " = {\n";  
  
		for (const auto& val : cd.seq) {  
			std::cout << "    literal = ";  
			val.apply_visitor(x3::make_lambda_visitor<void>([](const auto& val){ std::cout << val.get_value(); }));  
			std::cout << "\n";  
		}  
  
		std::cout << "}\n";  
	}  
}  
```  
  
The code implements a simplified assignment/definition syntax from a more complex human-readable file format from my project. The basic idea of the syntax is to allow assigning multiple values to a single identifier, like this (no `,` and no `;` and line breaks matter):  
  
```  
abc = 123 "abc"  
xyz = 456 "xyz"  
```  
  
The code above has a typo in the input and I expect it to result in `x3::expectation_failure`. It throws this error correctly on Boost 1.76 but accepts the input in later versions.  
  
```  
$ git bisect bad  
2db3fde0d02b9a48877147c595289934525882a0 is the first bad commit  
commit 2db3fde0d02b9a48877147c595289934525882a0  
Author: Nikita Kniazev <nok.raven@gmail.com>  
Date:   Tue Jun 1 21:56:22 2021 +0300  
  
    X3: Remove forced iterator rollback in rule  
      
    Removes inconsistency when a parser part is factorized into a rule.  
      
    Fixes regression introduced in #670.  
  
 example/x3/rexpr/rexpr_full/test/parse_rexpr_test.cpp    |  6 ++++--  
 example/x3/rexpr/rexpr_full/test/testing.hpp             |  4 ++--  
 include/boost/spirit/home/x3/nonterminal/detail/rule.hpp | 10 +++-------  
 test/x3/error_handler.cpp                                |  7 +++++--  
 4 files changed, 14 insertions(+), 13 deletions(-)  
```  
  
When it doesn't error, it prints the following:  
  
```  
success:  
abc = {  
    literal = 123  
}  
xyz = {  
    literal = 456  
    literal = xyz  
}  
```  
  
Somehow it skips over entire `"abc` without reporting any `*_literal` object or raising any parse error/exception.  
  
What's even more bizarre, if you (un)comment which rules get `error_on_error` and `annotate_on_success`, e.g. by making all tag types empty, then the bug disappears. Somehow adding `error_on_error` to `string_literal_class` causes it to accept invalid input, even though the handler returns `x3::error_handler_result::fail`.

---

## Comment 1

> Username: Xeverous  
> Created at: 2025-09-25 10:00:41 UTC  
> Updated at: 2025-09-25 10:01:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333205058  

Calling @Kojoley because your commit appeared in git bisect, perhaps you will know more.

---

## Comment 2

> Username: saki7  
> Created at: 2025-09-25 10:56:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333399147  

I ask that we avoid placing blame on Nikita for this implementation, even if the bisect points to his commit. You may not be aware of the history, so let me share some context. In recent years, a few people harshly criticized him for otherwise reasonable changes. That took a serious toll, and he ultimately stepped back from active work. It was one of the saddest chapters in Spirit's history, and I am genuinely heartbroken.  
  
For this issue, I do not believe anyone here intends to repeat that pattern. I am sharing this background so we can keep the discussion constructive. Let’s focus on the technical problem and the path to a fix rather than assigning fault 😃   
  
Regarding the issue itself, I can confirm that this is somewhat unintuitive. However, I'm not sure if there's trivial way to fix. We can alter the iterator handling, but doing so might break someone else's code. I think the problem is that I'm not sure what the _correct_ (or well-defined) behavior should be.

---

## Comment 3

> Username: saki7  
> Created at: 2025-09-25 11:02:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333417303  

By the way, I think you can `return error_handler_result::rethrow;` as a workaround, as you're engaging expectation failure anyway.

---

## Comment 4

> Username: saki7  
> Created at: 2025-09-25 11:08:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333438177  

If you want me to consider this issue as the exception for feature-freeze, I need at least below information to proceed:  
  
- A minimal reproducible unit test that illustrates the expected design   
- A minimal reproducible unit test that illustrates the future incompatibility that will be occurring after the expected fix is deployed  
- A technical description that clearly explains why the specific change included in the commit 2db3fde0d02b9a48877147c595289934525882a0 resulted in such regression

---

## Comment 5

> Username: Xeverous  
> Created at: 2025-09-25 12:27:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333700599  

> I ask that we avoid placing blame on Nikita for this implementation, even if the bisect points to his commit.  
  
I am not aware of Spirit's history but I did not intent to place blame on anyone here. No sane person commits bugs on purpose. I just pinged Nikita because as the author of the change, he might be aware of something we don't know and the additional context can put some light into the issue - he was doing some significant changes to the library. I'm already grateful you and others are still willing to look into this given recent departure to X4.  
  
It seems to be a somewhat complex problem because Nikita's commit states it fixes a regression and now this commit turned out to create another regression. Perhaps there is a bigger issue (a design issue) that can not be solved by small commits. Maybe Nikita knows something whether "bigger design flaw" actually exists.  
  
> I think the problem is that I'm not sure what the correct (or well-defined) behavior should be.  
  
Yes and I think this might be an even bigger issue than this bug. Spirit X3 documentation taught me how to write grammars but not enough in "how to use the library effectively". I still do more or less blind assumptions about particular behaviors.  
  
You can even see that this very example has 2-layer `identifier` because for unknown reason, using it directly results in compilation errors related to attributes (`std::string` has very complex synthesis logic and it may/may not always be compatible with some `char_`-based grammars). There is also a chance it's completely irrelevant now and is just a remnant of a bug I reported much earlier that has been fixed long ago (maybe even by Nikita).  
  
> A minimal reproducible unit test that illustrates the expected design  
  
Sure, I will try to minimize the example further. Perhaps `string_literal` alone could be enough to test the bug.  
  
> A minimal reproducible unit test that illustrates the future incompatibility that will be occurring after the expected fix is deployed.  
  
Not sure what you meant here. The code is already incompatible across Boost versions - it works in 1.76 but not in 1.77. In my opinion it should error on the missing `"` but we obviously can't just blindly revert Nikita's commit.  
  
> A technical description that clearly explains why the specific change included in the commit 2db3fde0d02b9a48877147c595289934525882a0 resulted in such regression  
  
Right now I have no idea. And if I am supposed to write this explanation, I would very appreciate someone explaining to me how the implementation works. Spirit X3 documentation explains a lot but it treats the library like a black box - if something goes wrong or contrary to user's expectations, I have no idea where and why.

---

## Comment 6

> Username: Kojoley  
> Created at: 2025-09-25 12:41:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333749234  

It's been quite a while and I don't remember much, it's most likely the case #719 was supposed to fix. I remember I almost finished the patch but haven't updated the PR. I can try to find and upload my latest version of that PR, but I'm not going to rebase and resolve merge conflicts that emerged from the code churn that has happened since then, sorry.

---

## Comment 7

> Username: Kojoley  
> Created at: 2025-09-25 12:50:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333781668  

Here is your example reduced, rolling back an iterator in the error handler workarounds the issue:  
```c++  
#include <boost/spirit/home/x3.hpp>  
  
#include <iostream>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
  
struct error_on_error  
{  
	template <typename Iterator, typename Exception, typename Context>  
	x3::error_handler_result on_error(  
		Iterator& first,  
		const Iterator& last,  
		const Exception& ex, // x3::expectation_failure<Iterator> or similar type  
		const Context& context)  
	{  
        //first = last; // <-- uncomment to workaround the issue  
		return x3::error_handler_result::fail;  
	}  
};  
  
  
struct string_literal_class : error_on_error {};  
  
using string_literal_type = x3::rule<string_literal_class>;  
BOOST_SPIRIT_DECLARE(string_literal_type)  
  
const string_literal_type string_literal = "string";  
const auto string_literal_def = x3::lexeme['"' > *~x3::char_("\"\n\r") > '"'];  
BOOST_SPIRIT_DEFINE(string_literal)  
  
  
int main()  
{  
	std::string input =  
		"\"123\n" // intentionally bad input: missing "  
    ;  
  
	auto first = input.data();  
	const auto last = input.data() + input.size();  
  
	const auto parser = *string_literal > x3::eol;  
	if (!x3::phrase_parse(first, last, parser, x3::space - x3::eol)) {  
		std::cout << "parse failed\n";  
		return 1;  
	}  
  
	if (first != last) {  
		std::cout << last - first << " characters left\n";  
		return 1;  
	}  
  
	std::cout << "success\n";  
}  
```

---

## Comment 8

> Username: saki7  
> Created at: 2025-09-25 13:20:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3333998727  

Thanks for the code. I know it is merely a workaround, but I have a serious concern on modifying the iterator references from within the error handler.   
  
Recently I've made some design decision on this area:   
  
> https://github.com/boostorg/spirit_x4/commit/47945d1e8145abd32c7eaa2facee8e0891146bc3  
> Historically, Spirit has passed mutable lvalue references of the  
> *internal* iterators to the `on_success`/`on_error` handlers. This  
> behavior was semantically a mistake, because:  
>   (1) `on_success`/`on_error` mechanism was designed to be  
>       grammar-agnostic, and  
>   (2) it does not make sense to modify the grammar-specific  
>       iterator on the grammar-agnostic callback.  
>   
> Furthermore, any modification to X3's internal iterator variables  
> may invoke undefined behavior, since we had never provided any  
> kind of guarantee on how those variables are processed in X3's  
> implementation details.  
  
This was merged on X4 but abandoned on X3. However, the rationale still applies to X3 for some good reason.  
  
While the manual rollback `first = last;` might work for certain applications, the problem is that we provide no guarantee of how the modified iterators are interpreted on the caller's part (i.e., Spirit's implementation details). And there's a risk for exposing such flexibility to the (innocent) users.  
  
At the least, if we are going to fix the root problem, we need to clarify the "correct" semantics as I mentioned in https://github.com/boostorg/spirit/issues/833#issuecomment-3333438177.  
  
> > A minimal reproducible unit test that illustrates the future incompatibility that will be occurring after the expected fix is deployed.  
>  
> Not sure what you meant here. The code is already incompatible across Boost versions - it works in 1.76 but not in 1.77. In my opinion it should error on the missing " but we obviously can't just blindly revert Nikita's commit.  
  
I meant that, if we're going to change the behavior then we are going to have three states:  
  
1. Behavior of 1.76 and before  
2. Behavior from 1.77 until now  
3. Behavior after the potential fix is deployed  
  
I assume that 1 and 3 may or may not be the same. If there exists some legitimate code that works on 2 but fails after 3, we need a unit test that asserts parse failure on such scenario.  
  
> Yes and I think this might be an even bigger issue than this bug.  
  
I agree, and that's exactly the problem. If it turns out that we need to change the iterator rollback semantics in general, that would be a _huge_ semantic change on the entire X3 codebase.

---

## Comment 9

> Username: Xeverous  
> Created at: 2025-09-25 14:00:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334281086  

Thanks @Kojoley for a quick response and for smaller reproduction code.  
  
My fear of bigger design issue was true. Personally I find modifying iterators in error handlers a very risky idea - even if implementation would support it, some iterators can be very complex (someone could use Spirit to parse network stream bytes/packets) and giving library user a capability to modify them results in very error-prone code. I think it was a good decision to disallow modifying iterators in X4.  
  
> At the least, if we are going to fix the root problem, we need to clarify the "correct" semantics as I mentioned in https://github.com/boostorg/spirit/issues/833#issuecomment-3333438177.  
  
I was actually the person that reported missing error handling article in Spirit documentation long time ago because I found this area to be very undocumented. Since then the documentation contains a large example but I still feel uninformed how to use them effectively. Very little was explained what to do with all parameters in the handler.  
  
I don't know what current semantics are implemented but this is my opinion (as a user of the library):  
  
- if an error handler returns `x3::error_handler_result::fail`, the library should rollback the iterator  
- if X3 wants to allow users modifying iterators, it should define precise semantics and interaction with library's implementation  
- the library should not require any iterator modifications in error handler by default  
  
A side question, do you @saki7 know why Spirit uses exceptions for reporting errors? I'm not a type of person that bans general use of exceptions in C++ but I still find their use in this case surprising (bad input is not enough in my opinion to throw). Is there any particular benefit of using exceptions in Spirit instead of ordinary function returns? Is X4 planned to be same in this area?

---

## Comment 10

> Username: Xeverous  
> Created at: 2025-09-25 14:10:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334349382  

> I assume that 1 and 3 may or may not be the same. If there exists some legitimate code that works on 2 but fails after 3, we need a unit test that asserts parse failure on such scenario.  
  
I can't prove it but I think there is no legitimate code that works on 2. If some code would broke on 3, it would probably be broken on 1 aswell.  
  
I find this whole issue to be a bug for this reason: if a parse fails (and there is no alternative), it should fail top-level parse (by throwing or returning an iterator that `!= last`). There should be no situation where invalid input is skipped over and ignored. If a library user wants to skip over some invalid part and ignore it, the user should be required to modify the iterator in the handler.

---

## Comment 11

> Username: saki7  
> Created at: 2025-09-25 14:12:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334359719  

> A side question, do you @saki7 know why Spirit uses exceptions for reporting errors? I'm not a type of person that bans general use of exceptions in C++ but I still find their use in this case surprising (bad input is not enough in my opinion to throw). Is there any particular benefit of using exceptions in Spirit instead of ordinary function returns? Is X4 planned to be same in this area?  
  
Try this: <https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/non_throwing_expectations.html>  
  
It is a relatively new feature implemented in August 2024.  
  
You will have 90x faster runtime performance with the non-throwing expectation. This is implemented in both X3 and X4. I should also note that X4 even defaults to the non-throwing version.  
  
Disclosure: I'm the person who implemented the feature, and it was my primary contribution before I became the maintainer.

---

## Comment 12

> Username: Xeverous  
> Created at: 2025-09-25 14:26:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334442384  

> You will have 90x faster runtime performance with the non-throwing expectation. This is implemented in both X3 and X4. I should also note that X4 even defaults to the non-throwing version.  
  
Amazing news. Is the 90x speedup on any case or only when you throw and catch multiple times? In my project I always fail the parse completely on any exception so a single throw is enough to stop all work. But I guess the sole existence of all potential-throw points causes massive slowdown?

---

## Comment 13

> Username: saki7  
> Created at: 2025-09-25 14:45:48 UTC  
> Updated at: 2025-09-25 14:54:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334539747  

> Amazing news. Is the 90x speedup on any case or only when you throw and catch multiple times? In my project I always fail the parse completely on any exception so a single throw is enough to stop all work. But I guess the sole existence of all potential-throw points causes massive slowdown?  
  
The 90x speedup is a idealistic upperbound number confirmed on a benchmark code, which was tested on failing `a > b` for 100,000 times. In practice, the speedup is limited to the overhead of the C++ exceptions. If the exception is thrown only a single time, then it would have negligible effects. That said, I personally had some performance-critical parser application that parses enormous amount of geographic data, and it became 2-7x faster on real world data.   
  
> if an error handler returns x3::error_handler_result::fail, the library should rollback the iterator  
  
X4 actually requires the `::on_error` callback to return `void`.  
  
In my opinion, error handlers should not be permitted to modify the errors at all. It should only be able to "view" the error, just like the normal C++ `try-catch` block. Note that `throw;` (rethrow) is essentially not needed for parser combinator library, because the non-throwing expectation can always short-circuit.   
  
You have the feeling that modifying internal iterators are error-prone, right? Then think it this way: does it really make sense to return any value that is _not_ `::fail`? Every other enum values would expose the error handling detail into the surrounding grammar, and it would ultimately alter the meaning of `a > b` in the first place.   
  
> if X3 wants to allow users modifying iterators, it should define precise semantics and interaction with library's implementation  
  
Absolutely not! X4 banned the iterator modification for good reason, and we shouldn't make X3 worse than status quo.  
  
> the library should not require any iterator modifications in error handler by default  
  
IMHO, Spirit should not require any iterator modification in any user-facing API. Iterator on parser combinator library is implementation detail by definition, because the whole intent of the library is to hide such primitives from the user. The only exception might be the `raw[p]` directive, I think.

---

## Comment 14

> Username: Xeverous  
> Created at: 2025-09-25 15:20:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334699666  

> You have the feeling that modifying internal iterators are error-prone, right?  
  
Right.  
  
> Then think it this way: does it really make sense to return any value that is not `::fail`?  
  
It doesn't. But the documentation is not clear on this. Since there is also `::accept` and `::retry` I thought the library wanted to allow the users to "fix" some errors (maybe even modify input) - each rule can have a different error handler. This left me confused so I used only the exact code from documentation.  
  
Do you think X3 should remove this enumeration and make it return `void` like in X4? What to do with current (in my opinion incomplete) X3 documentation? Is fixing this worth your time?  
  
> Absolutely not! X4 banned the iterator modification for good reason, and we shouldn't make X3 worse than status quo.  
  
I have never used iterator modification in handlers in X3 (too scary, no documentation), but if you change `Iterator&` to `const Iterator&` it will be a breaking change. What do we do with X3 users who rely on the ability to modify iterators?

---

## Comment 15

> Username: saki7  
> Created at: 2025-09-25 15:34:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3334755583  

> Do you think X3 should remove this enumeration and make it return void like in X4? What to do with current (in my opinion incomplete) X3 documentation? Is fixing this worth your time?  
  
We can't. Initially I tried to implement all these sorts of good changes in X3 but many people argued about the breaking changes. That's why I built the consensus to create X4. It was very, very hard to convince the core developers, I'm almost exhausted by all the political conversations I have had on the mailing list. We should keep X3 frozen for god's sake.  
  
X4 is a great library which solves all these semantic errors and misfeatures, in a very user-friendly manner. I'd suggest the community to seriously consider moving toward the modern codebase.

---

## Comment 16

> Username: Xeverous  
> Created at: 2025-09-25 17:36:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3335207103  

> We should keep X3 frozen for god's sake.  
  
So what's the plan with this issue? Is there a chance for a fix that is not a breaking change? Change logic how/when iterators are rolled back?  
  
> I'd suggest the community to seriously consider moving toward the modern codebase.  
  
I would like to but C++23 is too high for me. I plan to build my project on many targets (including Emscripten) and I worry about the compatibility. I will probably move to Boost.Parser.

---

## Comment 17

> Username: Kojoley  
> Created at: 2025-09-25 22:54:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3336173582  

> Historically, Spirit has passed mutable lvalue references of the  
> internal iterators to the on_success/on_error handlers. This  
> behavior was semantically a mistake, because:  
> (1) on_success/on_error mechanism was designed to be  
> grammar-agnostic, and  
> (2) it does not make sense to modify the grammar-specific  
> iterator on the grammar-agnostic callback.  
  
I'm not a fan of error handler implementation in Spirit because it's done somewhere on a side from the actual parser, but I simply can't agree with that. Error handler can turn error into a success, if it can't adjust the iterator you are killing error recovery feature and the whole point of error handling. What is the purpose of error handlers then? Error reporting? I don't know if forcing to mix parser code with error recovery code is good either.  
  
> And there's a risk for exposing such flexibility to the (innocent) users.  
  
Everything had been already exposed, [Hyrum's Law](https://www.hyrumslaw.com/).  
  
> A side question, do you @saki7 know why Spirit uses exceptions for reporting errors? I'm not a type of person that bans general use of exceptions in C++ but I still find their use in this case surprising (bad input is not enough in my opinion to throw).  
  
It's much cleaner implementation, a non-throwing implementation infests everything with additional complexity and error propagation boilerplates. If you are using expectation failures as a general parser fail you are probably doing something that should've been in the core library, I'm guessing you are collecting an error path like that, that's was something I was working towards at that time IIRC.  
  
When I was looking into a alternate parser reimplementation, an exception throwing-catching implementation was making it so short, clean, functional and error prone that it throw me into a despair.  
  
> You will have 90x faster runtime performance with the non-throwing expectation.  
  
Maybe you are reporting a conservative number, when I was looking into the issue the difference was more than 1000x. But the throwing code path is slow because compilers don't do any optimization with exceptions. A simple throw-catch folding optimization after inclining is more than enough to eliminate exception handling in Spirit in cases where it matters most.  
  
> I find this whole issue to be a bug for this reason: if a parse fails (and there is no alternative), it should fail top-level parse (by throwing or returning an iterator that != last). There should be no situation where invalid input is skipped over and ignored. If a library user wants to skip over some invalid part and ignore it, the user should be required to modify the iterator in the handler.  
  
This is mostly correct.  
  
> I find this whole issue to be a bug for this reason: if a parse fails (and there is no alternative), it should fail top-level parse (by throwing or returning an iterator that != last).  
  
Parser fail and expectation fail are two distinct mechanisms and behaviors. Parser fail is generally backtrackable and how alternate and repeat parsers work, expectation fail is propagated to the point where handler is installed, if any, responsibility of recovering from it is on the (user's) error handler.  
  
> In my opinion, error handlers should not be permitted to modify the errors at all. It should only be able to "view" the error, **just like the normal C++ try-catch**  
  
You are wrong on this one, quoting [\[except.handle\]/16](https://eel.is/c++draft/except.handle#16)  
  
> When the handler declares a reference to an object, any changes to the referenced object are changes to the exception object and will have effect should that object be rethrown.

---

## Comment 18

> Username: saki7  
> Created at: 2025-09-26 08:24:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3337339192  

@Xeverous:  
  
> So what's the plan with this issue? Is there a chance for a fix that is not a breaking change? Change logic how/when iterators are rolled back?  
  
Reposting https://github.com/boostorg/spirit/issues/833#issuecomment-3333438177:  
  
- A minimal reproducible unit test that illustrates the expected design  
  - This must be included in the PR.  
- A minimal reproducible unit test that illustrates the future incompatibility that will be occurring after the expected fix is deployed  
  - If the PR author thinks that [there exists no legitimate application that works on 2](https://github.com/boostorg/spirit/issues/833#issuecomment-3334349382), then we don't need to fear about this point. Even if someone claims that the PR has breaking changes, we can say that we don't support broken grammar that relies on the wrong semantic assumption.  
- A technical description that clearly explains why the specific change included in the commit https://github.com/boostorg/spirit/commit/2db3fde0d02b9a48877147c595289934525882a0 resulted in such regression  
  - This is needed. You should at least try to investigate the original intent of the commit. In your PR, you should explain the original problem that the commit tried to solve, and also write a brief description on why the commit's approach was not properly implemented.  
  
If the above points are satisfied, I will approve the PR. That said, there is some caveat:  
  
- Do NOT touch unrelated parts in X3, even if this issue implies the library-wide iterator semantics has some design mistakes. You should only modify `rule.hpp` and relevant unit tests.  
- Although not mandatory, you should try to submit the same PR to [boostorg/spirit_x4](https://github.com/boostorg/spirit_x4). I will not merge X3's PR until the companion PR is submitted to X4. There are two options for you:   
  1. Wait for @saki7 to implement the companion PR on X4. This may take a decent amount of time, because I have a lot other things to do.  
  2. Submit the companion PR by yourself. This way I will merge them ASAP, as long as the PR satisfies the above criteria I mentioned.

---

## Comment 19

> Username: Xeverous  
> Created at: 2025-09-26 10:10:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3337893135  

@Kojoley in regards to https://github.com/boostorg/spirit/issues/833#issuecomment-3333749234 I could attempt to fix this issue. Can you recall any additional details about 2db3fde0d02b9a48877147c595289934525882a0? I know very little about X3 implementation and while I can do all the things @saki7 mentioned I worry I might break something else.  
  
While your commit was very small, the change I would attempt would go against its very intention ("Remove forced iterator rollback in rule"). My commit would look like I'm trying to revert your commit.  
  
So before attempting it, I want to understand precisely what X3 implementation should be in regards to iterator modification.  
  
> This is mostly correct.  
  
Why "mostly"? What did I got wrong?  
  
> Parser fail and expectation fail are two distinct mechanisms and behaviors. Parser fail is generally backtrackable and how alternate and repeat parsers work, expectation fail is propagated to the point where handler is installed, if any, responsibility of recovering from it is on the (user's) error handler.  
  
This is very informative to me (I don't remember this information in documentation). Now I think I misunderstood how expectations should be used.  
  
I initially thought that any failed grammar should roll back its iterator, kind of like when `a >> b` fails to parse `b`, it rolls back to a position before `a`.  
  
Now I understand that this applies only to `a >> b`, not when it is `a > b`. When it's `a > b` Spirit intentionally does not create a backtracking point and throws an expectation failure, leaving iterators in the middle of the input that failed to satisfy current grammar. If this is true, then I have been using Spirit incorrectly for a long time.

---

## Comment 20

> Username: Xeverous  
> Created at: 2025-09-26 10:52:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338112137  

@Kojoley I played with your workaround and now I think I understand everything.  
  
- My initial intention was to implement comprehensive error handling in my application. Because at that time the article about error handling did not exist on X3 documentation, I reported it was missing and I copied the code from it as soon as it was available. There is high chance it wasn't polished yet at that point.  
- Because of the above, I misunderstood how error handling should be implemented. The `on_error` and `on_success` should be used only when the library user wants to modify library's behavior or store additional metadata. The default behavior of throwing `x3::expectation_failure` is enough for me.  
- I thought that if I wanted to catch any errors, I had to install an error handler and gather some information in the callback. Now I know I just need to `catch (const x3::expectation_failure<iterator_type>&)` when parse fails.  
- I misunderstood how errors are reported. All parsers that allow backtracking/reuse (`*a`, `a >> b`, `a | b` etc.) implement iterator rollback by themselves. `a > b` intentionally leaves iterators in the failed state, in the middle of something to indicate a fatal failure.  
- I misunderstood what `x3::error_handler_result::fail` meant. Now I know that returning this in error handler effectively changes the meaning of `a > b` into `a >> b` which I don't want.  
  
I removed `error_on_error` handler in my project's code, added `catch` on parse call and now it looks like everything works correctly. Error messages in my project have significantly improved (no more weird multiple backtracking/skipping behaviors, all clear expectation failures) and all of my unit tests pass.  
  
@saki7 This issue is not a bug. Returning `x3::error_handler_result::fail` should result in continuation of the surrounding grammar. I should not use it and instead either return `x3::error_handler_result::rethrow` or just remove `error_or_error` completely and only catch exceptions.

---

## Comment 21

> Username: saki7  
> Created at: 2025-09-26 11:19:58 UTC  
> Updated at: 2025-09-26 12:45:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338205469  

> When it's a > b Spirit intentionally does not create a backtracking point and throws an expectation failure, leaving iterators in the middle of the input that failed to satisfy current grammar.   
  
Correct. The expectation failure is designed to yield a hard error, which should short-circuit every surrounding grammar and immediately return to the caller. The "caller" here is normally the _entry point_, that is the invocation of `x3::parse(...)` or `x3::phrase_parse(...)`.  
  
The mechanism of error handler changes the above semantics. It provides the user with the opportunity to turn the hard error into something else.  
- Returning `error_handler_result::rethrow`: maintain the error to be hard error.  
  - Throwing version: literally rethrows the C++ exception.  
  - Non-throwing version: short-circuits everything and return to the caller.  
- Returning `error_handler_result::fail`: turn the error into a soft error. That means, the surrounding grammar should continue from the old iterator position.  
  
The essential part is the second one, where you're currently seeing a continuation, which is perfectly fine, but the iterator position is somewhat modified (because of the unintended side effect on the failed expectation).   
  
I think, if we implement your suggested behavior, the failure at `"abc = 123 \"abc\n"` should result in a total parse failure, because all components in your grammar definition is wrapped by your error handler, which _correctly_ changes the expectation failure into the soft error.  
  
Nevertheless, Spirit shouldn't falsely yield the broken AST in your example, regardless of what the error handler does. In your program, since you're returning `error_handler_result::fail`, the expectation failure in `string_literal_def` should fall back all the way to `constant_definition_def`, so eventually the parser would try to match `"abc = 123 ..."` against `> x3::eoi`, which itself also "fails" due to the error handler, resulting in total parse failure. This is my understanding of the expected behavior in your example. Please correct me if I'm wrong; even I make some mistakes.  
  
So it could be considered as a bug, but it's more like a design mistake about how the iterator position should be rolled back in Spirit's core.

---

## Comment 22

> Username: Xeverous  
> Created at: 2025-09-26 12:49:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338580933  

> Nevertheless, Spirit shouldn't falsely yield the broken AST in your example, regardless of what the error handler does.  
  
I would say it depends. A potential user (not me, this is not what I wanted) might want to change hard errors into soft errors, to allow further parsing. The error handler would collect additional metadata. The program would know that input is invalid, but could potentially proceed in order to implement graceful degradation and return partial result. This would be hard to implement (requires different logic for each expectation failure) but I can think of some uses cases where some user would want this behavior.  
  
> In your program, since you're returning `error_handler_result::fail`, the expectation failure in `string_literal_def` should fall back all the way to `constant_definition_def`, so eventually the parser would try to match `"abc = 123 ..."` against `x3::eoi`, which itself also "fails" due to the error handler, resulting in total parse failure. This is my understanding of the expected behavior in your example. Please correct me if I'm wrong; even I make some mistakes.  
  
Correct and this is what has been happening in many places when I was working on my project. Many input files with invalid data would repeatedly trigger `error_on_error`, causing application's log to print multiple backtracking points. The logs were not very useful - only the first error was informative. Everything further in the log contained higher-level backtracking points, each with a message like `expected <grammar_name> or x3::eoi`. These messages were confusing and unnecessary. It looked like this (where X > Y > Z):  
  
```  
line X, column 14: expected " here  
line X, column 0: expected constant_definition here  
line Y, column 0: expected definition here  
line Z, column 0: expected variables_section here  
line 0, column 0: expected input here  
line 0, column 0: expected eoi here  
```  
  
> I think, if we implement your suggested behavior, the failure at `"abc = 123 \"abc\n"` should result in a total parse failure, because all components in your grammar definition is wrapped by your error handler, which correctly changes the expectation failure into the soft error.  
  
This is true. If this issue is a bug, it is precisely this - the parse should not succeed. What should X3 precisely do with iterators on expectation failure - I don't know.  
  
___  
  
Even if technically valid, my use of `error_on_error` was not what I wanted. I will adjust my project by removing this handler and just stop on first exception. This entire issue is a result of my misunderstanding how to handle X3 expectation failures. If you look at the example on https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html it still suggests to return `::fail`. I think this is a misleading example. I think the documentation should contain 2 (or more examples):  
  
- the most simple usage: no handlers, just `catch (const x3::expectation_failure<itetator_type>&)` and print an error with data from exception object  
- an example which implements error handler that returns `::fail` and adjusts iterators in order to proceed with the parsing. Application prints that input is invalid but returns partial result.

---

## Comment 23

> Username: saki7  
> Created at: 2025-09-26 12:53:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338595521  

By the way, if you're abandoning this issue and work around by returning `x3::error_handler_result::rethrow`, you should really start using [the non-throwing expectation](https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/non_throwing_expectations.html), because it is exactly the case I intended to optimize. This is somewhat personal, but if people don't use my implementation, it's a very sad thing for a maintainer.

---

## Comment 24

> Username: saki7  
> Created at: 2025-09-26 12:58:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338616472  

> an example which implements error handler that returns ::fail and adjusts iterators in order to proceed with the parsing. Application prints that input is invalid but returns partial result.  
  
No! Remember, I said that exposing internal iterators to library users is not the intended design. The whole intent of parser combinator library is to hide such primitives from the user. The usage of `::fail` is perfectly fine, whereas the current iterator rollback behavior is not intended at all. It should be considered either a bug or a design mistake.

---

## Comment 25

> Username: Xeverous  
> Created at: 2025-09-26 14:09:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3338908649  

> By the way, if you're abandoning this issue and work around by returning `x3::error_handler_result::rethrow`, you should really start using [the non-throwing expectation](https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/non_throwing_expectations.html), because it is exactly the case I intended to optimize. This is somewhat personal, but if people don't use my implementation, it's a very sad thing for a maintainer.  
  
I am going to remove `error_on_error` entirely. I just don't need it. I had it because I thought this was the way errors should be handled because this was the only example in the documentation. Now I know I can remove all this handler code and just use `expectation_failure` object directly, without injecting any `on_error` callbacks into the parser.  
  
And yes, I will use non-throwing expectation. I wasn't aware of this feature before I opened this issue but I like the idea. I prefer to reserve exceptions for really catastrophic errors. Overall, the knowledge I gained from this issue will both significantly simplify my code and fix some unit tests. And my application will have better error messages on top of it.  
  
> the current iterator rollback behavior is not intended at all. It should be considered either a bug or a design mistake.  
  
Ok. I can't really answer whether it is a bug or a design flaw. I claimed it to be a regression bug but I wasn't using the library correctly and did not achieve what I wanted.  
  
I don't require any changes to library behavior. If you think this is a bug and requires some change, the change won't affect my code. I won't be using `on_error` callback.  
  
___  
  
Any thoughts from you on the documentation of the error handling? To me the employee example is misleading, because it could just catch the `expectation_failure` exception (or use non-throwing expectation). In my opinion, there should be a simple example - showcasing how to receive errors without handlers and a complex example that has very good reason to use a handler and return a specific value of `x3::error_handler_result` (with a comment what to do or not to do with iterators).

---

## Comment 26

> Username: saki7  
> Created at: 2025-09-26 15:05:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339092225  

> Overall, the knowledge I gained from this issue will both significantly simplify my code and fix some unit tests. And my application will have better error messages on top of it.  
  
I'm glad to hear that. The reason I initially replied to your report with the very long detail is because I thought it was the good opportunity to increment the number of people who knows the core mechanics. You may still proceed to submit PR for fixing the iterator position on the `::fail` case, that would save the potential users suffering from the unintuitive outcome, but that's up to you, and I won't insist.  
  
> Any thoughts from you on the documentation of the error handling?  
  
If we are to clarify the error handling behavior in the documentation, that inevitably requires us to clarify the expected behavior on the `::fail` case. My stance is that the current iterator position is unintuitive, and should be fixed in a PR.  
  
> In my opinion, there should be a simple example - showcasing how to receive errors without handlers and a complex example that has very good reason to use a handler and return a specific value of x3::error_handler_result (with a comment what to do or not to do with iterators).  
  
This boils down to my previous comment https://github.com/boostorg/spirit/issues/833#issuecomment-3334539747:   
  
> ... does it really make sense to return any value that is _not_ `::fail`? Every other enum values would expose the error handling detail into the surrounding grammar, and it would ultimately alter the meaning of `a > b` in the first place.  
  
In other words, I suspect that the mechanism of error handling in Spirit is not well designed. It's truly a good thing that you're suggesting constructive changes to the documentation, but if we are keeping the deprecated error handling scheme, there's little benefit of improving documentation on top of it.  
  
At the least, I agree that the current documentation is lacking fundamental information.  
  
The "best" choice (for me) is that you'd start using X4 for some portion of your applications which do not require conservative toolchain like Emscripten. I'm looking forward to seeing more contributors for X4, especially for the documentation domain. It should be noted that this cooperation (if it's ever going to happen) would benefit your life too, because you might be using X4 anyway in few more years, even if you choose to stay on X3 for a while (which is perfectly fine for me).

---

## Comment 27

> Username: saki7  
> Created at: 2025-09-26 15:46:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339294398  

Also note that I'm not unthinkingly using C++23, because there was some people who requested using C++26 in X4, which I kindly rejected as it is too bleeding edge for now.  
  
To clarify, I chose C++23 for X4 because, it is already published as the international standard ISO/IEC 14882:2024, and all 3 major compilers (GCC/Clang/MSVC) has baseline support.

---

## Comment 28

> Username: Xeverous  
> Created at: 2025-09-26 16:15:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339425146  

> You may still proceed to submit PR for fixing the iterator position on the `::fail` case, that would save the potential users suffering from the unintuitive outcome, but that's up to you, and I won't insist.  
  
I'm not entirely sure what should be done in such case. My guess is it should do exactly what @Kojoley proposed as a workaround. That is, if the error handler turns a hard error into a soft error, it should also rollback the iterator just like soft errors do.  
  
> In other words, I suspect that the mechanism of error handling in Spirit is not well designed. It's truly a good thing that you're suggesting constructive changes to the documentation, but if we are keeping the deprecated error handling scheme, there's little benefit of improving documentation on top of it.  
  
I think it would be good to inform about the flaws of X3 error handling design in the documentation. 1) it would clarify how to properly use the library given its flaws 2) it would incentivize users to move to X4.  
  
> At the least, I agree that the current documentation is lacking fundamental information.  
  
Not strictly related to this issue, but as a user of the library I thought of numerous additions to the documentation that would improve library user's experience (both X3 and X4):  
  
- More examples. While the documentation covers majority of API, most functionality is explained only in a single example. Some features, when combined with others result in very complex code and while I thought I could figure out every feature, on many occasions when I was reporting a real bug I was informed how my code could be improved (usually removing unnecessary uses of `BOOST_SPIRIT_DECLARE/DEFINE` or some syntax shortcuts).  
- More introductory information about parsers. I know a lot from Computer Science topics but parsing was completely new to me. I had to educate myself what is recursive descent/ascent, what is "greedy parser", AST and more. I don't expect Spirit to teach its users everything from these topics (it's just too much) but it could definitely list key differences between PEGs and other grammars plus provide a list of Computer Science topics one should know before using the library.  
- Common mistakes. I know that a grammar like `eps | x` makes no sense (it will never match `x`) but in more complex cases, it is easy to make a mistake if you aren't aware. I have plenty of `a | b` but it took me some time to realize that if `a = *x`, then I will never match `b`. There was also a case with infinite recursion but I don't remember what the exact problem was - only that recursive grammars must require some delimeter like `{` to nest correctly. I suspect a lot of such cases could be compile-time detected.  
- "Grammar design patterns". While working on my project, I had to make some big decisions how to parse certain things. For example, I could parse `(keyword1 >> a >> b >> c) | (keyword2 >> x >> y)` or `any_keyword >> +arg`. The first grammar is much more strict and results in more parse failures. The second is more loose, fails less often but requires more processing after parse is complete to validate the input. Both approaches have their benefits and drawbacks and it took me a long time to realize this fact. I am still unsure what level of strictness/looseness I should implement. I think it would be a great value if Spirit could help users decide. Probably there is no universal guideline here but the documentation could at least list benefits/drawbacks of both approaches.  
  
> The "best" choice (for me) is that you'd start using X4 for some portion of your applications which do not require conservative toolchain like Emscripten  
  
Sorry, not possible. The very core of the project relies on Spirit. I just checked my newest setup (not even Emscripten) and I have GCC 13.3, which is below required GCC 14.  
  
> you might be using X4 anyway in few more years, even if you choose to stay on X3 for a while (which is perfectly fine for me)  
  
This is definitely possible. But if Spirit continues its "always pushing C++ boundaries" philosophy, does it mean it will be raising its requirements as time goes on? By the time I could want to move from X3 to X4, X4 could raise required C++ standard again - am I right? Or would that be X5 then?

---

## Comment 29

> Username: Xeverous  
> Created at: 2025-09-26 16:23:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339464632  

> Also note that I'm not unthinkingly using C++23  
  
Can you list C++23 (and possibly C++26) features that allow a better implementation? I could definitely start compiling as C++20 and take advantage of concepts and more constexpr but I wonder what newer standards mean for Spirit.

---

## Comment 30

> Username: saki7  
> Created at: 2025-09-26 16:29:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339490483  

> I just checked my newest setup (not even Emscripten) and I have GCC 13.3, which is below required GCC 14.  
  
@Xeverous I just checked GCC 13 on CI but it's technically not feasible because GCC 13 does not implement `deducing this`. Since the version number you suggested is `13.3`, are you using Ubuntu? If so, you can simply use GCC 14 by `sudo apt install g++-14`, I think. Could you try it out?  
  
https://github.com/boostorg/spirit_x4/actions/runs/18043389977/job/51348060190?pr=51#step:16:125  
  
> Can you list C++23 (and possibly C++26) features that allow a better implementation?  
  
Here's the info: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275961209

---

## Comment 31

> Username: Xeverous  
> Created at: 2025-09-26 16:35:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339535941  

> are you using Ubuntu  
  
No, it's Mint, but it is an Ubuntu-derived distro. g++14 is indeed available. I don't remember but perhaps I downgraded to 13.3 because there is a false positive on some `memcpy` stuff that prints very scary warnings (and memcpy is used indirectly inside STL). I might be wrong though, I have multiple setups.  
  
Will have to check Emscripten.

---

## Comment 32

> Username: saki7  
> Created at: 2025-09-26 16:41:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3339560921  

> This is definitely possible. But if Spirit continues its "always pushing C++ boundaries" philosophy, does it mean it will be raising its requirements as time goes on? By the time I could want to move from X3 to X4, X4 could raise required C++ standard again - am I right? Or would that be X5 then?  
  
Your understanding is very much approaching the fundamentals of the modernization. My current policy for bumping the requirement in the future is exactly this:   
  
> To clarify, I chose C++23 for X4 because, it is already published as the international standard ISO/IEC 14882:2024, and all 3 major compilers (GCC/Clang/MSVC) has baseline support.  
  
When C++26 gets published as the international standard, and the 3 major compilers implement the basic support for C++26, then it is time for C++26. But that's not going to happen immediately, even after the condition is met. I would wait for at least few months before I decide the actual bump.  
  
Additionally, I expect the Ubuntu (as the most popular distro) has that GCC version available, which matches the status quo on GCC14/C++23.  
  
This way, the policy shouldn't aggressively bump the version, while we still can push the boundaries of C++.  
  
I know some big companies are rather conservative, they do serious business and they have legitimate reasons for being conservative. That said, Spirit is a brilliant library that is used by many C++ experts. If we don't use the most recent version, who would use it? Who will convince their boss to bump their company's C++ version, when no library actually requires the version? That's the whole point of X4's modernization.

---

## Comment 33

> Username: Xeverous  
> Created at: 2025-09-26 20:09:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340332246  

I have read linked reasons for C++23 features (and the whole thread). I understand you are very energetic about using the newest features. I was the same when I started programming - I insisted on C++11 everywhere possible because it was an absolute game changer of a standard.  
  
I'm not as energetic now, because I have experienced some pain with constant tool updates and reduced stability. Concepts and modules are attractive to me, but I haven't seen any big project moving to modules yet. My current job uses C++20, which is the newest standard I ever had professionally and modules are still not utilized.  
  
> I know some big companies are rather conservative, they do serious business and they have legitimate reasons for being conservative. That said, Spirit is a brilliant library that is used by many C++ experts. If we don't use the most recent version, who would use it? Who will convince their boss to bump their company's C++ version, when no library actually requires the version? That's the whole point of X4's modernization.  
  
You might be surprised but not as many people want the newest standard possible. Some like Vinnie mentioned prefer wider standard compatibility. This also attracts users. Just few years ago I have been writing C89 code (not even C99!) as a part of my job. I am very happy when the company I work for uses C++17 and doesn't ban exceptions or complex templates.  
  
I use Spirit in my private open-source project and I wouldn't be surprised if a particular employer would disallow using such advanced library. Many projects would not allow Boost at all.  
  
___  
  
So continue as you like, you are the maintainer after all. I do keep evolving my own projects and bumping their standards and sometimes also push for newer standards in my job. I just don't push as much as you.

---

## Comment 34

> Username: Xeverous  
> Created at: 2025-09-26 20:57:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340500661  

Just checked Emscripten. Latest prebuilt package toolchain is 4.0.15 and it offers Clang 22.0.0. I'm actually surprised it is that recent.

---

## Comment 35

> Username: Kojoley  
> Created at: 2025-09-26 23:38:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340795160  

> [@Kojoley](https://github.com/Kojoley) in regards to [#833 (comment)](https://github.com/boostorg/spirit/issues/833#issuecomment-3333749234) I could attempt to fix this issue. Can you recall any additional details about [2db3fde](https://github.com/boostorg/spirit/commit/2db3fde0d02b9a48877147c595289934525882a0)? I know very little about X3 implementation and while I can do all the things [@saki7](https://github.com/saki7) mentioned I worry I might break something else.  
>   
> While your commit was very small, the change I would attempt would go against its very intention ("Remove forced iterator rollback in rule"). My commit would look like I'm trying to revert your commit.  
>   
> So before attempting it, I want to understand precisely what X3 implementation should be in regards to iterator modification.  
  
See #704.  
  
> > This is mostly correct.  
>   
> Why "mostly"? What did I got wrong?  
  
I think you've found the answered in your message?  
  
> I initially thought that any failed grammar should roll back its iterator, kind of like when `a >> b` fails to parse `b`, it rolls back to a position before `a`.  
>   
> Now I understand that this applies only to `a >> b`, not when it is `a > b`. When it's `a > b` Spirit intentionally does not create a backtracking point and throws an expectation failure, leaving iterators in the middle of the input that failed to satisfy current grammar. If this is true, then I have been using Spirit incorrectly for a long time.  
  
Sequence parser (`a >> b`) is the wrong place to roll back iterator IMO as I had described in #704, the #719 was going change that.  
  
The reason for not rolling back iterator where it's not required - you are getting error location for free, you just look where iterator points after the failure. The issue your example got into is that kleene star parser `*a` should've rolled back iterator when it turned fail to success, but doesn't do it, and it's what #719 is supposed to fix.  
  
> * Returning `error_handler_result::rethrow`: maintain the error to be hard error.  
>     
>   * Non-throwing version: short-circuits everything and return to the caller.  
  
I don't know what that means. It propagates the error to the next error handler.  
  
> If you look at the example on [boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html](https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html) it still suggests to return `::fail`. I think this is a misleading example. I think the documentation should contain 2 (or more examples):  
>   
> * the most simple usage: no handlers, just `catch (const x3::expectation_failure<itetator_type>&)` and print an error with data from exception object  
> * an example which implements error handler that returns `::fail` and adjusts iterators in order to proceed with the parsing. Application prints that input is invalid but returns partial result.  
  
I think the reason is just that x3 documentation and implementation is an adaptation of Qi's https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/html/spirit/qi/tutorials/mini_xml___error_handling.html which already might have been confusing because it doesn't stress enough that you are attaching error handler to a **rule**, so `::fail` makes that **rule** `Quit and fail`, not the whole parser. It's just when error handler is attached to the top rule of a grammar there will be no chance for the parser to continue, and the documentation shows exactly that scenario (most likely for simplicity) and fails to communicate that.  
  
> By the way, if you're abandoning this issue and work around by returning `x3::error_handler_result::rethrow`, you should really start using [the non-throwing expectation](https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/non_throwing_expectations.html), because it is exactly the case I intended to optimize. This is somewhat personal, but if people don't use my implementation, it's a very sad thing for a maintainer.  
  
I would say it's exactly opposite. You don't want to use exceptions when you are repeatedly triggering them, which happens when you use `::fail`. The only counterexample would be when you repeatedly call parser itself, e.g. when you parser is your network service and you are afraid of getting DoSed with invalid data by a malicious user, but even that case is not as strong because you will ultimately have a point when you will need D/DoS protection on top of your service.

---

## Comment 36

> Username: Xeverous  
> Created at: 2025-09-26 23:55:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340825962  

> See https://github.com/boostorg/spirit/issues/704.  
  
I agree with Joel's list of key design guuarantees. I can not imagine Spirit working differently. If you parse `a >> b | c >> d` and it fails on `b`, it should roll back before `a` and attempt `c` followed by `d`. If `c` fails, roll back before `c` (which should be the same as before `a`) and go into parent grammar for its next alternative.  
  
> I don't know what that means. It propagates the error to the next error handler.  
  
The idea of the non-throwing feature is to allow use of X3 just as if it did not use exceptions to report parse failures. `error_handler_result::fail` is a regular soft error like failure to match an alternative. Exceptions are hard errors, e.g. failing to match `b` on `a > b`. With non-throwing feature enabled, the code should behave exactly as it behaved with exceptions enabled, just have slightly different top-level parse call (now an additional argument to store result in instead of surrounding try-catch block).  
  
> It's just when error handler is attached to the top rule of a grammar there will be no chance for the parser to continue, and the documentation shows exactly that scenario (most likely for simplicity) and fails to communicate that.  
  
I agree. Another reason why current documentation needs improvement.

---

## Comment 37

> Username: Kojoley  
> Created at: 2025-09-27 00:11:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340847576  

> > See [#704](https://github.com/boostorg/spirit/issues/704).  
>   
> I agree with Joel's list of key design guuarantees. I can not imagine Spirit working differently. If you parse `a >> b | c >> d` and it fails on `b`, it should roll back before `a` and attempt `c` followed by `d`. If `c` fails, roll back before `c` (which should be the same as before `a`) and go into parent grammar for its next alternative.  
  
This is offtopic, you should be writing in that issue, otherwise you message is lost in the discussion. But you got things wrong, the responsibility of setting the iterator correctly is on the parser, not on the subparser. Alternate parser should set (roll back) iterator for `c >> d` after `a >> b` has failed. There is no need for sequence parser to roll back after `b` in `a >> b` because it will be done by alternate parser. It's also a consistency issue, because when you do `parse(it, last, a)` the `it` iterator is not guaranteed to be rolled back.  
  
> > I don't know what that means. It propagates the error to the next error handler.  
>   
> The idea of...  
  
I mean, I don't know what is `caller` and what is `everything` in `short-circuits everything and return to the caller`. It can't be call=parser call and everything=grammar because when you have multiple error handlers on the parsing tree they will catch the rethrown exception.

---

## Comment 38

> Username: Kojoley  
> Created at: 2025-09-27 00:33:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3340877849  

> > It's just when error handler is attached to the top rule of a grammar there will be no chance for the parser to continue, and the documentation shows exactly that scenario (most likely for simplicity) and fails to communicate that.  
>   
> I agree. Another reason why current documentation needs improvement.  
  
This is an open source project. Anyone can open a pull request with documentation improvements and documentation changes are generally much easier to be merged than the code changes. The reason documentation is in `it is what it is` state is that it's a subset of knowledge of people who wrote it, and as a expert on the subject one naturally reads it in a different way, filling the gaps or looking at things as obvious while they might not be for people who the documentation is targeted. There is also a time pressure. I was spending a lot of time reading people's issues and finding ways to change the library in a way that it can be used intuitively instead of documenting preferred ways of using the library. Usually when multiple people run into an issue, the issue is not the documentation, generally people don't read documentation until they've already run into the issue.

---

## Comment 39

> Username: saki7  
> Created at: 2025-09-27 02:10:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3341021508  

Reposting my comment to clarify that the PR is actually wanted, and I have certain caveats for this. https://github.com/boostorg/spirit/issues/833#issuecomment-3337339192

---

## Comment 40

> Username: Xeverous  
> Created at: 2025-09-27 13:31:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3341715387  

> By the way, if you're abandoning this issue and work around by returning `x3::error_handler_result::rethrow`, you should really start using [the non-throwing expectation](https://www.boost.org/doc/libs/1_89_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/non_throwing_expectations.html), because it is exactly the case I intended to optimize. This is somewhat personal, but if people don't use my implementation, it's a very sad thing for a maintainer.  
  
@saki7 sorry for bothering you again, but which Boost version implements the non-throwing feature? The documentation does not inform about required version on the article of the feature or on the changelog

---

## Comment 41

> Username: saki7  
> Created at: 2025-09-27 13:41:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3341727658  

> sorry for bothering you again, but which Boost version implements the non-throwing feature? The documentation does not inform about required version on the article of the feature or on the changelog  
  
Check #788. When you look at the commit's page on GitHub, you can see the list of git tags that the commit belongs to. Each Boost version is tagged as the git tag. If you don't know this feature, please refer to the help page of GitHub, or ask ChatGPT. This kind of investigation technique is the basic skill you would need for creating a convincing PR.

---

## Comment 42

> Username: Xeverous  
> Created at: 2025-09-27 15:08:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3341846790  

> When you look at the commit's page on GitHub, you can see the list of git tags that the commit belongs to.  
  
Thanks, I didn't know that.  
  
> or ask ChatGPT  
  
No no no. I'm not asking AI for anything other than generating spam and troll messages.  
  
> This kind of investigation technique is the basic skill you would need for creating a convincing PR.  
  
I know, I know. I could also just view git log and find the commit. I also realized I can edit version number in the link https://www.boost.org/doc/libs/1_88_0/libs/spirit/doc/x3/html/index.html   
  
Do you think the version required should be stated in the documentation? Or does boost have some sort of implicit convention "if documentation mentions it, it is in this version already"?

---

## Comment 43

> Username: saki7  
> Created at: 2025-09-27 15:34:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/833#issuecomment-3341864392  

> Do you think the version required should be stated in the documentation? Or does boost have some sort of implicit convention "if documentation mentions it, it is in this version already"?  
  
Please focus on the points I mentioned in my comment https://github.com/boostorg/spirit/issues/833#issuecomment-3337339192   
  
There's no point of updating the documentation unless the unintuitive iterator position (which is the main topic of issue) is fixed. That's why I said I'm wanting a PR that satisfies the 3 criteria I mentioned. Documentation update can be considered a viable PR only after the code itself is fixed. https://github.com/boostorg/spirit/issues/833#issuecomment-3339092225
