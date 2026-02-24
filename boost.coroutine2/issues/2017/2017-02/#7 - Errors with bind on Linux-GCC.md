# #7 - Errors with bind on Linux/GCC [Closed]

> Username: uecasm  
> Created at: 2017-02-27 02:29:46 UTC  
> Updated at: 2017-03-01 08:16:56 UTC  
> Closed at: 2017-02-28 07:04:03 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7  

Continuing the [boost-users thread](http://lists.boost.org/boost-users/2017/02/87199.php), to avoid spamming the list too much now this is identified as a bug.  (And possibly a continuation of #4 )  
  
As suggested I tried using current develop (boostorg/context@b4e18ff6) of Boost.Context (header and lib); it made no difference.  
  
After also updating Boost.Coroutine2 to current develop (6bfa86ef5), the error changed:  
  
````  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_control_block_cc.ipp:332:44: error: call of overloaded 'callcc(const std::allocator_arg_t&, boost::context::preallocated&, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&, boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = {anonymous}::TestMethod(void*)::__lambda14]::__lambda7, {anonymous}::TestMethod(void*)::__lambda14>)' is ambiguous  
                  std::forward< Fn >( fn) ) );  
                                            ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_control_block_cc.ipp:332:44: note: candidates are:  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:474:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = {anonymous}::TestMethod(void*)::__lambda14]::__lambda7, {anonymous}::TestMethod(void*)::__lambda14>}]  
 callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:488:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = {anonymous}::TestMethod(void*)::__lambda14]::__lambda7, {anonymous}::TestMethod(void*)::__lambda14>; Arg = {}]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:519:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = {anonymous}::TestMethod(void*)::__lambda14]::__lambda7, {anonymous}::TestMethod(void*)::__lambda14>]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:462:1: note: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = {anonymous}::TestMethod(void*)::__lambda14]::__lambda7, {anonymous}::TestMethod(void*)::__lambda14>}; <template-parameter-1-3> = void]  
 callcc( Fn && fn, Arg ... arg) {  
 ^  
````

---

## Comment 1

> Username: olk  
> Created at: 2017-02-27 07:10:30 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-282644381  

boost.coroutine2 already tests bind in the unit-tests - and does not fail.  
Please provide code (preferable a unit-test) that reproduces the problem.

---

## Comment 2

> Username: uecasm  
> Created at: 2017-02-27 23:35:53 UTC  
> Updated at: 2017-02-27 23:51:47 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-282893017  

This is a complete source file that demonstrates the error using the develop sources of Context and Coroutine2 mentioned above:  
  
````  
#include <functional>  
#include <boost/coroutine2/coroutine.hpp>  
  
typedef boost::coroutines2::coroutine<void>::push_type coroutine;  
typedef boost::coroutines2::coroutine<void>::pull_type yield_context;  
typedef std::vector<coroutine> coroutine_list;  
  
struct Data  
{  
    Data() : done(false) {}  
  
    bool done;  
};  
  
template<typename R, typename F>  
coroutine_list ForEachParallel(R& r, const F& f)  
{  
    coroutine_list coros;  
    for (auto& i : r)  
    {  
        coros.emplace_back(std::bind(f, std::ref(i), std::placeholders::_1));  
    }  
    return coros;  
}  
  
struct Test  
{  
    Test();  
  
    std::vector<Data> m_data;  
    coroutine_list m_coros;  
};  
  
Test::Test()  
{  
    m_data.resize(5);  
    m_coros = ForEachParallel(m_data, [](Data& data, yield_context& yield)  
    {  
        while (!data.done) yield();  
    });  
}  
  
void main()  
{  
    Test test;  
    // we don't even need to call the coroutines to generate the error  
}  
````  
  
It could probably be further simplified, but this seems short enough.  As noted previously, equivalent code to this compiles and runs correctly with both coroutine1 and coroutine2 with VS2015, and with coroutine1 but not coroutine2 using GCC or clang.

---

## Comment 3

> Username: uecasm  
> Created at: 2017-02-27 23:54:09 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-282896495  

Actually correction: making the function parameter of ForEachParallel either `F f` or `const F& f` rather than `F& f` lets the above compile ok in clang, but still not gcc.  Although I was still getting an issue compiling my full code with clang, but perhaps the error came from a different usage.

---

## Comment 4

> Username: olk  
> Created at: 2017-02-28 07:04:03 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-282959715  

next time add the compiler out put, please!  
  
your code is ill-formed - you have to pass the functions as rvalue-references:  
  
template<typename R, typename F>  
coroutine_list ForEachParallel(R& r, F&& f)  
...  
coros.emplace_back(std::bind(std::forward<F>(f), std::ref(i), std::placeholders::_1));

---

## Comment 5

> Username: uecasm  
> Created at: 2017-02-28 09:48:11 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-282992605  

The compiler output was in the original thread that I linked at the top.  Also, not only does the error still occur if using an rvalue-reference, that usage is illegal because it means that bind could potentally move-from the same object multiple times.  While that is **probably** harmless on a lambda object, it's still bad practice.

---

## Comment 6

> Username: olk  
> Created at: 2017-02-28 10:58:43 UTC  
> Updated at: 2017-02-28 11:05:46 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283009152  

I was able to compile your code (with modifications) with gcc-6.3.1, clang-3.9.1, intel-17.0.1 - I used the code from branch develop

---

## Comment 7

> Username: olk  
> Created at: 2017-02-28 11:05:09 UTC  
> Updated at: 2017-02-28 11:09:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283010526  

your code generates errors like:  
  
clang:  
no matching function for call to 'ForEachParallel'  
    m_coros = ForEachParallel(m_data, [](Data& data, yield_context& yield)  
              ^~~~~~~~~~~~~~~  
fibonacci.cpp:26:16: note: candidate function [with R = std::vector<Data, std::allocator<Data> >, F = (lambda at fibonacci.cpp:47:39)] not viable: expects an l-value for 2nd argument  
coroutine_list ForEachParallel(R& r, F& f)  
  
intel:  
no instance of function template "ForEachParallel" matches the argument list  
            argument types are: (std::vector<Data, std::allocator<Data>>, lambda [](Data &, yield_context &)->void)  
      m_coros = ForEachParallel(m_data, [](Data& data, yield_context& yield)  
fibonacci.cpp(26): note: this candidate was rejected because arguments do not match  
  coroutine_list ForEachParallel(R& r, F& f)

---

## Comment 8

> Username: olk  
> Created at: 2017-02-28 11:56:53 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283020448  

With  
  
template<typename R, typename F>  
coroutine_list ForEachParallel(R& r, F&& f)  
  
F&& -> F& (reference collapsing) for lvalue-references  
F&& a rvalue-reference for temporaries  
  
keep in mind that 'f' becomes in both cases an lvalue

---

## Comment 9

> Username: uecasm  
> Created at: 2017-02-28 23:20:34 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283193604  

Yes, but using `std::forward` on that lvalue will convert it back to an rvalue reference (if it was not originally an lvalue), which might get moved-from when passed on (to `bind` in this case), so it's illegal to do that inside a loop.  You're correct that the parameter can be specified as `F&&` without issue, but that's of little practical benefit and might be confusing.  `const F&` is safest.  
  
----  
  
The error matching lvalue second parameter sounds like you're using the wrong version of the code -- I had accidentally omitted the `const` in the original post, which I corrected a few minutes later; see the updated version of the code in the issue.  (An unfortunate side effect of VC's non-const extension rules, so it compiled fine there.)  This is not the error I'm talking about.  
  
Again the "real" error messages were posted in the original Boost thread, but for avoidance of confusion here they are again (updated for this test code):  
  
````  
In file included from /mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/coroutine.hpp:49:0,  
                 from /mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/coroutine.hpp:15,  
                 from test.cpp:3:  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_control_block_cc.ipp: In instantiation of ‘boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]’:  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/create_control_block.ipp:51:68:   required from ‘ControlBlock* boost::coroutines2::detail::create_control_block(StackAllocator, Fn&&) [with ControlBlock = boost::coroutines2::detail::push_coroutine<void>::control_block; StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]’  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_coroutine.ipp:158:81:   required from ‘boost::coroutines2::detail::push_coroutine<void>::push_coroutine(StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]’  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_coroutine.ipp:153:62:   required from ‘boost::coroutines2::detail::push_coroutine<void>::push_coroutine(Fn&&) [with Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>; <template-parameter-1-2> = void]’  
/usr/include/c++/4.8/ext/new_allocator.h:120:4:   required from ‘void __gnu_cxx::new_allocator<_Tp>::construct(_Up*, _Args&& ...) [with _Up = boost::coroutines2::detail::push_coroutine<void>; _Args = {std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>}; _Tp = boost::coroutines2::detail::push_coroutine<void>]’  
/usr/include/c++/4.8/bits/alloc_traits.h:254:4:   required from ‘static typename std::enable_if<std::allocator_traits<_Alloc>::__construct_helper<_Tp, _Args>::value, void>::type std::allocator_traits<_Alloc>::_S_construct(_Alloc&, _Tp*, _Args&& ...) [with _Tp = boost::coroutines2::detail::push_coroutine<void>; _Args = {std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>}; _Alloc = std::allocator<boost::coroutines2::detail::push_coroutine<void> >; typename std::enable_if<std::allocator_traits<_Alloc>::__construct_helper<_Tp, _Args>::value, void>::type = void]’  
/usr/include/c++/4.8/bits/alloc_traits.h:393:57:   required from ‘static decltype (_S_construct(__a, __p, (forward<_Args>)(std::allocator_traits::construct::__args)...)) std::allocator_traits<_Alloc>::construct(_Alloc&, _Tp*, _Args&& ...) [with _Tp = boost::coroutines2::detail::push_coroutine<void>; _Args = {std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>}; _Alloc = std::allocator<boost::coroutines2::detail::push_coroutine<void> >; decltype (_S_construct(__a, __p, (forward<_Args>)(std::allocator_traits::construct::__args)...)) = <type error>]’  
/usr/include/c++/4.8/bits/vector.tcc:97:40:   required from ‘void std::vector<_Tp, _Alloc>::emplace_back(_Args&& ...) [with _Args = {std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>}; _Tp = boost::coroutines2::detail::push_coroutine<void>; _Alloc = std::allocator<boost::coroutines2::detail::push_coroutine<void> >]’  
test.cpp:22:9:   required from ‘coroutine_list ForEachParallel(R&, const F&) [with R = std::vector<Data>; F = Test::Test()::__lambda6; coroutine_list = std::vector<boost::coroutines2::detail::push_coroutine<void> >]’  
test.cpp:41:6:   required from here  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_control_block_cc.ipp:332:44: error: call of overloaded ‘callcc(const std::allocator_arg_t&, boost::context::preallocated&, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&, boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]::__lambda5, std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)> >)’ is ambiguous  
                  std::forward< Fn >( fn) ) );  
                                            ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/push_control_block_cc.ipp:332:44: note: candidates are:  
