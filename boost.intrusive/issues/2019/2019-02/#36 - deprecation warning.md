# #36 - deprecation warning [Closed]

> Username: vinniefalco  
> Created at: 2019-02-02 19:02:15 UTC  
> Updated at: 2020-01-16 15:11:09 UTC  
> Closed at: 2020-01-16 15:11:09 UTC  
> Url: https://github.com/boostorg/intrusive/issues/36  

See: https://wandbox.org/permlink/zsA9pNqip1v6bZod  
  
```  
/opt/wandbox/boost-1.68.0/gcc-head/include/boost/intrusive/detail/tree_iterator.hpp:73:32: note: because 'boost::intrusive::tree_iterator<boost::intrusive::mhtraits<boost::beast::http::basic_fields<std::allocator<char> >::value_type, boost::intrusive::set_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link> >, &boost::beast::http::basic_fields<std::allocator<char> >::value_type::set_hook_>, false>' has user-provided 'boost::intrusive::tree_iterator<ValueTraits, IsConst>::tree_iterator(const boost::intrusive::tree_iterator<typename boost::intrusive::iiterator<ValueTraits, IsConst, std::bidirectional_iterator_tag>::value_traits, false>&) [with ValueTraits = boost::intrusive::mhtraits<boost::beast::http::basic_fields<std::allocator<char> >::value_type, boost::intrusive::set_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link> >, &boost::beast::http::basic_fields<std::allocator<char> >::value_type::set_hook_>; bool IsConst = false; typename boost::intrusive::iiterator<ValueTraits, IsConst, std::bidirectional_iterator_tag>::value_traits = boost::intrusive::mhtraits<boost::beast::http::basic_fields<std::allocator<char> >::value_type, boost::intrusive::set_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link> >, &boost::beast::http::basic_fields<std::allocator<char> >::value_type::set_hook_>]'  
   73 |    BOOST_INTRUSIVE_FORCEINLINE tree_iterator(tree_iterator<value_traits, false> const& other)  
      |                                ^~~~~~~~~~~~~  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-05 10:27:53 UTC  
> Url: https://github.com/boostorg/intrusive/issues/36#issuecomment-469627450  

Is this duplicated from #34?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-05 14:36:06 UTC  
> Url: https://github.com/boostorg/intrusive/issues/36#issuecomment-469702622  

I am unsure

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-01-16 15:11:09 UTC  
> Url: https://github.com/boostorg/intrusive/issues/36#issuecomment-575196021  

Duplicated issue, closing.
