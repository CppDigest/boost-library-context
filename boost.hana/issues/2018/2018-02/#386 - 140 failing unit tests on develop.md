# #386 - 140 failing unit tests on develop [Closed]

> Username: bebuch  
> Created at: 2018-02-20 12:03:38 UTC  
> Updated at: 2018-03-03 08:46:05 UTC  
> Closed at: 2018-03-03 08:46:05 UTC  
> Url: https://github.com/boostorg/hana/issues/386  

With both clang and GCC I have 140 failing unit tests on the develop branch. Its always somthing like:  
  
```  
    "g++-6"   -fPIC -m64 -O0 -fno-inline -Wall -g -std=gnu++1z -DBOOST_ALL_NO_LIB=1  -I"../../.." -I"../include" -I"_include" -c -o "../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug/ext/boost/fusion/deque/auto/at.o" "ext/boost/fusion/deque/auto/at.cpp"  
  
...failed gcc.compile.c++ ../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug/ext/boost/fusion/deque/auto/at.o...  
...skipped <p../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug>test~ext~boost~fusion~deque~auto~at for lack of <p../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug>ext/boost/fusion/deque/auto/at.o...  
...skipped <p../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug>test~ext~boost~fusion~deque~auto~at.run for lack of <p../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~at.test/gcc-6/debug>test~ext~boost~fusion~deque~auto~at...  
gcc.compile.c++ ../../../bin.v2/libs/hana/test/test~ext~boost~fusion~deque~auto~any_of.test/gcc-6/debug/ext/boost/fusion/deque/auto/any_of.o  
In file included from ../../../boost/type_traits/is_convertible.hpp:20:0,  
                 from ../../../boost/type_traits/is_empty.hpp:12,  
                 from ../../../boost/mpl/empty_base.hpp:23,  
                 from ../../../boost/fusion/sequence/intrinsic/at.hpp:15,  
                 from ../../../boost/hana/ext/boost/fusion/detail/common.hpp:22,  
                 from ../../../boost/hana/ext/boost/fusion/deque.hpp:16,  
                 from ext/boost/fusion/deque/auto/_specs.hpp:8,  
                 from ext/boost/fusion/deque/auto/any_of.cpp:5:  
../../../boost/type_traits/is_convertible.hpp: In instantiation of ‘struct boost::is_convertible<boost::hana::ext::boost::fusion::deque_tag, boost::fusion::detail::from_sequence_convertible_type>’:  
../../../boost/mpl/aux_/nested_type_wknd.hpp:26:31:   required from ‘struct boost::mpl::aux::nested_type_wknd<boost::is_convertible<boost::hana::ext::boost::fusion::deque_tag, boost::fusion::detail::from_sequence_convertible_type> >’  
../../../boost/mpl/not.hpp:39:8:   required from ‘struct boost::mpl::not_<boost::is_convertible<boost::hana::ext::boost::fusion::deque_tag, boost::fusion::detail::from_sequence_convertible_type> >’  
../../../boost/mpl/aux_/nested_type_wknd.hpp:26:31:   required from ‘struct boost::mpl::aux::nested_type_wknd<boost::mpl::not_<boost::is_convertible<boost::hana::ext::boost::fusion::deque_tag, boost::fusion::detail::from_sequence_convertible_type> > >’  
../../../boost/mpl/aux_/preprocessed/gcc/and.hpp:48:8:   required from ‘struct boost::mpl::and_<boost::mpl::not_<boost::is_convertible<boost::hana::ext::boost::fusion::deque_tag, boost::fusion::detail::from_sequence_convertible_type> >, boost::mpl::is_sequence<boost::hana::ext::boost::fusion::deque_tag>, mpl_::bool_<true>, mpl_::bool_<true>, mpl_::bool_<true> >’  
../../../boost/fusion/support/detail/is_mpl_sequence.hpp:21:12:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../../../boost/fusion/support/tag_of.hpp:52:16:   required from ‘struct boost::fusion::detail::tag_of_impl<boost::hana::ext::boost::fusion::deque_tag, void>’  
../../../boost/fusion/support/tag_of.hpp:70:16:   required from ‘struct boost::fusion::traits::tag_of<boost::hana::ext::boost::fusion::deque_tag, void>’  
../../../boost/hana/core/tag_of.hpp:22:12:   required from ‘struct boost::hana::tag_of<boost::hana::ext::boost::fusion::deque_tag, void>’  
../../../boost/hana/concept/searchable.hpp:27:13:   required from ‘struct boost::hana::Searchable<boost::hana::ext::boost::fusion::deque_tag>’  
../../../boost/hana/any_of.hpp:41:10:   required from ‘constexpr auto boost::hana::any_of_t::operator()(Xs&&, Pred&&) const [with Xs = boost::fusion::deque<>; Pred = <lambda()>::<lambda(auto:2)>]’  
_include/auto/any_of.hpp:22:5:   required from here  
../../../boost/static_assert.hpp:31:45: error: static assertion failed: From argument type to is_convertible must be a complete type  
 #     define BOOST_STATIC_ASSERT_MSG( ... ) static_assert(__VA_ARGS__)  
                                             ^  
../../../boost/type_traits/is_convertible.hpp:484:4: note: in expansion of macro ‘BOOST_STATIC_ASSERT_MSG’  
    BOOST_STATIC_ASSERT_MSG(boost::is_complete<From>::value || boost::is_void<From>::value || boost::is_array<From>::value, "From argument type to is_convertible must be a complete type");  
    ^~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The `From` is a Hana-Tag struct (`boost::hana::ext::boost::fusion::deque_tag` in this case) which is not complete. (BTW: Are Hana-Tags required to be compete? The documentation does not mention this explicitly, but in the examples they are not.)

---

## Comment 1

> Username: ldionne  
> Created at: 2018-02-21 03:47:40 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-367204864  

Hana tags should not be required to be complete. I can't reproduce locally using the classic CMake workflow and Boost 1.65.1. Also, [Travis CI](https://travis-ci.org/boostorg/hana/builds/340055287) does not report any failure. Can you please provide a minimal complete example of how you're setting up Hana to reproduce?

---

## Comment 2

> Username: bebuch  
> Created at: 2018-02-21 11:14:31 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-367294073  

I used bjam in the test directory to compile the tests.  
  
```  
$ bjam toolset=gcc  
$ bjam toolset=clang  
```  
  
I can reproduce it with a newly checked-out repository:  
  
```  
git clone --recursive "https://github.com/boostorg/boost.git" modular-boost  
cd modular-boost  
git checkout develop  
git submodule foreach git checkout develop  
./bootstrap.sh  
./b2 headers  
PATH=$(pwd):$PATH  
cd libs/hana/test/  
bjam toolset=gcc -j 8  
```

---

## Comment 3

> Username: ldionne  
> Created at: 2018-02-27 18:31:09 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-368979023  

I could not reproduce locally, even with your instructions, but now I just saw it fail on Travis: https://travis-ci.org/boostorg/hana/jobs/346757876. I'll look into that.

---

## Comment 4

> Username: ldionne  
> Created at: 2018-02-27 18:46:43 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-368983824  

I believe this is a Fusion bug. I am able to reproduce with this:  
  
```c++  
#include <boost/fusion/include/tag_of.hpp>  
struct incomplete;  
using X = ::boost::fusion::traits::tag_of<incomplete>::type  
  
int main() { }  
```  
  
My guess is that Fusion used to accept incomplete types in `tag_of`, but they don't anymore.

---

## Comment 5

> Username: ldionne  
> Created at: 2018-02-27 19:00:36 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-368988226  

Ok, I believe this started happening in https://github.com/boostorg/type_traits/commit/ac351390b215b73d8a9e16d2ca37ba2d9d88a14f. Presumably, `fusion::tag_of` using a type trait that requires the input type to be complete, but it does not check for that (and it doesn't document that the input of `tag_of` must be complete AFAICT).

---

## Comment 6

> Username: ldionne  
> Created at: 2018-03-03 08:46:05 UTC  
> Url: https://github.com/boostorg/hana/issues/386#issuecomment-370131558  

This should have been fixed in Fusion now. Thanks a lot for reporting, this could have been a big breakage.
