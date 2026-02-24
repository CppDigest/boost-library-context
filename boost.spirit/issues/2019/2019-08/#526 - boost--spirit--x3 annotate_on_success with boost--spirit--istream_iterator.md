# #526 - boost::spirit::x3 annotate_on_success with boost::spirit::istream_iterator [Closed]

> Username: maximiliank  
> Created at: 2019-08-23 12:48:09 UTC  
> Updated at: 2021-02-26 23:43:10 UTC  
> Closed at: 2021-02-26 23:43:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/526  

When using boost spirit x3 with an error handler and rule derived from annotate_on_success and the underlying iterator is an boost::spirit::istream_iterator it does not compile due to an incomplete type error:  
``error: invalid application of 'sizeof' to an incomplete type 'boost::spirit::x3::error_handler_tag'``  
  
Here is an example code to reproduce the error:  
```  
#include <iostream>  
#include <fstream>  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/utility/annotate_on_success.hpp>  
#include <boost/spirit/home/x3/support/utility/error_reporting.hpp>  
#include <boost/spirit/include/support_istream_iterator.hpp>  
#include <boost/fusion/include/io.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
namespace Parser {  
	struct Rule_t;  
	const auto rule = x3::rule<Rule_t> {"testrule"} = x3::int_;  
	struct Rule_t : x3::annotate_on_success {};  
}  
struct ParseString  
{  
	using Iterator = std::string::const_iterator;  
	explicit ParseString(const std::string& str)  
			: begin_(str.cbegin()), end_(str.cend())  
	{}  
  
	Iterator begin()  
	{ return begin_; }  
  
	Iterator end()  
	{ return end_; }  
  
private:  
	Iterator begin_;  
	Iterator end_;  
};  
struct ParseFile  
{  
	using Iterator = boost::spirit::istream_iterator;  
	explicit ParseFile(const std::string& str)  
			: instream_(str), begin_(instream_)  
	{  
		instream_.unsetf(std::ios::skipws);  
	}  
  
	Iterator begin()  
	{ return begin_; }  
  
	Iterator end()  
	{ return end_; }  
  
private:  
	std::ifstream instream_;  
	Iterator begin_;  
	Iterator end_;  
};  
  
template<bool UseFileIterator>  
bool test_parse(const std::string& str)  
{  
	using StreamType = std::conditional_t<UseFileIterator, ParseFile, ParseString>;  
	using IteratorType = typename StreamType::Iterator;  
	using ErrorHandlerType = x3::error_handler<IteratorType>;  
  
	StreamType stream(str);  
	auto begin = stream.begin();  
	const auto end = stream.end();  
	ErrorHandlerType errorHandler(begin, end, std::cerr);  
  
	const auto grammar = x3::with<x3::error_handler_tag>(std::ref(errorHandler))[Parser::rule];  
  
	return x3::parse(begin, end, grammar);  
}  
  
int main()  
{  
	std::cout << test_parse<false>("teststring") << std::endl;  
        // The following line yields an error  
	std::cout << test_parse<true>("test.csv") << std::endl;  
}  
```  
You can find the example on [godbolt](https://godbolt.org/z/yKXzlg).

---

## Comment 1

> Username: maximiliank  
> Created at: 2021-02-26 23:32:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/526#issuecomment-786949572  

Any ideas what the problem could be?
