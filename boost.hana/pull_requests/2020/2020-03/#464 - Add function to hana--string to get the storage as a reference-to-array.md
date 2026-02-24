# #464 Add function to hana::string to get the storage as a reference-to-array. [Open]

> Username: jonesmz  
> Created at: 2020-03-15 08:49:44 UTC  
> Updated at: 2020-03-15 10:02:04 UTC  
> Url: https://github.com/boostorg/hana/pull/464  

I want to use hana::string to construct the function name provided to the boost::log macro BOOST_LOG_NAMED_SCOPE, but that macro ultimately constructs an object who's constructor has this signature:  
  
```cpp  
    template< typename T, size_type LenV >  
    BOOST_CONSTEXPR basic_string_literal(T(&p)[LenV]) BOOST_NOEXCEPT;  
```  
Adding the "c_str_ref()" function to hana::string provides a very straightforward way to resolve this need.  
  
For example:  
```cpp  
BOOST_LOG_NAMED_SCOPE(BOOST_HANA_STRING("my_function_name_here").c_str_ref());  
```  
  
Of course, the example above is a very simplified example. The real use case is to to support passing the result of compile-time string manipulation logic into the macro.

---

## Comment 1

> Username: jonesmz  
> Created_at: 2020-03-15 10:02:03 UTC  
> Url: https://github.com/boostorg/hana/pull/464#issuecomment-599189688  

To provide more context, I want to do this:  
  
https://godbolt.org/z/TK9szF  
  
```cpp  
#include <boost/hana/string.hpp>  
#include <boost/log/attributes/named_scope.hpp>  
#include <string_view>  
  
template<typename METASTR,  size_t ... INDICIES>  
constexpr auto build_hana_string_from_string_literal(std::index_sequence<INDICIES...>)  
{  
    return boost::hana::string<METASTR{}.chars[INDICIES]...>{};  
}  
  
template<size_t OFFSET, size_t ... INDICIES>  
constexpr auto offset_index_sequence(std::index_sequence<INDICIES...>)  
{  
    return std::index_sequence<OFFSET+INDICIES...>{};  
}  
  
template <std::size_t OFFSET, std::size_t N>  
constexpr auto make_offset_index_sequence()  
{  
    return offset_index_sequence<OFFSET>(std::make_index_sequence<N>{});  
}  
  
template<typename METASTR>  
constexpr auto build_funcname_string(void)  
{  
    constexpr std::string_view view{METASTR{}.chars};  
    constexpr size_t end   = view.find('(');  
    constexpr size_t start = view.rfind(' ', end) + 1;  
    constexpr auto functionNameWithNamespace = build_hana_string_from_string_literal<METASTR>(  
                                                   make_offset_index_sequence<start, end-start>());  
    return functionNameWithNamespace + BOOST_HANA_STRING("()");  
}  
  
#define CUSTOM_LOG_NAMED_SCOPE_INTERNAL(raw, eval, outcome, file, line, type) \  
    static constexpr const char * raw  = BOOST_CURRENT_FUNCTION;              \  
    static constexpr const auto & eval = []()                                 \  
    {                                                                         \  
        struct MetaStr { const char * const chars = raw; };                   \  
        return build_funcname_string<MetaStr>();                              \  
    }().c_str_ref();                                                          \  
    boost::log::attributes::named_scope::sentry outcome(eval, file, line, type)  
  
// Inspired from boost/log/attributes/named_scope.hpp  
#define CUSTOM_LOG_FUNCTION() \  
    CUSTOM_LOG_NAMED_SCOPE_INTERNAL(BOOST_LOG_UNIQUE_IDENTIFIER_NAME(_scope_name_raw),     \  
                                    BOOST_LOG_UNIQUE_IDENTIFIER_NAME(_scope_name_eval),    \  
                                    BOOST_LOG_UNIQUE_IDENTIFIER_NAME(_scope_name_outcome), \  
                                    __FILE__, __LINE__, boost::log::attributes::named_scope_entry::function)  
  
void foo(void)  
{  
    CUSTOM_LOG_FUNCTION(); // Generates the scope attribute "foo()"  
}  
  
int main()  
{  
    CUSTOM_LOG_FUNCTION(); // Generates the scope attribute "main()"  
  
    foo(); // results in a scope attribute for the logger of main()->foo()  
    return 0;  
}  
```  
  
Aside from the missing c_str_ref() function, which this PR adds, this works great on both Clang 9 and GCC 9.  
  
I've implemented this myself with a very similar meta string class that's inspired by hana::string, but would much rather use hana::string directly.

---
