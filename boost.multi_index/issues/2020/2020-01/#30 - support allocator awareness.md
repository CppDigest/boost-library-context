# #30 - support allocator awareness [Closed]

> Username: star-e  
> Created at: 2020-01-08 03:44:06 UTC  
> Updated at: 2020-01-28 09:24:07 UTC  
> Closed at: 2020-01-28 09:24:07 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30  

Currently, we cannot use multi_index with c++17 std::pmr::polymorphic_allocator, because multi_index didn't provide copy/move constructors with an extra allocator parameter.  
  
For example, as shown in [https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/multi_index_container.html#constructors)](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/multi_index_container.html#constructors),  
multi_index only provided the following copy/move constructors  
`  
multi_index_container(const multi_index_container<Value,IndexSpecifierList,Allocator>& x);  
multi_index_container(multi_index_container<Value,IndexSpecifierList,Allocator>&& x);  
`  
  
  
The following two constructors are not provided.  
`  
multi_index_container(const multi_index_container<Value,IndexSpecifierList,Allocator>& x, const allocator_type& alloc);  
multi_index_container(multi_index_container<Value,IndexSpecifierList,Allocator>&& x, const allocator_type& alloc);  
`  
  
According to [https://en.cppreference.com/w/cpp/named_req/AllocatorAwareContainer](https://en.cppreference.com/w/cpp/named_req/AllocatorAwareContainer), these two special constructors are required when used with nested containers, such as  
`  
std::pmr::vector<boost::multi_index::multi_index_container<T, ..., std::pmr::polymorphic_allocator<T>>>.  
`  
  
Could you please add these special constructors?  
  
I have tested the following code in boost 1.71. It seems working, but I am not very sure about it.  
`  multi_index_container(  
    const multi_index_container<Value,IndexSpecifierList,Allocator>& x, const Allocator& al):  
    bfm_allocator(al),  
    bfm_header(),  
    super(x),  
    node_count(0)  
  {  
    copy_map_type map(bfm_allocator::member,x.size(),x.header(),header());  
    for(const_iterator it=x.begin(),it_end=x.end();it!=it_end;++it){  
      map.clone(it.get_node());  
    }  
    super::copy_(x,map);  
    map.release();  
    node_count=x.size();  
    /* Not until this point are the indices required to be consistent,  
     * hence the position of the invariant checker.  
     */  
    BOOST_MULTI_INDEX_CHECK_INVARIANT;  
  }  
  multi_index_container(BOOST_RV_REF(multi_index_container) x, const Allocator& al):  
    bfm_allocator(al),  
    bfm_header(),  
    super(x,detail::do_not_copy_elements_tag()),  
    node_count(0)  
  {  
    BOOST_MULTI_INDEX_CHECK_INVARIANT;  
    BOOST_MULTI_INDEX_CHECK_INVARIANT_OF(x);  
    swap_elements_(x);  
  }  
`  
  
Thanks a lot!

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-01-08 08:47:54 UTC  
> Updated at: 2020-01-26 12:33:15 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30#issuecomment-571949205  

Yes, Boost.MultiIndex is not AllocatorAware, I can add this to the lib backlog.  
  
As for the allocator-aware ctors you sketched:  
  
- The copy ctor looks right.  
- The move ctor should only use `swap_elements_` if `x.get_allocator()==al`.   
  
Besides, as you know, there's more to being AllocatorAware than providing these two ctors (like handling `propagate_on_container_*` traits).

---

## Comment 2

> Username: star-e  
> Created at: 2020-01-08 10:05:47 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30#issuecomment-571978824  

Yes, making container allocator aware is not an easy job.  
Thank you for your suggestion and I really appreciate your works!

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-01-26 12:36:40 UTC  
> Updated at: 2020-01-26 12:37:20 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30#issuecomment-578497935  

Hi, allocator awareness has been added in the sequence of commits:  
  
* a8d34a56f78eb11b09abefe9dc0a9ad2f2815a01  
* ff46d35312cb5ecd2d342496f46240ad5c95ab23  
* adbb136e436247104b3d45dd00c1963ea2038e54  
* 3e8928834cee227b82b64b4f521e4b86ce6132bd  
* 81dbe86f25c23e075ac31702b2f4d1239d5f1a10  
  
You can download the current develop branch code at:  
  
https://github.com/boostorg/multi_index/tree/81dbe86f25c23e075ac31702b2f4d1239d5f1a10  
  
Care to test locally and report any problems you find?  
  
Best regards,

---

## Comment 4

> Username: star-e  
> Created at: 2020-01-26 13:18:24 UTC  
> Updated at: 2020-01-27 08:13:42 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30#issuecomment-578501070  

Sure！I will test it with my codebase.  
  
Thanks a lot！  
  
Best regards，

---

## Comment 5

> Username: star-e  
> Created at: 2020-01-28 09:17:06 UTC  
> Url: https://github.com/boostorg/multi_index/issues/30#issuecomment-579151159  

I have tested the code, it works as expected. Great job!  
If I encounter any problem in the future, I will post it here asap.  
  
Best regards,
