# #44 - [Feature request] make wrappers [Open]

> Username: denzor200  
> Created at: 2021-07-06 17:18:46 UTC  
> Updated at: 2021-07-07 07:21:13 UTC  
> Url: https://github.com/boostorg/multi_index/issues/44  

Inspired by `std::make_tuple` for `std::tuple`..  
I recently tried to create 4 simple make methods for types from this library:  
```  
template<typename T, typename M>  
ordered_unique<T, M> make_ordered_unique(T, M)  
{  
    return {};  
}  
   
template<typename T, typename M>  
ordered_non_unique<T, M> make_ordered_non_unique(T, M)  
{  
    return {};  
}  
   
template<typename... T>  
indexed_by<T...> make_indexed_by(T...)  
{  
    return {};  
}  
   
template<typename T, typename I>  
multi_index_container<T, I> make_multi_index_container(I)  
{  
    return {};  
}  
```  
  
These methods allow me to write like this:  
```  
auto es = make_multi_index_container<employee>(make_indexed_by(  
        make_ordered_unique(     tag<id>{},  BOOST_MULTI_INDEX_MEMBER(employee,int,id){}),  
        make_ordered_non_unique( tag<name>{},BOOST_MULTI_INDEX_MEMBER(employee,std::string,name){}),   
        make_ordered_non_unique( tag<age>{}, BOOST_MULTI_INDEX_MEMBER(employee,int,age){})));  
```  
  
..And the same code without methods (how it works now):  
```  
typedef multi_index_container<  
  employee,  
  indexed_by<  
    ordered_unique<  
      tag<id>,  BOOST_MULTI_INDEX_MEMBER(employee,int,id)>,  
    ordered_non_unique<  
      tag<name>,BOOST_MULTI_INDEX_MEMBER(employee,std::string,name)>,  
    ordered_non_unique<  
      tag<age>, BOOST_MULTI_INDEX_MEMBER(employee,int,age)> >  
> employee_set;  
  
employee_set es;  
```  
  
In my opinion, the variant with make-methods is much better. Why not support for these methods on the library side?

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-07-07 07:21:13 UTC  
> Url: https://github.com/boostorg/multi_index/issues/44#issuecomment-875355181  

Hi Denis, thank you for the suggestion. I don't see this feature as particularly appealing, at least in its current form:  
* There's little/none type saving, the alternative declaration involves the same info (I mean, there is no actual type deduction as with `std::make_tuple`).  
* To be complete, `make_multi_index_container` should have to take care of all [index construction arguments](https://www.boost.org/libs/multi_index/doc/tutorial/creation.html#ctor_args_list) rather than simply default-constructing the container. Achieving this is an interesting, but not trivial, exercise.
