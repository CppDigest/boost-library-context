# #416 - Making an interactive shell with Spirit [Closed]

> Username: syyyr  
> Created at: 2018-11-08 13:17:20 UTC  
> Updated at: 2019-02-09 01:33:39 UTC  
> Closed at: 2019-01-12 12:01:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/416  

Hello,  
I'm trying to create an interactive shell with the help of Spirit X3. Let's say I want to create a parser for the "ls" command:  
```  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
namespace x3 = boost::spirit::x3;  
struct context_path_tag;  
  
bool checkPath(const std::vector<std::string> location, const std::string& name)  
{  
    // logic for checking the filesystem  
}  
  
struct ls_command {  
    std::vector<std::string> options;  
    std::vector<std::string> path;  
};  
BOOST_FUSION_ADAPT_STRUCT(ls_command, options, path)  
  
struct name_class {  
    template <typename T, typename Iterator, typename Context>  
    void on_success(Iterator const&, Iterator const&, T& ast, Context const& context)  
    {  
        auto& context_path = x3::get<context_path_tag>(context);  
  
        if (!checkPath(context_path, ast))  
            _pass(context) = false;  
        else  
            context_path.push_back(ast);  
    }  
};  
  
struct path_class;  
struct ls_option_class;  
struct ls_class;  
  
x3::rule<name_class, std::string> name;  
x3::rule<ls_option_class, std::string> ls_option;  
x3::rule<path_class, std::vector<std::string>> path;  
x3::rule<ls_class, ls_command> ls;  
  
auto const name_def = +x3::alpha;  
auto const ls_option_def = x3::string("--all") | x3::string("--recursive");  
auto const path_def = name % '/';  
auto const ls_def = x3::lit("ls") >> *ls_option >> path;  
BOOST_SPIRIT_DEFINE(name, ls_option, path, ls)  
  
int main(int argc, char* argv[])  
{  
    while (true) {  
        std::string line;  
        std::getline(std::cin, line);  
        std::vector<std::string> context_path;  
        auto grammar = x3::with<context_path_tag>(context_path)[ls];  
  
        auto it = line.begin();  
        ls_command cmd;  
        x3::phrase_parse(it, line.end(), grammar, x3::space, cmd);  
    }  
    return 0;  
}  
```  
The idea behind the on\_success handler is, that I don't know what files are there in the filesystem, so I use the `x3::alpha` parser to parse anything, that could be a valid path fragment and then I use the handler to check whether that path really exists with the help of `context_path` and `checkPath` and then either failing the parser or pushing the path fragment into `context_path`. Now let's say I want to implement some of sort of auto-completion for my program. Now, I would like to use Spirit to parse this string and tell me which rule was the first to fail parsing. For example, the user types in "ls directory/another\_dir/nonexistent\_dir" and the parsing stops just before the last slash (because I would artificially fail the rule because of the nonexistent directory) and the first failed rule would be the `name` rule.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-11-13 19:56:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/416#issuecomment-438414042  

I am sorry, but it is not clear for me what is the question. This is the issue tracker (primary for bug reports), please be specific about your problem. For general discussions it is better to use [spirit mail list](https://lists.sourceforge.net/lists/listinfo/spirit-general) or stack overflow.  
  
> The idea behind the on_success handler is, that I don't know what files are there in the filesystem, so I use the `x3::alpha` parser to parse anything, that could be a valid path fragment and then I use the handler to check whether that path really exists with the help of context_path and checkPath and then either failing the parser  
  
You cannot fail parser that already succeeded, but you can attach a [semantic action](https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/tutorials/semantic_actions.html) that will fail parsing depends on a result of a parser it was attached to.  
  
> or pushing the path fragment into context_path. Now let's say I want to implement some of sort of auto-completion for my program. Now, I would like to use Spirit to parse this string and tell me which rule was the first to fail parsing.  
  
You should check out [tutorials](https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/tutorials.html), there is one [on an error handling topic](https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html). Googling the auto-completion topic should have been led you to [a great answer](https://stackoverflow.com/questions/47354226/how-to-provider-user-with-autocomplete-suggestions-for-given-boostspirit-gramm) by @sehesnips on stack overflow (it is for Qi, but should be possible to implement the same for X3 without huge changes).  
  
> For example, the user types in "ls directory/another_dir/nonexistent_dir" and the parsing stops just before the last slash (because I would artificially fail the rule because of the nonexistent directory) and the first failed rule would be the name rule.  
  
I do not see why you want to perform such validation/autocompletion (you mentioned both of them in your question) inside a parser. I would suggest you not to mix an app logic with parsing, separating responsibilities will save you a lot of maintenance time.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-01-12 12:01:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/416#issuecomment-453742056  

I am closing this because there was no response for a long time and it is not a bug report.
