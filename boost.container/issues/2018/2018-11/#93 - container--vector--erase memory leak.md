# #93 - container::vector::erase memory leak [Closed]

> Username: longpractice  
> Created at: 2018-11-19 12:23:06 UTC  
> Updated at: 2018-12-01 12:00:39 UTC  
> Closed at: 2018-12-01 12:00:38 UTC  
> Url: https://github.com/boostorg/container/issues/93  

In boost container 1.68 and 1.67, the following code on boost::container::vector would trigger a memory leak:  
```cpp  
while (true)  
{  
    vector<vector<int>> vv{ {1}, {1} };  
    vv[0].reserve(1000000);  
    vv.erase(vv.begin());  
    vv.insert(vv.begin(), { 1 });  
}  
```  
---  
Below are some analysis of the cause of the problem.  
  
To avoid confusions, we firstly do the following representation:  
  
```cpp  
vector<int> v0 = vv[0];  
vector<int> v1 = vv[1];  
auto i0 = vv.begin();  
```  
  
Looking into the implementation of `vv.erase(i0)` gives a clue about the problem:  
  
```cpp  
iterator erase(const_iterator position)  
{  
    BOOST_ASSERT(this->priv_in_range(position));  
    const pointer p = vector_iterator_get_ptr(position);  
    T *const pos_ptr = boost::movelib::to_raw_pointer(p);  
    T *const beg_ptr = this->priv_raw_begin();  
    T *const new_end_ptr = ::boost::container::move(pos_ptr + 1, beg_ptr + this->m_holder.m_size, pos_ptr);  
    //Move elements forward and destroy last  
    this->priv_destroy_last(pos_ptr != new_end_ptr);  
    return iterator(p);  
}  
```  
  
In this case, `vv::erase(i0)` firstly calls `v0 = move(v1)` using v0's move asignment operator which delegates the task to `v0.priv_move_assign(move(v1))`:  
  
```cpp  
template<class OtherAllocator>  
void priv_move_assign(BOOST_RV_REF_BEG vector<T, OtherAllocator> BOOST_RV_REF_END x  
    , typename dtl::disable_if_or  
        < void  
        , dtl::is_version<OtherAllocator, 0>  
        , dtl::is_different<OtherAllocator, allocator_type>  
        >::type * = 0)  
{  
............  
    //Resources can be transferred if both allocators are  
    //going to be equal after this function (either propagated or already equal)  
    if(are_both_propagable){  
        //Destroy objects but retain memory in case x reuses it in the future  
        this->clear();  
        this->m_holder.swap_resources(x.m_holder);  
    }  
.............  
}  
```  
  
As it says in the the comment "`//Destroy objects but retain memory in case x reuses it in the future`", calling `v0.clear()` and `v0->m_holder.swap_resources(v1.m_holder)` makes v1 holding v0's unfreed memory(`v0.clear()` will not free v0's memory).  
  
Then going back to `vv.erase(i0)`. It then tries to destroy v1 which is holding v0's unfreed memory by calling `vv.priv_destroy_last(true)`:  
  
```c  
void priv_destroy_last(const bool moved = false) BOOST_NOEXCEPT_OR_NOTHROW  
{  
    (void)moved;  
    const bool skip_destructor = value_traits::trivial_dctr || (value_traits::trivial_dctr_after_move && moved);  
    if(!skip_destructor){  
        value_type* const p = this->priv_raw_end() - 1;  
        allocator_traits_type::destroy(this->get_stored_allocator(), p);  
    }  
    --this->m_holder.m_size;  
}  
```  
It, however, tells that `value::traits::trivial_dctr_after_move` (which is `vector<int>::traits::trivial_dctr_after_move`) is true and skips the destructor of v1 which is holding v0's unfreed memory, and thereby creates a memory leak.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-12-01 12:00:38 UTC  
> Url: https://github.com/boostorg/container/issues/93#issuecomment-443421231  

Many thanks for the report!
