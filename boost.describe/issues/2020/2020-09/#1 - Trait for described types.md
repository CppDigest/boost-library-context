# #1 - Trait for described types [Closed]

> Username: vinipsmaker  
> Created at: 2020-09-25 13:36:16 UTC  
> Updated at: 2021-10-26 14:50:10 UTC  
> Closed at: 2021-10-26 14:50:10 UTC  
> Url: https://github.com/boostorg/describe/issues/1  

Right now I can create overload for functions that will only participate if there is reflection for them with Hana (trait `boost::hana::Struct<T>`). It'd be nice if describe also have a trait for types that were "described".

---

## Comment 1

> Username: pdimov  
> Created at: 2020-09-25 14:42:25 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-698969699  

The `describe_...` traits cause a substitution failure if invoked on a type that hasn't been annotated. You can use this to restrict overloads.  
```  
template<class E, class D = describe_enumerators<E>> void f( E e ) { ... }  
```

---

## Comment 2

> Username: vinipsmaker  
> Created at: 2020-09-25 15:12:59 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-698986485  

That's a little of inconvenience thou. Couldn't you make it easier by converting this pattern into a trait as Hana does? It's fairly straightforward to combine multiple (possibly negated) traits through `std::enable_if`. They do read easier.

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-06-14 16:29:46 UTC  
> Updated at: 2021-06-14 16:30:41 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-860821291  

> The `describe_...` traits cause a substitution failure if invoked on a type that hasn't been annotated.  
  
Should'nt the following code do the trick (apparently it's not) ?  
  
```cpp  
namespace boost::describe  
{  
    template <typename T, typename Enable = void>  
    struct is_described : std::false_type {};  
  
    template <typename T>  
    struct is_described<T, describe_members<T, mod_any_access>> : std::true_type {};  
  
    template <class T>  
    inline constexpr bool is_described_v = is_described<T>::value;  
}  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2021-06-14 16:38:30 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-860826884  

You need to wrap `describe_members<T, mod_any_access>` in a `void_t`, because it doesn't match the primary `void` otherwise.

---

## Comment 5

> Username: sdebionne  
> Created at: 2021-06-15 07:35:37 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-861260378  

Thank you! For the records, here is a working `is_described<>`:  
  
```cpp  
namespace boost::describe  
{  
    template <typename T, typename Enable = void>  
    struct is_described : std::false_type {};  
  
    template <typename T>  
    struct is_described<T, std::void_t<describe_members<T, mod_any_access>>> : std::true_type {};  
  
    template <class T>  
    inline constexpr bool is_described_v = is_described<T>::value;  
}  
```  
  
I personally think `is_described<>` can be useful, for instance when reflecting recursively:  
  
```cpp  
template <class T, typename Md = boost::describe::describe_members<T, boost::describe::mod_any_access>>  
void print(std::ostream& os, T const& t, int indent = 0)  
{  
    bool first = true;  
    boost::mp11::mp_for_each<Md>([&](auto D) {  
        for (int i = 0 ; i < indent ; i++)  
            os << "\t";  
  
        os << "." << D.name << " = " << t.*D.pointer << std::endl;  
  
        // Recursively print class members if they are described  
        using return_t = std::decay_t<boost::callable_traits::return_type_t<decltype(D.pointer)>>;  
        if constexpr (std::is_class_v<return_t> && boost::describe::is_described_v<return_t>)  
            print(os, t.*D.pointer, ++indent);  
    });  
}  
```  
  
It's pretty straightforward to implement but would you be interested in a PR for `is_described<>` that would support struct/class and enumerators?

---

## Comment 6

> Username: pdimov  
> Created at: 2021-10-26 14:50:10 UTC  
> Url: https://github.com/boostorg/describe/issues/1#issuecomment-952019072  

This is now implemented as three separate traits `has_describe_enumerators`, `has_describe_bases` and `has_describe_members`. The latter two in practice give the same result and are effectively equivalent to the `is_described` trait above.