In file included from /mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/pull_control_block_cc.hpp:14:0,  
                 from /mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/detail/coroutine.hpp:37,  
                 from /mnt/ThirdParty/boost/boost_1_63_0/include/boost/coroutine2/coroutine.hpp:15,  
                 from test.cpp:3:  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:474:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]::__lambda5, std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)> >}]  
 callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:488:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]::__lambda5, std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)> >; Arg = {}]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:519:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]::__lambda5, std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)> >]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
 ^  
/mnt/ThirdParty/boost/boost_1_63_0/include/boost/context/continuation.hpp:462:1: note: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::coroutines2::detail::wrapper<boost::coroutines2::detail::push_coroutine<void>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)>]::__lambda5, std::_Bind<Test::Test()::__lambda6(std::reference_wrapper<Data>, std::_Placeholder<1>)> >}; <template-parameter-1-3> = void]  
 callcc( Fn && fn, Arg ... arg) {  
 ^  
````  
  
Perhaps this is a bug in gcc 4.8 then.  So I'll just continue using coroutine1 in the meantime.

---

## Comment 10

> Username: olk  
> Created at: 2017-03-01 05:47:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283251090  

as I suggested - do you use the code from branch develop (e.g. forthcoming boost-1.64)?  
  
if I change you code to:  
  
coroutine_list ForEachParallel(R& r, F f)  
  
or  
  
coroutine_list ForEachParallel(R& r, F const& f)  
  
it still compiles for gcc-6.3.1, clang-3.9.1 and intel-17.0.1  
  
unfortunately, I can't reproduce your problem

---

## Comment 11

> Username: uecasm  
> Created at: 2017-03-01 08:14:21 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283273684  

Yes, I am using the develop code, and it does compile on clang-3.4.  It does not compile on gcc-4.8, as shown above.  If it does not reproduce on gcc-6.3, then it's probably a gcc bug, as I said before.

---

## Comment 12

> Username: uecasm  
> Created at: 2017-03-01 08:16:56 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/7#issuecomment-283274148  

If it helps, the build machine I'm using at the moment is based on Ubuntu trusty.  gcc 4.8 is the standard compiler in that version, which is why it's using it.
