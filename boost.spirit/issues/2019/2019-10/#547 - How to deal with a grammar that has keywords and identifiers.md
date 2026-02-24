# #547 - How to deal with a grammar that has keywords and identifiers? [Closed]

> Username: Xeverous  
> Created at: 2019-10-17 19:35:52 UTC  
> Updated at: 2019-10-18 21:25:02 UTC  
> Closed at: 2019-10-18 21:19:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/547  

Note: this is more of a LL design question, not X3 usage question.  
  
___  
  
I have a grammar that has a subgrammars `identifier = value_expr` and `keyword value_expr` (only whitespace between here). `value_expr` can be any expression that denotes a value (a function call, a literal, an identifier). The problem is error handling in case of specific typo errors.  
  
___  
  
Such example works correctly (`Identified`, `Corrupted`, `true`, `false` are a keywords):  
  
```  
x = true  
Identified false  
Corrupted x  
```  
  
Such example parses correctly and then triggers error in the compiler that walks the Spirit-generated AST and finds identifiers that were not defined.  
  
```  
x = true  
Identified false  
Corrupted y  
```  
  
But this example fails to parse:  
  
```  
x = true  
Identified falsee  
Corrupted x  
```  
  
It is because the parser consumes `Identified false` text and then leaves `e` for any grammar that goes next. This causes very unclear syntax errors, like "expected eoi" instead of "expected condition"/"expected value_expr".  
  
**I would like to edit the keywords or identifier grammar to make the parser read entire "falsee"**. Basically do not treat any string as a keyword until it has hit some skip parser elements. Consume as many consecutive letters valid for an identifier as possible. Is there any recommended way of doing this?

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-10-17 23:54:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543415277  

To make your problem clear, please provide a grammar and a desired output. It is hard to suggest you anything without understanding your actual problem and things you have already tried.

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-10-18 18:49:32 UTC  
> Updated at: 2019-10-18 18:52:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543885009  

Ok, so here is a "minimal" example (pretty minimal considering my actual project is 10k lines and this is only 150).  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/spirit/home/x3/support/utility/lambda_visitor.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
#include <iostream>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
	struct identifier  
	{  
		identifier& operator=(std::string str)  
		{  
			value = std::move(str);  
			return *this;  
		}  
  
		std::string value;  
	};  
  
	struct value_expr : x3::variant<bool, identifier>  
	{  
		using base_type::base_type;  
		using base_type::operator=;  
	};  
  
	enum class condition_type { corrupted, identified };  
  
	struct condition  
	{  
		condition_type type;  
		value_expr value;  
	};  
}  
  
BOOST_FUSION_ADAPT_STRUCT(ast::condition, type, value)  
  
namespace parser  
{  
	namespace x3 = boost::spirit::x3;  
	namespace ascii = boost::spirit::x3::ascii;  
  
	struct booleans_ : x3::symbols<bool>  
	{  
		booleans_()  
		{  
			add  
				("true", true)  
				("false", false)  
			;  
		}  
  
	};  
	const booleans_ booleans;  
  
	struct condition_type_exprs_ : x3::symbols<ast::condition_type>  
	{  
		condition_type_exprs_()  
		{  
			add  
				("Identified", ast::condition_type::identified)  
				("Corrupted",  ast::condition_type::corrupted)  
			;  
		}  
  
	};  
	const condition_type_exprs_ condition_type_exprs;  
  
	// identifier has an extra intermediate rule because Spirit for (?) it's container detection reasons  
	// can not match identifier grammar with a struct that contains only std::string (compiles only with std::string directly)  
	// to workaround, we just add 1 more step with the same grammar  
	// https://stackoverflow.com/questions/18166958  
	x3::rule<struct identifier_impl, std::string> const identifier_impl = "identifier";  
	auto const identifier_impl_def = x3::lexeme[(x3::alpha | x3::char_('_')) > *(x3::alnum | x3::char_('_'))];  
  
	x3::rule<struct identifier, ast::identifier> const identifier = identifier_impl.name;  
	auto const identifier_def = identifier_impl;  
  
	x3::rule<struct value_expr, ast::value_expr> const value_expr = "value expression";  
	auto const value_expr_def = booleans | identifier;  
  
	x3::rule<struct condition, ast::condition> const condition = "condition";  
	auto const condition_def = condition_type_exprs > value_expr;  
  
	x3::rule<struct grammar, std::vector<ast::condition>> const grammar = "conditions";  
	auto const grammar_def = *condition > x3::eoi;  
  
	BOOST_SPIRIT_DEFINE(identifier_impl, identifier, value_expr, condition, grammar)  
}  
  
void print_condition(ast::condition const& c)  
{  
	if (c.type == ast::condition_type::identified)  
		std::cout << "Identified ";  
	else  
		std::cout << "Corrupted ";  
  
	c.value.apply_visitor(x3::make_lambda_visitor<void>(  
		[](bool b) {  
			std::cout << "literal(" << std::boolalpha << b << ")\n";  
		},  
		[](ast::identifier const& id) {  
			std::cout << "identifier(" << id.value << ")\n";  
		}  
	));  
}  
  
template <typename It>  
void print_ast(It first, It last)  
{  
	for (auto it = first; it != last; ++it)  
		print_condition(*it);  
}  
  
