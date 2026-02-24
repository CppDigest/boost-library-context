# #44 - safe_base only works for scalar types [Closed]

> Username: akrzemi1  
> Created at: 2017-03-15 22:51:35 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-14 22:09:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/44  

Class template `safe_base` is defined as follows:  
```c++  
template<  
    class Stored,  
    Stored Min, // <-- non-type parameter  
    Stored Max, // <-- non-type parameter  
    class P,  
    class E   
>  
class safe_base;  
```  
Note that two values of type `Stored` are passed as template non-type parameters. This means that `Stored` can only be a (built-in) scalar type. Because only scalar types (apart from pointers and references) are allowed as non-type template parameters.  
  
From reading the documentation one gets the impression that the library would work for any type that models `Integer<T>` concept, but that is not true. Even `safe<safe<int>>` doesn't work.  
  
Technically, this could be fixed by using a different implementation of `safe_base`:  
```c++  
template<  
    class Stored,  
    class MinMaxPolicy, // <-- type parameter  
    class P,  
    class E   
>  
class safe_base  
{  
    constexpr Stored Min = MinMaxPolicy::min();  
    constexpr Stored Max = MinMaxPolicy::max();  
    // ...  
};  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-14 22:09:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/44#issuecomment-413032365  

Of course you're correct on all this.  For now though I'm not going to address it.  But I have added a long section in Pending issues section discussing it.  For what it's worth, my preference would be to do something like:  
```  
template<typename T>  
struct range {  
    T m_lowest;  
    T m_highest;  
    // default implementation  
    range(  
        const & T t_min,   
        const & T t_max  
    ) :  
        m_lowest(std::numeric_limits<T>::lowest(t_min),  
        m_highest(std::numeric_limits<T>::max(t_max)  
    {}  
};  
```  
Then redeclare safe_base, etc.  accordingly

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-08-17 07:08:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/44#issuecomment-413777915  

One thing to add. In C++20 we will be able to use other literal types as template non-type parameters, not only scalars:  
  
```c++  
template< std::pair<int, long> P > // will be valid in C++20  
struct X;   
```  
  
It will not address all the types, but a wide variety.
