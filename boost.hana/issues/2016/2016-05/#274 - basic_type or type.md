# #274 - basic_type or type? [Closed]

> Username: viboes  
> Created at: 2016-05-21 08:08:59 UTC  
> Updated at: 2016-07-17 21:24:10 UTC  
> Closed at: 2016-07-17 21:24:10 UTC  
> Url: https://github.com/boostorg/hana/issues/274  

http://boostorg.github.io/hana/structboost_1_1hana_1_1type.html defines the class template `type` while I believe that it should be `basic_type`.  
  
Should the data types index `type`or `basic_type`?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-05-21 17:36:13 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-220790770  

`hana::type` is just a type alias template of a struct that is derived from `hana::basic_type`.  
  
Consider the following snippet:  
  
```  
    template <typename T>  
    struct type_impl {  
        struct _ : basic_type<T> { };  
    };  
  
    template <typename T>  
    using type = typename type_impl<T>::_;  
```  
  
The reason for this is that it's representation is meant to be implementation defined.

---

## Comment 2

> Username: viboes  
> Created at: 2016-05-22 09:28:52 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-220822755  

OK.   
  
Wondering if  `basic_type` merits an entry on the index of Data Types.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2016-05-22 18:23:19 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-220847611  

Oh I see. I'm not sure. As far as I can tell the separation of `type` and `basic_type` is a hack for "subtle reasons having to do with the ADL", and `basic_type` is not to be used on its own. I could be very wrong though.

---

## Comment 4

> Username: viboes  
> Created at: 2016-05-22 20:11:16 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-220853382  

The documentation says that the functions taking a type must use `basic_type<T>`, so it merits to be in the index IMHO.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-05-24 20:37:29 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-221393825  

As Jason suggested, `basic_type` should only be used for pattern matching on `hana::type`s. I feel like giving it its own Index entry would give it too much importance.  
  
If I had to re-do this part of Hana, I think I would make `hana::type` equivalent to the current `hana::basic_type`, and would provide a `hana::protect` alias equivalent to the current `hana::type` to handle the subtle ADL cases.

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-05-24 20:41:53 UTC  
> Url: https://github.com/boostorg/hana/issues/274#issuecomment-221394966  

> If I had to re-do this part of Hana, I think I would make hana::type equivalent to the current hana::basic_type, and would provide a hana::protect alias equivalent to the current hana::type to handle the subtle ADL cases.  
  
I like this, perhaps for Hana 2.0.
