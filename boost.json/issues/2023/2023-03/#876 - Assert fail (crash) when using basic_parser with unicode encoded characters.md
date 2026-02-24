# #876 - Assert fail (crash) when using basic_parser with unicode encoded characters [Closed]

> Username: guboulan  
> Created at: 2023-03-24 17:10:39 UTC  
> Updated at: 2023-03-27 10:00:10 UTC  
> Closed at: 2023-03-27 10:00:10 UTC  
> Url: https://github.com/boostorg/json/issues/876  

### Version of Boost  
1.80  
  
### Steps necessary to reproduce the problem  
  
```  
#define BOOST_JSON_STACK_BUFFER_SIZE 32  
#include "boost/json/basic_parser_impl.hpp"  
#define BUFFER_SIZE BOOST_JSON_STACK_BUFFER_SIZE      
  
struct TestParser  
{  
  TestParser() {};  
  // basic parser dummy implementation  
  bool on_document_begin(error_code& ec) { return true; }  
  // [...]  
};  
  
void testBasicParser() {  
  std::string test = R"({"test":"test1tes2test3test\u2019test7test8test9test1test2test3test4test5test6test7test8})";  
    
  boost::json::parse_options options;  
  boost::json::basic_parser<TestParser> parser(options);  
  boost::json::error_code ec;  
  std::stringstream input(test);  
  char buffer[BUFFER_SIZE];  
  while (!input.eof()) {  
    input.read(buffer, BUFFER_SIZE);  
    parser.write_some(true, buffer, BUFFER_SIZE, ec);  
  }  
}  
```  
  
The assert is hit by the temp.push_back() in basic_parser<Handler>::  
parse_escaped (https://github.com/boostorg/json/blob/develop/include/boost/json/basic_parser_impl.hpp#L1680).  
It occurs because the encoded character is split into the two buffer (the first buffer end precisely in the middle of the unicode encoded character). The buffer start with the full unicode character (3 character) but the input still contains 30 characters while the temp buffer only allows 32.  
  
I reproed the issue initially with the default BOOST_JSON_STACK_BUFFER_SIZE on iOS seldomly while processing some large json payloads.  
  
### All relevant compiler information  
Build with BOOST_ALL_NO_LIB on Windows. Also occurs on iOS with the default BOOST_JSON_STACK_BUFFER_SIZE (256) value.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-03-24 20:29:56 UTC  
> Url: https://github.com/boostorg/json/issues/876#issuecomment-1483372452  

@guboulan can you check if #877 fixes your problem?

---

## Comment 2

> Username: guboulan  
> Created at: 2023-03-27 09:07:57 UTC  
> Url: https://github.com/boostorg/json/issues/876#issuecomment-1484779292  

@grisumbras It does fix my issue. Thanks!
