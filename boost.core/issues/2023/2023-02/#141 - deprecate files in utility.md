# #141 - deprecate files in utility/ [Open]

> Username: vinniefalco  
> Created at: 2023-02-03 16:13:20 UTC  
> Updated at: 2023-02-03 23:29:29 UTC  
> Url: https://github.com/boostorg/core/issues/141  

There are four deprecated files in utility/ but there is no pragma warning or notice to users aside from a comment which no one sees. If we want to normalize this library (by not having a boost/utility directory) then we should add a warning message for a release or two and then remove the files entirely.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-02-03 17:48:12 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416201878  

It requires quite a bit more work than just adding a pragma. One needs to also scan through other Boost libraries (and their tests) and create PRs updating includes. I'm not saying we shouldn't do this, it's just it takes time to do it.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-02-03 17:53:01 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416207021  

```  
C:\boost-git\develop>dist\bin\boostdep boost/utility/addressof.hpp  
Inclusion report for <boost/utility/addressof.hpp> (in module core):  
  
    from asio:  
        <boost/asio/detail/memory.hpp>  
  
    from bimap:  
        <boost/bimap/relation/detail/mutant.hpp>  
  
    from compute:  
        <boost/compute/algorithm/detail/copy_to_device.hpp>  
        <boost/compute/algorithm/detail/copy_to_host.hpp>  
  
    from foreach:  
        <boost/foreach.hpp>  
  
    from fusion:  
        <boost/fusion/functional/invocation/detail/that_ptr.hpp>  
  
    from interprocess:  
        <boost/interprocess/allocators/adaptive_pool.hpp>  
        <boost/interprocess/allocators/allocator.hpp>  
        <boost/interprocess/allocators/detail/allocator_common.hpp>  
        <boost/interprocess/allocators/node_allocator.hpp>  
        <boost/interprocess/allocators/private_adaptive_pool.hpp>  
        <boost/interprocess/allocators/private_node_allocator.hpp>  
  
    from proto:  
        <boost/proto/detail/decltype.hpp>  
        <boost/proto/expr.hpp>  
        <boost/proto/extends.hpp>  
  
    from python:  
        <boost/python/object/value_holder.hpp>  
  
    from range:  
        <boost/range/detail/microsoft.hpp>  
        <boost/range/mfc.hpp>  
  
    from spirit:  
        <boost/spirit/home/x3/nonterminal/detail/rule.hpp>  
  
    from test:  
        <boost/test/utils/class_properties.hpp>  
  
    from type_erasure:  
        <boost/type_erasure/any.hpp>  
        <boost/type_erasure/detail/adapt_to_vtable.hpp>  
  
    from unordered:  
        <boost/unordered/detail/implementation.hpp>  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2023-02-03 17:54:28 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416208947  

```  
C:\boost-git\develop>dist\bin\boostdep boost/utility/enable_if.hpp  
Inclusion report for <boost/utility/enable_if.hpp> (in module core):  
  
    from accumulators:  
        <boost/accumulators/numeric/functional.hpp>  
        <boost/accumulators/numeric/functional/complex.hpp>  
        <boost/accumulators/numeric/functional/valarray.hpp>  
        <boost/accumulators/numeric/functional/vector.hpp>  
        <boost/accumulators/numeric/functional_fwd.hpp>  
        <boost/accumulators/statistics/rolling_variance.hpp>  
  
    from asio:  
        <boost/asio/detail/type_traits.hpp>  
  
    from assign:  
        <boost/assign/list_of.hpp>  
  
    from chrono:  
        <boost/chrono/duration.hpp>  
        <boost/chrono/io/duration_get.hpp>  
        <boost/chrono/io_v1/chrono_io.hpp>  
  
    from compute:  
        <boost/compute/algorithm/copy.hpp>  
        <boost/compute/algorithm/fill.hpp>  
        <boost/compute/algorithm/sort.hpp>  
        <boost/compute/algorithm/sort_by_key.hpp>  
        <boost/compute/detail/is_buffer_iterator.hpp>  
        <boost/compute/detail/is_contiguous_iterator.hpp>  
        <boost/compute/iterator/buffer_iterator.hpp>  
        <boost/compute/kernel.hpp>  
  
    from contract:  
        <boost/contract/call_if.hpp>  
        <boost/contract/detail/condition/cond_inv.hpp>  
        <boost/contract/detail/condition/cond_subcontracting.hpp>  
        <boost/contract/old.hpp>  
  
    from coroutine:  
        <boost/coroutine/detail/symmetric_coroutine_yield.hpp>  
  
    from detail:  
        <boost/detail/identifier.hpp>  
  
    from foreach:  
        <boost/foreach.hpp>  
  
    from fusion:  
        <boost/fusion/adapted/struct/detail/define_struct.hpp>  
        <boost/fusion/algorithm/auxiliary/copy.hpp>  
        <boost/fusion/algorithm/auxiliary/move.hpp>  
        <boost/fusion/algorithm/query/count.hpp>  
        <boost/fusion/algorithm/query/count_if.hpp>  
        <boost/fusion/algorithm/query/find.hpp>  
        <boost/fusion/algorithm/query/find_fwd.hpp>  
        <boost/fusion/algorithm/query/find_if.hpp>  
        <boost/fusion/algorithm/query/find_if_fwd.hpp>  
        <boost/fusion/algorithm/transformation/detail/replace_if.hpp>  
        <boost/fusion/algorithm/transformation/erase.hpp>  
        <boost/fusion/algorithm/transformation/flatten.hpp>  
        <boost/fusion/algorithm/transformation/insert.hpp>  
        <boost/fusion/algorithm/transformation/push_back.hpp>  
        <boost/fusion/algorithm/transformation/push_front.hpp>  
        <boost/fusion/algorithm/transformation/replace.hpp>  
        <boost/fusion/algorithm/transformation/replace_if.hpp>  
        <boost/fusion/algorithm/transformation/reverse.hpp>  
        <boost/fusion/container/deque/deque.hpp>  
        <boost/fusion/container/deque/detail/cpp03/deque.hpp>  
        <boost/fusion/container/generation/pair_tie.hpp>  
        <boost/fusion/container/list/cons.hpp>  
        <boost/fusion/container/map/map.hpp>  
        <boost/fusion/container/vector/detail/cpp03/vector10.hpp>  
        <boost/fusion/container/vector/detail/cpp03/vector20.hpp>  
        <boost/fusion/container/vector/detail/cpp03/vector30.hpp>  
        <boost/fusion/container/vector/detail/cpp03/vector40.hpp>  
        <boost/fusion/container/vector/detail/cpp03/vector50.hpp>  
        <boost/fusion/iterator/equal_to.hpp>  
        <boost/fusion/sequence/hash.hpp>  
        <boost/fusion/sequence/intrinsic/begin.hpp>  
        <boost/fusion/sequence/intrinsic/end.hpp>  
        <boost/fusion/sequence/intrinsic/segments.hpp>  
        <boost/fusion/sequence/intrinsic/size.hpp>  
        <boost/fusion/sequence/intrinsic_fwd.hpp>  
        <boost/fusion/sequence/io/out.hpp>  
        <boost/fusion/support/pair.hpp>  
        <boost/fusion/support/segmented_fold_until.hpp>  
        <boost/fusion/support/tag_of.hpp>  
        <boost/fusion/tuple/detail/tuple.hpp>  
        <boost/fusion/view/iterator_range/detail/segmented_iterator_range.hpp>  
  
    from gil:  
        <boost/gil/extension/toolbox/metafunctions/channel_type.hpp>  
  
    from graph:  
        <boost/graph/compressed_sparse_row_graph.hpp>  
        <boost/graph/lookup_edge.hpp>  
        <boost/graph/named_graph.hpp>  
        <boost/graph/overloading.hpp>  
        <boost/graph/vf2_sub_graph_iso.hpp>  
        <boost/pending/property.hpp>  
  
    from graph_parallel:  
        <boost/graph/distributed/mpi_process_group.hpp>  
  
    from icl:  
        <boost/icl/concept/comparable.hpp>  
        <boost/icl/concept/container.hpp>  
        <boost/icl/concept/interval.hpp>  
        <boost/icl/concept/interval_set_value.hpp>  
        <boost/icl/detail/interval_map_algo.hpp>  
        <boost/icl/interval_bounds.hpp>  
  
    from iostreams:  
        <boost/iostreams/detail/enable_if_stream.hpp>  
  
    from lambda:  
        <boost/lambda/detail/lambda_functors.hpp>  
  
    from lockfree:  
        <boost/lockfree/spsc_queue.hpp>  
  
    from mpi:  
        <boost/mpi/detail/mpi_datatype_cache.hpp>  
  
    from msm:  
        <boost/msm/back/metafunctions.hpp>  
        <boost/msm/back/state_machine.hpp>  
        <boost/msm/front/euml/common.hpp>  
        <boost/msm/front/euml/container.hpp>  
        <boost/msm/front/euml/operator.hpp>  
  
    from multi_array:  
        <boost/multi_array/multi_array_ref.hpp>  
        <boost/multi_array/view.hpp>  
  
    from numeric~odeint:  
        <boost/numeric/odeint/algebra/fusion_algebra_dispatcher.hpp>  
        <boost/numeric/odeint/external/eigen/eigen_resize.hpp>  
        <boost/numeric/odeint/integrate/integrate.hpp>  
        <boost/numeric/odeint/iterator/impl/adaptive_iterator_impl.hpp>  
        <boost/numeric/odeint/iterator/impl/times_iterator_impl.hpp>  
        <boost/numeric/odeint/iterator/integrate/integrate.hpp>  
        <boost/numeric/odeint/stepper/base/explicit_error_stepper_base.hpp>  
        <boost/numeric/odeint/stepper/base/explicit_error_stepper_fsal_base.hpp>  
        <boost/numeric/odeint/stepper/base/explicit_stepper_base.hpp>  
        <boost/numeric/odeint/stepper/controlled_runge_kutta.hpp>  
        <boost/numeric/odeint/util/copy.hpp>  
        <boost/numeric/odeint/util/resize.hpp>  
        <boost/numeric/odeint/util/same_size.hpp>  
  
    from numeric~ublas:  
        <boost/numeric/ublas/detail/returntype_deduction.hpp>  
        <boost/numeric/ublas/matrix_vector.hpp>  
        <boost/numeric/ublas/operation/size.hpp>  
        <boost/numeric/ublas/traits.hpp>  
  
    from phoenix:  
        <boost/phoenix/bind/bind_member_variable.hpp>  
        <boost/phoenix/bind/detail/cpp03/bind_member_function.hpp>  
        <boost/phoenix/core/environment.hpp>  
  
    from property_tree:  
        <boost/property_tree/ptree.hpp>  
        <boost/property_tree/stream_translator.hpp>  
  
    from proto:  
        <boost/proto/detail/decltype.hpp>  
        <boost/proto/fusion.hpp>  
        <boost/proto/generate.hpp>  
        <boost/proto/make_expr.hpp>  
        <boost/proto/matches.hpp>  
        <boost/proto/operators.hpp>  
        <boost/proto/proto_fwd.hpp>  
        <boost/proto/transform/default.hpp>  
        <boost/proto/transform/env.hpp>  
  
    from ptr_container:  
        <boost/ptr_container/detail/is_convertible.hpp>  
        <boost/ptr_container/detail/reversible_ptr_container.hpp>  
  
    from python:  
        <boost/python/detail/enable_if.hpp>  
        <boost/python/numpy/ndarray.hpp>  
  
    from random:  
        <boost/random/detail/seed.hpp>  
  
    from range:  
        <boost/range/algorithm/for_each.hpp>  
        <boost/range/detail/any_iterator.hpp>  
        <boost/range/detail/microsoft.hpp>  
        <boost/range/has_range_iterator.hpp>  
        <boost/range/iterator_range_core.hpp>  
        <boost/range/size_type.hpp>  
  
    from rational:  
        <boost/rational.hpp>  
  
    from regex:  
        <boost/regex/v4/regex_traits_defaults.hpp>  
  
    from serialization:  
        <boost/serialization/collections_load_imp.hpp>  
  
    from sort:  
        <boost/sort/spreadsort/detail/float_sort.hpp>  
        <boost/sort/spreadsort/detail/integer_sort.hpp>  
        <boost/sort/spreadsort/detail/string_sort.hpp>  
  
    from spirit:  
        <boost/spirit/home/karma/auto/meta_create.hpp>  
        <boost/spirit/home/karma/char/char.hpp>  
        <boost/spirit/home/karma/detail/alternative_function.hpp>  
        <boost/spirit/home/karma/detail/generate_auto.hpp>  
        <boost/spirit/home/karma/directive/center_alignment.hpp>  
        <boost/spirit/home/karma/directive/left_alignment.hpp>  
        <boost/spirit/home/karma/directive/right_alignment.hpp>  
        <boost/spirit/home/karma/meta_compiler.hpp>  
        <boost/spirit/home/karma/numeric/bool.hpp>  
        <boost/spirit/home/karma/numeric/int.hpp>  
        <boost/spirit/home/karma/numeric/real.hpp>  
        <boost/spirit/home/karma/numeric/uint.hpp>  
        <boost/spirit/home/karma/stream/detail/format_manip_auto.hpp>  
        <boost/spirit/home/karma/stream/stream.hpp>  
        <boost/spirit/home/karma/string/lit.hpp>  
        <boost/spirit/home/lex/meta_compiler.hpp>  
        <boost/spirit/home/qi/auto/meta_create.hpp>  
        <boost/spirit/home/qi/char/char.hpp>  
        <boost/spirit/home/qi/detail/parse_auto.hpp>  
        <boost/spirit/home/qi/directive/lexeme.hpp>  
        <boost/spirit/home/qi/directive/no_skip.hpp>  
        <boost/spirit/home/qi/directive/skip.hpp>  
        <boost/spirit/home/qi/meta_compiler.hpp>  
        <boost/spirit/home/qi/numeric/detail/numeric_utils.hpp>  
        <boost/spirit/home/qi/stream/detail/match_manip_auto.hpp>  
        <boost/spirit/home/qi/string/lit.hpp>  
        <boost/spirit/home/support/adapt_adt_attributes.hpp>  
        <boost/spirit/home/support/attributes.hpp>  
        <boost/spirit/home/support/attributes_fwd.hpp>  
        <boost/spirit/home/support/detail/make_cons.hpp>  
        <boost/spirit/home/support/sequence_base_id.hpp>  
        <boost/spirit/home/support/utree/detail/utree_detail2.hpp>  
        <boost/spirit/home/support/utree/utree.hpp>  
        <boost/spirit/home/support/utree/utree_traits.hpp>  
        <boost/spirit/home/x3/core/parser.hpp>  
        <boost/spirit/home/x3/directive/lexeme.hpp>  
        <boost/spirit/home/x3/directive/no_skip.hpp>  
        <boost/spirit/home/x3/directive/skip.hpp>  
        <boost/spirit/home/x3/support/numeric_utils/detail/extract_int.hpp>  
        <boost/spirit/home/x3/support/traits/attribute_of.hpp>  
        <boost/spirit/home/x3/support/traits/has_attribute.hpp>  
        <boost/spirit/home/x3/support/traits/is_substitute.hpp>  
  
    from test:  
        <boost/test/data/monomorphic/generators/xrange.hpp>  
        <boost/test/tools/collection_comparison_op.hpp>  
        <boost/test/tools/cstring_comparison_op.hpp>  
        <boost/test/tools/floating_point_comparison.hpp>  
        <boost/test/tools/fpc_op.hpp>  
        <boost/test/utils/named_params.hpp>  
  
    from type_erasure:  
        <boost/type_erasure/any.hpp>  
        <boost/type_erasure/binding.hpp>  
        <boost/type_erasure/builtin.hpp>  
        <boost/type_erasure/detail/access.hpp>  
        <boost/type_erasure/operators.hpp>  
        <boost/type_erasure/param.hpp>  
  
    from units:  
        <boost/units/quantity.hpp>  
  
    from unordered:  
        <boost/unordered/detail/implementation.hpp>  
  
    from utility:  
        <boost/utility/base_from_member.hpp>  
  
    from xpressive:  
        <boost/xpressive/detail/core/peeker.hpp>  
        <boost/xpressive/detail/utility/traits_utils.hpp>  
        <boost/xpressive/match_results.hpp>  
        <boost/xpressive/regex_actions.hpp>  
        <boost/xpressive/regex_algorithms.hpp>  
        <boost/xpressive/regex_compiler.hpp>  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2023-02-03 17:55:24 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416209871  

