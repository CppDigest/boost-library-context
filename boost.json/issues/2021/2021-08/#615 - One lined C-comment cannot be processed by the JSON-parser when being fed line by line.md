# #615 - One lined C-comment cannot be processed by the JSON-parser when being fed line by line [Closed]

> Username: dpronin  
> Created at: 2021-08-25 06:19:14 UTC  
> Updated at: 2022-10-22 16:29:43 UTC  
> Closed at: 2022-10-22 16:29:43 UTC  
> Url: https://github.com/boostorg/json/issues/615  

### Version of Boost  
  
Boost version: 1.76.0  
  
### Steps necessary to reproduce the problem  
  
```c++  
#include <iostream>  
#include <sstream>  
#include <string>  
  
#include <boost/json.hpp>  
  
namespace {  
  
inline auto parse_line_by_line(std::istream &is_json) {  
  using namespace boost::json;  
  parse_options opt{.allow_comments = true,  
                    .allow_trailing_commas = true,  
                    .allow_invalid_utf8 = true};  
  stream_parser p{storage_ptr(), opt};  
  std::string line;  
  while (std::getline(is_json, line))  
    p.write(line);  
  p.finish();  
  return p.release();  
}  
  
inline auto parse(const char *json) {  
  using namespace boost::json;  
  parse_options opt{.allow_comments = true,  
                    .allow_trailing_commas = true,  
                    .allow_invalid_utf8 = true};  
  return parse(json, storage_ptr(), opt);  
}  
  
void validate(const char *json, const char *style) {  
  std::cout << "parsing Json (style " << style << "): " << std::endl;  
  std::cout << json << std::endl;  
  std::istringstream iss{json};  
  
  try {  
    auto jvalue = parse(json);  
  } catch (boost::system::system_error const &ex) {  
    std::cerr << "parsing just JSON-string, error: " << ex.what() << '\n';  
  }  
  
  try {  
    auto jvalue = parse_line_by_line(iss);  
  } catch (boost::system::system_error const &ex) {  
    std::cerr << "parsing JSON-string stream line by line, error: " << ex.what()  
              << '\n';  
  }  
}  
  
} // anonymous namespace  
  
int main(int argc, char const *argv[]) {  
  constexpr char const kJson_1lined_style_comment[] = R"json(  
{  
    // this is my comment  
    "foo": "bar",  
    "baz": "boo"  
}  
)json";  
  constexpr char const kJson_multilined_style_comment[] = R"json(  
{  
    /* this is my comment */  
    "foo": "bar",  
    "baz": "boo"  
}  
)json";  
  
  validate(kJson_1lined_style_comment, "one lined comment");  
  std::cout << "----------------------" << std::endl;  
  validate(kJson_multilined_style_comment, "multilined comment");  
  
  return 0;  
}  
```  
  
### All relevant compiler information  
  
```  
clang version 12.0.1  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/lib/llvm/12/bin  
```  
  
### Clarification  
  
If you compile and run the application I have posted above herein you will find the boost::json library just clearly parses json as a plain string whilst there are exceptions when parsing as stream line by line  
The issue is that one lined comments in a JSON source make the JSON-parser based on line by line digesting throw an exception about incomplete JSON where as multiline comments do not  
  
Could someone sort it out and help me understand if this is a bug or definitely expected behaviour?  
  
Thank you in advance

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-08-25 06:27:03 UTC  
> Url: https://github.com/boostorg/json/issues/615#issuecomment-905220976  

`std::getline` essentially removes whitespace at the end of the line. Please check for that.

---

## Comment 2

> Username: dpronin  
> Created at: 2021-08-25 06:44:18 UTC  
> Updated at: 2021-08-25 06:52:10 UTC  
> Url: https://github.com/boostorg/json/issues/615#issuecomment-905228842  

@grisumbras, thank you. You led me to the right thought that I should have fed the stream_parser with "\n" because, as you might have meant, `std::getline` eliminates `\n' at the end of a string leaving JSON-parser without a crucial knowledge whether a line has ended or not  
I fixed my code by adding one more character '\n' at the end of a line read from the stream:  
```c++  
inline auto parse_line_by_line(std::istream &is_json) {  
  using namespace boost::json;  
  parse_options opt{.allow_comments = true,  
                    .allow_trailing_commas = true,  
                    .allow_invalid_utf8 = true};  
  stream_parser p{storage_ptr(), opt};  
  std::string line;  
  while (std::getline(is_json, line)) {  
    line.push_back('\n');  
    p.write(line);  
  }  
  p.finish();  
  return p.release();  
}  
```  
This function now works correctly from JSON-parser's point of view  
  
But I would like to request someone for the docs to be updated because the [example](https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing.streaming_parser) with stream_parser given is not comprehensive and may lead to misunderstanding like I was confused

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-08-25 06:48:46 UTC  
> Url: https://github.com/boostorg/json/issues/615#issuecomment-905230924  

Good idea, this is the second time we've had someone make this mistake.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-08-25 13:23:20 UTC  
> Url: https://github.com/boostorg/json/issues/615#issuecomment-905498040  

> this is the second time we've had someone make this mistake.  
  
The second time _it was reported_. Which means it has probably happened a lot more :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-08-25 13:46:40 UTC  
> Updated at: 2021-08-25 13:46:59 UTC  
> Url: https://github.com/boostorg/json/issues/615#issuecomment-905516135  

The place to put the note in the documentation is anywhere the parser option "allow comments" is discussed, since that's the only way to make the problem appear. Any example code which sets it to true should also have a comment warning about `getline`.
