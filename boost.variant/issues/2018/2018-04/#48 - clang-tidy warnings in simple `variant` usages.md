# #48 - clang-tidy warnings in simple `variant` usages [Closed]

> Username: akrzemi1  
> Created at: 2018-04-26 11:55:47 UTC  
> Updated at: 2018-05-24 07:02:49 UTC  
> Closed at: 2018-05-23 19:05:38 UTC  
> Url: https://github.com/boostorg/variant/issues/48  

clang-tidy (version 7) gives me a "[clang-analyzer-core.uninitialized.Assign]" when analyzing the following program with `boost::variant`:  
  
```c++  
#include <boost/variant.hpp>  
  
struct B {};  
  
struct A  
{  
  bool b;  
  A() : b(true) {}  
  A& operator=(A&& r) { b = r.b;  return *this; }  
  A& operator=(const A& r) = default;  
  A(A const&) = default;  
  A(A &&) = default;  
};  
  
int main()  
{  
  boost::variant<A, B> variantA;  
  variantA = B{};  
}  
```  
  
Boost version is 1.65.  
  
It says (correctly or not) that `A::b` may be read from without having been initialized.  
  
The warning does not appear when I use `int` instead of `B` or when I declare the move assignment of `A` as defaulted.  
  
This may be a bug in `boost::variant` or a false positive in clang-tidy. I am unable to determine it myself. Here is the full report from clang-tidy:  
  