This one is easy:  
```  
C:\boost-git\develop>dist\bin\boostdep boost/utility/explicit_operator_bool.hpp  
Inclusion report for <boost/utility/explicit_operator_bool.hpp> (in module core):  
  
    from coroutine:  
        <boost/coroutine/asymmetric_coroutine.hpp>  
        <boost/coroutine/detail/symmetric_coroutine_call.hpp>  
        <boost/coroutine/detail/symmetric_coroutine_yield.hpp>  
  
    from sync:  
        <boost/sync/thread_specific/thread_specific_ptr.hpp>  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2023-02-03 17:56:06 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416210482  

```  
Inclusion report for <boost/utility/swap.hpp> (in module core):  
  
    from property_tree:  
        <boost/property_tree/detail/ptree_implementation.hpp>  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-02-03 18:52:23 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416267924  

> It requires quite a bit more work than just adding a pragma  
  
Yeah well, I bet that after the pragma ships for a few releases, those lists that Peter quoted will be shorter :)

---

## Comment 7

> Username: pdimov  
> Created at: 2023-02-03 19:13:20 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416289546  

You will lose that bet.

---

## Comment 8

> Username: Lastique  
> Created at: 2023-02-03 20:13:00 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416353573  

> > It requires quite a bit more work than just adding a pragma  
>   
> Yeah well, I bet that after the pragma ships for a few releases, those lists that Peter quoted will be shorter :)  
  
This means user complaints. We'd like to avoid that, if possible.

---

## Comment 9

> Username: pdimov  
> Created at: 2023-02-03 23:24:34 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416523137  

Yes. It doesn't look good for us when a new Boost release starts giving out deprecation warnings coming from inside Boost and not user code. So before we do that we need to fix everything on our side first.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2023-02-03 23:29:29 UTC  
> Url: https://github.com/boostorg/core/issues/141#issuecomment-1416527739  

oh yeah... that's fair :)
