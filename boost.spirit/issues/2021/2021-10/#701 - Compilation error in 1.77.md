# #701 - Compilation error in 1.77 [Closed]

> Username: syyyr  
> Created at: 2021-10-18 09:10:51 UTC  
> Updated at: 2021-10-20 12:49:14 UTC  
> Closed at: 2021-10-20 12:49:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/701  

Hi, since Boost 1.77, I am not able to compile my project that uses Spirit X3. I read in the changelog, that there were some breaking changes, but I'm not sure if that's what's affecting me. To be honest, I am kinda lost in the error message. I am not sure what it means. This is the compilation error https://gist.github.com/syyyr/3ee4bac793a0724b4976138ec35f42ed. My grammars can be seen here https://github.com/CESNET/netconf-cli/blob/master/src/grammars.hpp, the AST is in the other files next to this one, but I think that https://github.com/CESNET/netconf-cli/blob/master/src/ast_path.hpp is where the problem might be. Could you please help me solve this issue?

---

## Comment 1

> Username: djowel  
> Created at: 2021-10-18 09:44:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-945593226  

I haven't investigated, but have you tried incrementally minimizing the grammar/ast to see the problem spot? It should be possible to narrow it down to something minimal.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-10-18 17:52:12 UTC  
> Updated at: 2021-10-18 18:16:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946017651  

> ```  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp: In instantiation of ‘void boost::spirit::x3::traits::move_to(Source&&, Dest&) [with Source = boost::variant<schemaPath_, dataPath_>; Dest = dataPath_]’:  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp:30:28:   required from ‘static void boost::spirit::x3::default_transform_attribute<Exposed, Transformed>::post(Exposed&, Transformed&&) [with Exposed = dataPath_; Transformed = boost::variant<schemaPath_, dataPath_>]’  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:342:32:   required from ‘static bool boost::spirit::x3::detail::rule_parser<Attribute, ID, skip_definition_injection>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = PathParser<PathParserMode::AnyPath, CompletionMode::Schema>; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<writableOps_tag, const WritableOps, boost::spirit::x3::context<boost::spirit::x3::error_handler_tag, std::reference_wrapper<boost::spirit::x3::error_handler<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >, boost::spirit::x3::context<parser_context_tag, ParserContext, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type> > > >; ActualAttribute = dataPath_; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = boost::variant<schemaPath_, dataPath_>; ID = anyPath_class; bool skip_definition_injection = true]’  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from ‘bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_, skip_definition_injection>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<writableOps_tag, const WritableOps, boost::spirit::x3::context<boost::spirit::x3::error_handler_tag, std::reference_wrapper<boost::spirit::x3::error_handler<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >, boost::spirit::x3::context<parser_context_tag, ParserContext, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type> > > >; Attribute_ = dataPath_; ID = anyPath_class; RHS = PathParser<PathParserMode::AnyPath, CompletionMode::Schema>; Attribute = boost::variant<schemaPath_, dataPath_>; bool force_attribute_ = false; bool skip_definition_injection = true]’  
> ```  
  
Rule with ID `anyPath_class` requested conversion from `boost::variant<schemaPath_, dataPath_>` to `dataPath_`, which is unsafe and not provided.  
  
> ```  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/operator/alternative.hpp:41:45:   required from ‘bool boost::spirit::x3::alternative<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<writableOps_tag, const WritableOps, boost::spirit::x3::context<boost::spirit::x3::error_handler_tag, std::reference_wrapper<boost::spirit::x3::error_handler<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >, boost::spirit::x3::context<parser_context_tag, ParserContext, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type> > > >; RContext = ls_; Attribute = boost::variant<dataPath_, schemaPath_, module_>; Left = boost::spirit::x3::rule_definition<anyPath_class, PathParser<PathParserMode::AnyPath, CompletionMode::Schema>, boost::variant<schemaPath_, dataPath_>, false, true>; Right = boost::spirit::x3::sequence<boost::spirit::x3::rule<module_class, module_>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >]’  
> /opt/br2-external-build/per-package/netconf-cli/host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/boost/spirit/home/x3/operator/detail/sequence.hpp:253:34:   [ skipping 40 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
> ```  
  
I cannot for sure say where that happens (path is truncated by GCC `[ skipping 40 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]`), but `rule<anyPath_class, ...> | rule<module_class, ...> >> lit(...)` most likely is here https://github.com/CESNET/netconf-cli/blob/2e092b324796bd869e3f5375c09656b0897786ae/src/path_parser.hpp#L341-L343, the attribute of the parser is `struct ls_` https://github.com/CESNET/netconf-cli/blob/2e092b324796bd869e3f5375c09656b0897786ae/src/ast_commands.hpp#L38-L58  
  
IIUC the idea is that:  
- `*(space_separator >> ls_options)` goes into `std::vector<LsOption> m_options;`, and  
- `-(space_separator >> (anyPath | (module >> "*")))` goes into `boost::optional<boost::variant<dataPath_, schemaPath_, module_>> m_path;`.  
  
where  
- `anyPath` produces `AnyPath` which is `using AnyPath = boost::variant<schemaPath_, dataPath_>;`, and  
- `module` produces `module_`  
  
so `anyPath | (module >> "*")` (`boost::variant<dataPath_, schemaPath_> | module_`) parses into `boost::variant<dataPath_, schemaPath_, module_>`, which looks fine and should work.  
  
I suspect https://github.com/boostorg/spirit/pull/665 uncovered some bug, @syyyr could you try reverting it locally?

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-10-18 18:25:11 UTC  
> Updated at: 2021-10-18 18:26:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946044753  

Yep, there is a long standing bug in the alternative parser  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/variant.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
struct X {};  
struct Y {};  
struct Z {};  
  
int main()  
{  
    boost::variant<X, Y, Z> v;  
    boost::variant<Y, Z> a;  
    v = a; // variant supports that convertion  
    char const* s = "";  
    parse(s, s, x3::attr(boost::variant<Y, X>{}) | x3::attr(Z{}), v);  
}  
```  
  
https://godbolt.org/z/T6vM3r6cr

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-10-18 18:43:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946060157  

@syyyr `PathParser` https://github.com/CESNET/netconf-cli/blob/2e092b324796bd869e3f5375c09656b0897786ae/src/path_parser.hpp#L267-L268 must have `static bool const has_attribute = true;`. After this fix you could replace https://github.com/CESNET/netconf-cli/blob/2e092b324796bd869e3f5375c09656b0897786ae/src/path_parser.hpp#L341  
```cpp  
auto const anyPath = x3::rule<class anyPath_class, AnyPath>{"anyPath"} = PathParser<PathParserMode::AnyPath, CompletionMode::Schema>{};  
```  
with  
```cpp  
const PathParser<PathParserMode::AnyPath, CompletionMode::Schema> anyPath{};  
```  
and that hopefully will workaround the issue.

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-10-18 18:45:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946061809  

Also https://github.com/CESNET/netconf-cli/blob/2e092b324796bd869e3f5375c09656b0897786ae/src/path_parser.hpp#L268 should have been `using attribute_type = typename ModeToAttribute<PARSER_MODE>::type;`

---

## Comment 6

> Username: syyyr  
> Created at: 2021-10-19 09:24:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946527790  

Hi, thank you for looking into this.  
  
The problem does appear after #665. I tried applying your suggested changes, but I can't get it to compile. This is what I did:  
```diff  
diff --git a/src/path_parser.hpp b/src/path_parser.hpp  
index 0aace94..f60789c 100644  
--- a/src/path_parser.hpp  
+++ b/src/path_parser.hpp  
@@ -265,7 +265,8 @@ struct ModeToAttribute<PathParserMode::DataPathListEnd> {  
   
 template <PathParserMode PARSER_MODE, CompletionMode COMPLETION_MODE>  
 struct PathParser : x3::parser<PathParser<PARSER_MODE, COMPLETION_MODE>> {  
-    using attribute_type = ModeToAttribute<PARSER_MODE>;  
+    using attribute_type = typename ModeToAttribute<PARSER_MODE>::type;  
+    static bool const has_attribute = true;  
     std::function<bool(const Schema&, const std::string& path)> m_filterFunction;  
   
     PathParser(const std::function<bool(const Schema&, const std::string& path)>& filterFunction = [](const auto&, const auto&) { return true; })  
@@ -338,7 +339,7 @@ struct PathParser : x3::parser<PathParser<PARSER_MODE, COMPLETION_MODE>> {  
 // The PathParser class would get a boost::variant as the attribute, but I  
 // don't want to deal with that, so I use these wrappers to ensure the  
 // attribute I want (and let Spirit deal with boost::variant).  
-auto const anyPath = x3::rule<class anyPath_class, AnyPath>{"anyPath"} = PathParser<PathParserMode::AnyPath, CompletionMode::Schema>{};  
+const PathParser<PathParserMode::AnyPath, CompletionMode::Schema> anyPath{};  
 auto const dataPath = x3::rule<class dataPath_class, dataPath_>{"dataPath"} = PathParser<PathParserMode::DataPath, CompletionMode::Data>{};  
 auto const dataPathListEnd = x3::rule<class dataPath_class, dataPath_>{"dataPath"} = PathParser<PathParserMode::DataPathListEnd, CompletionMode::Data>{};  
```  
The original error is fixed, but I get another error: https://gist.github.com/syyyr/edeb2bdd66ae345e6843d3fc8ea04a59  
I think the problem now is that Spirit is giving the wrong type to `attr`. I usually fix that problem by wrapping my parsers with x3::rule and enforce the correct attribute, but you suggested that I get rid of the x3::rule. Maybe something else is missing?

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-10-19 13:48:55 UTC  
> Updated at: 2021-10-19 15:02:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/701#issuecomment-946743099  

Unfortunately, if your parser produces a variant type and you use it in an alternative parser which parses into a variant attribute which is not exactly the same type (e.g. "broader") -- you will hit the bug. If you cannot skip the release you could essentially get a fix https://github.com/boostorg/spirit/pull/702 by overriding the `variant_find_substitute` with a specialization:  
```cpp  
#if BOOST_VERSION <= 107700  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
    // backport the fix https://github.com/boostorg/spirit/pull/702  
    template <typename... Types, typename T>  
    struct variant_find_substitute<boost::variant<Types...>, T>  
    {  
       using variant_type = boost::variant<Types...>;  
       ... // copy the rest content of the fixed variant_find_substitute  
    }  
}  
#endif  
```
