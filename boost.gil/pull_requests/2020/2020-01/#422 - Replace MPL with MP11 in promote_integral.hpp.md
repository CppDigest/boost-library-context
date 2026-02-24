# #422 Replace MPL with MP11 in promote_integral.hpp [Closed]

> Username: parthsarthi1  
> Created at: 2020-01-18 18:54:27 UTC  
> Updated at: 2020-03-10 18:48:29 UTC  
> Closed at: 2020-03-10 18:48:29 UTC  
> Url: https://github.com/boostorg/gil/pull/422  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
Use type traits and features of C++11, then use Boost.MP11.  
Clean up and reformat code according to the current guidelines.  
  
### References  
  
continuation of issue #280   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
- [x] ensure CI cases compile   
- [ ] Ensure all CI test cases pass  
- [ ] Merge all commits in one single commit  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-01-18 20:02:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/422#pullrequestreview-344959476  

Thanks for the PR. It needs some polishing.  
Please, see my comments for details.  
  
Most importantly, the whole ` include/boost/gil/.idea/` directory and ` include/boost/gil/promote_integral.hpp.gch` binary file must be removed.

📁 include/boost/gil/.idea/gil.iml

```diff
   1 |+ <?xml version="1.0" encoding="UTF-8"?>
```

> Username: mloskot  
> Created_at: 2020-01-18 19:47:46 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368244949  

Please, remove the whole `.idea` directory. There is no place for any IDE and user-specific files in the repo.

> Username: parthsarthi1  
> Created_at: 2020-01-18 20:05:15 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368245777  

sure , i'll remove them right now. thanks


📁 include/boost/gil/promote_integral.hpp

```diff
  24 |- 
  19 |+ #include <boost/mp11/list.hpp>
  20 |+ #include<boost/mp11/algorithm.hpp>
```

> Username: mloskot  
> Created_at: 2020-01-18 19:48:35 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368244993  

Nitpicking: Please add space between `#include` and `<` here

---

📁 include/boost/gil/promote_integral.hpp

```diff
  61 |                     T,
  65 |-                     typename boost::mpl::next<Iterator>::type,
  62 |+                     typename  boost::mp11::detail::mp_front_impl<Iterator>::type,
```

> Username: mloskot  
> Created_at: 2020-01-18 19:50:16 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368245051  

I'm not convinced it is a good idea to use MP11's implementation detail here. We should stick to features from the MP11 public interface only. In fact, "nothing in `detail::` should ever be used or touched in any way by user code" - MP11 author's words  
  
NItpicking: Superfluous whitespaces between `typename` and `boost::`, one is enough

---

📁 include/boost/gil/promote_integral.hpp

```diff
 183 |-             typename boost::mpl::begin<integral_types>::type,
 184 |-             typename boost::mpl::end<integral_types>::type,
 180 |+             typename boost::mp11::detail::mp_front_impl<integral_types>::type,
```

> Username: mloskot  
> Created_at: 2020-01-18 19:57:03 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368245417  

Same as in previous comment, I don't understand why the use of MP11 `detail`

---

📁 include/boost/gil/promote_integral.hpp

```diff
 184 |-             typename boost::mpl::end<integral_types>::type,
 180 |+             typename boost::mp11::detail::mp_front_impl<integral_types>::type,
 181 |+             typename boost::mp11::detail::mp_at_c_impl<integral_types ,boost::mp11::mp_size<integral_types>::value -1>::type,
```

> Username: mloskot  
> Created_at: 2020-01-18 19:57:30 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368245435  

Nitpicking: `integral_types ,boost` should have comma to the left `integral_types, boost`


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-01-19 09:08:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/422#pullrequestreview-344987478  

📁 include/boost/gil/promote_integral.hpp

```diff
  61 |                     T,
  65 |-                     typename boost::mpl::next<Iterator>::type,
  62 |+                     typename boost::mp11::mp_front<Iterator>::type,
```

> Username: mloskot  
> Created_at: 2020-01-19 09:07:23 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368277519  

`mpl::next` is iterator over sequence, `mp11::mp_front` is element of sequence.  
This `promote_to_larger` iteration could be re-worked a bit to index-based iteration, using `mp_front` (or `mp_first` synonym), `mp_at_c` and `mp_size`, I think.  
  
The mismatch above is the reason of the current CI failures.

---

📁 include/boost/gil/promote_integral.hpp

```diff
 184 |-             typename boost::mpl::end<integral_types>::type,
 180 |+             typename boost::mp11::mp_front<integral_types>::type,
 181 |+             typename boost::mp11::mp_at_c<integral_types, boost::mp11::mp_size<integral_types>::value -1>::type,
```

> Username: mloskot  
> Created_at: 2020-01-19 09:07:55 UTC  
> Updated_at: 2020-02-21 13:24:16 UTC  
> Url: https://github.com/boostorg/gil/pull/422#discussion_r368277540  

