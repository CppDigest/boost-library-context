# #290 - Accessing map with result of `decltype_` does not compile [Closed]

> Username: jacquelinekay  
> Created at: 2016-06-19 23:50:33 UTC  
> Updated at: 2016-07-18 15:12:58 UTC  
> Closed at: 2016-07-18 15:12:58 UTC  
> Url: https://github.com/boostorg/hana/issues/290  

I am trying to index into a `hana::map` using the result of `hana::decltype_` and iterating over the contents of a tuple.  
  
My understanding was that `hana::decltype_` produces a `hana::type_c`, which seemed to be suitable for indexing into my map. I have also tried constructing a key using language decltype and wrapping that in `type_c`, but I got the same error.  
  
Is this incorrect usage?  
  
```  
#include <iostream>  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
enum Id : uint8_t {  
  a, b, c  
};  
  
constexpr auto id_map = hana::make_map(  
    hana::make_pair(hana::type_c<std::string>, Id::a),  
    hana::make_pair(hana::type_c<int>, Id::b),  
    hana::make_pair(hana::type_c<bool>, Id::c)  
  );  
  
int main() {  
  hana::tuple<std::string, int, bool> types_tuple;  
  
  hana::for_each(types_tuple, [](const auto & x) {  
    std::cout << "ID is: " << id_map[hana::decltype_(x)] << std::endl;  
  });  
}  
```  
  
Gives this error in clang 3.8 (truncated significantly), on current master:  
  
```  
/home/jackie/code/cmbml/external/hana/include/boost/hana/optional.hpp:108:9: error: static_assert  
      failed "hana::optional::operator* requires a non-empty optional"  
        static_assert(detail::wrong<dummy...>{},  
        ^             ~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/jackie/code/cmbml/external/hana/include/boost/hana/map.hpp:358:52: note: in instantiation of  
      function template specialization 'boost::hana::optional<>::operator*<>' requested here  
            constexpr std::size_t index = decltype(*MaybeIndex{}){}();  
                                                   ^  
/home/jackie/code/cmbml/external/hana/include/boost/hana/at_key.hpp:49:23: note: in instantiation  
      of function template specialization 'boost::hana::at_key_impl<boost::hana::map_tag,  
      void>::apply<const  
      boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<std::__cxx11::basic_string<char>,  
      0>, boost::hana::detail::bucket<int, 1>, boost::hana::detail::bucket<bool, 2> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::type_impl<std::__cxx11::basic_string<char>  
      >::_, Id>, boost::hana::pair<boost::hana::type_impl<int>::_, Id>,  
      boost::hana::pair<boost::hana::type_impl<bool>::_, Id> > > &, boost::hana::type_impl<const  
      std::__cxx11::basic_string<char> >::_>' requested here  
        return AtKey::apply(static_cast<Xs&&>(xs), key);  
                      ^  
```  
  
And a bit further down:  
  
