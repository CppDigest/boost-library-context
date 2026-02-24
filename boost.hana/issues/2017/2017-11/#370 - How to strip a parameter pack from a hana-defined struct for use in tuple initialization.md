# #370 - How to strip a parameter pack from a hana-defined struct for use in tuple initialization? [Closed]

> Username: invexed  
> Created at: 2017-11-16 16:13:43 UTC  
> Updated at: 2017-11-16 19:36:02 UTC  
> Closed at: 2017-11-16 19:36:01 UTC  
> Url: https://github.com/boostorg/hana/issues/370  

Hi,  
  
I have defined a struct like so, using `BOOST_HANA_DEFINE_STRUCT`:  
  
```  
struct A  
{  
    BOOST_HANA_DEFINE_STRUCT(A,  
        (B1, b1),  
        (B2, b2),  
        (B3, b3)  
    );  
};  
```  
  
How can I initialize an `std::tuple` (or `hana::tuple`) of `std::vector`s of types aliased in `B1`, `B2` and `B3`?  
  
```  
std::tuple<  
    std::vector<B1::value_type>  
    std::vector<B2::value_type>  
    std::vector<B3::value_type>  
> my_tuple;  
```  
  
Thanks in advance for any help you can offer.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-11-16 17:10:21 UTC  
> Updated at: 2017-11-16 17:11:55 UTC  
> Url: https://github.com/boostorg/hana/issues/370#issuecomment-344991975  

For starters, you're gonna want some commas. :stuck_out_tongue:   
  
To get the types of the members of a `hana::Struct` you could use [`hana::members`](http://boostorg.github.io/hana/group__group-Struct.html#gad301dd8e9fb4639d7874619c97d6d427).  
  
Something like this would work:  
```cpp  
auto make_my_tuple = [](auto ...members)  
{  
  return hana::type<hana::tuple<std::vector<typename decltype(members)::value_type>...>>{};  
};  
  
using MyTuple = typename decltype(hana::unpack(hana::members(std::declval<A>()), make_my_tuple))::type;  
```  
  
:coffee:

---

## Comment 2

> Username: invexed  
> Created at: 2017-11-16 19:36:01 UTC  
> Url: https://github.com/boostorg/hana/issues/370#issuecomment-345038295  

I've got no hope with TMP if I can't even handle a couple of commas!  
  
Thanks so much for your time; it worked like a charm. I'll close the issue now.
