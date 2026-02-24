# #680 - spirit X3 repeat with a variable [Closed]

> Username: matthijs  
> Created at: 2021-05-22 19:41:06 UTC  
> Updated at: 2025-05-10 00:25:33 UTC  
> Closed at: 2025-05-10 00:25:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/680  

Hi,  
  
I am trying to parse something like: 1 byte (length) followed by N bytes (where N is the length from the first byte). I've come up with the following minimal example:  
```c++  
#include <iostream>  
#include <string>  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/binary.hpp>  
  
using namespace boost::spirit::x3;  
  
int main(int argc, char** argv) {  
  
  // input defined as:  
  // - refid length (1 byte)  
  // - refid (length determined by 'refid length above')  
  const char *input = "\x06" // refid length (1 byte)  
    "\x61\x42\x63\x44\x65\x66" // refid (6 characters: aBcDef)  
    ;  
  
  // begin / end iterator  
  auto it = input;  
  auto it_end = input+7;  
  
  uint8_t refid_size;  
  auto refid_size_f = [&refid_size](auto& ctx) { refid_size = _attr(ctx); };  
  
  std::string result;  
  bool r = parse(it, it_end,  
    byte_[refid_size_f]  
    > repeat(refid_size)[char_], // when replacing refid_size with 6, it give the correct result  
    result);  
  if (!r)  
  {  
    std::cerr << "failed to parse...\n";  
  }  
  if (it != it_end)  
  {  
    std::cerr << "it != it_end\n";  
  }  
  
  std::cout << "msg: " << result << "\n";  
  return 0;  
}  
```  
On stackoverflow I found a way to achieve [this](https://stackoverflow.com/questions/33624149/boost-spirit-x3-cannot-compile-repeat-directive-with-variable-factor), but I guess there should be a nicer way to do this kind of parsing.  
  
Another approach (from someone on Slack) is to do it in two steps:  
```c++  
auto make_tail_parser = [](auto len) {  
  return repeat(len)[char_];  
};  
bool r = parse(it, it_end, byte_[refid_size_f]);  
r = parse(it, it_end, make_tail_parser(refid_size), result);  
```  
  
It would be nice if X3 could support something that was possible with qi:  
```c++  
byte_[_a = _1] > repeat(_a)[char_]  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-22 20:30:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/680#issuecomment-846459850  

It could be done in X3 with semantic actions + context variables:  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <vector>  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
  
    auto s = "5: 1 2 3 4 5", e = s + std::strlen(s);  
    std::vector<int> v;  
    class len_tag;  
    if (phrase_parse(s, e, x3::with<len_tag>(0)[x3::uint_[([](auto& ctx) { x3::get<len_tag>(ctx) = x3::_attr(ctx); })] >> ':' >> *(x3::eps[([](auto& ctx) { x3::_pass(ctx) = x3::get<len_tag>(ctx)-- > 0; })] >> x3::int_)], x3::space, v)) {  
        std::cout << "Result:\n";  
        for (auto x : v)  
            std::cout << x << '\n';  
    }  
    else  
        std::cout << "Failed!\n";  
}  
```  
  
---  
  
An implementation of (a previously rejected) `repeat(byte_)[char_]` solution could be found here https://stackoverflow.com/a/54351229/3621421

---

## Comment 2

> Username: matthijs  
> Created at: 2021-05-23 13:56:11 UTC  
> Updated at: 2021-05-23 13:56:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/680#issuecomment-846567213  

Thanks a lot, I am able to correctly parse the input now. I have one question though, this belongs to a somewhat bigger grammar with seperate rules, is there a particular reason why I need to add a semantic action to push the character to the std::string container (see example):  
```c++  
#include <string>  
  
#define BOOST_SPIRIT_X3_DEBUG  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/binary.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
/// GRAMMAR  
struct refid_length_tag;  
x3::rule<class refid, std::string> const refid = "refid";  
auto const refid_def = x3::with<refid_length_tag>(0)[x3::byte_[([](auto& ctx) { x3::get<refid_length_tag>(ctx) = _attr(ctx); })]  
	>> *(x3::eps[([](auto& ctx) { x3::_pass(ctx) = x3::get<refid_length_tag>(ctx)-- > 0; })] >> x3::char_[([](auto& ctx) { _val(ctx) += _attr(ctx); })])  
	];  
  
BOOST_SPIRIT_DEFINE(refid)  
/// END GRAMMAR  
  
int main(int argc, char** argv) {  
  
	// input  
	const char *input = "\x06" // refid length (1 byte)  
		"\x61\x42\x63\x44\x65\x66" // refid (6 characters: aBcDef)  
		;  
  
	// begin / end iterator  
	auto it = input;  
	auto it_end = input+7;  
  
	std::string msg;  
	bool result = parse(it, it_end, refid, msg);  
	if (!result)  
	{  
		std::cout << "failed to parse...\n";  
	}  
	if (it != it_end)  
	{  
		std::cout << "it != it_end\n";  
	}  
  
	std::cout << "msg: " << msg << std::endl;  
	return 0;  
}  
```  
When I leave out the following semantic action:  
```c++  
x3::char_[([](auto& ctx) { _val(ctx) += _attr(ctx); })]  
// becomes  
x3::char_  
```  
it will not populate the std::string for this rule.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-05-23 15:08:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/680#issuecomment-846577976  

Semantic actions on a rule definition inhibit automatic attribute propagation. Define your rule placeholder as `x3::rule<class refid, std::string, true> const refid` to force attribute propagation.

---

## Comment 4

> Username: matthijs  
> Created at: 2021-05-23 17:29:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/680#issuecomment-846597579  

Ah thanks!
