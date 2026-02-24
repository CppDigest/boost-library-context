# #173 - [Tutorial] Document how hana::when can be used to spice up tag-dispatched methods [Closed]

> Username: ldionne  
> Created at: 2015-09-05 20:07:41 UTC  
> Updated at: 2015-11-30 01:26:52 UTC  
> Closed at: 2015-11-30 01:26:52 UTC  
> Url: https://github.com/boostorg/hana/issues/173  



---

## Comment 1

> Username: FrankHB  
> Created at: 2015-11-02 00:47:42 UTC  
> Url: https://github.com/boostorg/hana/issues/173#issuecomment-152883837  

Is it true that `void_t` + `enable_if_t` is not enough here?

---

## Comment 2

> Username: ldionne  
> Created at: 2015-11-02 22:54:54 UTC  
> Url: https://github.com/boostorg/hana/issues/173#issuecomment-153184090  

You're wondering whether `when<>` is equivalent to `void_t` + `enable_if`? They are not, because using `when<>` allows us to add an extra level of priority for partial specializations. Specifically, here's how we do it:  
  
``` c++  
template <typename T, typename = void>  
struct Foo;  
  
template <typename T>  
struct Foo<T> : Foo<T, when<true>> { };  
```  
  
Then, when partially specializing `Foo`, you have three choices. The first one is explicit specialization:  
  
``` c++  
template <>  
struct Foo<hana::tuple<int>> { };  
```  
  
This has the highest priority, in the sense that `Foo<hana::tuple<int>>` will now refer to that specialization. The second level is a normal partial specialization:  
  
``` c++  
template <typename ...T>  
struct Foo<hana::tuple<T...>> { };  
```  
  
Now, `Foo<hana::tuple<int>>` would still refer to the first one, but `Foo<hana::tuple<float>>` would refer to the second one. This third one is `when`-based specialization:  
  
``` c++  
template <typename Ts>  
struct Foo<Ts, hana::when<hana::Sequence<Ts>::value>> { };  
```  
  
Now, `Foo` will work with anything that is a `Sequence`. When it is used with a `hana::tuple`, however, the second one will be preferred. However, if we used   
  
``` c++  
template <typename Ts>  
struct Foo<Ts, std::void_t<std::enable_if_t<hana::Sequence<Ts>::value>>> { };  
```  
  
then using `Foo` with a `hana::tuple` would be ambiguous, because both this specialization and the one for `tuple<T...>` would match.

---

## Comment 3

> Username: FrankHB  
> Created at: 2015-11-03 00:46:45 UTC  
> Url: https://github.com/boostorg/hana/issues/173#issuecomment-153205901  

Got it. Thanks.
