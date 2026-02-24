# #65 - Convert mpl sequence to mp_list [Closed]

> Username: bernhardmgruber  
> Created at: 2021-09-14 16:20:18 UTC  
> Updated at: 2021-10-28 18:48:37 UTC  
> Closed at: 2021-10-28 18:48:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/65  

While mp11 provides support for treating an `mp_list` as an MPL sequence, I could not find the reverse functionality for converting an MPL sequence into an `mp_list`. I need this functionality to interface with an older code base. I think if I had a simple `mpl::vector<...>`, mp11 would work straight away. Unfortunately, I have some kind of nested `mpl::item_v` list. I came up with the following type function:  
```c++  
namespace boost::mp11 {  
    namespace internal {  
        struct append {  
            template <typename L, typename V>  
            struct apply {  
                using type = mp_push_back<L, V>;  
            };  
        };  
    }  
  
    template <typename MplSequence>  
    using mp_list_from_mpl_sequence = typename boost::mpl::accumulate<MplSequence, mp_list<>, internal::append>::type;  
}  
```  
This could be used like this:  
```c++  
struct weighting;  
struct momentum;  
struct position;  
  
using old = boost::mpl::v_item<weighting,  
    boost::mpl::v_item<momentum,  
        boost::mpl::v_item<position,  
            boost::mpl::vector0<mpl_::na>, 0>, 0>, 0>;  
  
using new_ = boost::mp11::mp_list_from_mpl_sequence<old>; // boost::mp11::mp_list<position, momentum, weighting>  
```  
  
Complete example: https://godbolt.org/z/6PKveq3G8  
  
This might not be the best implementation, but it seems to work in my case. Could you please consider adding such a functionality to the MPL support of mp11? Thank you!

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-14 16:33:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/65#issuecomment-919317938  

The canonical MPL way to do this is to use `mpl::copy`, as in  
```  
using new_ = mpl::copy<old, mpl::back_inserter<mp11::mp_list<>>>::type;  
```  
https://godbolt.org/z/nhx7PaGfW  
  
Since mp11 cannot include MPL headers, as it has no dependencies, I can't add a helper for this, but it's short enough.

---

## Comment 2

> Username: bernhardmgruber  
> Created at: 2021-09-14 19:37:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/65#issuecomment-919456903  

That is very helpful, thank you!  
  
Would you mind to add this snippet to the MPL support section of the documentation?
