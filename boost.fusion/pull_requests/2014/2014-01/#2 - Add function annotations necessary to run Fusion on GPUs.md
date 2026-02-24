# #2 Add function annotations necessary to run Fusion on GPUs [Merged]

> Username: ericniebler  
> Created at: 2014-01-28 22:48:04 UTC  
> Updated at: 2014-07-22 15:20:17 UTC  
> Merged at: 2014-01-28 23:04:23 UTC  
> Closed at: 2014-01-28 23:04:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/2  

I want to be able to use Fusion on GPUs. That requires that `__host__ __device__` annotations be added to all functions. It's a pain, but I've already done the hard work for you. :-)  
  
There is a Boost.Config macro for this: `BOOST_GPU_ENABLED`. Instead, I've gone with a Fusion-specific macro, `BOOST_FUSION_GPU_ENABLED`, and added a `config.hpp` that sets it to `BOOST_GPU_ENABLED` by default.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-01-28 23:04:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/2#issuecomment-33538078  

Wow, that is amazing! What can I say? Awesome!  
  
Merged.  
  
On 1/29/14, 6:48 AM, Eric Niebler wrote:  
  
> I want to be able to use Fusion on GPUs. That requires that |**host** **device**|  
> annotations be added to all functions. It's a pain, but I've already done the hard work  
> for you. :-)  
>   
> There is a Boost.Config macro for this: |BOOST_GPU_ENABLED|. Instead, I've gone with a  
> Fusion-specific macro, |BOOST_FUSION_GPU_ENABLED|, and added a |config.hpp| that sets it  
> to |BOOST_GPU_ENABLED| by default.  
>   
> ---  
>   
> ```  
>     You can merge this Pull Request by running  
> ```  
>   
>    git pull https://github.com/ericniebler/fusion gpu-enabled-2  
>   
> Or view, comment on, or merge it at:  
>   
> https://github.com/boostorg/fusion/pull/2  
>   
> ```  
>     Commit Summary  
> ```  
> - gpu-enable functions  
> - gpu-enable more functions, but not stream inserters and extractors  
> - Merge remote-tracking branch 'upstream/develop' into gpu-enabled-2  
> - gpu-enable vector assign  
> - final gpu-enabling tweaks  
> - Merge remote-tracking branch 'upstream/develop' into gpu-enabled-2  
>     
>   ```  
>   File Changes  
>   ```  
> - _M_ include/boost/fusion/adapted.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-0 (1)  
> - _M_ include/boost/fusion/adapted/adt.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-1 (1)  
> - _M_ include/boost/fusion/adapted/adt/adapt_adt.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-2 (1)  
> - _M_ include/boost/fusion/adapted/adt/adapt_adt_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-3 (1)  
> - _M_ include/boost/fusion/adapted/adt/adapt_assoc_adt.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-4 (1)  
> - _M_ include/boost/fusion/adapted/adt/adapt_assoc_adt_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-5 (1)  
> - _M_ include/boost/fusion/adapted/adt/detail/adapt_base.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-6 (12)  
> - _M_ include/boost/fusion/adapted/adt/detail/extension.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-7 (2)  
> - _M_ include/boost/fusion/adapted/array/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-8 (2)  
> - _M_ include/boost/fusion/adapted/array/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-9 (2)  
> - _M_ include/boost/fusion/adapted/array/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-10 (2)  
> - _M_ include/boost/fusion/adapted/array/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-11 (2)  
> - _M_ include/boost/fusion/adapted/array/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-12 (1)  
> - _M_ include/boost/fusion/adapted/array/is_view_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-13 (1)  
> - _M_ include/boost/fusion/adapted/array/size_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-14 (1)  
> - _M_ include/boost/fusion/adapted/array/tag_of.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-15 (1)  
> - _M_ include/boost/fusion/adapted/array/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-16 (1)  
> - _M_ include/boost/fusion/adapted/array/value_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-17 (1)  
> - _M_ include/boost/fusion/adapted/boost_array.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-18 (1)  
> - _M_ include/boost/fusion/adapted/boost_array/array_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-19 (5)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-20 (2)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-21 (2)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/category_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-22 (1)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-23 (2)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-24 (1)  
> - _M_ include/boost/fusion/adapted/boost_array/detail/is_view_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-25 (1)  
> - _M_ include/boost/fusion/adapted/boost_array/tag_of.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-26 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-27 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple/boost_tuple_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-28 (9)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-29 (2)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-30 (2)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-31 (2)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-32 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/is_view_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-33 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/size_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-34 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-35 (1)  
> - _M_ include/boost/fusion/adapted/boost_tuple/tag_of.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-36 (1)  
> - _M_ include/boost/fusion/adapted/mpl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-37 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-38 (2)  
> - _M_ include/boost/fusion/adapted/mpl/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-39 (2)  
> - _M_ include/boost/fusion/adapted/mpl/detail/category_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-40 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/empty_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-41 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-42 (2)  
> - _M_ include/boost/fusion/adapted/mpl/detail/has_key_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-43 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-44 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/is_view_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-45 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/size_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-46 (1)  
> - _M_ include/boost/fusion/adapted/mpl/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-47 (1)  
> - _M_ include/boost/fusion/adapted/mpl/mpl_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-48 (6)  
> - _M_ include/boost/fusion/adapted/std_pair.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-49 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-50 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-51 (2)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-52 (2)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-53 (2)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-54 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/is_view_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-55 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/size_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-56 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-57 (1)  
> - _M_ include/boost/fusion/adapted/std_tuple/std_tuple_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-58 (6)  
> - _M_ include/boost/fusion/adapted/std_tuple/tag_of.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-59 (1)  
> - _M_ include/boost/fusion/adapted/struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-60 (1)  
> - _M_ include/boost/fusion/adapted/struct/adapt_assoc_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-61 (1)  
> - _M_ include/boost/fusion/adapted/struct/adapt_assoc_struct_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-62 (1)  
> - _M_ include/boost/fusion/adapted/struct/adapt_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-63 (1)  
> - _M_ include/boost/fusion/adapted/struct/adapt_struct_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-64 (1)  
> - _M_ include/boost/fusion/adapted/struct/define_assoc_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-65 (1)  
> - _M_ include/boost/fusion/adapted/struct/define_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-66 (1)  
> - _M_ include/boost/fusion/adapted/struct/define_struct_inline.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-67 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/adapt_base.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-68 (3)  
> - _M_ include/boost/fusion/adapted/struct/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-69 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-70 (3)  
> - _M_ include/boost/fusion/adapted/struct/detail/define_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-71 (11)  
> - _M_ include/boost/fusion/adapted/struct/detail/define_struct_inline.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-72 (13)  
> - _M_ include/boost/fusion/adapted/struct/detail/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-73 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-74 (3)  
> - _M_ include/boost/fusion/adapted/struct/detail/extension.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-75 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-76 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/namespace.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-77 (1)  
> - _M_ include/boost/fusion/adapted/struct/detail/proxy_type.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-78 (2)  
> - _M_ include/boost/fusion/algorithm.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-79 (1)  
> - _M_ include/boost/fusion/algorithm/auxiliary.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-80 (1)  
> - _M_ include/boost/fusion/algorithm/auxiliary/copy.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-81 (5)  
> - _M_ include/boost/fusion/algorithm/auxiliary/move.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-82 (5)  
> - _M_ include/boost/fusion/algorithm/iteration.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-83 (1)  
> - _M_ include/boost/fusion/algorithm/iteration/accumulate.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-84 (3)  
> - _M_ include/boost/fusion/algorithm/iteration/accumulate_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-85 (3)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-86 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/for_each.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-87 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/preprocessed/fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-88 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/preprocessed/iter_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-89 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/preprocessed/reverse_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-90 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/preprocessed/reverse_iter_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-91 (11)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/segmented_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-92 (4)  
> - _M_ include/boost/fusion/algorithm/iteration/detail/segmented_for_each.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-93 (4)  
> - _M_ include/boost/fusion/algorithm/iteration/fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-94 (1)  
> - _M_ include/boost/fusion/algorithm/iteration/fold_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-95 (4)  
> - _M_ include/boost/fusion/algorithm/iteration/for_each.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-96 (3)  
> - _M_ include/boost/fusion/algorithm/iteration/for_each_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-97 (3)  
> - _M_ include/boost/fusion/algorithm/iteration/iter_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-98 (1)  
> - _M_ include/boost/fusion/algorithm/iteration/iter_fold_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-99 (4)  
> - _M_ include/boost/fusion/algorithm/iteration/reverse_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-100 (1)  
> - _M_ include/boost/fusion/algorithm/iteration/reverse_fold_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-101 (4)  
> - _M_ include/boost/fusion/algorithm/iteration/reverse_iter_fold.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-102 (1)  
> - _M_ include/boost/fusion/algorithm/iteration/reverse_iter_fold_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-103 (4)  
> - _M_ include/boost/fusion/algorithm/query.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-104 (1)  
> - _M_ include/boost/fusion/algorithm/query/all.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-105 (2)  
> - _M_ include/boost/fusion/algorithm/query/any.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-106 (2)  
> - _M_ include/boost/fusion/algorithm/query/count.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-107 (2)  
> - _M_ include/boost/fusion/algorithm/query/count_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-108 (2)  
> - _M_ include/boost/fusion/algorithm/query/detail/all.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-109 (10)  
> - _M_ include/boost/fusion/algorithm/query/detail/any.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-110 (10)  
> - _M_ include/boost/fusion/algorithm/query/detail/count.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-111 (5)  
> - _M_ include/boost/fusion/algorithm/query/detail/count_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-112 (10)  
> - _M_ include/boost/fusion/algorithm/query/detail/find_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-113 (8)  
> - _M_ include/boost/fusion/algorithm/query/detail/segmented_find.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-114 (5)  
> - _M_ include/boost/fusion/algorithm/query/detail/segmented_find_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-115 (5)  
> - _M_ include/boost/fusion/algorithm/query/find.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-116 (3)  
> - _M_ include/boost/fusion/algorithm/query/find_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-117 (3)  
> - _M_ include/boost/fusion/algorithm/query/find_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-118 (3)  
> - _M_ include/boost/fusion/algorithm/query/find_if_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-119 (3)  
> - _M_ include/boost/fusion/algorithm/query/none.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-120 (2)  
> - _M_ include/boost/fusion/algorithm/transformation.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-121 (1)  
> - _M_ include/boost/fusion/algorithm/transformation/clear.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-122 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/preprocessed/zip10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-123 (9)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/preprocessed/zip20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-124 (19)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/preprocessed/zip30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-125 (29)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/preprocessed/zip40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-126 (39)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/preprocessed/zip50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-127 (49)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/replace.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-128 (5)  
> - _M_ include/boost/fusion/algorithm/transformation/detail/replace_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-129 (5)  
> - _M_ include/boost/fusion/algorithm/transformation/erase.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-130 (6)  
> - _M_ include/boost/fusion/algorithm/transformation/erase_key.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-131 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/filter.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-132 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/filter_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-133 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/insert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-134 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/insert_range.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-135 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/join.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-136 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/pop_back.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-137 (6)  
> - _M_ include/boost/fusion/algorithm/transformation/pop_front.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-138 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/push_back.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-139 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/push_front.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-140 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/remove.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-141 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/remove_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-142 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/replace.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-143 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/replace_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-144 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/reverse.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-145 (2)  
> - _M_ include/boost/fusion/algorithm/transformation/transform.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-146 (3)  
> - _M_ include/boost/fusion/algorithm/transformation/zip.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-147 (1)  
> - _M_ include/boost/fusion/container.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-148 (1)  
> - _M_ include/boost/fusion/container/deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-149 (1)  
> - _M_ include/boost/fusion/container/deque/back_extended_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-150 (4)  
> - _M_ include/boost/fusion/container/deque/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-151 (3)  
> - _M_ include/boost/fusion/container/deque/deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-152 (15)  
> - _M_ include/boost/fusion/container/deque/deque_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-153 (1)  
> - _M_ include/boost/fusion/container/deque/deque_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-154 (5)  
> - _M_ include/boost/fusion/container/deque/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-155 (2)  
> - _M_ include/boost/fusion/container/deque/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-156 (2)  
> - _M_ include/boost/fusion/container/deque/detail/build_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-157 (4)  
> - _M_ include/boost/fusion/container/deque/detail/convert_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-158 (2)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/as_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-159 (2)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/build_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-160 (2)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-161 (13)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/deque_forward_ctor.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-162 (3)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/deque_keyed_values.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-163 (2)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/deque_keyed_values_call.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-164 (2)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-165 (10)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-166 (20)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-167 (30)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-168 (40)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-169 (50)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-170 (40)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-171 (70)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-172 (100)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-173 (130)  
> - _M_ include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-174 (160)  
> - _M_  
>   include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque_keyed_values10.hpp https://github.com/boostorg/fusion/pull/2/files#diff-175  
>   (22)  
> - _M_  
>   include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque_keyed_values20.hpp https://github.com/boostorg/fusion/pull/2/files#diff-176  
>   (42)  
> - _M_  
>   include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque_keyed_values30.hpp https://github.com/boostorg/fusion/pull/2/files#diff-177  
>   (62)  
> - _M_  
>   include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque_keyed_values40.hpp https://github.com/boostorg/fusion/pull/2/files#diff-178  
>   (82)  
> - _M_  
>   include/boost/fusion/container/deque/detail/cpp03/preprocessed/deque_keyed_values50.hpp https://github.com/boostorg/fusion/pull/2/files#diff-179  
>   (102)  
> - _M_ include/boost/fusion/container/deque/detail/deque_keyed_values.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-180 (5)  
> - _M_ include/boost/fusion/container/deque/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-181 (2)  
> - _M_ include/boost/fusion/container/deque/detail/is_sequence_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-182 (1)  
> - _M_ include/boost/fusion/container/deque/detail/keyed_element.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-183 (18)  
> - _M_ include/boost/fusion/container/deque/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-184 (1)  
> - _M_ include/boost/fusion/container/deque/front_extended_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-185 (4)  
> - _M_ include/boost/fusion/container/generation.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-186 (1)  
> - _M_ include/boost/fusion/container/generation/cons_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-187 (3)  
> - _M_ include/boost/fusion/container/generation/deque_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-188 (2)  
> - _M_ include/boost/fusion/container/generation/detail/pp_deque_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-189 (1)  
> - _M_ include/boost/fusion/container/generation/detail/pp_make_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-190 (3)  
> - _M_ include/boost/fusion/container/generation/detail/pp_make_map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-191 (3)  
> - _M_ include/boost/fusion/container/generation/detail/pp_map_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-192 (4)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/deque_tie10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-193 (10)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/deque_tie20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-194 (20)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/deque_tie30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-195 (30)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/deque_tie40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-196 (40)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/deque_tie50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-197 (50)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/list_tie10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-198 (10)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/list_tie20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-199 (20)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/list_tie30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-200 (30)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/list_tie40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-201 (40)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/list_tie50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-202 (50)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_deque10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-203 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_deque20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-204 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_deque30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-205 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_deque40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-206 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_deque50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-207 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_list10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-208 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_list20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-209 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_list30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-210 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_list40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-211 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_list50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-212 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_map10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-213 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_map20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-214 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_map30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-215 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_map40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-216 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_map50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-217 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_set10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-218 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_set20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-219 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_set30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-220 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_set40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-221 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_set50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-222 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_vector10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-223 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_vector20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-224 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_vector30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-225 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_vector40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-226 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/make_vector50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-227 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/map_tie10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-228 (12)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/map_tie20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-229 (22)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/map_tie30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-230 (32)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/map_tie40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-231 (42)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/map_tie50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-232 (52)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/vector_tie10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-233 (10)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/vector_tie20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-234 (20)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/vector_tie30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-235 (30)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/vector_tie40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-236 (40)  
> - _M_ include/boost/fusion/container/generation/detail/preprocessed/vector_tie50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-237 (50)  
> - _M_ include/boost/fusion/container/generation/ignore.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-238 (1)  
> - _M_ include/boost/fusion/container/generation/list_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-239 (1)  
> - _M_ include/boost/fusion/container/generation/make_cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-240 (3)  
> - _M_ include/boost/fusion/container/generation/make_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-241 (2)  
> - _M_ include/boost/fusion/container/generation/make_list.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-242 (3)  
> - _M_ include/boost/fusion/container/generation/make_map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-243 (2)  
> - _M_ include/boost/fusion/container/generation/make_set.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-244 (3)  
> - _M_ include/boost/fusion/container/generation/make_vector.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-245 (3)  
> - _M_ include/boost/fusion/container/generation/map_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-246 (2)  
> - _M_ include/boost/fusion/container/generation/pair_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-247 (3)  
> - _M_ include/boost/fusion/container/generation/vector_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-248 (1)  
> - _M_ include/boost/fusion/container/list.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-249 (1)  
> - _M_ include/boost/fusion/container/list/cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-250 (15)  
> - _M_ include/boost/fusion/container/list/cons_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-251 (12)  
> - _M_ include/boost/fusion/container/list/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-252 (4)  
> - _M_ include/boost/fusion/container/list/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-253 (4)  
> - _M_ include/boost/fusion/container/list/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-254 (2)  
> - _M_ include/boost/fusion/container/list/detail/build_cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-255 (3)  
> - _M_ include/boost/fusion/container/list/detail/convert_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-256 (2)  
> - _M_ include/boost/fusion/container/list/detail/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-257 (2)  
> - _M_ include/boost/fusion/container/list/detail/empty_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-258 (1)  
> - _M_ include/boost/fusion/container/list/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-259 (2)  
> - _M_ include/boost/fusion/container/list/detail/equal_to_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-260 (1)  
> - _M_ include/boost/fusion/container/list/detail/list_forward_ctor.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-261 (1)  
> - _M_ include/boost/fusion/container/list/detail/list_to_cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-262 (1)  
> - _M_ include/boost/fusion/container/list/detail/list_to_cons_call.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-263 (1)  
> - _M_ include/boost/fusion/container/list/detail/next_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-264 (2)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-265 (15)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-266 (25)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-267 (35)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-268 (45)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-269 (55)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list_to_cons10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-270 (10)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list_to_cons20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-271 (20)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list_to_cons30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-272 (30)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list_to_cons40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-273 (40)  
> - _M_ include/boost/fusion/container/list/detail/preprocessed/list_to_cons50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-274 (50)  
> - _M_ include/boost/fusion/container/list/detail/reverse_cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-275 (3)  
> - _M_ include/boost/fusion/container/list/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-276 (1)  
> - _M_ include/boost/fusion/container/list/list.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-277 (6)  
> - _M_ include/boost/fusion/container/list/list_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-278 (1)  
> - _M_ include/boost/fusion/container/map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-279 (1)  
> - _M_ include/boost/fusion/container/map/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-280 (6)  
> - _M_ include/boost/fusion/container/map/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-281 (3)  
> - _M_ include/boost/fusion/container/map/detail/at_key_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-282 (3)  
> - _M_ include/boost/fusion/container/map/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-283 (2)  
> - _M_ include/boost/fusion/container/map/detail/build_map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-284 (4)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/as_map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-285 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-286 (3)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-287 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-288 (3)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/convert_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-289 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/deref_data_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-290 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-291 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-292 (2)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/key_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-293 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/limits.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-294 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-295 (7)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/map_forward_ctor.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-296 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/map_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-297 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/as_map10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-298 (10)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/as_map20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-299 (20)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/as_map30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-300 (30)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/as_map40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-301 (40)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/as_map50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-302 (50)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/map10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-303 (16)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/map20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-304 (26)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/map30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-305 (36)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/map40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-306 (46)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/preprocessed/map50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-307 (56)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-308 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/value_of_data_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-309 (1)  
> - _M_ include/boost/fusion/container/map/detail/cpp03/value_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-310 (1)  
> - _M_ include/boost/fusion/container/map/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-311 (2)  
> - _M_ include/boost/fusion/container/map/detail/map_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-312 (30)  
> - _M_ include/boost/fusion/container/map/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-313 (1)  
> - _M_ include/boost/fusion/container/map/detail/value_at_key_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-314 (1)  
> - _M_ include/boost/fusion/container/map/map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-315 (14)  
> - _M_ include/boost/fusion/container/map/map_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-316 (1)  
> - _M_ include/boost/fusion/container/map/map_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-317 (6)  
> - _M_ include/boost/fusion/container/set.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-318 (1)  
> - _M_ include/boost/fusion/container/set/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-319 (3)  
> - _M_ include/boost/fusion/container/set/detail/as_set.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-320 (2)  
> - _M_ include/boost/fusion/container/set/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-321 (2)  
> - _M_ include/boost/fusion/container/set/detail/convert_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-322 (2)  
> - _M_ include/boost/fusion/container/set/detail/deref_data_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-323 (1)  
> - _M_ include/boost/fusion/container/set/detail/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-324 (2)  
> - _M_ include/boost/fusion/container/set/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-325 (2)  
> - _M_ include/boost/fusion/container/set/detail/key_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-326 (1)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/as_set10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-327 (10)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/as_set20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-328 (20)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/as_set30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-329 (30)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/as_set40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-330 (40)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/as_set50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-331 (50)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/set10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-332 (15)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/set20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-333 (25)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/set30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-334 (35)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/set40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-335 (45)  
> - _M_ include/boost/fusion/container/set/detail/preprocessed/set50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-336 (55)  
> - _M_ include/boost/fusion/container/set/detail/set_forward_ctor.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-337 (1)  
> - _M_ include/boost/fusion/container/set/detail/value_of_data_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-338 (1)  
> - _M_ include/boost/fusion/container/set/detail/value_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-339 (1)  
> - _M_ include/boost/fusion/container/set/limits.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-340 (1)  
> - _M_ include/boost/fusion/container/set/set.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-341 (6)  
> - _M_ include/boost/fusion/container/set/set_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-342 (1)  
> - _M_ include/boost/fusion/container/vector.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-343 (1)  
> - _M_ include/boost/fusion/container/vector/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-344 (3)  
> - _M_ include/boost/fusion/container/vector/detail/advance_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-345 (1)  
> - _M_ include/boost/fusion/container/vector/detail/as_vector.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-346 (2)  
> - _M_ include/boost/fusion/container/vector/detail/at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-347 (3)  
> - _M_ include/boost/fusion/container/vector/detail/begin_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-348 (2)  
> - _M_ include/boost/fusion/container/vector/detail/convert_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-349 (2)  
> - _M_ include/boost/fusion/container/vector/detail/deref_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-350 (2)  
> - _M_ include/boost/fusion/container/vector/detail/distance_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-351 (2)  
> - _M_ include/boost/fusion/container/vector/detail/end_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-352 (2)  
> - _M_ include/boost/fusion/container/vector/detail/equal_to_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-353 (1)  
> - _M_ include/boost/fusion/container/vector/detail/next_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-354 (2)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/as_vector10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-355 (10)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/as_vector20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-356 (20)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/as_vector30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-357 (30)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/as_vector40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-358 (40)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/as_vector50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-359 (50)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vector10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-360 (230)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vector20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-361 (230)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vector30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-362 (230)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vector40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-363 (230)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vector50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-364 (230)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vvector10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-365 (34)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vvector20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-366 (54)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vvector30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-367 (74)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vvector40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-368 (94)  
> - _M_ include/boost/fusion/container/vector/detail/preprocessed/vvector50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-369 (114)  
> - _M_ include/boost/fusion/container/vector/detail/prior_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-370 (2)  
> - _M_ include/boost/fusion/container/vector/detail/value_at_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-371 (1)  
> - _M_ include/boost/fusion/container/vector/detail/value_of_impl.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-372 (1)  
> - _M_ include/boost/fusion/container/vector/detail/vector_forward_ctor.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-373 (2)  
> - _M_ include/boost/fusion/container/vector/detail/vector_n.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-374 (25)  
> - _M_ include/boost/fusion/container/vector/limits.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-375 (1)  
> - _M_ include/boost/fusion/container/vector/vector.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-376 (17)  
> - _M_ include/boost/fusion/container/vector/vector10.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-377 (3)  
> - _M_ include/boost/fusion/container/vector/vector10_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-378 (1)  
> - _M_ include/boost/fusion/container/vector/vector20.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-379 (1)  
> - _M_ include/boost/fusion/container/vector/vector20_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-380 (1)  
> - _M_ include/boost/fusion/container/vector/vector30.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-381 (1)  
> - _M_ include/boost/fusion/container/vector/vector30_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-382 (1)  
> - _M_ include/boost/fusion/container/vector/vector40.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-383 (1)  
> - _M_ include/boost/fusion/container/vector/vector40_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-384 (1)  
> - _M_ include/boost/fusion/container/vector/vector50.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-385 (1)  
> - _M_ include/boost/fusion/container/vector/vector50_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-386 (1)  
> - _M_ include/boost/fusion/container/vector/vector_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-387 (1)  
> - _M_ include/boost/fusion/container/vector/vector_iterator.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-388 (2)  
> - _M_ include/boost/fusion/functional.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-389 (1)  
> - _M_ include/boost/fusion/functional/adapter.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-390 (1)  
> - _M_ include/boost/fusion/functional/adapter/fused.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-391 (6)  
> - _M_ include/boost/fusion/functional/adapter/fused_function_object.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-392 (6)  
> - _M_ include/boost/fusion/functional/adapter/fused_procedure.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-393 (6)  
> - _M_ include/boost/fusion/functional/adapter/unfused.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-394 (9)  
> - _M_ include/boost/fusion/functional/adapter/unfused_typed.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-395 (5)  
> - _M_ include/boost/fusion/functional/generation.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-396 (1)  
> - _M_ include/boost/fusion/functional/generation/detail/gen_make_adapter.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-397 (1)  
> - _M_ include/boost/fusion/functional/generation/make_fused.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-398 (1)  
> - _M_ include/boost/fusion/functional/generation/make_fused_function_object.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-399 (1)  
> - _M_ include/boost/fusion/functional/generation/make_fused_procedure.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-400 (1)  
> - _M_ include/boost/fusion/functional/generation/make_unfused.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-401 (1)  
> - _M_ include/boost/fusion/functional/invocation.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-402 (1)  
> - _M_ include/boost/fusion/functional/invocation/detail/that_ptr.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-403 (9)  
> - _M_ include/boost/fusion/functional/invocation/invoke.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-404 (13)  
> - _M_ include/boost/fusion/functional/invocation/invoke_function_object.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-405 (8)  
> - _M_ include/boost/fusion/functional/invocation/invoke_procedure.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-406 (10)  
> - _M_ include/boost/fusion/include/accumulate.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-407 (1)  
> - _M_ include/boost/fusion/include/adapt_adt.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-408 (1)  
> - _M_ include/boost/fusion/include/adapt_adt_named.cpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-409 (1)  
> - _M_ include/boost/fusion/include/adapt_adt_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-410 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_adt.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-411 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_adt_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-412 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_class.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-413 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_class_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-414 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-415 (1)  
> - _M_ include/boost/fusion/include/adapt_assoc_struct_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-416 (1)  
> - _M_ include/boost/fusion/include/adapt_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-417 (1)  
> - _M_ include/boost/fusion/include/adapt_struct_named.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-418 (1)  
> - _M_ include/boost/fusion/include/adapted.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-419 (1)  
> - _M_ include/boost/fusion/include/adapter.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-420 (1)  
> - _M_ include/boost/fusion/include/advance.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-421 (1)  
> - _M_ include/boost/fusion/include/algorithm.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-422 (1)  
> - _M_ include/boost/fusion/include/all.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-423 (1)  
> - _M_ include/boost/fusion/include/any.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-424 (1)  
> - _M_ include/boost/fusion/include/array.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-425 (1)  
> - _M_ include/boost/fusion/include/as_deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-426 (1)  
> - _M_ include/boost/fusion/include/as_list.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-427 (1)  
> - _M_ include/boost/fusion/include/as_map.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-428 (1)  
> - _M_ include/boost/fusion/include/as_set.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-429 (1)  
> - _M_ include/boost/fusion/include/as_vector.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-430 (1)  
> - _M_ include/boost/fusion/include/at.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-431 (1)  
> - _M_ include/boost/fusion/include/at_c.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-432 (1)  
> - _M_ include/boost/fusion/include/at_key.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-433 (1)  
> - _M_ include/boost/fusion/include/auxiliary.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-434 (1)  
> - _M_ include/boost/fusion/include/back.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-435 (1)  
> - _M_ include/boost/fusion/include/begin.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-436 (1)  
> - _M_ include/boost/fusion/include/boost_array.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-437 (1)  
> - _M_ include/boost/fusion/include/boost_tuple.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-438 (1)  
> - _M_ include/boost/fusion/include/category_of.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-439 (1)  
> - _M_ include/boost/fusion/include/clear.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-440 (1)  
> - _M_ include/boost/fusion/include/comparison.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-441 (1)  
> - _M_ include/boost/fusion/include/cons.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-442 (1)  
> - _M_ include/boost/fusion/include/cons_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-443 (1)  
> - _M_ include/boost/fusion/include/container.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-444 (1)  
> - _M_ include/boost/fusion/include/convert.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-445 (1)  
> - _M_ include/boost/fusion/include/copy.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-446 (1)  
> - _M_ include/boost/fusion/include/count.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-447 (1)  
> - _M_ include/boost/fusion/include/count_if.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-448 (1)  
> - _M_ include/boost/fusion/include/deduce.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-449 (1)  
> - _M_ include/boost/fusion/include/deduce_sequence.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-450 (1)  
> - _M_ include/boost/fusion/include/define_assoc_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-451 (1)  
> - _M_ include/boost/fusion/include/define_struct.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-452 (1)  
> - _M_ include/boost/fusion/include/define_struct_inline.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-453 (1)  
> - _M_ include/boost/fusion/include/deque.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-454 (1)  
> - _M_ include/boost/fusion/include/deque_fwd.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-455 (1)  
> - _M_ include/boost/fusion/include/deque_tie.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-456 (1)  
> - _M_ include/boost/fusion/include/deref.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-457 (1)  
> - _M_ include/boost/fusion/include/deref_data.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-458 (1)  
> - _M_ include/boost/fusion/include/distance.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-459 (1)  
> - _M_ include/boost/fusion/include/empty.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-460 (1)  
> - _M_ include/boost/fusion/include/end.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-461 (1)  
> - _M_ include/boost/fusion/include/equal_to.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-462 (1)  
> - _M_ include/boost/fusion/include/erase.hpp  
>   https://github.com/boostorg/fusion/pull/2/files#diff-463 (1)

---

## Comment 2

> Username: djowel  
> Created_at: 2014-01-28 23:05:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/2#issuecomment-33538111  

This is amazing, Eric! What can I say? Awesome!

---

## Comment 3

> Username: ericniebler  
> Created_at: 2014-01-29 00:33:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/2#issuecomment-33544655  

A million thanks, Joel.

---

## Comment 4

> Username: ericniebler  
> Created_at: 2014-03-12 22:24:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/2#issuecomment-37476751  

Can we get this merged to master?

---

## Comment 5

> Username: djowel  
> Created_at: 2014-03-16 23:48:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/2#issuecomment-37775857  

i'll do this today.

---