```  
main.cpp:9:27: warning: Assigned value is garbage or undefined [clang-analyzer-core.uninitialized.Assign]  
  A& operator=(A&& r) { b = r.b;  return *this; }  
                          ^  
main.cpp:18:2: note: Calling 'variant::operator='  
        variantA = B{};  
        ^  
boost-root/include/boost/variant/variant.hpp:2197:9: note: Calling 'variant::move_assign'  
        move_assign( detail::variant::move(rhs) );  
        ^  
boost-root/include/boost/variant/variant.hpp:2170:9: note: Taking true branch  
        if (this->apply_visitor(direct_move) == false)  
        ^  
boost-root/include/boost/variant/variant.hpp:2178:21: note: Calling constructor for 'variant'  
            variant temp( detail::variant::move(rhs) );  
                    ^  
boost-root/include/boost/variant/variant.hpp:1798:9: note: Calling 'variant::convert_construct'  
        convert_construct( detail::variant::move(operand), 1L);  
        ^  
boost-root/include/boost/variant/variant.hpp:1627:15: note: Calling 'initializer_node::initialize'  
              initializer::initialize(  
              ^  
boost-root/include/boost/variant/detail/initializer.hpp:119:30: note: Assuming 'value' is 0  
                BOOST_ASSERT(!is_reference_content_t::value);  
                             ^  
boost-root/include/boost/assert.hpp:60:36: note: expanded from macro 'BOOST_ASSERT'  
# define BOOST_ASSERT(expr) assert(expr)  
                                   ^  
/usr/include/assert.h:89:5: note: expanded from macro 'assert'  
  ((expr)                                                               \  
    ^  
boost-root/include/boost/variant/detail/initializer.hpp:119:17: note: '?' condition is true  
                BOOST_ASSERT(!is_reference_content_t::value);  
                ^  
boost-root/include/boost/assert.hpp:60:29: note: expanded from macro 'BOOST_ASSERT'  
# define BOOST_ASSERT(expr) assert(expr)  
                            ^  
/usr/include/assert.h:89:4: note: expanded from macro 'assert'  
  ((expr)                                                               \  
   ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:17: note: Calling 'operator new'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                ^  
/opt/atseintl/adm/thirdparty/gcc-6.4.0-multilib/bin/../lib/gcc/x86_64-pc-linux-gnu/6.4.0/../../../../include/c++/6.4.0/new:147:3: note: Returning without writing to '__p'  
{ return __p; }  
  ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:17: note: Returning from 'operator new'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:36: note: Calling 'move'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                                   ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:36: note: Returning from 'move'  
boost-root/include/boost/variant/detail/initializer.hpp:123:17: note: Returning without writing to 'dest'  
                return BOOST_MPL_AUX_VALUE_WKND(index)::value; // which  
                ^  
boost-root/include/boost/variant/variant.hpp:1627:15: note: Returning from 'initializer_node::initialize'  
              initializer::initialize(  
              ^  
boost-root/include/boost/variant/variant.hpp:1798:9: note: Returning from 'variant::convert_construct'  
        convert_construct( detail::variant::move(operand), 1L);  
        ^  
boost-root/include/boost/variant/variant.hpp:2178:21: note: Returning from constructor for 'variant'  
            variant temp( detail::variant::move(rhs) );  
                    ^  
boost-root/include/boost/variant/variant.hpp:2179:29: note: Calling 'move'  
            variant_assign( detail::variant::move(temp) );  
                            ^  
boost-root/include/boost/move/utility_core.hpp:213:12: note: Returning without writing to 't'  
         { return static_cast<typename ::boost::move_detail::remove_reference<T>::type &&>(t); }  
           ^  
boost-root/include/boost/variant/variant.hpp:2179:29: note: Returning from 'move'  
            variant_assign( detail::variant::move(temp) );  
                            ^  
boost-root/include/boost/variant/variant.hpp:2179:13: note: Calling 'variant::variant_assign'  
            variant_assign( detail::variant::move(temp) );  
            ^  
boost-root/include/boost/variant/variant.hpp:2129:13: note: Assuming the condition is true  
        if (which_ == rhs.which_)  
            ^  
boost-root/include/boost/variant/variant.hpp:2129:9: note: Taking true branch  
        if (which_ == rhs.which_)  
        ^  
boost-root/include/boost/variant/variant.hpp:2132:51: note: Calling 'aligned_storage::address'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                                  ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Calling 'aligned_storage_imp::address'  
        return static_cast<type*>(this)->address();  
               ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Returning from 'aligned_storage_imp::address'  
boost-root/include/boost/variant/variant.hpp:2132:51: note: Returning from 'aligned_storage::address'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                                  ^  
boost-root/include/boost/variant/variant.hpp:2132:43: note: Calling constructor for 'move_storage'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                          ^  
boost-root/include/boost/variant/variant.hpp:585:14: note: Calling defaulted default constructor for 'static_visitor'  
    explicit move_storage(void* rhs_storage) BOOST_NOEXCEPT  
             ^  
boost-root/include/boost/variant/static_visitor.hpp:52:5: note: Calling implicit default constructor for 'is_static_visitor_tag'  
    static_visitor() = default;  
    ^  
boost-root/include/boost/variant/static_visitor.hpp:52:5: note: Returning from default constructor for 'is_static_visitor_tag'  
boost-root/include/boost/variant/variant.hpp:585:14: note: Returning from default constructor for 'static_visitor'  
    explicit move_storage(void* rhs_storage) BOOST_NOEXCEPT  
             ^  
boost-root/include/boost/variant/variant.hpp:588:5: note: Returning without writing to 'rhs_storage'  
    }  
    ^  
boost-root/include/boost/variant/variant.hpp:2132:43: note: Returning from constructor for 'move_storage'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                          ^  
boost-root/include/boost/variant/variant.hpp:2133:13: note: Calling 'variant::internal_apply_visitor'  
            this->internal_apply_visitor(visitor);  
            ^  
boost-root/include/boost/variant/variant.hpp:2407:23: note: Calling 'variant::which'  
              which_, which(), visitor, storage_.address()  
                      ^  
boost-root/include/boost/variant/variant.hpp:1394:13: note: Calling 'variant::using_backup'  
        if (using_backup())  
            ^  
boost-root/include/boost/variant/variant.hpp:1394:13: note: Returning from 'variant::using_backup'  
boost-root/include/boost/variant/variant.hpp:1394:9: note: Taking false branch  
        if (using_backup())  
        ^  
boost-root/include/boost/variant/variant.hpp:2407:23: note: Returning from 'variant::which'  
              which_, which(), visitor, storage_.address()  
                      ^  
boost-root/include/boost/variant/variant.hpp:2407:41: note: Calling 'aligned_storage::address'  
              which_, which(), visitor, storage_.address()  
                                        ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Calling 'aligned_storage_imp::address'  
        return static_cast<type*>(this)->address();  
               ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Returning from 'aligned_storage_imp::address'  
boost-root/include/boost/variant/variant.hpp:2407:41: note: Returning from 'aligned_storage::address'  
              which_, which(), visitor, storage_.address()  
                                        ^  
boost-root/include/boost/variant/variant.hpp:2406:16: note: Calling 'variant::internal_apply_visitor_impl'  
        return internal_apply_visitor_impl(  
               ^  
boost-root/include/boost/variant/variant.hpp:2392:16: note: Calling 'visitation_impl'  
        return detail::variant::visitation_impl(  
               ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:225:5: note: Control jumps to 'case 0:'  at line 238  
    switch (logical_which)  
    ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:240:11: note: Calling 'visitation_impl_invoke'  
        , BOOST_VARIANT_AUX_APPLY_VISITOR_STEP_CASE  
          ^  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:29:26: note: expanded from macro 'BOOST_PP_REPEAT'  
# define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                         ^  
boost-root/include/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
boost-root/include/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 21 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:53:56: note: expanded from macro 'BOOST_PP_REPEAT_1_2'  
# define BOOST_PP_REPEAT_1_2(m, d) BOOST_PP_REPEAT_1_1(m, d) m(2, 1, d)  
                                                       ^  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:52:36: note: expanded from macro 'BOOST_PP_REPEAT_1_1'  
# define BOOST_PP_REPEAT_1_1(m, d) m(2, 0, d)  
                                   ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:231:16: note: expanded from macro 'BOOST_VARIANT_AUX_APPLY_VISITOR_STEP_CASE'  
        return (visitation_impl_invoke)( \  
               ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:154:12: note: Calling 'visitation_impl_invoke_impl'  
    return (visitation_impl_invoke_impl)(  
           ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:113:11: note: Calling 'cast_storage'  
          cast_storage<T>(storage), 1L  
          ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:113:11: note: Returning from 'cast_storage'  
boost-root/include/boost/variant/detail/visitation_impl.hpp:112:12: note: Calling 'move_storage::internal_visit'  
    return visitor.internal_visit(  
           ^  
boost-root/include/boost/variant/variant.hpp:621:23: note: Calling 'move'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
                      ^  
boost-root/include/boost/move/utility_core.hpp:213:12: note: Returning without writing to 't.b'  
         { return static_cast<typename ::boost::move_detail::remove_reference<T>::type &&>(t); }  
           ^  
boost-root/include/boost/variant/variant.hpp:621:23: note: Returning from 'move'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
                      ^  
boost-root/include/boost/variant/variant.hpp:621:9: note: Calling move assignment operator for 'A'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
        ^  
main.cpp:9:27: note: Assigned value is garbage or undefined  
  A& operator=(A&& r) { b = r.b;  return *this; }  
                          ^  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-05-02 07:42:35 UTC  
> Url: https://github.com/boostorg/variant/issues/48#issuecomment-385891764  

**Part** of this is a false positive.  
  
The expression `variantA = B{};` is treated as `variantA = boost::variant<A, B>{B{}};`. In that case variant constructed from `B{}` has an empty uninitialized internal storage (`B` is empty and it's default contructor does nothing). Change `B` to `int`, and first 4 bytes of storage would be zero initialized.  
  
When the assignment `variantA = temp-variant` happens code is generated as if `temp-variant` could have `A` and `B` types in it. So when assigning to `variantA` an imaginary `A` from `temp-variant` that actually has uninitialized storage - the warning appears.  
  
**But** there are still some suspicious places:   
  
Why the `= default` fixes the warning - I'm not quire sure. Probably the implicitly added `noexcept` on `operator=` makes the variant more sure in it's content and it does less copyings/movings of internal storage. Could you please check that by adding `noexcept` to the `operator=` in your example?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-05-02 09:12:01 UTC  
> Url: https://github.com/boostorg/variant/issues/48#issuecomment-385914251  

I will only be able to check it in a couple of days. I will get back to you with the test results.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2018-05-07 11:24:19 UTC  
> Url: https://github.com/boostorg/variant/issues/48#issuecomment-387035677  

It may be that `= default` makes the class trivially copyable. Declaring the assignment `noexcept` gives similar results:  
  
```  
main.cpp:9:36: warning: Assigned value is garbage or undefined [clang-analyzer-core.uninitialized.Assign]  
  A& operator=(A&& r) noexcept { b = r.b;  return *this; }  
                                   ^  
