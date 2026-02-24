# #78 - proxy iterators have no operator -> [Closed]

> Username: CornedBee  
> Created at: 2025-04-25 14:13:03 UTC  
> Updated at: 2025-05-08 01:39:16 UTC  
> Closed at: 2025-04-26 22:59:34 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/78  

In commit 965b111 (so first released in Boost 1.83), the arrow operators were disabled for iterators whose reference type wasn't a language reference.  
  
This means that proxy iterators (as done by the proxy_iterator_interface alias) no longer have operator->, which breaks a lot of code. This change is not listed in the release notes for any Boost version.  
  
I don't understand why this change was made. The whole point of proxy_arrow_result is to support such types. With the change, proxy_arrow_result is effectively unused, since the only users are the arrow operators that are also disabled.  
  
Is there a particular reason for this change, or should it simply be reverted?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:59:34 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/78#issuecomment-2832703727  

Hi, Sebastian!  Been a while.  The reason is that I am tracking the changes to the paper about this that is currently being standardized.  I think I just forgot to mention it in release notes (sorry about that).  The committee folks from SG7 Ranges are adamant that `operator->` should almost never be used, and that we should not have it as part of the proxy arrow helper.  You can use your version own that works like the older proxy arrow from before this change -- the arrow type is a template parameter to the iterator_interface class.

---

## Comment 2

> Username: CornedBee  
> Created at: 2025-04-28 08:06:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/78#issuecomment-2834329172  

Hi Zach. Thank you for the info. Do you have a paper number for this effort? I tried looking in the mailings but couldn't find anything like this.

---

## Comment 3

> Username: tzlaine  
> Created at: 2025-05-08 01:39:15 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/78#issuecomment-2861163876  

After looking more closely, what I wrote before was only half right.  Turns out, the disablement of the `operator->` comes from the `iterator_interface::operator->` constraints, not from a change to `proxy_arrow_result`.  If you look at `detail::make_pointer` you can see what's going on:  
  
```cpp  
    namespace detail {  
        template<typename Pointer, typename Reference, typename T>  
        auto make_pointer(  
            T && value,  
            std::enable_if_t<  
                std::is_pointer<Pointer>::value &&  
                    std::is_reference<Reference>::value,  
                int> = 0) -> decltype(std::addressof(value))  
        {  
            return std::addressof(value);  
        }  
  
        template<typename Pointer, typename Reference, typename T>  
        auto make_pointer(  
            T && value,  
            std::enable_if_t<  
                !std::is_pointer<Pointer>::value &&  
                    !std::is_same<Pointer, void>::value &&  
                    std::is_reference<Reference>::value,  
                int> = 0)  
        {  
            return Pointer(std::forward<T>(value));  
        }  
```  
  
So, 1) if we return a language-pointer from `iterator_interface::operator->`, `iterator_interface::reference` it must also be a language-reference; and 2) if we return a proxy pointer pointer type from `iterator_interface::operator->`, that type must not be `void`, and `iterator_interface::reference` must still be a language-reference.  
  
In all other cases, `operator*` must be used instead.  These are the semantics implied by the `std::ranges` work, as communicated to me in committee reviews of `iterator_interface`.  I don't have words describing this in a paper, just review minutes (which I'm not free to share publicly).