Same here, `mp_front` and `mp_at_c` are not 1:1 for `mpl::begin` and `mpl::end`.


---

## Comment 3

> Username: mloskot  
> Created_at: 2020-02-06 09:24:56 UTC  
> Updated_at: 2020-02-06 09:26:10 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-582811665  

The `mp_for_each` does not seem to me to be the right or optimal tool for the job. It should be feasible to resolve the type look-up completely at compile-time.  
  
For example, consider this simplified example of the integral promotion using the classic technique (e.g. search for [termination conditions](https://books.google.co.uk/books?id=4dV9tALro-AC&q=termination%20conditions) in the classic "C++ Template Metaprogramming" book):  
  
```cpp  
// https://godbolt.org/z/Ta9Jzt  
#include <boost/mp11.hpp>  
#include <climits>  
#include <type_traits>  
namespace mp11 = boost::mp11;  
template <typename T>  
struct bit_size : std::integral_constant<std::size_t, (CHAR_BIT * sizeof(T))>  
{  
    static_assert(std::is_fundamental<T>::value, "fundamental type required");  
};  
template <typename T, typename L, std::size_t I, std::size_t N, std::size_t MinBitSize>  
struct promote_impl  
{  
     using current_type = mp11::mp_at_c<L, I>;  
     using type = typename std::conditional  
        <  
            (bit_size<current_type>::value >= MinBitSize),  
            current_type,  
            typename promote_impl<T, L, I+1, N, MinBitSize>::type  
        >::type;  
};  
template <typename T, typename L, std::size_t I, std::size_t MinSize>  
struct promote_impl<T, L, I, I, MinSize>  
{  
    using type = T;  
};  
template <typename T>  
struct promote  
{  
    using integral_types = mp11::mp_list<short, int, long, long long>; // example of possibilities  
    using type = typename promote_impl  
        <  
            T,  
            integral_types,  
            0,  
            mp11::mp_size<integral_types>::value,  
            2 * bit_size<T>::value - 1  
        >::type;  
};  
int main()  
{  
     static_assert(std::is_same<promote<char>::type, short>::value, "not short");  
     static_assert(std::is_same<promote<short>::type, int>::value, "not int");  
}  
```  
  
Now, considering we have C++11 and MP11 at our disposal, that code can be greatly simplified with `mp_find_if`, `mp_find_if_q` with search condition expressed as a template.  
  
I once discussed those techniques with @pdimov and he suggested that for this short list of type options it would be easier and more readable to just `mp_cond`. For examples, something along these lines  
  
```cpp  
using type = mp_cond  
<  
    mp_bool<sizeof(Q) >= 2*sizeof(short)>, short,  
    ...  
    mp_true, long long  
>;  
```  
  
If you don't mind some C++11/MP11 learning and experimenting, you could evaluate those alternatives.

---

## Comment 4

> Username: parthsarthi1  
> Created_at: 2020-02-09 11:53:34 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-583836391  

@mloskot I'll correct all indentation errors, and rebase all the commits , just wanted to check what other errors I might come across, and I saw the constexpr version (, that seems easier than that mp_for_each , and yeah I do realize for this small list of 4-5 types , mp_cond would have been the easier approach .

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-02-10 16:47:59 UTC  
> Updated_at: 2020-02-10 16:48:58 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-584218005  

@parthsarthiprasad   
>  just wanted to check what other errors I might come across  
  
Not sure what you mean.  
  
I'd do this:  
  
1. Pick one option and implement complete _feature-wise equivalent_ of `boost::gil::promote_integral` using C++11 and MP11  
2. Update this PR  
3. Ensure all CI jobs compile  
4. Ensure all CI jobs pass all test cases in https://github.com/boostorg/gil/blob/develop/test/core/promote_integral.cpp  
5. Review  
  
If you'd like to try an alternative implementation, then submit it as separate PR and follow the same workflow of 1. through 5.,  then we can compare.

---

## Comment 6

> Username: parthsarthi1  
> Created_at: 2020-02-21 16:59:30 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-589741594  

@mloskot ill check what error im facing right now , im able to build this on my system , hope to correct this asap.   
will fous on CI jobs pass all test cases in https://github.com/boostorg/gil/blob/develop/test/core/promote_integral.cpp.  
hope that's not a big issue.

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-02-21 17:44:33 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-589759196  

@parthsarthi1   
> hope that's not a big issue.  
  
It's your PR, so you set the pace. I'm imposing any 'deadlines' :) If I need to get it done sooner, I'll ask.

---

## Comment 8

> Username: mloskot  
> Created_at: 2020-03-10 18:48:28 UTC  
> Url: https://github.com/boostorg/gil/pull/422#issuecomment-597252506  

Closing as alternative implementation in #441 has been merged.

---