main.cpp:18:2: note: Calling 'variant::operator='  
        variantA = B{};  
        ^  
boost-root/include/boost/variant/variant.hpp:2197:9: note: Calling 'variant::move_assign'  
        move_assign( detail::variant::move(rhs) );  
        ^  
boost-root/include/boost/variant/variant.hpp:2170:9: note: Taking true branch  
        if (this->apply_visitor(direct_move) == false)  
        ^  
boost-root/include/boost/variant/variant.hpp:2178:21: note: Calling constructor for 'variant'  
            variant temp( detail::variant::move(rhs) );  
                    ^  
boost-root/include/boost/variant/variant.hpp:1798:9: note: Calling 'variant::convert_construct'  
        convert_construct( detail::variant::move(operand), 1L);  
        ^  
boost-root/include/boost/variant/variant.hpp:1627:15: note: Calling 'initializer_node::initialize'  
              initializer::initialize(  
              ^  
boost-root/include/boost/variant/detail/initializer.hpp:119:30: note: Assuming 'value' is 0  
                BOOST_ASSERT(!is_reference_content_t::value);  
                             ^  
boost-root/include/boost/assert.hpp:60:36: note: expanded from macro 'BOOST_ASSERT'  
# define BOOST_ASSERT(expr) assert(expr)  
                                   ^  
/usr/include/assert.h:89:5: note: expanded from macro 'assert'  
  ((expr)                                                               \  
    ^  
boost-root/include/boost/variant/detail/initializer.hpp:119:17: note: '?' condition is true  
                BOOST_ASSERT(!is_reference_content_t::value);  
                ^  
boost-root/include/boost/assert.hpp:60:29: note: expanded from macro 'BOOST_ASSERT'  
# define BOOST_ASSERT(expr) assert(expr)  
                            ^  
/usr/include/assert.h:89:4: note: expanded from macro 'assert'  
  ((expr)                                                               \  
   ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:17: note: Calling 'operator new'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                ^  
lib/gcc/x86_64-pc-linux-gnu/6.4.0/../../../../include/c++/6.4.0/new:147:3: note: Returning without writing to '__p'  
{ return __p; }  
  ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:17: note: Returning from 'operator new'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:36: note: Calling 'move'  
                new(dest) value_T( boost::detail::variant::move(operand) );  
                                   ^  
boost-root/include/boost/variant/detail/initializer.hpp:122:36: note: Returning from 'move'  
boost-root/include/boost/variant/detail/initializer.hpp:123:17: note: Returning without writing to 'dest'  
                return BOOST_MPL_AUX_VALUE_WKND(index)::value; // which  
                ^  
boost-root/include/boost/variant/variant.hpp:1627:15: note: Returning from 'initializer_node::initialize'  
              initializer::initialize(  
              ^  
boost-root/include/boost/variant/variant.hpp:1798:9: note: Returning from 'variant::convert_construct'  
        convert_construct( detail::variant::move(operand), 1L);  
        ^  
boost-root/include/boost/variant/variant.hpp:2178:21: note: Returning from constructor for 'variant'  
            variant temp( detail::variant::move(rhs) );  
                    ^  
boost-root/include/boost/variant/variant.hpp:2179:29: note: Calling 'move'  
            variant_assign( detail::variant::move(temp) );  
                            ^  
boost-root/include/boost/move/utility_core.hpp:213:12: note: Returning without writing to 't'  
         { return static_cast<typename ::boost::move_detail::remove_reference<T>::type &&>(t); }  
           ^  
boost-root/include/boost/variant/variant.hpp:2179:29: note: Returning from 'move'  
            variant_assign( detail::variant::move(temp) );  
                            ^  
boost-root/include/boost/variant/variant.hpp:2179:13: note: Calling 'variant::variant_assign'  
            variant_assign( detail::variant::move(temp) );  
            ^  
boost-root/include/boost/variant/variant.hpp:2129:13: note: Assuming the condition is true  
        if (which_ == rhs.which_)  
            ^  
boost-root/include/boost/variant/variant.hpp:2129:9: note: Taking true branch  
        if (which_ == rhs.which_)  
        ^  
boost-root/include/boost/variant/variant.hpp:2132:51: note: Calling 'aligned_storage::address'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                                  ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Calling 'aligned_storage_imp::address'  
        return static_cast<type*>(this)->address();  
               ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Returning from 'aligned_storage_imp::address'  
boost-root/include/boost/variant/variant.hpp:2132:51: note: Returning from 'aligned_storage::address'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                                  ^  
boost-root/include/boost/variant/variant.hpp:2132:43: note: Calling constructor for 'move_storage'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                          ^  
boost-root/include/boost/variant/variant.hpp:585:14: note: Calling defaulted default constructor for 'static_visitor'  
    explicit move_storage(void* rhs_storage) BOOST_NOEXCEPT  
             ^  
boost-root/include/boost/variant/static_visitor.hpp:52:5: note: Calling implicit default constructor for 'is_static_visitor_tag'  
    static_visitor() = default;  
    ^  
boost-root/include/boost/variant/static_visitor.hpp:52:5: note: Returning from default constructor for 'is_static_visitor_tag'  
boost-root/include/boost/variant/variant.hpp:585:14: note: Returning from default constructor for 'static_visitor'  
    explicit move_storage(void* rhs_storage) BOOST_NOEXCEPT  
             ^  
boost-root/include/boost/variant/variant.hpp:588:5: note: Returning without writing to 'rhs_storage'  
    }  
    ^  
boost-root/include/boost/variant/variant.hpp:2132:43: note: Returning from constructor for 'move_storage'  
            detail::variant::move_storage visitor(rhs.storage_.address());  
                                          ^  
boost-root/include/boost/variant/variant.hpp:2133:13: note: Calling 'variant::internal_apply_visitor'  
            this->internal_apply_visitor(visitor);  
            ^  
boost-root/include/boost/variant/variant.hpp:2407:23: note: Calling 'variant::which'  
              which_, which(), visitor, storage_.address()  
                      ^  
boost-root/include/boost/variant/variant.hpp:1394:13: note: Calling 'variant::using_backup'  
        if (using_backup())  
            ^  
boost-root/include/boost/variant/variant.hpp:1394:13: note: Returning from 'variant::using_backup'  
boost-root/include/boost/variant/variant.hpp:1394:9: note: Taking false branch  
        if (using_backup())  
        ^  
boost-root/include/boost/variant/variant.hpp:2407:23: note: Returning from 'variant::which'  
              which_, which(), visitor, storage_.address()  
                      ^  
boost-root/include/boost/variant/variant.hpp:2407:41: note: Calling 'aligned_storage::address'  
              which_, which(), visitor, storage_.address()  
                                        ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Calling 'aligned_storage_imp::address'  
        return static_cast<type*>(this)->address();  
               ^  
boost-root/include/boost/type_traits/aligned_storage.hpp:120:16: note: Returning from 'aligned_storage_imp::address'  
boost-root/include/boost/variant/variant.hpp:2407:41: note: Returning from 'aligned_storage::address'  
              which_, which(), visitor, storage_.address()  
                                        ^  
boost-root/include/boost/variant/variant.hpp:2406:16: note: Calling 'variant::internal_apply_visitor_impl'  
        return internal_apply_visitor_impl(  
               ^  
boost-root/include/boost/variant/variant.hpp:2392:16: note: Calling 'visitation_impl'  
        return detail::variant::visitation_impl(  
               ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:225:5: note: Control jumps to 'case 0:'  at line 238  
    switch (logical_which)  
    ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:240:11: note: Calling 'visitation_impl_invoke'  
        , BOOST_VARIANT_AUX_APPLY_VISITOR_STEP_CASE  
          ^  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:29:26: note: expanded from macro 'BOOST_PP_REPEAT'  
# define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                         ^  
boost-root/include/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
boost-root/include/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 21 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:53:56: note: expanded from macro 'BOOST_PP_REPEAT_1_2'  
# define BOOST_PP_REPEAT_1_2(m, d) BOOST_PP_REPEAT_1_1(m, d) m(2, 1, d)  
                                                       ^  
boost-root/include/boost/preprocessor/repetition/repeat.hpp:52:36: note: expanded from macro 'BOOST_PP_REPEAT_1_1'  
# define BOOST_PP_REPEAT_1_1(m, d) m(2, 0, d)  
                                   ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:231:16: note: expanded from macro 'BOOST_VARIANT_AUX_APPLY_VISITOR_STEP_CASE'  
        return (visitation_impl_invoke)( \  
               ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:154:12: note: Calling 'visitation_impl_invoke_impl'  
    return (visitation_impl_invoke_impl)(  
           ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:113:11: note: Calling 'cast_storage'  
          cast_storage<T>(storage), 1L  
          ^  
boost-root/include/boost/variant/detail/visitation_impl.hpp:113:11: note: Returning from 'cast_storage'  
boost-root/include/boost/variant/detail/visitation_impl.hpp:112:12: note: Calling 'move_storage::internal_visit'  
    return visitor.internal_visit(  
           ^  
boost-root/include/boost/variant/variant.hpp:621:23: note: Calling 'move'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
                      ^  
boost-root/include/boost/move/utility_core.hpp:213:12: note: Returning without writing to 't.b'  
         { return static_cast<typename ::boost::move_detail::remove_reference<T>::type &&>(t); }  
           ^  
boost-root/include/boost/variant/variant.hpp:621:23: note: Returning from 'move'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
                      ^  
boost-root/include/boost/variant/variant.hpp:621:9: note: Calling move assignment operator for 'A'  
        lhs_content = ::boost::detail::variant::move(*static_cast<T* >(rhs_storage_));  
        ^  
main.cpp:9:36: note: Assigned value is garbage or undefined  
  A& operator=(A&& r) noexcept { b = r.b;  return *this; }  
```

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-05-23 19:05:38 UTC  
> Url: https://github.com/boostorg/variant/issues/48#issuecomment-391461913  

Great! There's no logic in `boost::variant` that depends on type triviality (but there is some logic that depends on type's `noexcept`).  
  
So this is a false positive and no suspicious places remain.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2018-05-24 07:02:49 UTC  
> Url: https://github.com/boostorg/variant/issues/48#issuecomment-391610820  

Hold on. I am not immediately convinced from the above explanation that it is a false positive. I can imagine that there might be an UB somewhere, that renders the correct code in most places, but gets exposed  when the type is trivially copyable. The sources of `boost::variant` may not have an explicit optimization for trivially-copyable types, but the optimization may come from the compiler.   
  
If you are convinced that this is a false positive, would it be possible to put   
```// NOLINT(clang-analyzer-core.uninitialized.Assign)```  
or  
```assert()```  
in appropriate places so that this warning does not show up?
