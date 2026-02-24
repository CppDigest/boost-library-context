# #363 - boost::test complains about the type missing operator<< where the operator is present. [Open]

> Username: bartekbl  
> Created at: 2022-11-13 09:50:25 UTC  
> Updated at: 2023-10-14 13:34:22 UTC  
> Url: https://github.com/boostorg/test/issues/363  

I tried to used boost::test with data driven tests, but one of my input samples is `vector<string>`. Boost::test complains about missing operator<< even when I define it.  
  
Self contained example:  
  
```  
#define BOOST_TEST_MODULE test module name  
  
#include <string>  
#include <vector>  
#include <iostream>  
#include <sstream>  
  
using namespace std;  
using namespace std::string_literals;  
  
ostream& operator<<(ostream& out, const vector<string>& vs)  
{  
	for (const auto& s:vs) out << s;  
	return out;  
}  
  
//#include <boost/test/included/unit_test.hpp>  
//#include <boost/test/data/test_case.hpp>  
#include "boost_1_80_0/boost/test/included/unit_test.hpp"  
#include "boost_1_80_0/boost/test/data/test_case.hpp"  
  
namespace bdata = boost::unit_test::data;  
  
BOOST_AUTO_TEST_CASE(test_case_name2)  
{  
	cout << vector<string>{"a"};  
	ostringstream sout;  
	sout << vector<string>{{"ab"},{"cd"}};  
	BOOST_TEST(sout.str() == "abcd");  
}  
  
BOOST_DATA_TEST_CASE(test_case_name, bdata::make({vector<string>{"a"},vector<string>{"a"},vector<string>{"a"}}), value)  
{  
}  
```  
  
fails to build with the error:  
  
`/usr/include/boost/type_traits/detail/has_binary_operator.hpp:54:150: error: no match for ‘operator<<’ (operand types are ‘std::basic_ostream<char>’ and ‘std::vector<std::__cxx11::basic_string<char> >’)`  
  
see attachment for full error log  
[error.txt](https://github.com/boostorg/test/files/9996899/error.txt)  
  
If you comment-out the BOOST_DATA_TEST_CASE it builds and run correctly.

---

## Comment 1

> Username: bartekbl  
> Created at: 2023-10-14 13:34:21 UTC  
> Url: https://github.com/boostorg/test/issues/363#issuecomment-1762917237  

I found a hint on stack overflow that operator<< needs to be defined in the same namespace as the type being printed which in this case means std (because vector comes from std). I think we can close this issue, but this caveat should be mentioned in the docs.
