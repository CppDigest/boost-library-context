# #327 Auto attribute propagation failed when semantic action present [Closed]

> Username: octopus-prime  
> Created at: 2017-12-15 12:18:51 UTC  
> Updated at: 2017-12-23 16:43:04 UTC  
> Closed at: 2017-12-23 01:38:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/327  

See https://svn.boost.org/trac10/ticket/5713  
  
Added suggested test. Passed.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2017-12-15 13:34:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/327#pullrequestreview-83807404  

The PR could be three times shorter + no x3 tests.

📁 test/qi/rule2.cpp

```diff
 163 |+         V val;
 164 |+         R r;
 165 |+         r %= int_ >> int_;
```

> Username: Kojoley  
> Created_at: 2017-12-15 13:26:54 UTC  
> Updated_at: 2017-12-19 12:04:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#discussion_r157200112  

This is redundant https://github.com/boostorg/spirit/blob/95e1432c0c81bb107283ffaedc1d4c15f57ee747/test/qi/sequence.cpp#L120

> Username: octopus-prime  
> Created_at: 2017-12-15 14:01:18 UTC  
> Updated_at: 2017-12-19 12:04:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#discussion_r157206872  

So, what you suggest for this PR? Close?!

> Username: Kojoley  
> Created_at: 2017-12-15 17:55:01 UTC  
> Updated_at: 2017-12-19 12:04:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#discussion_r157261450  

Remove the redundant test case.


---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-12-15 14:07:09 UTC  
> Updated_at: 2017-12-15 14:07:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352013932  

Or should there only 1 test (instead of 3):  
  
```cpp  
        typedef boost::fusion::vector<int, int> V;  
        typedef rule<char const*, V(), space_type> R;  
        V val;  
        R r;  
        r %= (int_ >> int_)[_1];  
        BOOST_TEST((test_attr("1 5", r, val, space) && val == V(1, 5)));  
```  
  
Since this failed in 1.47.0...

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-15 18:14:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352074795  

https://svn.boost.org/trac10/ticket/5713 was fixed with https://github.com/boostorg/spirit/commit/f2b627ed9985bb09518d4e089a31575a32470b01, test case added in https://github.com/boostorg/spirit/commit/4727fc8f495809a0b6f23847b1309cf35a5c3620.

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-12-15 20:08:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352099809  

Are you sure that  
https://github.com/boostorg/spirit/commit/95e1432c0c81bb107283ffaedc1d4c15f57ee747 and/or  
https://github.com/boostorg/spirit/commit/4727fc8f495809a0b6f23847b1309cf35a5c3620 contain the same test?!  
  
They look so different...

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-12-18 11:18:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352398079  

Still unsure what to do here. Just closing this PR?

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-12-19 11:59:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352729531  

Undoing https://github.com/boostorg/spirit/commit/f2b627ed9985bb09518d4e089a31575a32470b01 has no impact on the test cases provided here. https://github.com/boostorg/spirit/commit/f2b627ed9985bb09518d4e089a31575a32470b01 might be NOT the fix and https://github.com/boostorg/spirit/commit/4727fc8f495809a0b6f23847b1309cf35a5c3620 not the right test cases.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2017-12-19 15:25:22 UTC  
> Updated_at: 2017-12-19 16:19:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-352791200  

That's because you are trying to cheat and you don't understand the problem.  
The test case you are adding only catches the bug in c++03, because `boost::fusion::vector<int,int>` and `boost::fusion::vector2<int,int>` are the same types in c++11.  
  
```cpp  
#include <boost/fusion/container/vector.hpp>  
#include <boost/type_traits/is_same.hpp>  
#include <boost/mpl/assert.hpp>  
  
int main()  
{  
    typedef boost::fusion::vector<int,int> a;  
    typedef boost::fusion::vector2<int,int> b;  
    BOOST_MPL_ASSERT(( boost::is_same<a, b> ));  
    return 0;  
}  
```  
  
The right test case should be something like this (if I understand the problem correctly):  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/spirit/include/qi.hpp>  
  
namespace custom_parser   
{   
    BOOST_SPIRIT_TERMINAL(bug)  
}   
  
namespace boost { namespace spirit   
{   
    template <>  
    struct use_terminal<qi::domain, custom_parser::tag::bug>   
      : mpl::true_   
    {};   
}}  
  
namespace custom_parser   
{   
    struct bug_helper  
    {  
        bug_helper() {}  
        bug_helper(int) {}  
        operator int() const { return 123; }  
    };  
  
    struct bug_parser   
      : boost::spirit::qi::primitive_parser<bug_parser>  
    {  
        template <typename Context, typename Iterator>  
        struct attribute  
        {  
            typedef bug_helper type;  
        };  
  
        template <typename Iterator, typename Context  
          , typename Skipper, typename Attribute>  
        bool parse(Iterator& first, Iterator const& last  
          , Context&, Skipper const& skipper, Attribute& attr) const  
        {  
            boost::spirit::qi::skip_over(first, last, skipper);  
            boost::spirit::traits::assign_to(bug_helper(), attr);  
            return true;  
        }  
  
        template <typename Context>  
        boost::spirit::info what(Context&) const  
        {  
            return boost::spirit::info("bug");  
        }  
    };  
}  
  
namespace boost { namespace spirit { namespace qi  
{  
    template <typename Modifiers>  
    struct make_primitive<custom_parser::tag::bug, Modifiers>  
    {  
        typedef custom_parser::bug_parser result_type;  
  
        result_type operator()(unused_type, unused_type) const  
        {  
            return result_type();  
        }  
    };  
}}}  
  
int main()  
{  
    using namespace boost::spirit::qi;  
    rule<char const*, int() > r;  
    r %= custom_parser::bug[_1];  
    int val;  
    char const* const it = "";  
    BOOST_TEST((parse(it, it, r, val)));  
    BOOST_TEST((val == 123));  
    return boost::report_errors();  
}  
```  
It is similar check to what have been done in `regression_binary_action.cpp`, just without a custom parser (because it relies on binary parser internal structure).  
If @hkaiser agree with me, I think `regression_binary_action.cpp` need to be rewritten with a custom parser or static assert on binary parser attribute type added and something similar need to be added to x3 tests suite.

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2017-12-23 01:38:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-353699416  

https://svn.boost.org/trac10/ticket/5713 is closed - so is this PR.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2017-12-23 16:43:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/327#issuecomment-353736062  

A shorter test case:  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/spirit/include/qi.hpp>  
  
struct custom_int  
{  
    custom_int() {}  
    custom_int(int) {}  
    operator int() const { return 123; }  
};  
  
  
int main()  
{  
    using namespace boost::spirit::qi;  
    rule<char const*, int() > r;  
    r %= attr(custom_int())[_1];  
    int val;  
    char const* const it = "";  
    BOOST_TEST((parse(it, it, r, val)));  
    BOOST_TEST((val == 123));  
    return boost::report_errors();  
}  
```

---
