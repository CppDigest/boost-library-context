# #248 - Optional rule in sequence with vector rule drops the optional rule's attribute [Closed]

> Username: knowikow  
> Created at: 2025-07-10 10:08:20 UTC  
> Updated at: 2025-07-27 22:53:46 UTC  
> Closed at: 2025-07-27 22:53:06 UTC  
> Url: https://github.com/boostorg/parser/issues/248  

- Boost version: 1.88.0  
- Environment: MS Visual Studio 2022  
  
Here is a test that shows a possible problem that might be related to #125 -- I also checked the test from #125, and it worked for me:  
  
```  
#include <boost/parser/parser.hpp>  
#include <boost/test/included/unit_test.hpp>  
#include <boost/utility/identity_type.hpp>  
  
#include <tuple>  
#include <vector>  
  
namespace bp = boost::parser;  
  
static constexpr bp::rule<struct symbol, int>               symbol  = "//";  
static constexpr bp::rule<struct vector, std::vector<int>>  list    = "<int>(,<int>)*";  
static constexpr bp::rule<struct working, std::vector<int>> working = "working";  
static constexpr bp::rule<struct failing, std::vector<int>> failing = "failing";  
  
static auto const symbol_def = bp::symbols<int>{  
    {"//",  0}  
};  
static constexpr auto list_def    = bp::int_ % ',';  
static constexpr auto working_def = -symbol >> (bp::int_ % ',');  
static constexpr auto failing_def = -symbol >> list;  
  
BOOST_PARSER_DEFINE_RULES(  
    symbol,  
    list,  
    working,  
    failing  
);  
  
template<typename> constexpr int  rule = 0;  
template<>         constexpr auto rule<decltype(working)> = working;  
template<>         constexpr auto rule<decltype(failing)> = failing;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(  
    issue,  
    R,  
    BOOST_IDENTITY_TYPE((std::tuple<decltype(working), decltype(failing)>))  
)  
{  
    auto const result   = bp::parse("//1,2,3", rule<R>, bp::ws, bp::trace::on);  
    auto const expected = std::vector<int>{0, 1, 2, 3};  
    BOOST_REQUIRE(result.has_value());  
    BOOST_CHECK_EQUAL_COLLECTIONS(result->begin(), result->end(), expected.begin(), expected.end());  
}  
```  
We have two top-level rules, both are parsing a comma-separated list of integers into a vector, with an optional symbol at the start that prepends 0:  
  
- _working_ includes the rule for the comma-separated list directly: `-symbol >> (bp::int_ % ',');`  
- _failing_ uses a separate rule named _list_: `-symbol >> list`  
  
Referring to the tutorial section [Sequence parser attribute rules](https://www.boost.org/doc/libs/boost_1_88_0/doc/html/boost_parser/tutorial.html#boost_parser.tutorial.attribute_generation.sequence_parser_attribute_rules)  
  
>  optional<T> >> C<T> -> C<T>   
  
my expectation was that both rules, when used on the string `"//1,2,3"`, would produce a vector `{0, 1, 2, 3}`, but only _working_ does. The _failing_ rule produces the vector `{1, 2, 3}`, i. e., it drops the attribute from the optional symbol.  
The output for the failing test is  
```  
check { result->begin(), result->end() } == { expected.begin(), expected.end() } has failed.   
Mismatch at position 0: 1 != 0  
Mismatch at position 1: 2 != 1  
Mismatch at position 2: 3 != 2  
Collections size mismatch: 3 != 4  
```  
and the debug trace output is  
```  
[begin failing; input="//1,2,3"]  
  [begin -/ >> <int>(/<int>)*; input="//1,2,3"]  
    [begin -/; input="//1,2,3"]  
      [begin /; input="//1,2,3"]  
        [begin symbols<int>; input="//1,2,3"]  
          matched "//"  
          attribute: 0  
        [end symbols<int>; input="1,2,3"]  
        matched "//"  
        attribute: 0  
      [end /; input="1,2,3"]  
      matched "//"  
      attribute: 0  
    [end -/; input="1,2,3"]  
    [begin <int>(/<int>)*; input="1,2,3"]  
      [begin int_ % ','; input="1,2,3"]  
        [begin int_; input="1,2,3"]  
          matched "1"  
          attribute: 1  
        [end int_; input=",2,3"]  
        [begin ','; input=",2,3"]  
          [begin char_(','); input=",2,3"]  
            matched ","  
            attribute: ','  
          [end char_(','); input="2,3"]  
          matched ","  
        [end ','; input="2,3"]  
        [begin int_; input="2,3"]  
          matched "2"  
          attribute: 2  
        [end int_; input=",3"]  
        [begin ','; input=",3"]  
          [begin char_(','); input=",3"]  
            matched ","  
            attribute: ','  
          [end char_(','); input="3"]  
          matched ","  
        [end ','; input="3"]  
        [begin int_; input="3"]  
          matched "3"  
          attribute: 3  
        [end int_; input=""]  
        [begin ','; input=""]  
          [begin char_(','); input=""]  
            no match  
          [end char_(','); input=""]  
          no match  
        [end ','; input=""]  
        matched "1,2,3"  
        attribute: <<unprintable-value>>  
      [end int_ % ','; input=""]  
      matched "1,2,3"  
      attribute: <<unprintable-value>>  
    [end <int>(/<int>)*; input=""]  
    matched "//1,2,3"  
    attribute: (<<unprintable-value>>)  
  [end -/ >> <int>(/<int>)*; input=""]  
  matched "//1,2,3"  
  attribute: <<unprintable-value>>  
[end failing; input=""]  
--------------------  
parse succeeded  
--------------------  
```  
  
Did I miss something in the definition of my _list_ rule?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-27 22:53:46 UTC  
> Url: https://github.com/boostorg/parser/issues/248#issuecomment-3124774537  

Thanks very much for pointing this out!  This is not something you missed.  It was a bug in rule_parser that was blowing away the partial result from the symbol parser.
