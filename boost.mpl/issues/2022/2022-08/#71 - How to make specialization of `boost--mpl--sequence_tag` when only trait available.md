# #71 - How to make specialization of `boost::mpl::sequence_tag` when only trait available? [Open]

> Username: denzor200  
> Created at: 2022-08-17 11:25:00 UTC  
> Updated at: 2025-07-03 04:32:50 UTC  
> Url: https://github.com/boostorg/mpl/issues/71  

I have no problems when i know template of type, that will be passed into `boost::mpl::sequence_tag`:  
```  
namespace boost { namespace mpl  
{  
    template <typename>  
    struct sequence_tag;  
  
    template <typename... Elements>  
    struct sequence_tag<std::tuple<Elements...>>  
    {  
        typedef fusion::fusion_sequence_tag type;  
    };  
}}  
```  
  
But my case is when have no access for `std::tuple` template and i only have an `is_tuple` trait:  
```  
namespace boost { namespace mpl  
{  
    template <typename, typename=void>  
    struct sequence_tag;  
  
    template <typename T>  
    struct sequence_tag<T, std::enable_if_t<is_tuple<T>::value>>  // won't work  
    {  
        typedef fusion::fusion_sequence_tag type;  
    };  
}}  
```  
  
SFINAE is not working on `boost::mpl::sequence_tag`, does it have an another way?

---

## Comment 1

> Username: ideepakchauhan7  
> Created at: 2025-07-03 04:32:50 UTC  
> Url: https://github.com/boostorg/mpl/issues/71#issuecomment-3030657775  

To define `boost::mpl::sequence_tag` for types that satisfy the `is_tuple` trait without relying on SFINAE (which isn't working in this context), you can use template specialization with a helper struct. This approach leverages the compiler's ability to select the correct specialization based on the boolean value of ` is_tuple<T>::value`.  
  
```  
namespace boost { namespace mpl {  
  
    // Helper template with specializations based on is_tuple<T>::value  
    template <typename T, bool IsTuple>  
    struct sequence_tag_impl;  
  
    // Specialization for when is_tuple<T>::value is true  
    template <typename T>  
    struct sequence_tag_impl<T, true> {  
        typedef fusion::fusion_sequence_tag type;  
    };  
  
    // Specialization for when is_tuple<T>::value is false  
    template <typename T>  
    struct sequence_tag_impl<T, false> {  
        typedef void type;  // Adjust this to the appropriate default tag  
    };  
  
    // Primary sequence_tag forwards to the helper based on is_tuple<T>::value  
    template <typename T>  
    struct sequence_tag : sequence_tag_impl<T, is_tuple<T>::value> {};  
  
}}  
```  
  
**Note :**   
In the `false` specialization, the default tag is set to `void`. Replace `void` with the correct default tag as required by your specific use case or Boost.MPL's expectations.
