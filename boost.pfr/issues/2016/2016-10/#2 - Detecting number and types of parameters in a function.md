# #2 - Detecting number and types of parameters in a function. [Closed]

> Username: aminroosta  
> Created at: 2016-10-08 23:26:40 UTC  
> Updated at: 2016-10-12 18:33:15 UTC  
> Closed at: 2016-10-09 12:58:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/2  

Not really an issue :-)  
I was watching your talk about this fantastic library, and someone asked you "how to find the number and types of arguments of a function".   
  
I'm using the following approach in [my library](https://github.com/aminroosta/sqlite_modern_cpp/blob/master/hdr/sqlite_modern_cpp/utility/function_traits.h). And it only works with `lambda` and `functors`. @apolukhin Sorry for throwing it here, but i will be really glad to know if this triggers any idea for you on how to support plain functions as well.  
  
``` c++  
        template<typename> struct function_traits;  
  
        template <typename Function>  
        struct function_traits : public function_traits<  
            decltype(&Function::operator())  
        > { };  
  
        template <  
            typename    ClassType,  
            typename    ReturnType,  
            typename... Arguments  
        >  
        struct function_traits<  
            ReturnType(ClassType::*)(Arguments...) const  
        > {  
            typedef ReturnType result_type;  
  
            template <std::size_t Index>  
            using argument = typename std::tuple_element<  
                Index,  
                std::tuple<Arguments...>  
            >::type;  
  
            static const std::size_t arity = sizeof...(Arguments);  
        };  
  
    /* support the non-const operator ()  
     * this will work with user defined functors */  
        template <  
            typename    ClassType,  
            typename    ReturnType,  
            typename... Arguments  
        >  
        struct function_traits<  
            ReturnType(ClassType::*)(Arguments...)  
        > {  
            typedef ReturnType result_type;  
  
            template <std::size_t Index>  
            using argument = typename std::tuple_element<  
                Index,  
                std::tuple<Arguments...>  
            >::type;  
  
            static const std::size_t arity = sizeof...(Arguments);  
};  
```

---

## Comment 1

> Username: AntonBikineev  
> Created at: 2016-10-09 12:40:46 UTC  
> Updated at: 2016-10-09 12:42:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/2#issuecomment-252484297  

@aminroosta   
Sorry for chiming in.  
Your approach is cool. Doesn't another straightforward partial specialization solve the problem with plain functions?  
  
```  
template <  
    typename    ReturnType,  
    typename... Arguments  
>  
struct function_traits<  
    ReturnType (Arguments...)  
> {  
    typedef ReturnType result_type;  
  
    template <std::size_t Index>  
    using argument = typename std::tuple_element<  
        Index,  
        std::tuple<Arguments...>  
    >::type;  
  
    static const std::size_t arity = sizeof...(Arguments);  
};  
  
void foo(char, double, std::string) {}  
  
static_assert(std::is_same<function_traits<decltype(foo)>::argument<0u>, char>::value);  
static_assert(std::is_same<function_traits<decltype(foo)>::argument<1u>, double>::value);  
static_assert(std::is_same<function_traits<decltype(foo)>::argument<2u>, std::string>::value);  
```

---

## Comment 2

> Username: aminroosta  
> Created at: 2016-10-09 12:58:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/2#issuecomment-252485046  

Thanks @AntonBikineev  
That's much simpler that i thought, it works perfectly 👍.

---

## Comment 3

> Username: apolukhin  
> Created at: 2016-10-12 18:33:15 UTC  
> Url: https://github.com/boostorg/pfr/issues/2#issuecomment-253298796  

It would be cool, if you could check the function_traits in Boost: http://www.boost.org/doc/libs/1_62_0/libs/type_traits/doc/html/boost_typetraits/reference/function_traits.html  
  
If some functionality is missing please make a pull request to update/improve the library.  
  
This may help a lot of people.
