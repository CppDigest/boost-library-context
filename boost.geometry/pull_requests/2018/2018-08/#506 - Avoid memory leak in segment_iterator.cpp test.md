# #506 Avoid memory leak in segment_iterator.cpp test [Merged]

> Username: springmeyer  
> Created at: 2018-08-12 18:35:08 UTC  
> Updated at: 2018-11-26 18:16:04 UTC  
> Merged at: 2018-08-13 14:35:01 UTC  
> Closed at: 2018-08-13 14:35:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/506  

This fixes this memory leak:  
  
```  
=================================================================  
==8204==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 8 byte(s) in 1 object(s) allocated from:  
    #0 0x101c3c592 in wrap__Znwm (/Users/danespringmeyer/projects/boost/libs/geometry/mason_packages/.link/lib/clang/5.0.1/lib/darwin/libclang_rt.asan_osx_dynamic.dylib:x86_64h+0x63592)  
    #1 0x100c6127a in test_linestring_of_point_pointers::test_method() (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10022027a)  
    #2 0x100c5e802 in test_linestring_of_point_pointers_invoker() (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10021d802)  
    #3 0x1011fbb89 in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1007bab89)  
    #4 0x100cff1de in boost::function0<void>::operator()() const (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1002be1de)  
    #5 0x101170e81 in boost::detail::forward::operator()() (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10072fe81)  
    #6 0x10116fc86 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10072ec86)  
    #7 0x10116de50 in boost::function0<int>::operator()() const (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10072ce50)  
    #8 0x100b4cf60 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10010bf60)  
    #9 0x100b4c474 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10010b474)  
    #10 0x100b4dbaa in boost::execution_monitor::execute(boost::function<int ()> const&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x10010cbaa)  
    #11 0x100ae3f64 in boost::execution_monitor::vexecute(boost::function<void ()> const&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1000a2f64)  
    #12 0x100b1ed95 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1000ddd95)  
    #13 0x100b3c124 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1000fb124)  
    #14 0x100b34121 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1000f3121)  
    #15 0x100b1b24e in boost::unit_test::framework::run(unsigned long, bool) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1000da24e)  
    #16 0x100bfcc1f in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1001bbc1f)  
    #17 0x100bfe2f0 in main (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/iterators/iterators_segment_iterator.test/clang-darwin-5.0/debug/iterators_segment_iterator:x86_64+0x1001bd2f0)  
    #18 0x7fff6c6d8014 in start (/usr/lib/system/libdyld.dylib:x86_64+0x1014)  
```  
  
This appears to have landed in https://github.com/boostorg/geometry/commit/424ba7431cc3c155d05e613a3ff0e88029e0a21a

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2018-08-12 21:20:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/506#issuecomment-412372536  

Hi @springmeyer ,  
Thanks for this as well, also here I'm of course OK with merging it.  
Cheers, Barend

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-08-13 12:58:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/506#issuecomment-412508946  

@awulkiew Is it waiting for you to give green light, do you want to merge yourself?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2018-08-13 14:34:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/506#issuecomment-412539781  

I don't know. Of course I'm ok with it. ;)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-08-13 16:08:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/506#issuecomment-412572532  

@awulkiew Since you've been the active merger I thought you may wish to filter all incoming changes (eg. in case you have some other priorities enqueued first).

---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-08-13 16:28:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/506#issuecomment-412579182  

@mloskot ok, I get it, thanks.

---
