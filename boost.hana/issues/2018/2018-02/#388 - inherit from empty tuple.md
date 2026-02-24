# #388 - inherit from empty tuple [Closed]

> Username: Bergmann89  
> Created at: 2018-02-28 21:41:36 UTC  
> Updated at: 2018-05-30 17:04:34 UTC  
> Closed at: 2018-05-30 17:04:33 UTC  
> Url: https://github.com/boostorg/hana/issues/388  

A class inherited from an empty tuple can not be passed to boost::hana::unpack() (or any other hana method that uses unpack).  
  
```  
    template<typename... T>  
    struct test : hana::tuple<T...> { };  
  
    hana::for_each(test<> { }, [](auto){  
        cout << "fuu" << endl;  
    });  
```  
  
As soon as you have at least one template parameter, everything works as expected.  
  
```  
    hana::for_each(test<int> { }, [](auto){  
        cout << "fuu" << endl;  
    });  
```  
  
Possible solution for that issue:  
  
```  
    template <>  
    struct unpack_impl<tuple_tag> {  
  
        template<typename Xs, typename = void>  
        struct helper  
        {  
            template <typename F>  
            static constexpr decltype(auto) apply(Xs&& xs, F&& f)  
                { return hana::unpack(static_cast<Xs&&>(xs).storage_, static_cast<F&&>(f)); }  
        };  
  
        template<typename Xs>  
        struct helper<Xs,   std::enable_if_t<  
                                std::is_base_of<  
                                    tuple<>,  
                                    std::remove_cv_t<  
                                        std::remove_reference_t<Xs>  
                                    >  
                                >::value  
                            >>  
        {  
            template <typename F>  
            static constexpr decltype(auto) apply(Xs&&, F&& f)  
                { return static_cast<F&&>(f)(); }  
        };  
  
        template <typename Xs, typename F>  
        static constexpr decltype(auto) apply(Xs&& xs, F&& f) {  
            return helper<Xs>::apply(static_cast<Xs&&>(xs), static_cast<F&&>(f));  
        }  
    };  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-02-28 22:19:42 UTC  
> Updated at: 2018-03-01 00:12:51 UTC  
> Url: https://github.com/boostorg/hana/issues/388#issuecomment-369403347  

I remember having a discussion on this a couple of years ago, and the conclusion was that inheriting from these data structures is not supported. There doesn't appear to be any evidence of this in the documentation though.  
  
It is half working because of the public member `using hana_tag = tuple_tag` makes `tag_of` yield that tag which is incorrect if your type is meant to be different.  
  
IOW `tuple_tag` is meant for the set of `tuple<T...>` only.  
  
EDIT: See the manual for [Generalities on Containers](http://boostorg.github.io/hana/index.html#tutorial-containers)

---

## Comment 2

> Username: ldionne  
> Created at: 2018-03-03 08:49:19 UTC  
> Url: https://github.com/boostorg/hana/issues/388#issuecomment-370131794  

Indeed, inheriting from Hana sequences is not allowed and supported. I will make that clearer in the documentation, and I'll see if I can make `hana::tuple` `final`.

---

## Comment 3

> Username: ldionne  
> Created at: 2018-05-30 17:04:33 UTC  
> Url: https://github.com/boostorg/hana/issues/388#issuecomment-393238713  

This has been fixed in #391