```  
In file included from /home/jackie/code/cmbml/external/hana/include/boost/hana.hpp:133:  
/home/jackie/code/cmbml/external/hana/include/boost/hana/map.hpp:358:66: error: illegal initializer  
      type 'decltype(* MaybeIndex{})' (aka 'void')  
            constexpr std::size_t index = decltype(*MaybeIndex{}){}();  
                                                                 ^  
/home/jackie/code/cmbml/external/hana/include/boost/hana/at_key.hpp:49:23: note: in instantiation  
      of function template specialization 'boost::hana::at_key_impl<boost::hana::map_tag,  
      void>::apply<const  
      boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<std::__cxx11::basic_string<char>,  
      0>, boost::hana::detail::bucket<int, 1>, boost::hana::detail::bucket<bool, 2> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::type_impl<std::__cxx11::basic_string<char>  
      >::_, Id>, boost::hana::pair<boost::hana::type_impl<int>::_, Id>,  
      boost::hana::pair<boost::hana::type_impl<bool>::_, Id> > > &, boost::hana::type_impl<const  
      std::__cxx11::basic_string<char> >::_>' requested here  
        return AtKey::apply(static_cast<Xs&&>(xs), key);  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-06-20 01:49:28 UTC  
> Updated at: 2016-06-20 01:52:12 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227035074  

`hana::decltype_` does not strip const qualifiers so the types are not equal. I do this all of the time and getting around it is a pain. There should be a `hana::decayed_type(x)` or something.  
  
Note the `boost::hana::type_impl<const std::__cxx11::basic_string<char>::_` at the end of the template params in the call to `at_key`.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-06-20 02:05:47 UTC  
> Updated at: 2016-06-20 02:05:56 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227036447  

Even **I** thought it would strip cv-qualifiers. What name shall we use for a decltype that strips everything (not only references)? `raw_decltype`?

---

## Comment 3

> Username: jacquelinekay  
> Created at: 2016-06-20 05:09:56 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227051438  

Yeah, I skimmed over the prominent part of the documentation that makes this obvious:  "decltype_ will strip any reference from the object's actual type... However, any cv-qualifiers will be retained." http://www.boost.org/doc/libs/1_61_0/libs/hana/doc/html/structboost_1_1hana_1_1type.html#a7d68ff2275cbb295556d83598f04e39a  
  
I'll throw in `decayed_decltype` as another name suggestion for the suggested convenience function for stripping both references and cv-qualifiers.  
  
Thanks also for adding a more concise error message when accessing a missing key.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-06-20 05:32:08 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227053461  

I'll keep this open until we have a clear solution for `decltype_` removing cv-qualifiers. The fact that you ran into this problem, Jason has it, and I wasn't able to figure out your problem quickly are all indications that something's missing here.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-06-21 14:35:26 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227459427  

What about `hana::typeid_`?

---

## Comment 6

> Username: ldionne  
> Created at: 2016-06-21 14:36:01 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-227459621  

After all, it gives back a `hana::type`, which can be seen as some kind of static `std::type_info`.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2016-07-01 00:51:59 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-229827997  

``` cpp  
                hana::equal(  
                  hana::traits::decay(hana::decltype_(path)),  
                  hana::first(pair)  
                )  
```

---

## Comment 8

> Username: ricejasonf  
> Created at: 2016-07-06 03:42:39 UTC  
> Updated at: 2016-07-06 03:42:50 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-230664016  

I think `hana::decltype_` itself should strip `const` qualifier or even decay the type completely. It would solve more problems than it creates.  
  
I don't think anyone expects a `hana::type_c<const int>` to come from `hana::decltype_(x);` just as they wouldn't expect it to return a `hana::type_c<const int&>`.  
  
If we have to make it a new function, I'd suggest deprecating the use of `hana::decltype_` in favor of the new one to avoid further confusion.

---

## Comment 9

> Username: jacquelinekay  
> Created at: 2016-07-06 16:25:55 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-230826781  

I was going to say that it would be confusing if `hana::decltype_` stripped const qualifiers because it defies the expectation set by the behavior of language `decltype`, but then again, it already breaks that expectation by stripping references. I think this usage if an existing keyword is what leads to the confusion in the first place: the analogous Hana behavior is halfway between language decltype and a function that produces a `type_c` for a pure type.  
  
I like the name `typeid_` a bit better because language `typeid` strips const qualifiers and references, though it does not strip pointer types.  
  
Keeping `decltype_` as is and introducing a new function to strip cv qualifiers is not very useful in Hana because cv qualifiers, like references qualifiers, are essentially meaningless for compile-time type-value computation. My impression is that a metafunction that takes a `type_c` shouldn't care if about the qualifiers that affect the runtime behavior of a value of that type, although I'd be curious if anyone can come up with a counterexample.

---

## Comment 10

> Username: ricejasonf  
> Created at: 2016-07-06 16:39:26 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-230831171  

That makes sense. I like `hana::typeid_` too then.

---

## Comment 11

> Username: ldionne  
> Created at: 2016-07-06 23:04:06 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-230933670  

`hana::typeid_` would faithfully model the stripping of ref-qualifiers and cv-qualifiers from whatever you put into it. It wouldn't strip pointers, however, just like `typeid`. I can't change the behavior of `decltype_` because it would be an interface breaking change. Instead, I'll add `typeid_`.

---

## Comment 12

> Username: ldionne  
> Created at: 2016-07-08 00:53:01 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-231248963  

Quick question: would `typeof` be better?

---

## Comment 13

> Username: ricejasonf  
> Created at: 2016-07-08 05:46:58 UTC  
> Url: https://github.com/boostorg/hana/issues/290#issuecomment-231280182  

`typeof` lacks the trailing underscore so that is a plus. slightly more terse  
  
It might confuse javascript programmers though. :laughing:  (lol jk)  
  
But seriously yeah `typeof`. :+1:
