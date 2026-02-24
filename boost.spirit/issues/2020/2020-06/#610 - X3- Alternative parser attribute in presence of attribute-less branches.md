# #610 - X3: Alternative parser attribute in presence of attribute-less branches [Open]

> Username: Kojoley  
> Created at: 2020-06-08 20:03:20 UTC  
> Updated at: 2025-07-07 16:11:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/610  

In #607 I rewrote the alternative parser attribute deducer to not use MPL, and while it is equivalent to the original, I forgot that the original was very questionable.  
  
It:  
1) Does not remove duplicates, so for `int_ | int_` the attribute will be `variant<int, int>` while it should be `int`  
2) It removes `unused_type`, so for `int_ | eps` the attribute will be `int`  
  
```cpp  
template <typename Parser, typename Context = boost::spirit::x3::unused_type>  
void print_parser_attribute_type(Parser const&)  
{  
    using attr_type = typename boost::spirit::x3::traits::attribute_of<Parser, Context>::type;  
    std::cout << boost::core::demangle(typeid(attr_type).name()) << '\n';  
}  
  
int main()  
{  
    using namespace boost::spirit::x3;  
    print_parser_attribute_type(int_ | int_);  
    print_parser_attribute_type(int_ | eps);  
}  
```  
  
While deduplication is a clear and simple issue, the `unused_type` part is tricky, and it also goes against the documentation https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/quick_reference/compound_attribute_rules.html#spirit_x3.quick_reference.compound_attribute_rules.compound_parser_attribute_types:  
  
```  
a: A, b: B --> (a | b): variant<A, B>  
a: A, b: Unused --> (a | b): optional<A>  
a: A, b: B, c: Unused --> (a | b | c): optional<variant<A, B> >  
a: Unused, b: B --> (a | b): optional<B>  
a: Unused, b: Unused --> (a | b): Unused  
a: A, b: A --> (a | b): A  
```  
  
However, I propose to not turn `int_ | bool_ | eps` into `optional<variant<int, bool>>`, instead leave `unused_type` and make it the first type in the variant, so for `int_ | bool_ | eps` the attribute is `variant<unused_type, int, bool>`. Making the first type `unused_type` means for variant that `unused_type` is constructed in default constructor, this is what we needed when the branch does not assign the value. Probably we need a trait when someone wants `std::monostate` instead of `unused_type`.  
For the case when alternative parser has an attribute of `variant<unused_type, T>` convert it to `optional<T>`, nothing new here, the same as in the documentation.

---

## Comment 1

> Username: saki7  
> Created at: 2025-06-28 00:54:55 UTC  
> Updated at: 2025-07-07 16:11:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/610#issuecomment-3014781535  

Godbolt for the current codebase: <https://godbolt.org/z/v79P53jds>  
  
---  
  
> 1. Does not remove duplicates, so for `int_ | int_` the attribute will be `variant<int, int>` while it should be `int`  
  
I've been using X3 for a long time, and this was surprising. The godbolt above shows it's real, but I've never encountered such redundant variant types in practice. For example, I tend to define parsers like `any_expr = expr_a | expr_b | expr_c` and I always manage to deserialize that into `Expression expr = do_parse();`. In other words, I've never experienced a compilation error that mentions something like `boost::variant<Expression, Expression, Expression>`.   
  
It turns out that `boost::variant` is historically being super permissive about such types, and handles subset-like assignments flexibly. We should dedupe the redundant types on our side, since it indicates a serious overhead in our implementation details.  
  
> 2. It removes `unused_type`, so for `int_ | eps` the attribute will be `int`  
  
The sanitization (or removal) of `unused_type` in parsers seems to be a very fundamental part of X3's attribute normalization. As you mentioned the behavior comes from the original MPL version, and the behavior seems persisted until today.  
  
If we are to change the behavior to keep `unused_type` in e.g. `int_ | eps`, that's going to be a *huge* design shift that invalidates many existing codes. We should embrace the status quo, and fix the documentation, not the code.  
  
Exposing *real* `unused_type` on the final variant type (as in `variant<unused_type, A, B>`) is a very bad idea that can lead to surprising results. There even was a PR that highlighted the risk; see https://github.com/boostorg/spirit/pull/740#issuecomment-1301105602. Furthermore, I can't recall any existing core functionality that *materializes* the real `unused_type`.  
  
Choosing other blank types (such as `std::monostate`) does not solve real issues; it will just expose unintuitive *placeholders* to users. I personally never expected to see the "blank" type on expressions like `int_ | eps`―in practice, such code is a common technique that temporarily introduces the `unused_type` on the one side of the branch, which I expect it to be *flattened* at last.
