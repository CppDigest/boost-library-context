# #47 Fix unused variable warning from BOOST_MPL_ASSERT_* on Clang [Merged]

> Username: Kojoley  
> Created at: 2020-04-03 15:40:18 UTC  
> Updated at: 2022-05-01 21:23:45 UTC  
> Merged at: 2022-05-01 21:23:45 UTC  
> Closed at: 2022-05-01 21:23:45 UTC  
> Url: https://github.com/boostorg/mpl/pull/47  

Fixes #41

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-04-09 14:35:22 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-611562262  

@pdimov @glenfe could you please help with review/merge?

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-04-09 22:53:28 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-611791919  

Maybe after 1.73 is released? Or do you want this in?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-04-10 00:44:12 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-611819776  

I do not have preferences, but I think that it is pretty safe to have this even in 1.73 as Clang goes through this branch on Linux.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-04-29 15:33:36 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-621289440  

Rebased to fix merge conflict

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-04-30 14:27:33 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-621888374  

I do not know what happen to CI here, I'll try to close/open the PR

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-05-08 19:27:08 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-625978696  

Ping

---

## Comment 7

> Username: glenfe  
> Created_at: 2020-06-02 23:04:06 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-637852284  

In which Boost libraries is this causing a problem with unused variable warnings?

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-06-02 23:16:41 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-637856178  

