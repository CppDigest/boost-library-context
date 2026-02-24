# #422 Classic: Fixed position_iterator forming reference to local [Merged]

> Username: Kojoley  
> Created at: 2018-11-23 00:21:01 UTC  
> Updated at: 2018-11-23 11:33:20 UTC  
> Merged at: 2018-11-23 11:33:15 UTC  
> Closed at: 2018-11-23 11:33:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/422  

When underlying iterator returns by value the corresponding position_iterator was providing iterator_adaptor with a reference type what lead to returning a reference to a local variable in dereference operator.  
  
Made a custom trait because it is a way more elegant than:  
```cpp  
    typedef typename boost::mpl::if_c<  
        boost::is_reference<reference>::value,  
        typename boost::add_reference<  
            typename boost::add_const<  
                typename boost::remove_reference<reference>::type  
            >::type  
        >::type,  
        typename boost::add_const<reference>::type  
    >::type const_reference_type;  
```  
  
Fixes https://svn.boost.org/trac10/ticket/9737.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-11-23 02:21:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/422#issuecomment-441144615  

wonderful!

---
