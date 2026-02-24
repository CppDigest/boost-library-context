# #138 - Empty ifdef block does not emit line directive for missing whitespace. [Closed]

> Username: realazthat  
> Created at: 2021-12-23 01:37:39 UTC  
> Updated at: 2022-06-25 14:14:45 UTC  
> Closed at: 2022-01-02 15:09:33 UTC  
> Url: https://github.com/boostorg/wave/issues/138  

* When using `#if 0 ... #endif` it properly writes out a `#line` directive that accounts for the whitespace.  
* However, when using `#ifdef NOT_DEFINED ... #endif`, no `#line` directive is emitted, leading to incorrect line numbering by subsequent compiler's accounting (compiler's reconstruct line numbers via properly emitted `#line` directives).  
  
  
Example code that is preprocessed:  
  
```C++  
  
// This should be line 2  
  
  
// A comment, followed by if with whitespace (line 5)  
#if 0  
// A lot of white space  
  
  
  
  
#endif  
// This should be line 13  
  
// A comment, followed by ifdef with whitespace (line 15)  
#ifdef NOT_DEFINED  
// A lot of white space  
  
  
  
  
#endif  
  
// This should be line 24  
  
  
  ```  
    
  Above preprocessed (with line numbering for your convenience).  
    
  ```C++  
/path/to/source/source.cxx:1 |  
/path/to/source/source.cxx:2 |// This should be line 2  
/path/to/source/source.cxx:3 |  
/path/to/source/source.cxx:4 |  
/path/to/source/source.cxx:5 |// A comment, followed by if with whitespace (line 5)  
/path/to/source/source.cxx:  |#line 13 "/path/to/source/source.cxx"  
/path/to/source/source.cxx:13|// This should be line 13  
/path/to/source/source.cxx:14|  
/path/to/source/source.cxx:15|// A comment, followed by ifdef with whitespace (line 15)  
/path/to/source/source.cxx:16|  
/path/to/source/source.cxx:17|// This should be line 24  
/path/to/source/source.cxx:18|  
/path/to/source/source.cxx:19|  
/path/to/source/source.cxx:20|    
```  
  
Full code to reproduce (you can pretty much ignore everything outside of `main()`, is just for the nice debug output):  
  
```C++  
/*  
reset && g++-11 \  
  /mnt/y/dump/code/gigaclip-project-2020/boost-wave-ifdef-whitespace.cxx \  
  --std=c++17 -I$HOME/boost/ -L$HOME/boost/stage/lib/ -lboost_wave \  
  -lboost_thread -lboost_filesystem  
  
LD_LIBRARY_PATH=$HOME/boost/stage/lib/ ./a.out  
*/  
  
#include <filesystem>  
#include <functional>  
#include <iostream>  
#include <map>  
#include <optional>  
#include <set>  
#include <sstream>  
#include <string>  
#include <vector>  
  
#include "boost/algorithm/string/classification.hpp"  
#include "boost/algorithm/string/join.hpp"  
#include "boost/algorithm/string/predicate.hpp"  
#include "boost/algorithm/string/split.hpp"  
#include "boost/core/span.hpp"  
#include "boost/lexical_cast.hpp"  
#include "boost/range/iterator_range.hpp"  
#include "boost/wave.hpp"  
#include "boost/wave/cpp_context.hpp"  
#include "boost/wave/cpp_exceptions.hpp"  
#include "boost/wave/cpplexer/cpp_lex_iterator.hpp"  
#include "boost/wave/language_support.hpp"  
  
  
  
template <typename Token>  
struct ContextPolicy  
    : boost::wave::context_policies::default_preprocessing_hooks {  
  // typedef boost::wave::context_policies::eat_whitespace<Token> base_type;  
  typedef boost::wave::context_policies::default_preprocessing_hooks base_type;  
  
  
  // template <typename ContextT, typename TokenT>  
  // bool may_skip_whitespace(ContextT const& ctx, TokenT& token,  
  //                          bool& skipped_newline) {  
  //   skipped_newline = false;  
  //   return false;  
  // }  
  
  
};  
  
  
  
std::string LeftPad(const std::string& value, std::size_t max_length);  
std::string RightPad(const std::string& value, std::size_t max_length);  
void StripLeadingDoubleQuotes(std::string* str);  
void StripTrailingDoubleQuotes(std::string* str);  
void StripDoubleQuotes(std::string* value);  
bool ParseLineDirective(const std::string& line, std::size_t* line_num,  
                        std::string* file_name);  
  
using TranslateFilename = std::function<std::string(const std::string& name)>;  
  
// Format a preprocessed file with file names and line numbers for debugging  
// view.  
//  
// preprocessed_source: The preprocessed file, which is expected to contain the  
// proper `#line` directives, so that this function can recover the original  
// file names and line numbers.  
//  
// translate: This translates the file names (or paths) in the preprocessed  
// source files to whatever you want to be displayed. This is useful for  
// displaying only the file name (instead of the entire path), or, for languages  
// like glsl which use numbers instead of file names/paths, you can translate  
// the numbers back to human readable file names. If the nullopt is used, then  
// then no translation will take place.  
//  
// untranslated_source_file_name: The file name of the main source file.  
std::string NumberTheLines(const std::string preprocessed_source,  
                           std::optional<TranslateFilename> translate,  
                           const std::string& untranslated_source_file_name);  
  
