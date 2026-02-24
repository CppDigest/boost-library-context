# #115 - Copy less tie_from_structure [Open]

> Username: precht  
> Created at: 2023-01-02 06:00:39 UTC  
> Updated at: 2023-01-02 10:05:38 UTC  
> Url: https://github.com/boostorg/pfr/issues/115  

Hi all, thanks for great work.  
  
The problem I have with tie_from_structure is that it calls copy assignments instead of move assignments.   
I think I understand why it happens: you convert the structure on the rhs of operator= into tuple of refs, and then assign it to the tuple of refs on the lhs.   
  
Have you ever been thinking about a way to convert the structure on the rhs into a tuple of values, not refs?  
To be more specific, I want to create a tuple, somehow call std::move on each member of the structure , and then assign that tuple (which now will be a rval) to the tuple of refs on the lhs of the operator?   
This way it should work same as for std::tie.  
  
I'm not familiar enough with your library to implement like that, so for now I'm using a workaround where I put both lhs args and rhs structure as a single function parameters:  
  
https://godbolt.org/z/ajrGMPMez    
  
Edit:  
  
Ok, so here is a working example of how I would like the tie_from_structure to behave:  
```  
  
template<std::size_t N, typename T>  
void tie_from_structure_assign_next(T &&)  
{  
    static_assert(N == boost::pfr::tuple_size_v<T>, "Wrong number of elements in tie_from_structure.");  
}  
  
template<std::size_t N, typename T, typename A, typename... Args>  
void tie_from_structure_assign_next(T &&t, A &a, Args &... args)  
{  
    a = std::move(boost::pfr::get<N>(t));  
    tie_from_structure_assign_next<boost::pfr::tuple_size_v<T> - sizeof...(Args), T>(std::forward<T>(t), args...);  
}  
  
template<typename T, typename A, typename... Args>  
void tie_from_structure_assign_all(T &&t, A &a, Args &... args)  
{  
    static_assert(boost::pfr::tuple_size_v<T> == sizeof...(Args) + 1, "Wrong number of elements in tie_from_structure.");  
    a = std::move(boost::pfr::get<0>(t));  
    tie_from_structure_assign_next<1, T>(std::forward<T>(t), args...);  
}  
  
template <typename T, typename Tuple, std::size_t... I>  
void tie_from_structure_assign(T &&t, Tuple &tuple, std::index_sequence<I...>) {  
    tie_from_structure_assign_all(std::forward<T>(t), std::get<I>(tuple)...);  
}  
  
template<typename... Elements>  
struct tie_from_structure_tuple : std::tuple<Elements&...>  
{  
    using base = std::tuple<Elements&...>;  
    using base::base;  
  
    template<typename T>  
    constexpr tie_from_structure_tuple &operator=(T &&t)  
    {  
        tie_from_structure_assign(std::forward<T>(t), dynamic_cast<base&>(*this), std::make_index_sequence<std::tuple_size<base>::value>());  
        return *this;  
    }  
};  
  
template<typename... Elements>  
constexpr tie_from_structure_tuple<Elements...> tie_from_structure(Elements &... args) noexcept  
{  
    return tie_from_structure_tuple<Elements...>(args...);  
}  
```  
  
Same example on godbolt: https://godbolt.org/z/Yq9nqdYnW  
  
With this implementation you call move assignment for all members of the structure.   
What do you think about it? Would it be possible to add sth like this to your library?
