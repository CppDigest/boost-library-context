# #38 - macos-13 sanitizer CI build failure [Open]

> Username: jeking3  
> Created at: 2025-06-02 20:57:50 UTC  
> Updated at: 2025-06-02 20:57:50 UTC  
> Url: https://github.com/boostorg/contract/issues/38  

```  
...failed testing.capture-output bin.v2/libs/contract/test/constructor-decl_pre_ends.test/clang-darwin-15/debug/x86_64/address-sanitizer-norecover/cxxstd-11-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/constructor-decl_pre_ends.run...  
testing.capture-output bin.v2/libs/contract/test/constructor-decl_pre_all.test/clang-darwin-15/debug/x86_64/address-sanitizer-norecover/cxxstd-11-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/constructor-decl_pre_all.run  
/bin/sh: line 4: 11804 Abort trap: 6           "bin.v2/libs/contract/test/constructor-decl_pre_all.test/clang-darwin-15/debug/x86_64/address-sanitizer-norecover/cxxstd-11-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/constructor-decl_pre_all" > "bin.v2/libs/contract/test/constructor-decl_pre_all.test/clang-darwin-15/debug/x86_64/address-sanitizer-norecover/cxxstd-11-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/constructor-decl_pre_all.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
a::ctor::pre  
b::ctor::pre  
c::ctor::pre  
c::static_inv  
c::ctor::old  
c::ctor::body  
c::static_inv  
c::inv  
c::ctor::post  
b::static_inv  
b::ctor::old  
b::ctor::body  
b::static_inv  
b::inv  
b::ctor::post  
a::static_inv  
a::ctor::old  
a::ctor::body  
a::static_inv  
a::inv  
a::ctor::post  
a::ctor::pre  
boost/function/function_template.hpp:789:14: runtime error: call to function boost::detail::function::void_function_obj_invoker<main::$_0, void, boost::contract::from>::invoke(boost::detail::function::function_buffer&, boost::contract::from) through pointer to incorrect function type 'void (*)(boost::detail::function::function_buffer &, boost::contract::from)'  
function_template.hpp:94: note: boost::detail::function::void_function_obj_invoker<main::$_0, void, boost::contract::from>::invoke(boost::detail::function::function_buffer&, boost::contract::from) defined here  
    #0 0x103dfecaf in boost::function_n<void, boost::contract::from>::operator()(boost::contract::from) const function_template.hpp:789  
    #1 0x103dfe9fa in boost::contract::exception_::pre_failure_unlocked(boost::contract::from) exception.hpp:220  
    #2 0x103dfef56 in boost::contract::exception_::pre_failure_locked(boost::contract::from) exception.hpp:226  
    #3 0x103bba88f in boost::contract::precondition_failure(boost::contract::from) exception.hpp:562  
    #4 0x103bb967c in boost::contract::constructor_precondition<a>::constructor_precondition<a::a()::'lambda'()>(a::a()::'lambda'() const&) constructor_precondition.hpp:116  
    #5 0x103bb908b in a::a() decl.hpp:150  
    #6 0x103b984e6 in a::a() decl.hpp:155  
    #7 0x103b960ec in main decl_pre_all.cpp:98  
    #8 0x7ff817652417 in start+0x767 (dyld:x86_64+0xfffffffffff6e417)  
SUMMARY: UndefinedBehaviorSanitizer: function-type-mismatch boost/function/function_template.hpp:789:14 in   
EXIT STATUS: 134  
====== END OUTPUT ======  
```