template <typename WaveException>  
void PrettyPrintWaveException(const WaveException& e);  
  
int main() {  
  try {  
    typedef boost::wave::cpplexer::lex_iterator<  
        boost::wave::cpplexer::lex_token<>>  
        LexIterator;  
    typedef ContextPolicy<typename LexIterator::token_type> ContextPolicyT;  
    typedef boost::wave::context<  
        /*Iterator=*/std::string::const_iterator,  
        /*LexIterator=*/LexIterator,  
        /*InputPolicy=*/  
        boost::wave::iteration_context_policies::load_file_to_string,  
        /*ContextPolicy=*/ContextPolicyT>  
        context_type;  
  
    std::string source_path = "/path/to/source/source.cxx";  
    std::string source = R"source(  
// This should be line 2  
  
  
// A comment, followed by if with whitespace (line 5)  
#if 0  
// A lot of white space  
  
  
  
  
#endif  
// This should be line 13  
  
// A comment, followed by ifdef with whitespace (line 15)  
#ifdef NOT_DEFINED  
// A lot of white space  
  
  
  
  
#endif  
  
// This should be line 24  
  
  
  )source";  
  
    ContextPolicyT ctx_policy;  
  
    context_type ctx(/*first_=*/source.begin(),  
                     /*last_=*/source.end(),  
                     /*fname=*/source_path.c_str(),  
                     /*ctx_policy=*/ctx_policy);  
    ctx.set_language(/*language=*/boost::wave::language_support::support_c99);  
    ctx.set_language(  
        /*language=*/boost::wave::enable_preserve_comments(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_insert_whitespace(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_single_line(ctx.get_language()));  
    ctx.set_language(/*language=*/boost::wave::enable_emit_line_directives(  
        ctx.get_language()));  
    ctx.set_language(/*language=*/boost::wave::enable_emit_pragma_directives(  
        ctx.get_language()));  
  
    context_type::iterator_type first = ctx.begin();  
    context_type::iterator_type last = ctx.end();  
  
  
    std::ostringstream sstr;  
    while (first != last) {  
      sstr << (*first).get_value();  
      ++first;  
    }  
  
    std::string preprocessed_source = sstr.str();  
  
  
    std::cout << NumberTheLines(/*preprocessed_source=*/preprocessed_source,  
                                /*translate=*/std::nullopt,  
                                /*untranslated_source_file_name=*/source_path)  
              << std::endl;  
  
  } catch (const boost::wave::cpp_exception& e) {  
    PrettyPrintWaveException(e);  
    return 1;  
  } catch (const boost::wave::cpplexer::cpplexer_exception& e) {  
    PrettyPrintWaveException(e);  
    return 1;  
  }  
  return 0;  
}  
  
  
std::string NumberTheLines(const std::string preprocessed_source,  
                           std::optional<TranslateFilename> translate,  
                           const std::string& untranslated_source_file_name) {  
  // TODO: paste an example of a few lines.  
  // line => (line string).  
  std::vector<std::string> lines;  
  // TODO: allow user defined newline.  
  boost::split(lines, preprocessed_source, boost::is_any_of("\n"));  
  
  // Unpack the optional into something usable:  
  TranslateFilename translate_;  
  if (translate) {  
    translate_ = *translate;  
  } else {  
    translate_ = [](const auto& file_name) { return file_name; };  
  }  
  
  // This will act as a cache for the translated (displayed) file names.  
  //  
  // (file name) => (displayed file names).  
  std::map<std::string, std::string> translated_file_names;  
  // Cache the main source file name.  
  translated_file_names[untranslated_source_file_name] =  
      translate_(untranslated_source_file_name);  
  
  
  // line => (file name).  
  std::vector<std::string> line_filenames;  
  // Track maximum displayed file name width, so the output can be properly  
  // padded.  
  std::size_t max_line_file_width =  
      translated_file_names[untranslated_source_file_name].size();  
  
  // line => (line number).  
  std::vector<std::size_t> line_numbers;  
  // Track maximum displayed line number width, so the output can be properly  
  // padded.  
  std::size_t max_line_num_width = 0;  
  
  // file => (current line number).  
  std::map<std::string, std::size_t> file2current_line;  
  
  // Track the line number for the main source file.  
  file2current_line[untranslated_source_file_name] = 1;  
  // Track which file we are in as we iterate the lines. We start with the main  
  // source file name.  
  std::string current_file_name = untranslated_source_file_name;  
  
  
  // Iterate through all the lines in the preprocessed file.  
  for (const std::string& line : lines) {  
    if (boost::algorithm::starts_with(line, "#line")) {  
      // TODO: paste an example of a line  
  
      std::size_t line_num = 0u;  
      std::string file_name;  
      bool success = ParseLineDirective(/*line=*/line, /*line_num=*/&line_num,  
                                        /*file_name=*/&file_name);  
      assert(success);  
  
      if (file2current_line.count(file_name) > 0) {  
        assert(line_num >= file2current_line[file_name]);  
      }  
  
      current_file_name = file_name;  
      file2current_line[file_name] = line_num;  
  
      // Append the line number and file name for this line.  
      // Line number of size_t(-1) marks a #line directive which has no position  
      // in the original file.  
      line_numbers.push_back(std::size_t(-1));  
      line_filenames.push_back(file_name);  
  
      if (translated_file_names.count(file_name) == 0) {  
        std::string translated_file_name = translate_(file_name);  
        translated_file_names[file_name] = translated_file_name;  
        max_line_file_width =  
            std::max(max_line_file_width, translated_file_name.size());  
      }  
  
    } else {  
  
      std::size_t line_num = file2current_line[current_file_name]++;  
      line_numbers.push_back(line_num);  
      line_filenames.push_back(current_file_name);  
  
      max_line_num_width = std::max(max_line_num_width,  
                                    std::to_string(line_numbers.back()).size());  
    }  
  }  
  
  // This is for debugging, to ensure we never output the same line twice.  
  //  
  // {(file name, line number)}  
  std::set<std::tuple<std::string, std::size_t>> seen;  
  // This will hold the formatted output lines.  
  std::vector<std::string> numbered_lines;  
  
  // Now iterate through the lines once more to construct the formatted lines.  
  for (std::size_t i = 0; i < lines.size(); ++i) {  
    std::string file_name = line_filenames.at(i);  
    std::string displayed_file_name = translated_file_names.at(file_name);  
  
    std::size_t line_num = line_numbers.at(i);  
    std::string displayed_line_num = std::to_string(line_num);  
  
    const std::string& line = lines.at(i);  
  
  
    // If the line number is this special size_t(-1), then we know that this  
    // line is a `#line` directive, so don't display it.  
    if (line_num == std::size_t(-1)) {  
      displayed_line_num = "";  
    } else {  
      assert(seen.count(std::make_tuple(file_name, line_num)) == 0);  
      seen.insert(std::make_tuple(file_name, line_num));  
    }  
  
  
    displayed_file_name = LeftPad(/*value=*/displayed_file_name,  
                                  /*max_length=*/max_line_file_width);  
    displayed_line_num = RightPad(/*value=*/displayed_line_num,  
                                  /*max_length=*/max_line_num_width);  
    numbered_lines.push_back(displayed_file_name + ":" + displayed_line_num +  
                             "|" + line);  
  }  
  
  // TODO: allow user defined newline.  
  return boost::algorithm::join(numbered_lines, "\n");  
}  
  
template <typename WaveException>  
void PrettyPrintWaveException(const WaveException& e) {  
  std::string severity = boost::wave::util::get_severity(e.get_severity());  
  
  std::cerr << "Error during preprocessing"  
            << "\n e.what(): " << e.what()  
            << "\n e.description(): " << e.description()  
            << "\n e.get_errorcode(): " << e.get_errorcode()  
            << "\n e.get_severity(): " << e.get_severity()  
            << "\n e.get_severity(): " << severity  
            << "\n e.is_recoverable(): " << e.is_recoverable()  
            << "\n e.file_name(): " << e.file_name()  
            << "\n e.file_name(): " << e.file_name() << ":" << e.line_no()  
            << "\n e.line_no(): " << e.line_no()  
            << "\n e.column_no(): " << e.column_no() << std::endl;  
}  
  
  
  
std::string LeftPad(const std::string& value, std::size_t max_length) {  
  assert(value.size() <= max_length);  
  std::size_t padding_length = max_length - value.size();  
  std::string padding = std::string(/*count=*/padding_length, /*ch=*/' ');  
  return padding + value;  
}  
std::string RightPad(const std::string& value, std::size_t max_length) {  
  assert(value.size() <= max_length);  
  std::size_t padding_length = max_length - value.size();  
  std::string padding = std::string(/*count=*/padding_length, /*ch=*/' ');  
  return value + padding;  
}  
  
void StripLeadingDoubleQuotes(std::string* str) {  
  auto it = std::find_if_not(str->begin(), str->end(),  
                             [](char c) { return c == '"'; });  
  str->erase(str->begin(), it);  
}  
  
void StripTrailingDoubleQuotes(std::string* str) {  
  auto it = std::find_if_not(str->rbegin(), str->rend(),  
                             [](char c) { return c == '"'; });  
  str->erase(str->rend() - it);  
}  
  
void StripDoubleQuotes(std::string* value) {  
  StripTrailingDoubleQuotes(value);  
  StripLeadingDoubleQuotes(value);  
}  
  
bool ParseLineDirective(const std::string& line, std::size_t* line_num,  
                        std::string* file_name) {  
  if (!boost::algorithm::starts_with(line, "#line")) {  
    return false;  
  }  
  
  // TODO: paste an example of a line  
  std::vector<std::string> line_parts;  
  // TODO: Probably handle quotes better.  
  boost::split(line_parts, line, boost::is_any_of(" "));  
  
  
  assert(line_parts.size() >= 3u);  
  assert(line_parts.at(0) == "#line");  
  
  *line_num = boost::lexical_cast<std::size_t>(line_parts.at(1));  
  boost::span<const std::string> file_name_parts(&line_parts.at(2),  
                                                 line_parts.size() - 2u);  
  *file_name = boost::algorithm::join(file_name_parts, "\n");  
  StripDoubleQuotes(/*value=*/file_name);  
  return true;  
}  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2021-12-24 08:15:11 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1000716698  

Encouragingly the standalone `wave` preprocessor seems to do the right thing here:  
  
```  
$ wave --c99 -p 3 /tmp/sample  
  
#line 2 "/tmp/sample"  
// This should be line 2  
  
  
// A comment, followed by if with whitespace (line 5)  
#line 13 "/tmp/sample"  
// This should be line 13  
  
// A comment, followed by ifdef with whitespace (line 15)  
  
#line 24 "/tmp/sample"  
// This should be line 24  
$  
```

---

## Comment 2

> Username: jefftrull  
> Created at: 2021-12-30 19:49:03 UTC  
> Updated at: 2021-12-30 20:08:21 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003160953  

OK, I think I've got it.  
  
For emitting `#line` directives it's crucial to know how many (if any) newlines were skipped during whitespace processing. The default preprocessing hooks make no attempt to count them in the `may_skip_whitespace` method, which has a `skipped_newline` out parameter for this purpose.  
  
The `eat_whitespace` context policy will do this for you, but without changes it will consume all the whitespace as well. So the solution (also used in the Wave preprocessing tool code) to get what you want is to implement your own `may_skip_whitespace` that uses `eat_whitespace` to record the presence of newlines, but always returns `false` ("do not consume"). A modified ContextPolicy that will do this for you is:  
  
```c++  
template <typename Token>  
struct ContextPolicy  
    : boost::wave::context_policies::eat_whitespace<Token> {  
  
    typedef boost::wave::context_policies::eat_whitespace<Token> base_type;  
  
    template <typename ContextT, typename TokenT>  
    bool may_skip_whitespace(ContextT const& ctx, TokenT& token,  
                             bool& skipped_newline) {  
        this->base_type::may_skip_whitespace(ctx, token, skipped_newline);  
        return false;  
    }  
};  
```  
Using this I was able to duplicate your desired output.

---

## Comment 3

> Username: jefftrull  
> Created at: 2021-12-30 20:06:53 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003165968  

@hkaiser should the default preprocessing hooks set `skipped_newline` maybe?

---

## Comment 4

> Username: hkaiser  
> Created at: 2021-12-30 20:25:43 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003170981  

> @hkaiser should the default preprocessing hooks set `skipped_newline` maybe?  
  
Two questions need to be answered for this:   
  
1. would that be the expected (and preferred) behavior?  
2. would this potentially break existing code?  
  
In the end the answer should be yes if the answer to 1. is 'likely' and to 2. 'unlikely'.

---

## Comment 5

> Username: jefftrull  
> Created at: 2021-12-31 17:37:45 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003424689  

Upon further reflection this is a bad idea, nevermind.  
  
It is a little funny that the correct production of line directives depends on the choice of preprocessor hooks. My intuition would have been that the default would do it. Hm.

---

## Comment 6

> Username: hkaiser  
> Created at: 2021-12-31 17:43:48 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003425490  

> It is a little funny that the correct production of line directives depends on the choice of preprocessor hooks. My intuition would have been that the default would do it. Hm.  
  
Indeed. I think we should make the change. If it breaks somebody's code, we'll deal with it later...

---

## Comment 7

> Username: jefftrull  
> Created at: 2021-12-31 18:45:00 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003434288  

Well, I thought about it a little bit and given that the default returns `false` (do not consume token), not touching `skipped_newline` (or setting it to `false`, maybe) actually seems like the right behavior... So maybe what's broken is the Wave core?

---

## Comment 8

> Username: jefftrull  
> Created at: 2021-12-31 22:18:59 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003459186  

Yep, it looks like the paths for handling `if` and `ifdef` are different... and the `if` path contains the code that handles emitting `#line`, in particular [here](https://github.com/boostorg/wave/blob/07f853eda7352f9b974a47c08bda6337174b995f/include/boost/wave/util/cpp_iterator.hpp#L1373)

---

## Comment 9

> Username: jefftrull  
> Created at: 2021-12-31 22:21:37 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003459466  

I think we should move handling for `ifdef` and `ifndef` out of the "simple pp directives" section for consistency, if you don't object.

---

## Comment 10

> Username: hkaiser  
> Created at: 2021-12-31 22:56:50 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1003462441  

> I think we should move handling for `ifdef` and `ifndef` out of the "simple pp directives" section for consistency, if you don't object.  
  
Sure, fine with me. Thanks!

---

## Comment 11

> Username: jefftrull  
> Created at: 2022-06-25 14:14:45 UTC  
> Url: https://github.com/boostorg/wave/issues/138#issuecomment-1166296515  

@realazthat develop branch contains another line directive-related bug fix that may be of interest to you (to be released in 1.80)
