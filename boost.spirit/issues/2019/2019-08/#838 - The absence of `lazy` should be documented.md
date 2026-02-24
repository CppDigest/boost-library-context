# #838 - [X3] The absence of `lazy` should be documented [Open]

> Username: voivoid  
> Created at: 2019-08-28 19:44:06 UTC  
> Updated at: 2025-10-10 16:10:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/838  

Hi!  
  
X3 lacks auxiliary 'lazy' parser despite X3's docs state the opposite:  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/quick_reference/auxiliary.html  
  
Please add the lazy parser or update the docs.  
  
  
Thanks!

---

## Comment 1

> Username: sehe  
> Created at: 2020-02-11 20:30:41 UTC  
> Updated at: 2022-06-25 21:59:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983294  

Came up with a Proof Of Concept idea on Stackoverflow (https://stackoverflow.com/questions/60171119/boost-spirit-x3-lazy-parser/60176802#60176802)  
  
Posting here for inspiration/idea forming  
  
-----  
  
I thought I would try my hand here.  
  
What is needed is some type-erasure around the iterator and attribute types. This is getting very close to the interface of a `qi::rule` in the old days.  
  
> To be complete we could actually also erase or transform contexts (e.g. to propagate the skipper inside the lazy rule), but I chose for simplicity here.  
>   
> In many cases the parsers to be lazily invoked might be lexemes anyways (as in the sample I will use)  
  
In our use-case, let's parse these inputs:  
  
    integer_value: 42  
    quoted_string: "hello world"  
    bool_value: true  
    double_value: 3.1415926  
  
We'll use a variant attribute type, and start with creating a `lazy_rule` parser that will allow us to erase the types:  
  
    using Value = boost::variant<int, bool, double, std::string>;  
    using It    = std::string::const_iterator;  
    using Rule  = x3::any_parser<It, Value>;  
  
## Passing The Lazy Subject Around  
  
Now, where do we "get" the lazy subject from?  
  
In Spirit Qi, we had the [Nabialek Trick](https://stackoverflow.com/search?q=user%3A85371+nabialek). This would use `qi::locals<>` or [inherited attributes](https://www.boost.org/doc/libs/1_72_0/libs/spirit/doc/html/spirit/abstracts/attributes/nonterminal_attributes.html), which basically both boiled down to using Phoenix lazy actors (`qi::_r1` or `qi::_a` etc) to evaluate to a value _from parser context_ at runtime.  
  
In X3 there is no Phoenix, and we will have to manipulate the context using semantic actions ourselves.  
  
The basic building block for this is the [`x3::with<T>[]` directive](https://stackoverflow.com/search?q=user%3A85371+x3+with+context)¹. Here's what we'll end up using as the parser:  
  
```c++  
x3::symbols<Rule> options;  
```  
  
Now we can add any parse expression to the options, by saying e.g. `options.add("anything", x3::eps);`.   
  
```c++  
auto const parser = x3::with<Rule>(Rule{}) [  
    set_context<Rule>[options] >> ':' >> lazy<Rule>  
];  
```  
  
This adds a `Rule` value to the context, which can be set (`set_context`) and "executed" (`lazy`).  
  
Like I said, we have to manipulate the context manually, so let's define some helpers that do this:  
  
```c++  
template <typename Tag>  
struct set_context_type {  
    template <typename P>  
    auto operator[](P p) const {  
        auto action = [](auto& ctx) {  
            x3::get<Tag>(ctx) = x3::_attr(ctx);  
        };  
        return x3::omit [ p [ action ] ];  
    }  
};  
  
template <typename Tag>  
struct lazy_type : x3::parser<lazy_type<Tag>> {  
    using attribute_type = typename Tag::attribute_type; // TODO FIXME?  
  
    template<typename It, typename Ctx, typename RCtx, typename Attr>  
    bool parse(It& first, It last, Ctx& ctx, RCtx& rctx, Attr& attr) const {  
        auto& subject = x3::get<Tag>(ctx);  
  
        It saved = first;  
        x3::skip_over(first, last, ctx);  
        if (x3::as_parser(subject).parse(first, last,  
                                         std::forward<Ctx>(ctx),  
                                         std::forward<RCtx>(rctx), attr)) {  
            return true;  
        } else {  
            first = saved;  
            return false;  
        }  
    }  
};  
  
template <typename T> static const set_context_type<T> set_context{};  
template <typename T> static const lazy_type<T> lazy{};  
```  
  
That's really all there is to it.  
  
## Demo Time  
  
In this demo, we run the above inputs (in function `run_tests()`) and it will use the parser as shown:  
  
```c++  
auto run_tests = [=] {  
    for (std::string const& input : {  
            "integer_value: 42",  
            "quoted_string: \"hello world\"",  
            "bool_value: true",  
            "double_value: 3.1415926",  
        })  
    {  
        Value attr;  
        std::cout << std::setw(36) << std::quoted(input);  
        if (phrase_parse(begin(input), end(input), parser, x3::space, attr)) {  
            std::cout << " -> success (" << attr << ")\n";  
        } else {  
            std::cout << " -> failed\n";  
        }  
    }  
};  
```  
      
First we will run:  
  
```c++  
options.add("integer_value", x3::int_);  
options.add("quoted_string", as<std::string> [  
        // lexeme is actually redundant because we don't use surrounding skipper yet  
        x3::lexeme [ '"' >> *('\\' >> x3::char_ | ~x3::char_('"')) >> '"' ]  
    ]);  
run_tests();  
```  
  
Which will print:  
  
    "integer_value: 42"                  -> success (42)  
    "quoted_string: \"hello world\""     -> success (hello world)  
    "bool_value: true"                   -> failed  
    "double_value: 3.1415926"            -> failed  
  
Now, we can demonstrate the dynamic nature of that parser, by extending `options`:  
  
```c++  
options.add("double_value", x3::double_);  
options.add("bool_value", x3::bool_);  
  
run_tests();  
```  
  
And the output becomes:  
  
    "integer_value: 42"                  -> success (42)  
    "quoted_string: \"hello world\""     -> success (hello world)  
    "bool_value: true"                   -> success (true)  
    "double_value: 3.1415926"            -> success (3.14159)  
  
> Note, I threw in another helper `as<>` that makes it easier to coerce the attribute type to `std::string` there. It's an evolution of [ideas in earlier answers](https://stackoverflow.com/questions/45457868/statefulness-of-spirit-v2-and-x3/45469919#45469919)  
  
## Full Listing Live On Coliru  
  
See it **<kbd>[Live On Coliru](http://coliru.stacked-crooked.com/a/2fd7c70fef082e6b)</kbd>**  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <iomanip>  
  
namespace x3 = boost::spirit::x3;  
  
namespace {  
    template <typename T>  
    struct as_type {  
        template <typename...> struct Tag{};  
  
        template <typename P>  
        auto operator[](P p) const {  
            return x3::rule<Tag<T, P>, T> {"as"} = x3::as_parser(p);  
        }  
    };  
  
    template <typename Tag>  
    struct set_lazy_type {  
        template <typename P>  
        auto operator[](P p) const {  
            auto action = [](auto& ctx) {  
                x3::get<Tag>(ctx) = x3::_attr(ctx);  
            };  
            return x3::omit [ p [ action ] ];  
        }  
    };  
  
    template <typename Tag>  
    struct do_lazy_type : x3::parser<do_lazy_type<Tag>> {  
        using attribute_type = typename Tag::attribute_type; // TODO FIXME?  
  
        template <typename It, typename Ctx, typename RCtx, typename Attr>  
        bool parse(It& first, It last, Ctx& ctx, RCtx& rctx, Attr& attr) const {  
            auto& subject = x3::get<Tag>(ctx);  
  
            It saved = first;  
            x3::skip_over(first, last, ctx);  
            if (x3::as_parser(subject).parse(first, last,  
                                             std::forward<Ctx>(ctx),  
                                             std::forward<RCtx>(rctx), attr)) {  
                return true;  
            } else {  
                first = saved;  
                return false;  
            }  
        }  
    };  
  
    template <typename T> static const as_type<T>       as{};  
    template <typename T> static const set_lazy_type<T> set_lazy{};  
    template <typename T> static const do_lazy_type<T>  do_lazy{};  
}  
  
int main() {  
    std::cout << std::boolalpha << std::left;  
  
    using Value = boost::variant<int, bool, double, std::string>;  
    using It    = std::string::const_iterator;  
    using Rule  = x3::any_parser<It, Value>;  
  
    x3::symbols<Rule> options;  
  
    auto const parser = x3::with<Rule>(Rule{}) [  
        set_lazy<Rule>[options] >> ':' >> do_lazy<Rule>  
    ];  
  
    auto run_tests = [=] {  
        for (std::string const input : {  
                "integer_value: 42",  
                "quoted_string: \"hello world\"",  
                "bool_value: true",  
                "double_value: 3.1415926",  
            })  
        {  
            Value attr;  
            std::cout << std::setw(36) << std::quoted(input);  
            if (phrase_parse(begin(input), end(input), parser, x3::space, attr)) {  
                std::cout << " -> success (" << attr << ")\n";  
            } else {  
                std::cout << " -> failed\n";  
            }  
        }  
    };  
  
    std::cout << "Supporting only integer_value and quoted_string:\n";  
    options.add("integer_value", x3::int_);  
    options.add("quoted_string", as<std::string> [  
            // lexeme is actually redundant because we don't use surrounding skipper yet  
            x3::lexeme [ '"' >> *('\\' >> x3::char_ | ~x3::char_('"')) >> '"' ]  
        ]);  
    run_tests();  
  
    std::cout << "\nAdded support for double_value and bool_value:\n";  
    options.add("double_value", x3::double_);  
    options.add("bool_value", x3::bool_);  
  
    run_tests();  
}  
```  
  
Printing the full output of:  
  
    Supporting only integer_value and quoted_string:  
    "integer_value: 42"                  -> success (42)  
    "quoted_string: \"hello world\""     -> success (hello world)  
    "bool_value: true"                   -> failed  
    "double_value: 3.1415926"            -> failed  
      
    Added support for double_value and bool_value:  
    "integer_value: 42"                  -> success (42)  
    "quoted_string: \"hello world\""     -> success (hello world)  
    "bool_value: true"                   -> success (true)  
    "double_value: 3.1415926"            -> success (3.14159)  
  
----  
  
¹ sadly the documentation is missing in action

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-02-11 20:37:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983352  

> What is needed is some type-erasure around the iterator and attribute types.   
  
There is [`any_parser`](https://github.com/boostorg/spirit/blob/master/test/x3/any_parser.cpp) that does it.

---

## Comment 3

> Username: sehe  
> Created at: 2020-02-11 20:55:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983388  

@Kojoley Dang. I remember seeing that thing and not knowing a use for it. I'll fiddle with it sometime to simplify the code there.

---

## Comment 4

> Username: sehe  
> Created at: 2020-02-11 21:13:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983411  

@Kojoley Indeed.  
  
Using `x3::any_parser` shaves off 25 lines of code (and adds the context support and [test-cases](https://github.com/boostorg/spirit/blob/master/test/x3/any_parser.cpp)).  
  
For future comparison: [before](http://coliru.stacked-crooked.com/a/daa5fd0cf1ba85c9) vs [after](http://coliru.stacked-crooked.com/a/36717769d5a83d36).  
  
Cheers! Answer and Github comment updated

---

## Comment 5

> Username: syyyr  
> Created at: 2020-03-25 12:52:51 UTC  
> Updated at: 2020-03-25 13:03:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983432  

Hello, is there any chance the lazy parser gets implemented? My use-case is parsing strings, which I get dynamically from a class I inject via the `x3::with` directive. Here is sample code how I would use the lazy parser:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
struct MyClass {  
    // this first return some sort of a runtime information  
    // example: the first returns files on disk and the other returns directories  
    std::vector<std::string> getString1();  
    std::vector<std::string> getString2();  
};  
  
struct class_tag;  
namespace x3 = boost::spirit::x3;  
  
struct String1 {  
    std::string m_value;  
};  
  
struct String2 {  
    std::string m_value;  
};  
  
template<typename Context>  
auto createString1Parser(Context& ctx) {  
    auto strings = x3::get<class_tag>(ctx).getString1();  
    x3::symbols<String1> symTable;  
    for (const auto& string : strings) {  
        symTable.add(string, String1{string});  
    }  
    return symTable;  
}  
  
template<typename Context>  
auto createString2Parser(Context& ctx) {  
    auto strings = x3::get<class_tag>(ctx).getString2();  
    x3::symbols<String2> symTable;  
    for (const auto& string : strings) {  
        symTable.add(string, String2{string});  
    }  
    return symTable;  
}  
  
x3::rule<class Rule_Class, boost::variant<String1, String2>> parser;  
  
auto const parser_def = createString1Parser | createString2Parser; // createStringParser is supposed to be called at runtime  
  
int main() {  
    auto injectedParser = x3::with<class_tag>(MyClass());  
}  
```  
What I've been doing until now was use this grammar:   
```cpp  
x3::rule<class String1_class, String1> string1;  
x3::rule<class String2_class, String2> string2;  
auto const string1_def = +x3::char_;  
auto const string2_def = +x3::char_;  
auto const parser_def = string1 | string2;  
```  
then validating the strings in `String1_class::on_success` and `String2_class::on_success` against `MyClass::getString1` and `MyClass::getString2` respectively and manually failing the parsers through `_pass(ctx) = false;` if I didn't get any matches. The main is that having essentially `+x3::char_ | +x3::char_` creates an ambiguity and the parser and I don't want that (it creates problems in more complex parsers).  
  
I tried to use the lazy parser implementation posted here and I was not able to use it to accomplish what I wanted to do (also the "after" version in the last post doesn't seem to work).  
  
What do you think about this? Or maybe I should use a different approach?  
  
Thanks.

---

## Comment 6

> Username: Kojoley  
> Created at: 2020-03-25 14:03:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983458  

There is no Phoenix integration in X3, it would need some other interface and since it is much easier to create customs parsers with X3 I am not sure all that would be worth it.  
  
@syyyr recreating `x3::symbols` is a heavy task I will not recommend doing that at run-time until required and if you have to there might be a better option. Anyway, will `x3::with<tag>(x3::symbols<?>())[populate_syms >> lazy<tag>]` work for you?

---

## Comment 7

> Username: syyyr  
> Created at: 2020-03-25 14:14:52 UTC  
> Updated at: 2020-03-25 14:23:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983482  

@Kojoley   
I was able to change `lazy` to work a little bit differently: it constructs an object of type `ParserCreator` (the template argument) and then calls its `operator()` with the context (which could be injected with `x3::with`). The `operator()` then returns a symbol table (or it could be any parser), which is then used to parse.  
Here is an example. In this code, two types of parsers are dynamically created: both iterate the current directory and create a symbol table. The first one fills with filenames of files and the other fills it with filenames of directories. After the parsing ends, I'm able to differentiate whether a `File` was parsed or a `Directory` was parsed.  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <filesystem>  
#include <iostream>  
namespace x3 = boost::spirit::x3;  
  
template <typename ParserCreator>  
struct lazy_type : x3::parser<lazy_type<ParserCreator>> {  
    using attribute_type = typename ParserCreator::attribute_type;  
  
    template<typename It, typename Ctx, typename RCtx, typename Attr>  
        bool parse(It& first, It last, Ctx& ctx, RCtx& rctx, Attr& attr) const {  
            return x3::as_parser(ParserCreator{}(ctx))  
                .parse(  
                        first, last,  
                        std::forward<Ctx>(ctx),  
                        std::forward<RCtx>(rctx),  
                        attr);  
        }  
};  
template <typename T> static const lazy_type<T>        lazy{};  
  
struct File {  
  std::string m_value;  
};  
  
struct Directory {  
  std::string m_value;  
};  
  
struct FileParser {  
    using attribute_type = File;  
    template <typename Context> auto operator()(Context &ctx) {  
        x3::symbols<File> sym;  
        std::cout << "files:";  
        for (const auto & entry : std::filesystem::directory_iterator(".")) {  
            if (!entry.is_regular_file()) {  
                continue;  
            }  
            auto filename = entry.path().filename().string();  
            std::cout << " " << filename;  
            sym.add(filename, File{filename});  
        }  
        std::cout << "\n";  
        return sym;  
    }  
};  
struct DirectoryParser {  
    using attribute_type = Directory;  
    template <typename Context> auto operator()(Context &ctx) {  
        x3::symbols<Directory> sym;  
        std::cout << "directories:";  
        for (const auto & entry : std::filesystem::directory_iterator(".")) {  
            if (!entry.is_directory()) {  
                continue;  
            }  
            auto filename = entry.path().filename().string();  
            std::cout << " " << filename;  
            sym.add(filename, Directory{filename});  
        }  
        std::cout << '\n';  
        return sym;  
    }  
};  
  
x3::rule<class Rule_Class, boost::variant<File, Directory>> parser;  
auto const parser_def = lazy<FileParser> | lazy<DirectoryParser>;  
BOOST_SPIRIT_DEFINE(parser)  
  
int main() {  
    using namespace std::string_literals;  
    std::cout << "What to parse: ";  
    std::string input;  
    std::cin >> input;  
    auto it = input.begin();  
    boost::variant<File, Directory> attr;  
    auto res = x3::parse(it, input.end(), parser, attr);  
    if (!res) {  
        std::cout << "failed to parse" << std::endl;  
        return 0;  
    }  
    if (attr.type() == typeid(File)) {  
      std::cout << "\ninput was parsed as File" << std::endl;  
    } else {  
      std::cout << "\ninput was parsed as Directory" << std::endl;  
    }  
}  
```  
Now, I'm not saying *exactly* this should be implemented in Spirit, but please let me know what you think about this approach.  
  
Edit:  
More info: I know that I could just create the symbols before the parsing begins, but the point is that maybe I do want to inject some more stuff into the parser (using `x3::with`) and generate the `lazy` parser based on that.

---

## Comment 8

> Username: Kojoley  
> Created at: 2020-03-25 16:04:28 UTC  
> Updated at: 2020-03-25 16:04:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983500  

> Now, I'm not saying exactly this should be implemented in Spirit, but please let me know what you think about this approach.  
  
I think that you can turn `FileParser`/`DirectoryParser` into a X3 parser (inherit from `x3::parser`, rename `operator()` into `parse` and change `return sym;` into `return sym.parse(...);`) and there will be no need in `lazy`.

---

## Comment 9

> Username: syyyr  
> Created at: 2020-03-25 17:45:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983513  

Thank you, I will try that.

---

## Comment 10

> Username: sehe  
> Created at: 2022-06-25 21:49:59 UTC  
> Updated at: 2022-06-25 21:54:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983525  

> @Kojoley Indeed.  
>   
> Using `x3::any_parser` shaves off 25 lines of code (and adds the context support and [test-cases](https://github.com/boostorg/spirit/blob/master/test/x3/any_parser.cpp)).  
>   
> For future comparison: [before](http://coliru.stacked-crooked.com/a/daa5fd0cf1ba85c9) vs [after](http://coliru.stacked-crooked.com/a/36717769d5a83d36).  
>   
> Cheers! Answer and Github comment updated  
  
Oh, :derp: The any_rule version lacked skipping. Thanks to [@olx for noticing](https://stackoverflow.com/questions/60171119/boost-spirit-x3-lazy-parser/60176802?noredirect=1#comment128510804_60176802). Fixed some more [afterer](http://coliru.stacked-crooked.com/a/2fd7c70fef082e6b)

---

## Comment 11

> Username: saki7  
> Created at: 2025-09-04 16:09:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983546  

Few updates:  
- `lazy` will not be supported in X3. I doubt there's any legit use case.   
- Most of the user's demand for supporting `lazy` is involving some kind of misunderstanding on how PEG is defined. PEGs are unambiguous, deterministic and always produce static grammar definition which operates on ordered choices (left to right). There's no need to dynamically create a RHS parser that depends on the left hand side of the runtime input. Spirit has historically provided a tool ("type-erased parsers") for bypassing that nature, but I think it should be discouraged today.  
- The same applies to `any_parser`. It is now officially deprecated after boostorg/spirit#807. Please refer to the PR's comment for background info.  
- The lack of `lazy` in X3 should be documented. The current documentation simply tells a lie because it was initially derived from the old version (Qi)'s documentation, I guess.

---

## Comment 12

> Username: saki7  
> Created at: 2025-10-10 16:10:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/838#issuecomment-3390983559  

Transferring this back to X3; the initial transfer was my mistake.  
  
X4 will be provided as-is and will not have problem regarding lack of information about unsupported feature.