void run(std::string_view input)  
{  
	std::cout << "==== input ====\n" << input << "\n==== parsing ====\n";  
  
	using iterator_type = std::string_view::const_iterator;  
  
	iterator_type first = input.cbegin();  
	iterator_type const last = input.cend();  
	std::vector<ast::condition> conditions;  
	try {  
		bool r = phrase_parse(first, last, parser::grammar, x3::space, conditions);  
		if (!r) {  
			std::cout << "parse failed\n";  
		}  
	}  
	catch (x3::expectation_failure<iterator_type> const& ex) {  
		std::cout << ex.what() << ": expected " << ex.which() << " on position: " << ex.where() - input.begin() << "\n";  
	}  
  
	if (first != last) {  
		std::cout << "not whole input was matched\n";  
	}  
  
	print_ast(conditions.cbegin(), conditions.cend());  
}  
  
int main()  
{  
	run("Corrupted true\nIdentified false\nCorrupted xyz");  
	run("Corrupted foo\nIdentified bar\nCorrupted xyz");  
	run("Corrupted falsee\nIdentified true\nCorrupted xxx");  
}  
```  
  
```  
==== input ====  
Corrupted true  
Identified false  
Corrupted xyz  
==== parsing ====  
Corrupted literal(true)  
Identified literal(false)  
Corrupted identifier(xyz)  
==== input ====  
Corrupted foo  
Identified bar  
Corrupted xyz  
==== parsing ====  
Corrupted identifier(foo)  
Identified identifier(bar)  
Corrupted identifier(xyz)  
==== input ====  
Corrupted falsee  
Identified true  
Corrupted xxx  
==== parsing ====  
boost::spirit::x3::expectation_failure: expectedeoi on position: 15  
not whole input was matched  
Corrupted literal(false)  
```  
  
The problem can be seen in the last input. The line `Corrupted falsee` is parsed as `condition_type_exprs > booleans`. The parser consumes `Corrupted false` and leaves `e`. I would like it to consume `falsee` as an identifier.  
  
So the problem is basically this:  
  
- grammar `identifier | keyword` - you are never going to enter keyword alternative  
- grammar `keyword | identifier` - you have a problem when first few characters of an identifier are a keyword (the token is parsed as keyword, not as an identifier)  
  
It looks like I need to do some look-ahead in one of these but I'm not sure what. Lookahead for skip parser (which definitely ends the current grammar)? Is this even possible? Or, should I define identifier differently?  
  
The identifier grammar looks good to me - it consumes as many characters as possible. Keywords are a problem because they can match identifier-substrings. But any workaround looks messy with how `x3::symbols` are supposed to be used.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-10-18 20:40:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543933982  

I see what you want, ambiguity resolution via greediest (longest) match. Unfortunately it is not how PEG parser resolves ambiguities (it just matches the first alternative, because of simplicity and performance reason). There is no perfect solution, if you want readability you can use `(booleans - identifier) | identifier`, if you want performance it will be `"true" >> !&+(x3::alnum | '_') >> x3::attr(true) | "false" >> !&+(x3::alnum | '_') >> x3::attr(false) | identifier`.

---

## Comment 4

> Username: Xeverous  
> Created at: 2019-10-18 20:47:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543937655  

Is the `+` in `!&+(x3::alnum | '_')` actually necessary?

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-10-18 20:48:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543938133  

You are right, it is not needed :)

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-10-18 20:51:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543939667  

And I forgot to make them lexemes `x3::lexeme["true" >> !&(x3::alnum | '_')] >> x3::attr(true) | x3::lexeme["false" >> !&(x3::alnum | '_')] >> x3::attr(false) | identifier`;

---

## Comment 7

> Username: Xeverous  
> Created at: 2019-10-18 20:58:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543942886  

So I have 2 choices:  
  
- parse identifier first and then check if its a keyword (although anything that comes to my mind - eg post-poning this to the compiler OR using semantic actions look like a very costly and potentially maintenance-heavy solution)  
- parse keyword first, each keyword should be followed by a grammar like `not_an_identifier = !&(x3::alnum | '_')` that performs look-ahead to ensure its not an identifier-partial-match  
  
I'm not sure but I think I could simply change any `symbols_instance > foobar` to `symbols_instance > not_an_identifier > foobar`.  
  
I don't get the need of lexemes. If there is a space after `true` that's definitely a keyword. And none of my keywords contain whitespace.

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-10-18 21:09:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543947469  

> I don't get the need of lexemes. If there is a space after true that's definitely a keyword. And none of my keywords contain whitespace.  
  
Are you sure? It will match `false` by `identifier` in `false e` then, what might be not what you want.

---

## Comment 9

> Username: Xeverous  
> Created at: 2019-10-18 21:13:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543949302  

How could it match `false` by `identifier` grammar in `false e` if I always look for keywords first?

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-10-18 21:19:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543952256  

Because of skipper. Let's not continue this. I hope I answered you question, even though this is not a support board, so I am closing the issue.

---

## Comment 11

> Username: Xeverous  
> Created at: 2019-10-18 21:25:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/547#issuecomment-543955224  

Ok, I get it, thanks for the help.
