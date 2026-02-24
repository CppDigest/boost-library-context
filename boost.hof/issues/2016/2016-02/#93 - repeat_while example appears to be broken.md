# #93 - repeat_while example appears to be broken [Closed]

> Username: tzlaine  
> Created at: 2016-02-10 17:54:22 UTC  
> Updated at: 2016-03-07 00:01:04 UTC  
> Closed at: 2016-02-11 02:45:03 UTC  
> Url: https://github.com/boostorg/hof/issues/93  

The repeat_while example is this:  
  
```  
struct increment  
{  
    template<class T>  
    constexpr std::integral_constant<int, T::value + 1> operator()(T) const  
    {  
        return std::integral_constant<int, T::value + 1>();  
    }  
};  
  
struct not_6  
{  
    template<class T>  
    constexpr std::integral_constant<bool, (T::value != 6)>   
    operator()(T) const  
    {  
        return std::integral_constant<bool, (T::value != 6)>();  
    }  
};  
  
typedef std::integral_constant<int, 1> one;  
typedef std::integral_constant<int, 6> six;  
  
typedef decltype(boost::fit::repeat_while(not_6())(increment())(std::integral_constant<int, 1>())) increment_until_6;  
  
  
constexpr auto increment_until_6 = boost::fit::repeat_while(not_6())(increment());  
static_assert(std::is_same<six, decltype(increment_until_6(one()))>::value, "Error");  
```  
  
This looks to me like it won't compile because "increment_until_6" is declared twice, once as a typedef and once as a value.  
  
This is why Boost libraries use BoostBook.  Its use prevents your sample code from being broken.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-02-11 02:45:02 UTC  
> Url: https://github.com/boostorg/hof/issues/93#issuecomment-182678166  

I fixed the example now. The extra typedef shouldn't be there. I also have all the examples compiling in the CI now.
