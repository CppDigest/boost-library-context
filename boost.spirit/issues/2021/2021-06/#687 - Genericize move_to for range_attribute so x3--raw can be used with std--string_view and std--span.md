# #687 - Genericize move_to for range_attribute so x3::raw[] can be used with std::string_view and std::span [Closed]

> Username: atgraham12  
> Created at: 2021-06-02 17:16:05 UTC  
> Updated at: 2025-05-10 00:19:54 UTC  
> Closed at: 2025-05-10 00:06:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/687  

The following patch allows `move_to()` to be used with types other than `boost::iterator_range`, in order that `x3::raw[]` can use attribute types like `std::string_view` or `std::span` (by merely specializing `is_range` for that type). Note that this won't work for these types before C++20, as `std::string_view` doesn't have an iterator-pair ctor, and `std::span` isn't available prior to then.  
  
This is also consistent with other overloads of `move_to()` such as the overload for `container_attribute`. (I wonder why `range_attribute` wasn't originally implemented this way.)  
```  
--- boost_1_76_0/boost/spirit/home/x3/support/traits/move_to.hpp        2021-04-  
13 09:23:01.000000000 -0700  
+++ boost_mine/boost/spirit/home/x3/support/traits/move_to.hpp  2021-05-31 10:34  
:21.950810803 -0700  
@@ -182,11 +182,11 @@  
            traits::move_to(first, last, fusion::front(dest));  
        }  
           
-        template <typename Iterator>  
+        template <typename Iterator, typename Dest>  
        inline void  
-        move_to(Iterator first, Iterator last, boost::iterator_range<Iterator>& rng, range_attribute)  
+        move_to(Iterator first, Iterator last, Dest& dest, range_attribute)  
        {  
-            rng = {first, last};  
+            dest = {first, last};  
        }  
    }  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-06-02 17:31:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/687#issuecomment-853244355  

Duplicate of #487.  
  
The patch that added `range_attribute` is a workaround. I was looking recently into the issue and I will almost certainly will replace `move_to` overload that accepts iterators with separate functions. The main problem in resolving the issue is that things like `std::string_view` and `std::span` implement container interface, and it is ambiguous what the user wants, it could be either creating a view into these iterators, or to copy elements from these iterators to the elements of the view is pointing to.
