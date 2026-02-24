# #57 UB: comparing unrelated pointers [Merged]

> Username: ecatmur  
> Created at: 2021-02-15 10:42:53 UTC  
> Updated at: 2021-04-18 12:00:13 UTC  
> Merged at: 2021-04-18 12:00:00 UTC  
> Closed at: 2021-04-18 12:00:00 UTC  
> Url: https://github.com/boostorg/intrusive/pull/57  

detected by gcc10 asan (-fsanitize=pointer-compare):  
  
==13==ERROR: AddressSanitizer: invalid-pointer-pair: 0x612000031cc8 0x61d0000000d0  
    #0 0x18f3270 in boost::intrusive::bucket_plus_vtraits<...>::priv_get_bucket_num_no_hash_store(boost::intrusive::slist_iterator<boost::intrusive::trivial_value_traits<boost::intrusive::slist_node_traits<void*>, (boost::intrusive::link_mode_type)0>, false>, boost::move_detail::bool_<false>) libs/intrusive/include/boost/intrusive/hashtable.hpp:935  
    #1 0x18f3270 in boost::intrusive::hashtable_impl<...>::priv_get_bucket_num_hash_dispatch(boost::intrusive::slist_iterator<boost::intrusive::trivial_value_traits<boost::intrusive::slist_node_traits<void*>, (boost::intrusive::link_mode_type)0>, false>, boost::move_detail::bool_<false>) libs/intrusive/include/boost/intrusive/hashtable.hpp:3509  
    #2 0x18f3270 in boost::intrusive::hashtable_impl<...>::priv_get_bucket_num(boost::intrusive::slist_iterator<boost::intrusive::trivial_value_traits<boost::intrusive::slist_node_traits<void*>, (boost::intrusive::link_mode_type)0>, false>) /home/edward.catmur/packages/boost/1.75.0-maven6/libs/intrusive/include/boost/intrusive/hashtable.hpp:3500  
    #3 0x18f3270 in boost::move_detail::disable_if_convertible<...>::type boost::intrusive::hashtable_impl<...>::erase_and_dispose<...>(...) libs/intrusive/include/boost/intrusive/hashtable.hpp:2360

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-04-18 12:00:13 UTC  
> Url: https://github.com/boostorg/intrusive/pull/57#issuecomment-821980495  

Many thanks for the patch!

---
