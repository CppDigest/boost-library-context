# #136 - Compilation error with libstdc++ and clang 3.6.1 [Closed]

> Username: camio  
> Created at: 2015-06-17 18:52:53 UTC  
> Updated at: 2015-06-28 11:21:45 UTC  
> Closed at: 2015-06-28 11:21:45 UTC  
> Url: https://github.com/boostorg/hana/issues/136  

```  
FAILED: /usr/bin/clang++   -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -DBOOST_HANA_TEST_PART=4 -I/home/david/code/hana/include -I/home/david/code/hana/test/include -I/home/david/code/hana/test    -ftemplate-backtrace-limit=0 -pedantic -std=c++1y -W -Wall -Wextra -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -MMD -MT test/CMakeFiles/test.ext.std.array.part4.dir/ext/std/array.cpp.o -MF test/CMakeFiles/test.ext.std.array.part4.dir/ext/std/array.cpp.o.d -o test/CMakeFiles/test.ext.std.array.part4.dir/ext/std/array.cpp.o -c /home/david/code/hana/test/ext/std/array.cpp  
In file included from /home/david/code/hana/test/ext/std/array.cpp:8:  
/home/david/code/hana/include/boost/hana/ext/std/array.hpp:115:39: error: implicit instantiation of undefined template 'std::tuple_size<const std::array<int, 1> >'  
            constexpr auto N = ::std::tuple_size<RawArray>::value;  
                                      ^  
/home/david/code/hana/include/boost/hana/fwd/iterable.hpp:277:26: note: in instantiation of function template specialization 'boost::hana::tail_impl<boost::hana::ext::std::Array, void>::apply<const std::array<int, 1> &>' requested here  
            return Tail::apply(static_cast<Xs&&>(xs));  
                         ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1162:35: note: in instantiation of function template specialization 'boost::hana::_tail::operator()<const std::array<int, 1> &>' requested here  
                equal_impl::apply(hana::tail(xs), hana::tail(ys))  
                                  ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1180:20: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::equal_helper<std::array<int, 1>, std::array<int, 1> >' requested here  
            return equal_helper(xs, ys, hana::if_(done, true_, false_));  
                   ^  
/home/david/code/hana/include/boost/hana/fwd/comparable.hpp:234:27: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::apply<std::array<int, 1>, std::array<int, 1> >' requested here  
            return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                          ^  
/home/david/code/hana/test/ext/std/array.cpp:81:45: note: in instantiation of function template specialization 'boost::hana::_equal<>::operator()<std::array<int, 1>, std::array<int, 1> >' requested here  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                                            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:38: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
                                     ^  
/home/david/code/hana/include/boost/hana/assert.hpp:137:14: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
            (__VA_ARGS__)                                                   \  
             ^  
/home/david/code/hana/include/boost/hana/assert.hpp:117:27: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            auto tmpvar = expr;                                             \  
                          ^  
/usr/bin/../lib64/gcc/x86_64-unknown-linux-gnu/5.1.0/../../../../include/c++/5.1.0/array:307:11: note: template is declared here  
    class tuple_size;  
          ^  
/home/david/code/hana/test/ext/std/array.cpp:81:13: error: expected expression  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:13: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:134:9: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
        BOOST_HANA_RUNTIME_CHECK_IMPL(                                      \  
        ^  
/home/david/code/hana/include/boost/hana/assert.hpp:120:17: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            >{}(),                                                          \  
                ^  
In file included from /home/david/code/hana/test/ext/std/array.cpp:8:  
/home/david/code/hana/include/boost/hana/ext/std/array.hpp:115:39: error: implicit instantiation of undefined template 'std::tuple_size<const std::array<int, 2> >'  
            constexpr auto N = ::std::tuple_size<RawArray>::value;  
                                      ^  
/home/david/code/hana/include/boost/hana/fwd/iterable.hpp:277:26: note: in instantiation of function template specialization 'boost::hana::tail_impl<boost::hana::ext::std::Array, void>::apply<const std::array<int, 2> &>' requested here  
            return Tail::apply(static_cast<Xs&&>(xs));  
                         ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1162:35: note: in instantiation of function template specialization 'boost::hana::_tail::operator()<const std::array<int, 2> &>' requested here  
                equal_impl::apply(hana::tail(xs), hana::tail(ys))  
                                  ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1180:20: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::equal_helper<std::array<int, 2>, std::array<int, 2> >' requested here  
            return equal_helper(xs, ys, hana::if_(done, true_, false_));  
                   ^  
/home/david/code/hana/include/boost/hana/fwd/comparable.hpp:234:27: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::apply<std::array<int, 2>, std::array<int, 2> >' requested here  
            return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                          ^  
/home/david/code/hana/test/ext/std/array.cpp:85:45: note: in instantiation of function template specialization 'boost::hana::_equal<>::operator()<std::array<int, 2>, std::array<int, 2> >' requested here  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                                            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:38: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
                                     ^  
/home/david/code/hana/include/boost/hana/assert.hpp:137:14: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
            (__VA_ARGS__)                                                   \  
             ^  
/home/david/code/hana/include/boost/hana/assert.hpp:117:27: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            auto tmpvar = expr;                                             \  
                          ^  
/usr/bin/../lib64/gcc/x86_64-unknown-linux-gnu/5.1.0/../../../../include/c++/5.1.0/array:307:11: note: template is declared here  
    class tuple_size;  
          ^  
/home/david/code/hana/test/ext/std/array.cpp:85:13: error: expected expression  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:13: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:134:9: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
        BOOST_HANA_RUNTIME_CHECK_IMPL(                                      \  
        ^  
/home/david/code/hana/include/boost/hana/assert.hpp:120:17: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            >{}(),                                                          \  
                ^  
In file included from /home/david/code/hana/test/ext/std/array.cpp:8:  
/home/david/code/hana/include/boost/hana/ext/std/array.hpp:115:39: error: implicit instantiation of undefined template 'std::tuple_size<const std::array<int, 3> >'  
            constexpr auto N = ::std::tuple_size<RawArray>::value;  
                                      ^  
/home/david/code/hana/include/boost/hana/fwd/iterable.hpp:277:26: note: in instantiation of function template specialization 'boost::hana::tail_impl<boost::hana::ext::std::Array, void>::apply<const std::array<int, 3> &>' requested here  
            return Tail::apply(static_cast<Xs&&>(xs));  
                         ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1162:35: note: in instantiation of function template specialization 'boost::hana::_tail::operator()<const std::array<int, 3> &>' requested here  
                equal_impl::apply(hana::tail(xs), hana::tail(ys))  
                                  ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1180:20: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::equal_helper<std::array<int, 3>, std::array<int, 3> >' requested here  
            return equal_helper(xs, ys, hana::if_(done, true_, false_));  
                   ^  
/home/david/code/hana/include/boost/hana/fwd/comparable.hpp:234:27: note: in instantiation of function template specialization 'boost::hana::Sequence::equal_impl<boost::hana::ext::std::Array, boost::hana::ext::std::Array>::apply<std::array<int, 3>, std::array<int, 3> >' requested here  
            return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                          ^  
/home/david/code/hana/test/ext/std/array.cpp:89:45: note: in instantiation of function template specialization 'boost::hana::_equal<>::operator()<std::array<int, 3>, std::array<int, 3> >' requested here  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                                            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:38: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
                                     ^  
/home/david/code/hana/include/boost/hana/assert.hpp:137:14: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
            (__VA_ARGS__)                                                   \  
             ^  
/home/david/code/hana/include/boost/hana/assert.hpp:117:27: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            auto tmpvar = expr;                                             \  
                          ^  
/usr/bin/../lib64/gcc/x86_64-unknown-linux-gnu/5.1.0/../../../../include/c++/5.1.0/array:307:11: note: template is declared here  
    class tuple_size;  
          ^  
/home/david/code/hana/test/ext/std/array.cpp:89:13: error: expected expression  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:215:13: note: expanded from macro 'BOOST_HANA_CONSTEXPR_CHECK'  
            BOOST_HANA_RUNTIME_CHECK(__VA_ARGS__)  
            ^  
/home/david/code/hana/include/boost/hana/assert.hpp:134:9: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK'  
        BOOST_HANA_RUNTIME_CHECK_IMPL(                                      \  
        ^  
/home/david/code/hana/include/boost/hana/assert.hpp:120:17: note: expanded from macro 'BOOST_HANA_RUNTIME_CHECK_IMPL'  
            >{}(),                                                          \  
                ^  
In file included from /home/david/code/hana/test/ext/std/array.cpp:7:  
In file included from /home/david/code/hana/include/boost/hana/assert.hpp:13:  
In file included from /home/david/code/hana/include/boost/hana/bool.hpp:24:  
In file included from /home/david/code/hana/include/boost/hana/lazy.hpp:15:  
In file included from /home/david/code/hana/include/boost/hana/applicative.hpp:24:  
In file included from /home/david/code/hana/include/boost/hana/functor.hpp:16:  
In file included from /home/david/code/hana/include/boost/hana/comparable.hpp:13:  
/home/david/code/hana/include/boost/hana/fwd/comparable.hpp:234:27: error: no matching member function for call to 'apply'  
            return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                   ~~~~~~~^~~~~  
/home/david/code/hana/test/laws/iterable.hpp:69:34: note: in instantiation of function template specialization 'boost::hana::_equal<>::operator()<std::array<int, 1>, std::array<int, 1> &>' requested here  
                BOOST_HANA_CHECK(hana::equal(  
                                 ^  
/home/david/code/hana/include/boost/hana/assert.hpp:187:14: note: expanded from macro 'BOOST_HANA_CHECK'  
            (__VA_ARGS__)                                                   \  
             ^  
/home/david/code/hana/include/boost/hana/assert.hpp:162:27: note: expanded from macro 'BOOST_HANA_CHECK_IMPL'  
            auto tmpvar = EXPR;                                             \  
                          ^  
/home/david/code/hana/include/boost/hana/detail/variadic/for_each.hpp:19:24: note: in instantiation of function template specialization 'boost::hana::test::TestIterable<boost::hana::ext::std::Array, boost::hana::test::laws>::TestIterable(boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> >)::(anonymous class)::operator()<std::array<int, 1> >' requested here  
                ((void)f(static_cast<Xs&&>(xs)), 1)...  
                       ^  
/home/david/code/hana/include/boost/hana/functional/partial.hpp:69:20: note: in instantiation of function template specialization 'boost::hana::detail::variadic::_for_each::operator()<(lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32), std::array<int, 0> &, std::array<int, 1> &, std::array<int, 2> &, std::array<int, 3> &, std::array<int, 4> &, std::array<int, 5> &>' requested here  
            return detail::std::move(f)(  
                   ^  
/home/david/code/hana/include/boost/hana/tuple.hpp:246:41: note: in instantiation of function template specialization 'boost::hana::_partial<boost::hana::detail::variadic::_for_each, boost::hana::detail::closure_impl<boost::hana::detail::element<0, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)> > >::operator()<std::array<int, 0> &, std::array<int, 1> &, std::array<int, 2> &, std::array<int, 3> &, std::array<int, 4> &, std::array<int, 5> &>' requested here  
            BOOST_HANA_PP_FOR_EACH_REF1(BOOST_HANA_PP_UNPACK)  
                                        ^  
/home/david/code/hana/include/boost/hana/tuple.hpp:149:9: note: expanded from macro 'BOOST_HANA_PP_FOR_EACH_REF1'  
        MACRO(&)                                                        \  
        ^  
/home/david/code/hana/include/boost/hana/tuple.hpp:244:26: note: expanded from macro 'BOOST_HANA_PP_UNPACK'  
                { return static_cast<F&&>(f)(static_cast<Xs REF>(xs).get...); } \  
                         ^  
/home/david/code/hana/include/boost/hana/functional/overload_linearly.hpp:80:18: note: in instantiation of function template specialization 'boost::hana::unpack_impl<boost::hana::Tuple, void>::unpack_tuple::operator()<boost::hana::detail::element<0, std::array<int, 0> >, boost::hana::detail::element<1, std::array<int, 1> >, boost::hana::detail::element<2, std::array<int, 2> >, boost::hana::detail::element<3, std::array<int, 3> >, boost::hana::detail::element<4, std::array<int, 4> >, boost::hana::detail::element<5, std::array<int, 5> > , boost::hana::_partial<boost::hana::detail::variadic::_for_each, boost::hana::detail::closure_impl<boost::hana::detail::element<0, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)> > > >' requested here  
        { return which<Args...>(int{})(static_cast<Args&&>(args)...); }  
                 ^  
/home/david/code/hana/include/boost/hana/tuple.hpp:252:20: note: in instantiation of function template specialization 'boost::hana::_overload_linearly<boost::hana::unpack_impl<boost::hana::Tuple, void>::unpack_tuple_t_metafunction, boost::hana::unpack_impl<boost::hana::Tuple, void>::unpack_tuple>::operator()<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > &, boost::hana::_partial<boost::hana::detail::variadic::_for_each, boost::hana::detail::closure_impl<boost::hana::detail::element<0, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)> > > >' requested here  
            return overload_linearly(  
                   ^  
/home/david/code/hana/include/boost/hana/fwd/foldable.hpp:1378:28: note: in instantiation of function template specialization 'boost::hana::unpack_impl<boost::hana::Tuple, void>::apply<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > &, boost::hana::_partial<boost::hana::detail::variadic::_for_each, boost::hana::detail::closure_impl<boost::hana::detail::element<0, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)> > > >' requested here  
            return Unpack::apply(static_cast<Xs&&>(xs), static_cast<F&&>(f));  
                           ^  
/home/david/code/hana/include/boost/hana/foldable.hpp:356:17: note: in instantiation of function template specialization 'boost::hana::_unpack::operator()<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > &, boost::hana::_partial<boost::hana::detail::variadic::_for_each, boost::hana::detail::closure_impl<boost::hana::detail::element<0, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)> > > >' requested here  
                hana::unpack(static_cast<Xs&&>(xs),  
                ^  
/home/david/code/hana/include/boost/hana/fwd/foldable.hpp:727:29: note: in instantiation of function template specialization 'boost::hana::foldable_detail::for_each_helper<boost::hana::Tuple, false>::apply<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > &, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)>' requested here  
            return ForEach::apply(static_cast<Xs&&>(xs), static_cast<F&&>(f));  
                            ^  
/home/david/code/hana/test/laws/iterable.hpp:36:13: note: in instantiation of function template specialization 'boost::hana::_for_each::operator()<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > &, (lambda at /home/david/code/hana/test/laws/iterable.hpp:36:32)>' requested here  
            hana::for_each(xs, [](auto xs) {  
            ^  
/home/david/code/hana/test/laws/iterable.hpp:27:39: note: in instantiation of function template specialization 'boost::hana::test::TestIterable<boost::hana::ext::std::Array, boost::hana::test::laws>::TestIterable<boost::hana::_tuple<std::array<int, 0>, std::array<int, 1>, std::array<int, 2>, std::array<int, 3>, std::array<int, 4>, std::array<int, 5> > >' requested here  
        using TestIterable<It, laws>::TestIterable;  
                                      ^  
/home/david/code/hana/include/boost/hana/sequence.hpp:1178:41: note: candidate template ignored: substitution failure [with Xs = std::array<int, 1>, Ys = std::array<int, 1>]  
        static constexpr decltype(auto) apply(Xs const& xs, Ys const& ys) {  
                                        ^  
7 errors generated.  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-18 02:30:00 UTC  
> Updated at: 2015-06-18 02:30:11 UTC  
> Url: https://github.com/boostorg/hana/issues/136#issuecomment-113016240  

Oops, I just realized no testing was done against libstdc++ (so this bug went unnoticed). The Travis build installs its own libc++ from source. It's a bit late now, but I'll try to add a Travis job testing against libstdc++ tomorrow. Thanks for the report.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-28 11:13:39 UTC  
> Url: https://github.com/boostorg/hana/issues/136#issuecomment-116259538  

The exact failure you encountered is actually [this](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=66693)  libstdc++ (non) bug.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-28 11:21:45 UTC  
> Url: https://github.com/boostorg/hana/issues/136#issuecomment-116261239  

Strictly speaking, this bug is fixed. However, we ought to properly test with Clang against libstdc++. I haven't been able to build libstdc++ on Travis so far, and I'm not sure building from source is the best option (takes long). I generalized this with #159.
