# #216 Karma.Tests: Fixed bool increment warning [Merged]

> Username: Kojoley  
> Created at: 2016-08-25 20:31:00 UTC  
> Updated at: 2017-11-12 21:57:20 UTC  
> Merged at: 2016-08-25 22:03:58 UTC  
> Closed at: 2016-08-25 22:03:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/216  

```  
warning: incrementing expression of type bool is deprecated and incompatible with C++1z [-Wdeprecated-increment-bool]  
karma/bool.cpp:120:40: note: in instantiation of template class 'boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::pre_inc, boost::proto::argsns_::list1<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::reference_wrapper<bool> >, 0> > >, 1> >' requested here  
        BOOST_TEST(test("true", bool_, ++phoenix::ref(b)));  
                                       ^  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2016-08-25 21:53:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76331972  

Hmmm, how can this be the same?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-25 21:55:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76332304  

`++phoenix::ref(b)` is lazy increment and `!phoenix::ref(b)` is lazy negation, is not it?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2016-08-25 21:58:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76332767  

At least this works for me on GCC and Clang https://travis-ci.org/Kojoley/spirit/jobs/155156461  
  
```  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_bool-p3.test  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on/karma  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/spirit/test/karma_binary3-p3.test/clang-linux-3.8/debug/coverage-on/karma/binary3.o  
clang-linux.link ../../../bin.v2/libs/spirit/test/karma_binary3-p3.test/clang-linux-3.8/debug/coverage-on/karma_binary3-p3  
testing.capture-output ../../../bin.v2/libs/spirit/test/karma_binary3-p3.test/clang-linux-3.8/debug/coverage-on/karma_binary3-p3.run  
**passed** ../../../bin.v2/libs/spirit/test/karma_binary3-p3.test/clang-linux-3.8/debug/coverage-on/karma_binary3-p3.test  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_buffer-p3.test  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_buffer-p3.test/clang-linux-3.8  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_buffer-p3.test/clang-linux-3.8/debug  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_buffer-p3.test/clang-linux-3.8/debug/coverage-on  
common.mkdir ../../../bin.v2/libs/spirit/test/karma_buffer-p3.test/clang-linux-3.8/debug/coverage-on/karma  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on/karma/bool.o  
clang-linux.link ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on/karma_bool-p3  
testing.capture-output ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on/karma_bool-p3.run  
**passed** ../../../bin.v2/libs/spirit/test/karma_bool-p3.test/clang-linux-3.8/debug/coverage-on/karma_bool-p3.test  
```

---

## Comment 4

> Username: djowel  
> Created_at: 2016-08-25 22:00:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76333054  

Yes it is, but why is ++phoenix::ref(b) removed? What did I miss?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2016-08-25 22:02:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76333257  

`warning: incrementing expression of type bool is deprecated and incompatible with C++1z [-Wdeprecated-increment-bool]`

---

## Comment 6

> Username: djowel  
> Created_at: 2016-08-25 22:03:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/216#discussion_r76333502  

Ah, I see!

---