```  
>grep -R BOOST_MPL_ASSERT boost  
boost/accumulators/framework/accumulator_set.hpp:    //BOOST_MPL_ASSERT((mpl::is_sequence<accumulators_type>));  
boost/accumulators/framework/accumulator_set.hpp:        BOOST_MPL_ASSERT((detail::contains_feature_of<Features, the_accumulator>));  
boost/accumulators/framework/depends_on.hpp:            BOOST_MPL_ASSERT((  
boost/accumulators/framework/depends_on.hpp:            BOOST_MPL_ASSERT((is_same<Tag, void>));  
boost/accumulators/framework/depends_on.hpp:            BOOST_MPL_ASSERT((is_same<AccumulatorSet, void>));  
boost/accumulators/framework/extractor.hpp:        BOOST_MPL_ASSERT((detail::is_accumulator_set<AccumulatorSet>));  
boost/accumulators/framework/extractor.hpp:        BOOST_MPL_ASSERT((detail::is_accumulator_set<AccumulatorSet>));                 \  
boost/accumulators/numeric/functional/valarray.hpp:                BOOST_MPL_ASSERT((is_same<bool, typename From::value_type>));  
boost/accumulators/statistics/error_of.hpp:        BOOST_MPL_ASSERT((this_feature_has_no_error_calculation<Feature>));  
boost/accumulators/statistics/moment.hpp:        BOOST_MPL_ASSERT_RELATION(N::value, >, 0);  
boost/accumulators/statistics/rolling_moment.hpp:        BOOST_MPL_ASSERT_RELATION(N::value, >, 0);  
boost/accumulators/statistics/tail.hpp:        BOOST_MPL_ASSERT((  
boost/accumulators/statistics/weighted_moment.hpp:        BOOST_MPL_ASSERT_RELATION(N::value, >, 0);  
boost/archive/basic_xml_iarchive.hpp:        BOOST_MPL_ASSERT((serialization::is_wrapper< T >));  
boost/archive/basic_xml_oarchive.hpp:        BOOST_MPL_ASSERT((serialization::is_wrapper< T >));  
boost/bimap/detail/debug/static_error.hpp:// Easier way to call BOOST_MPL_ASSERT_MSG in class scope to generate  
boost/bimap/detail/debug/static_error.hpp:        BOOST_MPL_ASSERT_MSG(false,                                           \  
boost/bimap/detail/test/check_metadata.hpp:// Easier way to call BOOST_MPL_ASSERT_MSG in class scope  
boost/bimap/detail/test/check_metadata.hpp:    BOOST_MPL_ASSERT_MSG(p1,p2,p3);                                           \  
boost/bimap/relation/detail/mutant.hpp:    BOOST_MPL_ASSERT((  
boost/bimap/relation/detail/mutant.hpp:    BOOST_MPL_ASSERT((  
boost/chrono/detail/static_assert.hpp:    BOOST_MPL_ASSERT_MSG(boost::mpl::bool_< (CND) >::type::value, MSG, TYPES)  
boost/flyweight/flyweight.hpp:  BOOST_MPL_ASSERT_MSG(  
boost/flyweight/key_value.hpp:      BOOST_MPL_ASSERT_MSG(  
boost/foreach.hpp:    BOOST_MPL_ASSERT_MSG( (!is_char_array<T>::value), IS_THIS_AN_ARRAY_OR_A_NULL_TERMINATED_STRING, (T&) );  
boost/foreach.hpp:    BOOST_MPL_ASSERT_MSG( (!is_char_array<T>::value), IS_THIS_AN_ARRAY_OR_A_NULL_TERMINATED_STRING, (T&) );  
boost/fusion/adapted/boost_array/array_iterator.hpp:        BOOST_MPL_ASSERT_RELATION(Pos, >=, 0);  
boost/fusion/adapted/boost_array/array_iterator.hpp:        BOOST_MPL_ASSERT_RELATION(Pos, <=, static_cast<int>(Array::static_size));  
boost/fusion/adapted/std_array/std_array_iterator.hpp:        BOOST_MPL_ASSERT_RELATION(Pos, >=, 0);  
boost/fusion/adapted/std_array/std_array_iterator.hpp:        BOOST_MPL_ASSERT_RELATION(Pos, <=, std::tuple_size<Array>::value);  
boost/fusion/algorithm/transformation/pop_back.hpp:            BOOST_MPL_ASSERT_NOT((result_of::empty<Sequence>));  
boost/fusion/container/deque/detail/at_impl.hpp:                BOOST_MPL_ASSERT_RELATION(next_down::value, !=, next_up::value);  
boost/fusion/container/deque/detail/value_at_impl.hpp:                BOOST_MPL_ASSERT_RELATION(next_down::value, !=, next_up::value);  
boost/fusion/iterator/advance.hpp:                BOOST_MPL_ASSERT_NOT((traits::is_random_access<Iterator>));  
boost/fusion/iterator/iterator_facade.hpp:            BOOST_MPL_ASSERT_NOT((traits::is_random_access<Iterator>));  
boost/fusion/sequence/intrinsic/detail/segmented_end_impl.hpp:        BOOST_MPL_ASSERT((traits::is_segmented<Sequence>));  
boost/fusion/view/iterator_range/detail/is_segmented_impl.hpp:                BOOST_MPL_ASSERT_RELATION(  
boost/fusion/view/iterator_range/detail/segmented_iterator_range.hpp:        BOOST_MPL_ASSERT((  
boost/fusion/view/iterator_range/detail/segmented_iterator_range.hpp:        BOOST_MPL_ASSERT((  
boost/fusion/view/iterator_range/detail/segmented_iterator_range.hpp:        BOOST_MPL_ASSERT((  
boost/fusion/view/iterator_range/detail/segmented_iterator_range.hpp:        BOOST_MPL_ASSERT((  
boost/fusion/view/iterator_range/detail/segments_impl.hpp:                BOOST_MPL_ASSERT((traits::is_segmented<typename impl::type>));  
boost/fusion/view/single_view/detail/at_impl.hpp:                BOOST_MPL_ASSERT((mpl::equal_to<N, mpl::int_<0> >));  
boost/fusion/view/single_view/detail/deref_impl.hpp:                BOOST_MPL_ASSERT((mpl::equal_to<typename Iterator::position, mpl::int_<0> >));  
boost/fusion/view/single_view/detail/equal_to_impl.hpp:                BOOST_MPL_ASSERT((is_same<typename add_const<typename It1::single_view_type>::type,   
boost/fusion/view/single_view/detail/value_at_impl.hpp:                BOOST_MPL_ASSERT((mpl::equal_to<N, mpl::int_<0> >));  
boost/fusion/view/single_view/detail/value_of_impl.hpp:                BOOST_MPL_ASSERT((mpl::equal_to<typename Iterator::position, mpl::int_<0> >));  
boost/fusion/view/zip_view/detail/at_impl.hpp:                BOOST_MPL_ASSERT((is_reference<SeqRef>));  
boost/fusion/view/zip_view/detail/begin_impl.hpp:                BOOST_MPL_ASSERT((is_reference<SeqRef>));  
boost/fusion/view/zip_view/detail/distance_impl.hpp:            BOOST_MPL_ASSERT_NOT((is_same<typename finder::type, result_of::end<typename SearchIt::iterators> >));  
boost/fusion/view/zip_view/detail/end_impl.hpp:                BOOST_MPL_ASSERT((is_reference<SeqRef>));  
boost/fusion/view/zip_view/zip_view.hpp:        BOOST_MPL_ASSERT((detail::all_references<Sequences>));  
boost/geometry/algorithms/assign.hpp:        BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/assign.hpp:        BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/assign_values.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/calculate_point_order.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/direction_code.hpp:    BOOST_MPL_ASSERT_MSG((false), NOT_IMPLEMENTED_FOR_THIS_CS, (CSTag));  
boost/geometry/algorithms/detail/direction_code.hpp:        BOOST_MPL_ASSERT_MSG((boost::is_same<units_t, units2_t>::value),  
boost/geometry/algorithms/detail/for_each_range.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/overlay/convert_ring.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/overlay/copy_segment_point.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/overlay/follow.hpp:    // BOOST_MPL_ASSERT(false);  
boost/geometry/algorithms/detail/overlay/intersection_insert.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/relate/result.hpp:    BOOST_MPL_ASSERT_MSG((is_valid),  
boost/geometry/algorithms/detail/sections/range_by_section.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/sections/sectionalize.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/sections/sectionalize.hpp:    BOOST_MPL_ASSERT((boost::is_same<ctype1, ctype2>));  
boost/geometry/algorithms/detail/tupled_output.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/tupled_output.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/detail/tupled_output.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/line_interpolate.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/algorithms/not_implemented.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/arithmetic/cross_product.hpp:    BOOST_MPL_ASSERT_MSG((false),  
boost/geometry/core/access.hpp:   BOOST_MPL_ASSERT_MSG  
boost/geometry/core/closure.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/coordinate_dimension.hpp:   BOOST_MPL_ASSERT_MSG  
boost/geometry/core/coordinate_dimension.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/core/coordinate_system.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/coordinate_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/cs.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/cs.hpp:    BOOST_MPL_ASSERT_MSG((false),  
boost/geometry/core/exterior_ring.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/exterior_ring.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/geometry_id.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/interior_rings.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/interior_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/interior_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/interior_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/interior_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/point_order.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/point_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/ring_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/core/ring_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/extensions/algebra/algorithms/assign.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_GEOMETRY, (GeometryTag, Geometry));  
boost/geometry/extensions/algebra/algorithms/rotation.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_DIMENSION, (Rotation));  
boost/geometry/extensions/algebra/algorithms/rotation.hpp:    BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THOSE_SYSTEMS, (V1, V2));  
boost/geometry/extensions/algebra/algorithms/rotation.hpp:    BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THOSE_SYSTEMS, (V1, V2));  
boost/geometry/extensions/algebra/algorithms/rotation.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THOSE_TAGS, (Tag1, Tag2, Rotation));  
boost/geometry/extensions/algebra/algorithms/rotation.hpp:    BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THOSE_SYSTEMS, (V1, V2));  
boost/geometry/extensions/algebra/algorithms/transform_geometrically.hpp:    BOOST_MPL_ASSERT_MSG((0 < Dimension), INVALID_DIMENSION, (Box));  
boost/geometry/extensions/algebra/algorithms/transform_geometrically.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THOSE_TAGS, (GTag, TTag));  
boost/geometry/extensions/algebra/algorithms/transform_geometrically.hpp:        BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_CS, (cs));  
boost/geometry/extensions/algebra/algorithms/transform_geometrically.hpp:        BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_CS, (cs));  
boost/geometry/extensions/algebra/core/coordinate_dimension.hpp:     BOOST_MPL_ASSERT_MSG(false,  
boost/geometry/extensions/algebra/core/coordinate_dimension.hpp:    BOOST_MPL_ASSERT_MSG(false,  
boost/geometry/extensions/algebra/geometries/concepts/matrix_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/matrix_concept.hpp:        //BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/quaternion_concept.hpp:        //BOOST_MPL_ASSERT_MSG(dim_check, INVALID_DIMENSION, (RotationQuaternion));  
boost/geometry/extensions/algebra/geometries/concepts/quaternion_concept.hpp:        //BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/quaternion_concept.hpp:        //BOOST_MPL_ASSERT_MSG(dim_check, INVALID_DIMENSION, (ConstRotationQuaternion));  
boost/geometry/extensions/algebra/geometries/concepts/quaternion_concept.hpp:        //BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_matrix_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_matrix_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_quaternion_concept.hpp:        BOOST_MPL_ASSERT_MSG(dim_check, INVALID_DIMENSION, (RotationQuaternion));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_quaternion_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_quaternion_concept.hpp:        BOOST_MPL_ASSERT_MSG(dim_check, INVALID_DIMENSION, (ConstRotationQuaternion));  
boost/geometry/extensions/algebra/geometries/concepts/rotation_quaternion_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/vector_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/algebra/geometries/concepts/vector_concept.hpp:        BOOST_MPL_ASSERT_MSG(cs_check, NOT_IMPLEMENTED_FOR_THIS_CS, (csystem));  
boost/geometry/extensions/gis/io/shapelib/shp_create_object.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/extensions/gis/io/shapelib/shp_read_object.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/extensions/nsphere/algorithms/disjoint.hpp:        BOOST_MPL_ASSERT_MSG(check_cs,  
boost/geometry/extensions/nsphere/algorithms/disjoint.hpp:        BOOST_MPL_ASSERT_MSG(check_cs,  
boost/geometry/extensions/nsphere/algorithms/disjoint.hpp:        BOOST_MPL_ASSERT_MSG(check_cs,  
boost/geometry/formulas/vertex_latitude.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/formulas/vertex_longitude.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/geometries/point.hpp:    BOOST_MPL_ASSERT_MSG((DimensionCount >= 1),  
boost/geometry/index/adaptors/query.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/algorithms/content.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_INDEXABLE_AND_TAG, (Indexable, Tag));  
boost/geometry/index/detail/algorithms/is_valid.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/algorithms/margin.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_GEOMETRY, (Geometry, Tag));  
boost/geometry/index/detail/algorithms/minmaxdist.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/algorithms/path_intersection.hpp:    BOOST_MPL_ASSERT_MSG((false), NOT_IMPLEMENTED_FOR_THIS_GEOMETRY_OR_INDEXABLE, (path_intersection));  
boost/geometry/index/detail/algorithms/segment_intersection.hpp://    BOOST_MPL_ASSERT_MSG((!::boost::is_unsigned<type>::value),  
boost/geometry/index/detail/algorithms/segment_intersection.hpp:    BOOST_MPL_ASSERT_MSG((false), NOT_IMPLEMENTED_FOR_THIS_GEOMETRY, (segment_intersection));  
boost/geometry/index/detail/algorithms/segment_intersection.hpp:    BOOST_MPL_ASSERT_MSG((false), SEGMENT_POINT_INTERSECTION_UNAVAILABLE, (segment_intersection));  
boost/geometry/index/detail/algorithms/segment_intersection.hpp:        BOOST_MPL_ASSERT_MSG(check, RELATIVE_DISTANCE_MUST_BE_FLOATING_POINT_TYPE, (RelativeDistance));  
boost/geometry/index/detail/algorithms/smallest_for_indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/algorithms/sum_for_indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/bounded_view.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/distance_predicates.hpp:    BOOST_MPL_ASSERT_MSG((false), INVALID_PREDICATE_OR_TAG, (calculate_distance));  
boost/geometry/index/detail/predicates.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/predicates.hpp:    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_TAG, (Tag));  
boost/geometry/index/detail/predicates.hpp:    BOOST_MPL_ASSERT_MSG((I < 1), INVALID_INDEX, (predicates_element));  
boost/geometry/index/detail/predicates.hpp://    BOOST_MPL_ASSERT_MSG((I < 2), INVALID_INDEX, (predicates_element));  
boost/geometry/index/detail/predicates.hpp:    BOOST_MPL_ASSERT_MSG((check), INVALID_INDEXES, (predicates_check_impl));  
boost/geometry/index/detail/predicates.hpp://    BOOST_MPL_ASSERT_MSG((First < 2 && Last <= 2 && First <= Last), INVALID_INDEXES, (predicates_check_impl));  
boost/geometry/index/detail/predicates.hpp://    BOOST_MPL_ASSERT_MSG((First < pred_len && Last <= pred_len && First <= Last), INVALID_INDEXES, (predicates_check_impl));  
boost/geometry/index/detail/predicates.hpp:    BOOST_MPL_ASSERT_MSG((check), INVALID_INDEXES, (predicates_check_impl));  
boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp:    BOOST_MPL_ASSERT_MSG(!boost::is_unsigned<R>::value, RESULT_CANT_BE_UNSIGNED, (R));  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/concept.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/node/node.hpp:    BOOST_MPL_ASSERT_MSG((is_leaf_element<element_type>::value),  
boost/geometry/index/detail/rtree/rstar/redistribute_elements.hpp://    BOOST_MPL_ASSERT_MSG(false, NOT_IMPLEMENTED_FOR_THIS_TAG, (ElementIndexableTag));  
boost/geometry/index/detail/rtree/utilities/gl_draw.hpp:    BOOST_MPL_ASSERT_MSG((false), NOT_IMPLEMENTED_FOR_THIS_TAG, (Tag));  
boost/geometry/index/detail/rtree/utilities/print.hpp:    BOOST_MPL_ASSERT_MSG((false), NOT_IMPLEMENTED_FOR_THIS_TAG, (Tag));  
boost/geometry/index/detail/rtree/visitors/insert.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/rtree/visitors/insert.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/detail/varray.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/indexable.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/geometry/index/parameters.hpp:    BOOST_MPL_ASSERT_MSG((0 < MinElements && 2*MinElements <= MaxElements+1),  
boost/geometry/index/parameters.hpp:    BOOST_MPL_ASSERT_MSG((0 < MinElements && 2*MinElements <= MaxElements+1),  
boost/geometry/index/parameters.hpp:    BOOST_MPL_ASSERT_MSG((0 < MinElements && 2*MinElements <= MaxElements+1),  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((distance_predicates_count <= 1), PASS_ONLY_ONE_DISTANCE_PREDICATE, (Predicates));  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((distance_predicates_count <= 1), PASS_ONLY_ONE_DISTANCE_PREDICATE, (Predicates));  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((distance_predicates_count <= 1), PASS_ONLY_ONE_DISTANCE_PREDICATE, (Predicates));  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((! is_void),  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((detail::is_range<Range>::value),  
boost/geometry/index/rtree.hpp:        BOOST_MPL_ASSERT_MSG((detail::is_range<Range>::value),  
boost/geometry/io/svg/svg_mapper.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/io/svg/write.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/iterators/concatenate_iterator.hpp:        BOOST_MPL_ASSERT_MSG((are_conv),  
boost/geometry/iterators/detail/segment_iterator/range_segment_iterator.hpp:        BOOST_MPL_ASSERT_MSG((are_conv), NOT_CONVERTIBLE, (types<OtherRange>));  
boost/geometry/iterators/flatten_iterator.hpp:        BOOST_MPL_ASSERT_MSG((are_conv),  
boost/geometry/iterators/point_iterator.hpp:        BOOST_MPL_ASSERT_MSG((is_conv),  
boost/geometry/iterators/point_reverse_iterator.hpp:        BOOST_MPL_ASSERT_MSG((is_conv),  
boost/geometry/iterators/segment_iterator.hpp:        BOOST_MPL_ASSERT_MSG((is_conv),  
boost/geometry/policies/robustness/get_rescale_policy.hpp:    BOOST_MPL_ASSERT_MSG((is_point),  
boost/geometry/srs/projection.hpp:    BOOST_MPL_ASSERT_MSG((lesser_dim >= MinDim),  
boost/geometry/srs/projection.hpp:    BOOST_MPL_ASSERT_MSG((false),  
boost/geometry/srs/projection.hpp:        BOOST_MPL_ASSERT_MSG((projections::detail::same_tags<LL, XY>::value),  
boost/geometry/srs/projection.hpp:        BOOST_MPL_ASSERT_MSG((projections::detail::same_tags<XY, LL>::value),  
boost/geometry/srs/projections/factory.hpp:    BOOST_MPL_ASSERT_MSG((false), INVALID_PARAMETERS_TYPE, (Params));  
boost/geometry/srs/projections/impl/base_static.hpp:    BOOST_MPL_ASSERT_MSG((false),  
boost/geometry/srs/projections/impl/base_static.hpp:        BOOST_MPL_ASSERT_MSG((false),  
boost/geometry/srs/projections/impl/pj_ell_set.hpp:    BOOST_MPL_ASSERT_MSG((is_found), UNKNOWN_ELLP_PARAM, (Params));  
boost/geometry/srs/projections/impl/pj_init.hpp:    BOOST_MPL_ASSERT_MSG((is_found), PROJECTION_NOT_NAMED, (params_type));  
boost/geometry/srs/projections/impl/pj_init.hpp:    BOOST_MPL_ASSERT_MSG((is_valid), UNKNOWN_UNIT_ID, (Params));  
boost/geometry/srs/projections/impl/pj_param.hpp:    BOOST_MPL_ASSERT_MSG((is_ok), INVALID_ARGUMENT, (Name));  
boost/geometry/srs/projections/proj/ob_tran.hpp:                BOOST_MPL_ASSERT_MSG((is_found), NO_ROTATION_PROJ, (params_type));  
boost/geometry/srs/projections/proj/ob_tran.hpp:                BOOST_MPL_ASSERT_MSG((is_specialized), NO_ROTATION_PROJ, (params_type));  
boost/geometry/srs/projections/proj/ob_tran.hpp:                BOOST_MPL_ASSERT_MSG((is_non_resursive), INVALID_O_PROJ_PARAMETER, (params_type));  
boost/geometry/srs/projections/proj/ob_tran.hpp:                BOOST_MPL_ASSERT_MSG((is_o_proj_not_ob_tran), INVALID_O_PROJ_PARAMETER, (StaticParameters));  
boost/geometry/srs/projections/spar.hpp:    BOOST_MPL_ASSERT_MSG((false), INVALID_ARGUMENT, (Parameters));  
boost/geometry/srs/projections/spar.hpp:    BOOST_MPL_ASSERT_MSG((is_found), PROJECTION_NOT_NAMED, (Tuple));  
boost/geometry/srs/projections/spar.hpp:    BOOST_MPL_ASSERT_MSG((is_found), NO_O_PROJ_PARAMETER, (Tuple));  
boost/geometry/srs/transformation.hpp:        BOOST_MPL_ASSERT_MSG((projections::detail::same_tags<GeometryIn, GeometryOut>::value),  
boost/geometry/srs/transformation.hpp:        BOOST_MPL_ASSERT_MSG((projections::detail::same_tags<GeometryIn, GeometryOut>::value),  
boost/geometry/strategies/agnostic/point_in_poly_winding.hpp:    BOOST_MPL_ASSERT_MSG(false,  
boost/geometry/strategies/area.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/azimuth.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/compare.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/distance_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/distance_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/within_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/within_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/within_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/concepts/within_concept.hpp:            BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/covered_by.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/densify.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/distance.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/distance.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/distance.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/distance.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/envelope.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/expand.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/geographic/parameters.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/index.hpp:    /*BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/index.hpp:    /*BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/intersection.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/io.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/line_interpolate.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/point_order.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/relate.hpp:    BOOST_MPL_ASSERT_MSG((same_strategies),  
boost/geometry/strategies/relate.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/side.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/transform.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/strategies/within.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/util/combine_if.hpp:        BOOST_MPL_ASSERT(( boost::mpl::equal<combinations, result_types> ));  
boost/geometry/util/compress_variant.hpp:        BOOST_MPL_ASSERT(( boost::mpl::equal<compressed::types, result_types> ));  
boost/geometry/util/compress_variant.hpp:        BOOST_MPL_ASSERT(( boost::equals<single_type, int> ));  
boost/geometry/util/transform_variant.hpp:        BOOST_MPL_ASSERT(( equal<result, transformed> ));  
boost/geometry/util/transform_variant.hpp:        BOOST_MPL_ASSERT(( equal<result, transformed> ));  
boost/geometry/views/detail/boundary_view/implementation.hpp:        BOOST_MPL_ASSERT_MSG((is_convertible),  
boost/geometry/views/detail/range_type.hpp:    BOOST_MPL_ASSERT_MSG  
boost/geometry/views/detail/two_dimensional_view.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/geometry/views/detail/two_dimensional_view.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/graph/distributed/detail/mpi_process_group.ipp:  //BOOST_MPL_ASSERT(mpl::not_<is_mpi_datatype<Type> >);  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT_NOT((  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT((is_iterator_category<Category>));  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT_NOT((is_iterator_category<Traversal>));  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT_NOT((is_iterator_traversal<Category>));  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT((is_iterator_traversal<Traversal>));  
boost/iterator/detail/facade_iterator_category.hpp:    BOOST_MPL_ASSERT_NOT((is_iterator_category<Traversal>));  
boost/iterator/iterator_concepts.hpp:      BOOST_MPL_ASSERT((boost::is_integral<difference_type>));  
boost/iterator/iterator_concepts.hpp:      BOOST_MPL_ASSERT_RELATION(std::numeric_limits<difference_type>::is_signed, ==, true);  
boost/local_function/aux_/macro/decl.hpp:    BOOST_MPL_ASSERT_MSG(false, /* always fails (there's an error) */ \  
boost/mpi/datatype.hpp:  BOOST_MPL_ASSERT((is_mpi_datatype<T>));  
boost/mpi/detail/binary_buffer_iprimitive.hpp:      BOOST_MPL_ASSERT((serialization::is_bitwise_serializable<BOOST_DEDUCED_TYPENAME remove_const<T>::type>));  
boost/mpi/detail/binary_buffer_iprimitive.hpp:      BOOST_MPL_ASSERT((serialization::is_bitwise_serializable<BOOST_DEDUCED_TYPENAME remove_const<T>::type>));  
boost/mpi/detail/binary_buffer_oprimitive.hpp:      BOOST_MPL_ASSERT((serialization::is_bitwise_serializable<BOOST_DEDUCED_TYPENAME remove_const<T>::type>));  
boost/mpi/detail/binary_buffer_oprimitive.hpp:      BOOST_MPL_ASSERT((serialization::is_bitwise_serializable<BOOST_DEDUCED_TYPENAME remove_const<T>::type>));  
boost/mpi/detail/mpi_datatype_cache.hpp:    BOOST_MPL_ASSERT((is_mpi_datatype<T>));  
boost/mpi/detail/mpi_datatype_oarchive.hpp:          BOOST_MPL_ASSERT((is_mpi_datatype<T>));  
boost/mpl/assert.hpp:#ifndef BOOST_MPL_ASSERT_HPP_INCLUDED  
boost/mpl/assert.hpp:#define BOOST_MPL_ASSERT_HPP_INCLUDED  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT((pred<x,...>))  
boost/mpl/assert.hpp:#define BOOST_MPL_ASSERT(pred) \  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT_NOT((pred<x,...>))  
boost/mpl/assert.hpp:#define BOOST_MPL_ASSERT_NOT(pred) \  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT((pred<x,...>))  
boost/mpl/assert.hpp:#define BOOST_MPL_ASSERT(pred) \  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT_NOT((pred<x,...>))  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_NOT(pred) \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_NOT(pred) \  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT_RELATION(x, ==|!=|<=|<|>=|>, y)  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_RELATION_IMPL(counter, x, rel, y)      \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_RELATION_IMPL(counter, x, rel, y)    \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_RELATION(x, rel, y) \  
boost/mpl/assert.hpp:BOOST_MPL_ASSERT_RELATION_IMPL(BOOST_MPL_AUX_PP_COUNTER(), x, rel, y) \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_RELATION(x, rel, y) \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_RELATION(x, rel, y) \  
boost/mpl/assert.hpp:// BOOST_MPL_ASSERT_MSG( (pred<x,...>::value), USER_PROVIDED_MESSAGE, (types<x,...>) )   
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_MSG_IMPL( counter, c, msg, types_ ) \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_MSG_IMPL( counter, c, msg, types_ )  \  
boost/mpl/assert.hpp:// Work around BOOST_MPL_ASSERT_MSG_IMPL generating multiple definition linker errors on VC++8.  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_MSG( c, msg, types_ ) \  
boost/mpl/assert.hpp:#   define BOOST_MPL_ASSERT_MSG( c, msg, types_ ) \  
boost/mpl/assert.hpp:BOOST_MPL_ASSERT_MSG_IMPL( BOOST_MPL_AUX_PP_COUNTER(), c, msg, types_ ) \  
boost/mpl/assert.hpp:#endif // BOOST_MPL_ASSERT_HPP_INCLUDED  
boost/mpl/aux_/na_assert.hpp:    BOOST_MPL_ASSERT_NOT((boost::mpl::is_na<type>)) \  
boost/mpl/aux_/push_back_impl.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/mpl/aux_/push_front_impl.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/mpl/aux_/test/assert.hpp:#define MPL_ASSERT(pred)                BOOST_MPL_ASSERT(pred)  
boost/mpl/aux_/test/assert.hpp:#define MPL_ASSERT_NOT(pred)            BOOST_MPL_ASSERT_NOT(pred)  
boost/mpl/aux_/test/assert.hpp:#define MPL_ASSERT_MSG(c, msg, types)   BOOST_MPL_ASSERT_MSG(c, msg, types)  
boost/mpl/aux_/test/assert.hpp:#define MPL_ASSERT_RELATION(x, rel, y)  BOOST_MPL_ASSERT_RELATION(x, rel, y)  
boost/mpl/for_each.hpp:    BOOST_MPL_ASSERT(( is_sequence<Sequence> ));  
boost/mpl/string.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/mpl/string.hpp:            BOOST_MPL_ASSERT_MSG((C0 != 0), POP_BACK_FAILED_MPL_STRING_IS_EMPTY, (mpl::string<>));  \  
boost/mpl/string.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/mpl/string.hpp:            BOOST_MPL_ASSERT_MSG((C0 != 0), POP_FRONT_FAILED_MPL_STRING_IS_EMPTY, (mpl::string<>)); \  
boost/msm/back/mpl_graph_fsm_check.hpp:            BOOST_MPL_ASSERT_RELATION( ::boost::msm::back::check_regions_orthogonality<Fsm>::states_in_regions_raw,  
boost/msm/back/mpl_graph_fsm_check.hpp:            BOOST_MPL_ASSERT_RELATION( ::boost::msm::back::check_no_unreachable_state<Fsm>::states_in_fsm,  
boost/msm/back/state_machine.hpp:        BOOST_MPL_ASSERT(( has_fsm_deferred_events<library_sm> ));  
boost/msm/back/state_machine.hpp:         BOOST_MPL_ASSERT_MSG(  
boost/msm/back/state_machine.hpp:         BOOST_MPL_ASSERT_MSG(                                                      \  
boost/msm/back/state_machine.hpp:         BOOST_MPL_ASSERT_MSG(  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<EntryFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<ExitFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<EntryFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<ExitFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<EntryFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<ExitFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<EntryFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<ExitFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<EntryFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<ExitFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<NoTransitionFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<OnExceptionFunctor,invalid_type> ));  
boost/msm/front/euml/state_grammar.hpp:    BOOST_MPL_ASSERT_NOT(( boost::is_same<STT,invalid_type> ));  
boost/parameter/aux_/arg_list.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/parameter/aux_/arg_list.hpp:            BOOST_MPL_ASSERT_NOT((_holds_maybe));  
boost/parameter/aux_/arg_list.hpp:            BOOST_MPL_ASSERT_NOT((_holds_maybe));  
boost/parameter/aux_/arg_list.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/parameter/aux_/arg_list.hpp:        BOOST_MPL_ASSERT_MSG(  
boost/parameter/aux_/arg_list.hpp:            BOOST_MPL_ASSERT_NOT((_holds_maybe));  
boost/parameter/aux_/arg_list.hpp:            BOOST_MPL_ASSERT_NOT((_holds_maybe));  
boost/parameter/aux_/arg_list.hpp:            BOOST_MPL_ASSERT_NOT((_holds_maybe));  
boost/parameter/aux_/pack/deduce_tag.hpp:        BOOST_MPL_ASSERT((  
boost/parameter/aux_/pack/unmatched_argument.hpp:        BOOST_MPL_ASSERT((  
boost/parameter/binding.hpp:        BOOST_MPL_ASSERT((  
boost/parameter/value_type.hpp:        BOOST_MPL_ASSERT((  
boost/phoenix/statement/switch.hpp:                BOOST_MPL_ASSERT_MSG(  
boost/property_map/property_map.hpp:      BOOST_MPL_ASSERT((boost::mpl::or_<  
boost/property_map/property_map.hpp:      BOOST_MPL_ASSERT((boost::is_same<value_type&, reference>));  
boost/proto/debug.hpp:    /// \note Equivalent to <tt>BOOST_MPL_ASSERT((proto::matches\<Expr, Grammar\>))</tt>  
boost/proto/debug.hpp:        BOOST_MPL_ASSERT((proto::matches<Expr, Grammar>));  
boost/proto/debug.hpp:    /// \note Equivalent to <tt>BOOST_MPL_ASSERT_NOT((proto::matches\<Expr, Grammar\>))</tt>  
boost/proto/debug.hpp:        BOOST_MPL_ASSERT_NOT((proto::matches<Expr, Grammar>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<bar,        result_of_fixup<bar>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<bar const,  result_of_fixup<bar const>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<bar,        result_of_fixup<bar &>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<bar const,  result_of_fixup<bar const &>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<void(*)(),  result_of_fixup<void(*)()>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<void(*)(),  result_of_fixup<void(* const)()>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<void(*)(),  result_of_fixup<void(* const &)()>::type>));  
boost/proto/detail/decltype.hpp:        //BOOST_MPL_ASSERT((is_same<void(*)(),  result_of_fixup<void(&)()>::type>));  
boost/proto/detail/deduce_domain.hpp:# define BOOST_PROTO_ASSERT_VALID_DOMAIN(DOM) BOOST_MPL_ASSERT_NOT((boost::is_same<DOM, boost::proto::detail::not_a_domain>))  
boost/proto/matches.hpp:    /// BOOST_MPL_ASSERT((  
boost/proto/transform/detail/pack.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/pack.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/detail/preprocessed/pack_impl.hpp:            BOOST_MPL_ASSERT_MSG(  
boost/proto/transform/env.hpp:                BOOST_MPL_ASSERT_NOT((is_same<result_type, key_not_found>)); // lookup failed  
boost/python/make_constructor.hpp:      BOOST_MPL_ASSERT_MSG(  
boost/python/object/class_metadata.hpp:        BOOST_MPL_ASSERT_NOT((boost::python::detail::is_same<Base,Derived>));  
boost/python/object/make_instance.hpp:        BOOST_MPL_ASSERT((mpl::or_<boost::python::detail::is_class<T>,  
boost/python/return_opaque_pointer.hpp:        BOOST_MPL_ASSERT_MSG( is_pointer<R>::value, RETURN_OPAQUE_POINTER_EXPECTS_A_POINTER_TYPE, (R));  
boost/range/concepts.hpp:            BOOST_MPL_ASSERT((is_integral<difference_type>));  
boost/range/concepts.hpp:            BOOST_MPL_ASSERT_RELATION(std::numeric_limits<difference_type>::is_signed, ==, true);  
boost/ratio/config.hpp:    BOOST_MPL_ASSERT_MSG(boost::mpl::bool_< (CND) >::type::value, MSG, TYPES)  
boost/serialization/version.hpp:    BOOST_MPL_ASSERT((                                                 \  
boost/serialization/version.hpp:    BOOST_MPL_ASSERT((                                                 \  
boost/spirit/home/support/assert_msg.hpp:// Work around the MPL problem in BOOST_MPL_ASSERT_MSG generating  
boost/spirit/home/support/assert_msg.hpp:        BOOST_MPL_ASSERT_MSG(Cond, Msg, Types)  
boost/spirit/repository/home/qi/operator/detail/keywords.hpp:                    BOOST_MPL_ASSERT_RELATION( mpl::size<no_duplicate_char_types>::value, ==, 1 );  
boost/spirit/repository/home/qi/operator/keywords.hpp:        BOOST_MPL_ASSERT_RELATION( non_kwd_subject_count::value, ==, 0 );  
boost/test/tools/assertion.hpp:        BOOST_MPL_ASSERT_MSG(false, CANT_USE_LOGICAL_OPERATOR_OR_WITHIN_THIS_TESTING_TOOL, () );  
boost/test/tools/assertion.hpp:        BOOST_MPL_ASSERT_MSG(false, CANT_USE_LOGICAL_OPERATOR_AND_WITHIN_THIS_TESTING_TOOL, () );  
boost/test/tools/assertion.hpp:        BOOST_MPL_ASSERT_MSG(false, CANT_USE_TERNARY_OPERATOR_WITHIN_THIS_TESTING_TOOL, () );  
boost/tti/detail/dclass.hpp:  BOOST_MPL_ASSERT((BOOST_TTI_NAMESPACE::detail::is_lambda_expression<BOOST_TTI_DETAIL_TP_MFC>)); \  
boost/tti/detail/denum.hpp:  BOOST_MPL_ASSERT((BOOST_TTI_NAMESPACE::detail::is_lambda_expression<BOOST_TTI_DETAIL_TP_MFC>)); \  
boost/tti/detail/dmem_data.hpp:        BOOST_MPL_ASSERT((boost::function_types::is_member_object_pointer<BOOST_TTI_DETAIL_TP_T>));  
boost/tti/detail/dmem_data.hpp:        BOOST_MPL_ASSERT((boost::function_types::is_member_object_pointer<BOOST_TTI_DETAIL_TP_T>));  
boost/tti/detail/dmem_fun.hpp:    BOOST_MPL_ASSERT((boost::function_types::is_member_function_pointer<BOOST_TTI_DETAIL_TP_T>)); \  
boost/tti/detail/dmem_fun_template.hpp:    BOOST_MPL_ASSERT((boost::function_types::is_member_function_pointer<BOOST_TTI_DETAIL_TP_T>)); \  
boost/tti/detail/dtype.hpp:  BOOST_MPL_ASSERT((BOOST_TTI_NAMESPACE::detail::is_lambda_expression<BOOST_TTI_DETAIL_TP_U>)); \  
boost/tti/detail/dunion.hpp:  BOOST_MPL_ASSERT((BOOST_TTI_NAMESPACE::detail::is_lambda_expression<BOOST_TTI_DETAIL_TP_MFC>)); \  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::type_erasure::is_relaxed<Concept>));  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::type_erasure::is_relaxed<Concept>));  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::type_erasure::is_relaxed<Concept>));  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::type_erasure::is_relaxed<Concept>));  
boost/type_erasure/any.hpp:        BOOST_MPL_ASSERT((::boost::is_same<  
boost/type_erasure/any_cast.hpp:    BOOST_MPL_ASSERT((::boost::is_pointer<T>));  
boost/type_erasure/any_cast.hpp:    BOOST_MPL_ASSERT((::boost::is_pointer<T>));  
boost/type_erasure/binding.hpp:    binding() { BOOST_MPL_ASSERT((::boost::type_erasure::is_relaxed<Concept>)); }  
boost/type_erasure/detail/normalize.hpp:    BOOST_MPL_ASSERT((::boost::is_same<T, U>));  
boost/type_erasure/detail/normalize.hpp:    BOOST_MPL_ASSERT((boost::is_same<typename ::boost::mpl::at<type, typename P::second>::type, result>));  
boost/type_erasure/detail/normalize.hpp:    BOOST_MPL_ASSERT((boost::is_same<typename ::boost::mpl::at<type, typename P::second>::type, result>));  
boost/type_erasure/detail/normalize.hpp:    BOOST_MPL_ASSERT((boost::is_same< ::boost::mp11::mp_second< ::boost::mp11::mp_map_find<type, typename P::second> >, result>));  
boost/type_erasure/detail/normalize.hpp:    BOOST_MPL_ASSERT((boost::is_same< ::boost::mp11::mp_second< ::boost::mp11::mp_map_find<type, typename P::second> >, result>));  
boost/type_erasure/detail/rebind_placeholders.hpp:    BOOST_MPL_ASSERT((boost::mpl::or_<  
boost/type_erasure/register_binding.hpp:    BOOST_MPL_ASSERT((boost::mpl::equal_to<boost::mpl::size<unknown_placeholders>, boost::mpl::int_<1> >));  
boost/units/detail/linear_algebra.hpp:    BOOST_MPL_ASSERT_MSG((N > 0), base_units_are_probably_not_linearly_independent, (void));  
boost/units/heterogeneous_system.hpp:    BOOST_MPL_ASSERT_MSG((!boost::is_same<exponents, inconsistent>::value), the_specified_dimension_is_not_representible_in_the_given_system, (types<Dimensions, System>));  
boost/units/quantity.hpp:        BOOST_MPL_ASSERT_NOT((detail::is_base_unit<Unit>));  
boost/units/unit.hpp:            BOOST_MPL_ASSERT((detail::check_system<System, Dim>));  
boost/units/unit.hpp:            BOOST_MPL_ASSERT((is_dimension_list<Dim>));  
boost/units/unit.hpp:        BOOST_MPL_ASSERT((detail::check_system<System, Dim>));  
boost/units/unit.hpp:        BOOST_MPL_ASSERT((is_dimension_list<Dim>));  
boost/variant/variant.hpp:    BOOST_MPL_ASSERT_NOT(( is_sequence_based_ ));  
boost/xpressive/detail/core/matcher/alternate_matcher.hpp:            BOOST_MPL_ASSERT_RELATION(unknown_width::value, !=, Alternates::width);  
boost/xpressive/detail/core/matcher/regex_matcher.hpp:            BOOST_MPL_ASSERT((is_static_xpression<Next>));  
boost/xpressive/detail/dynamic/matchable.hpp:        BOOST_MPL_ASSERT((is_same<Top, matchable_ex<BidiIter> >));  
boost/xpressive/detail/dynamic/matchable.hpp:        BOOST_MPL_ASSERT((is_same<Top, matchable_ex<BidiIter> >));  
boost/xpressive/detail/dynamic/parse_charset.hpp:    BOOST_MPL_ASSERT_RELATION(sizeof(uchar_t), ==, sizeof(char_type));  
boost/xpressive/detail/static/grammar.hpp:    BOOST_MPL_ASSERT\  
boost/xpressive/detail/static/is_pure.hpp:        BOOST_MPL_ASSERT_RELATION(0, !=, (width_of<Expr, Char>::value));  
boost/xpressive/detail/static/is_pure.hpp:        BOOST_MPL_ASSERT_RELATION(0, !=, (width_of<Expr, Char>::value));  
boost/xpressive/detail/static/static.hpp:    BOOST_MPL_ASSERT_RELATION(sizeof(stacked_xpression<Top, Next>), ==, sizeof(Next));  
boost/xpressive/detail/static/transforms/as_set.hpp:            BOOST_MPL_ASSERT_NOT((Not));  
boost/xpressive/detail/static/transmogrify.hpp:        BOOST_MPL_ASSERT((is_same<Char, char_type>));  
boost/xpressive/detail/static/width_of.hpp:        BOOST_MPL_ASSERT_RELATION(  
boost/xpressive/detail/utility/chset/basic_chset.ipp:    BOOST_MPL_ASSERT_RELATION(1, ==, sizeof(Char));  
boost/xpressive/detail/utility/tracking_ptr.hpp:    BOOST_MPL_ASSERT((is_base_and_derived<enable_reference_tracking<Type>, Type>));  
boost/xpressive/detail/utility/traits_utils.hpp:        BOOST_MPL_ASSERT((is_same<FromChar, char>));  
boost/xpressive/detail/utility/traits_utils.hpp:        BOOST_MPL_ASSERT((is_same<FromChar, char>));  
boost/xpressive/match_results.hpp:        BOOST_MPL_ASSERT((proto::matches<Arg, detail::ActionArgBinding>));  
boost/xpressive/match_results.hpp:        BOOST_MPL_ASSERT((is_same<typename arg_left_type::type, arg_right_type>));  
boost/xpressive/match_results.hpp:        BOOST_MPL_ASSERT_RELATION(sizeof(uchar_t), ==, sizeof(char_type));  
boost/xpressive/regex_actions.hpp:                BOOST_MPL_ASSERT_MSG(  
boost/xpressive/regex_compiler.hpp:        BOOST_MPL_ASSERT((is_same<char_type, typename iterator_value<FwdIter>::type>));  
boost/xpressive/regex_token_iterator.hpp:    BOOST_MPL_ASSERT((is_convertible<Int, int>));  
boost/xpressive/sub_match.hpp:    BOOST_MPL_ASSERT_MSG(  
boost/xpressive/traits/cpp_regex_traits.hpp:    BOOST_MPL_ASSERT_RELATION(sizeof(std::ctype_base::mask), ==, sizeof(umask_t));  
boost/xpressive/traits/cpp_regex_traits.hpp:    BOOST_MPL_ASSERT_RELATION(sizeof(umask_t), <=, sizeof(umaskex_t));  
boost/xpressive/traits/cpp_regex_traits.hpp:        BOOST_MPL_ASSERT_RELATION('\227', ==, std::ctype_base::print);  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION('\t', ==, L'\t');  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION(' ', ==, L' ');  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION('_', ==, L'_');  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION('\r', ==, L'\r');  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION('\n', ==, L'\n');  
boost/xpressive/traits/cpp_regex_traits.hpp:            BOOST_MPL_ASSERT_RELATION('\f', ==, L'\f');  
boost/xpressive/traits/cpp_regex_traits.hpp:        BOOST_MPL_ASSERT((is_same<char_type, char>));  
boost/xpressive/traits/c_regex_traits.hpp:        BOOST_MPL_ASSERT((is_same<char_type, char>));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0x07FF, ==, (_XB|_XA|_XS|_BB|_CN|_DI|_LO|_PU|_SP|_UP|_XD));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0x81FF, ==, (_ALPHA|_UPPER|_LOWER|_DIGIT|_SPACE|_PUNCT|_CONTROL|_BLANK|_HEX|_LEADBYTE));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0x81FF, ==, (_ALPHA|_UPPER|_LOWER|_DIGIT|_SPACE|_PUNCT|_CONTROL|_BLANK|_HEX|_LEADBYTE));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0377, ==, (_U|_L|_N|_S|_P|_C|_B|_X));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0xffff, ==, (_ISalnum|_ISalpha|_ISblank|_IScntrl|_ISdigit|_ISgraph|  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0x81FF, ==, (_ALPHA|_UPPER|_LOWER|_DIGIT|_SPACE|_PUNCT|_CONTROL|_BLANK|_HEX|_LEADBYTE));  
boost/xpressive/traits/detail/c_ctype.hpp:    BOOST_MPL_ASSERT_RELATION(0x81FF, ==, (_ALPHA|_UPPER|_LOWER|_DIGIT|_SPACE|_PUNCT|_CONTROL|_BLANK|_HEX|_LEADBYTE));  
```

---

## Comment 9

> Username: eldiener  
> Created_at: 2020-09-25 20:50:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/47#issuecomment-699148882  

I tried thje bimap tests with clang on Windows using clang-cl, with -Wunused-variable, and no errors occurred.

---
