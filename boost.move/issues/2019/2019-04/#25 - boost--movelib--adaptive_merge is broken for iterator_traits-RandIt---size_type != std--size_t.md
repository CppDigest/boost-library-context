# #25 - boost::movelib::adaptive_merge is broken for iterator_traits<RandIt>::size_type != std::size_t [Closed]

> Username: rainerdeyke  
> Created at: 2019-04-06 08:47:58 UTC  
> Updated at: 2019-04-11 17:46:36 UTC  
> Closed at: 2019-04-11 17:46:36 UTC  
> Url: https://github.com/boostorg/move/issues/25  

boost::movelib::adaptive_merge is broken for iterator_traits<RandIt>::size_type != std::size_t.  The problem is that merge_adaptive_ONlogN_recursive is called with different size parameters of different types, thereby preventing the template from being instantiated.  
  
Exact error message:  
`intermediate/rainer10/installed/mac32/include/boost/move/algo/detail/merge.hpp:969:5: error: no matching function for call to 'merge_adaptive_ONlogN_recursive'  
           merge_adaptive_ONlogN_recursive(first, middle, last, len1, len2, xbuf.begin(), uninitialized_len, comp);  
           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
intermediate/rainer10/installed/mac32/include/boost/move/algo/detail/adaptive_sort_merge.hpp:625:7: note: in instantiation of function template specialization 'boost::movelib::merge_adaptive_ONlogN<eldwood::raw_input_channel *, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> >, eldwood::raw_input_channel *>' requested here  
      merge_adaptive_ONlogN(first, middle, last, comp, xbuf.begin(), xbuf.capacity());  
      ^  
intermediate/rainer10/installed/mac32/include/boost/move/algo/adaptive_merge.hpp:118:7: note: in instantiation of function template specialization 'boost::movelib::detail_adaptive::stable_merge<eldwood::raw_input_channel *, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> >, boost::movelib::adaptive_xbuf<eldwood::raw_input_channel, eldwood::raw_input_channel *> >' requested here  
      stable_merge(first, first + middle, first + len, comp, xbuf);  
      ^  
intermediate/rainer10/installed/mac32/include/boost/move/algo/adaptive_merge.hpp:272:7: note: in instantiation of function template specialization 'boost::movelib::detail_adaptive::adaptive_merge_final_merge<eldwood::raw_input_channel *, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> >, boost::movelib::adaptive_xbuf<eldwood::raw_input_channel, eldwood::raw_input_channel *> >' requested here  
      adaptive_merge_final_merge   (first, len1, len2, collected, l_intbuf, l_block, use_internal_buf, xbuf_used, comp, xbuf);  
      ^  
intermediate/rainer10/installed/mac32/include/boost/move/algo/adaptive_merge.hpp:344:39: note: in instantiation of function template specialization 'boost::movelib::detail_adaptive::adaptive_merge_impl<eldwood::raw_input_channel *, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> >, boost::movelib::adaptive_xbuf<eldwood::raw_input_channel, eldwood::raw_input_channel *> >' requested here  
   ::boost::movelib::detail_adaptive::adaptive_merge_impl(first, size_type(middle - first), size_type(last - middle), comp, xbuf);  
                                      ^  
intermediate/rainer10/installed/mac32/include/boost/container/detail/flat_tree.hpp:133:20: note: in instantiation of function template specialization 'boost::movelib::adaptive_merge<eldwood::raw_input_channel *, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> > >' requested here  
   boost::movelib::adaptive_merge(braw, iraw, eraw, comp, eraw, dest.capacity()- dest.size());  
                   ^  
intermediate/rainer10/installed/mac32/include/boost/container/detail/flat_tree.hpp:889:8: note: in instantiation of function template specialization 'boost::container::dtl::flat_tree_container_inplace_merge<boost::container::vector<eldwood::raw_input_channel, boost::container::new_allocator<eldwood::raw_input_channel>, void>, boost::container::dtl::flat_tree_value_compare<std::__1::less<eldwood::raw_input_channel>, eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel> > >' requested here  
      (flat_tree_container_inplace_merge)(seq, it, this->priv_value_comp(), contiguous_tag);  
       ^  
intermediate/rainer10/installed/mac32/include/boost/container/flat_set.hpp:711:24: note: in instantiation of function template specialization 'boost::container::dtl::flat_tree<eldwood::raw_input_channel, boost::move_detail::identity<eldwood::raw_input_channel>, std::__1::less<eldwood::raw_input_channel>, void>::insert_unique<boost::container::vec_iterator<eldwood::raw_input_channel *, true> >' requested here  
      {  this->tree_t::insert_unique(first, last);  }  
                       ^  
intermediate/rainer10/obj/mac32__debug__startree/eldwood/core/input.cpp:251:19: note: in instantiation of function template specialization 'boost::container::flat_set<eldwood::raw_input_channel, std::__1::less<eldwood::raw_input_channel>, void>::insert<boost::container::vec_iterator<eldwood::raw_input_channel *, true> >' requested here  
    this->mapping.insert(rhs.mapping.begin(), rhs.mapping.end());  
                  ^  
intermediate/rainer10/installed/mac32/include/boost/move/algo/detail/merge.hpp:883:9: note: candidate template ignored: deduced conflicting types for parameter 'SizeType' ('unsigned int' vs. 'unsigned long')  
   void merge_adaptive_ONlogN_recursive  
`

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-04-10 21:46:17 UTC  
> Url: https://github.com/boostorg/move/issues/25#issuecomment-481879391  

Is the problem fixed with the commit?

---

## Comment 2

> Username: rainerdeyke  
> Created at: 2019-04-11 07:02:39 UTC  
> Url: https://github.com/boostorg/move/issues/25#issuecomment-481992409  

On 10.04.19 23:46, Ion Gaztañaga wrote:  
> Is the problem fixed with the commit?  
  
Yes.  
  
  
--   
Rainer Deyke (rainerd@eldwood.com)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2019-04-11 17:46:35 UTC  
> Url: https://github.com/boostorg/move/issues/25#issuecomment-482223148  

Thanks for the report!
