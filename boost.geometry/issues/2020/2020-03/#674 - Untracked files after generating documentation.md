# #674 - Untracked files after generating documentation [Closed]

> Username: mloskot  
> Created at: 2020-03-02 22:36:40 UTC  
> Updated at: 2020-03-05 21:49:54 UTC  
> Closed at: 2020-03-05 21:49:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/674  

After building docs, the local repo becomes polluted with untracked files.  
Any reason we do not to add those to `.gitignore`?  
  
```console  
/d/boost.wsl/libs/geometry (develop u= origin/develop) $ git status  
On branch develop  
Your branch is up to date with 'origin/develop'.  
  
Changes not staged for commit:  
  (use "git add <file>..." to update what will be committed)  
  (use "git restore <file>..." to discard changes in working directory)  
        modified:   doc/html/index.html  
  
Untracked files:  
  (use "git add <file>..." to include in what will be committed)  
        doc/doxy/doxygen_output/html_by_doxygen/  
        doc/doxy/doxygen_output/xml/  
        doc/generated/append.qbk  
        doc/generated/area.qbk  
        doc/generated/area_cartesian.qbk  
        doc/generated/area_geographic.qbk  
        doc/generated/area_spherical.qbk  
        doc/generated/arithmetic.qbk  
        doc/generated/assign.qbk  
        doc/generated/box.qbk  
        doc/generated/box_view.qbk  
        doc/generated/buffer.qbk  
        doc/generated/buffer_distance_asymmetric.qbk  
        doc/generated/buffer_distance_symmetric.qbk  
        doc/generated/buffer_end_flat.qbk  
        doc/generated/buffer_end_round.qbk  
        doc/generated/buffer_geographic_point_circle.qbk  
        doc/generated/buffer_join_miter.qbk  
        doc/generated/buffer_join_round.qbk  
        doc/generated/buffer_point_circle.qbk  
        doc/generated/buffer_point_square.qbk  
        doc/generated/buffer_side_straight.qbk  
        doc/generated/cartesian.qbk  
        doc/generated/centroid.qbk  
        doc/generated/centroid_average.qbk  
        doc/generated/centroid_bashein_detmer.qbk  
        doc/generated/centroid_exception.qbk  
        doc/generated/circular_iterator.qbk  
        doc/generated/clear.qbk  
        doc/generated/closeable_view.qbk  
        doc/generated/closing_iterator.qbk  
        doc/generated/closure.qbk  
        doc/generated/convert.qbk  
        doc/generated/convex_hull.qbk  
        doc/generated/convex_hull_graham_andrew.qbk  
        doc/generated/coordinate_system.qbk  
        doc/generated/coordinate_type.qbk  
        doc/generated/correct.qbk  
        doc/generated/covered_by.qbk  
        doc/generated/crosses.qbk  
        doc/generated/cs_tag.qbk  
        doc/generated/de9im_mask.qbk  
        doc/generated/de9im_matrix.qbk  
        doc/generated/de9im_static_mask.qbk  
        doc/generated/degree.qbk  
        doc/generated/densify.qbk  
        doc/generated/densify_cartesian.qbk  
        doc/generated/densify_geographic.qbk  
        doc/generated/densify_spherical.qbk  
        doc/generated/difference.qbk  
        doc/generated/dimension.qbk  
        doc/generated/discrete_frechet_distance.qbk  
        doc/generated/discrete_hausdorff_distance.qbk  
        doc/generated/disjoint.qbk  
        doc/generated/distance.qbk  
        doc/generated/distance_cross_track.qbk  
        doc/generated/distance_cross_track_point_box.qbk  
        doc/generated/distance_haversine.qbk  
        doc/generated/distance_projected_point.qbk  
        doc/generated/distance_pythagoras.qbk  
        doc/generated/distance_pythagoras_box_box.qbk  
        doc/generated/distance_pythagoras_point_box.qbk  
        doc/generated/dsv.qbk  
        doc/generated/enum.qbk  
        doc/generated/envelope.qbk  
        doc/generated/equals.qbk  
        doc/generated/ever_circling_iterator.qbk  
        doc/generated/exception.qbk  
        doc/generated/expand.qbk  
        doc/generated/exterior_ring.qbk  
        doc/generated/for_each.qbk  
        doc/generated/geographic.qbk  
        doc/generated/get.qbk  
        doc/generated/identity_view.qbk  
        doc/generated/interior_rings.qbk  
        doc/generated/interior_type.qbk  
        doc/generated/intersection.qbk  
        doc/generated/intersects.qbk  
        doc/generated/is_empty.qbk  
        doc/generated/is_simple.qbk  
        doc/generated/is_valid.qbk  
        doc/generated/length.qbk  
        doc/generated/line_interpolate.qbk  
        doc/generated/line_interpolate_cartesian.qbk  
        doc/generated/line_interpolate_geographic.qbk  
        doc/generated/line_interpolate_spherical.qbk  
        doc/generated/linestring.qbk  
        doc/generated/make.qbk  
        doc/generated/multi_linestring.qbk  
        doc/generated/multi_point.qbk  
        doc/generated/multi_polygon.qbk  
        doc/generated/num_geometries.qbk  
        doc/generated/num_interior_rings.qbk  
        doc/generated/num_points.qbk  
        doc/generated/num_segments.qbk  
        doc/generated/overlaps.qbk  
        doc/generated/perimeter.qbk  
        doc/generated/point.qbk  
        doc/generated/point_order.qbk  
        doc/generated/point_type.qbk  
        doc/generated/point_xy.qbk  
        doc/generated/polar.qbk  
        doc/generated/polygon.qbk  
        doc/generated/radian.qbk  
        doc/generated/referring_segment.qbk  
        doc/generated/register.qbk  
        doc/generated/relate.qbk  
        doc/generated/relation.qbk  
        doc/generated/reverse.qbk  
        doc/generated/reversible_view.qbk  
        doc/generated/ring.qbk  
        doc/generated/ring_type.qbk  
        doc/generated/segment.qbk  
        doc/generated/segment_view.qbk  
        doc/generated/set.qbk  
        doc/generated/side_geographic.qbk  
        doc/generated/side_side_by_cross_track.qbk  
        doc/generated/side_side_by_triangle.qbk  
        doc/generated/side_spherical_side_formula.qbk  
        doc/generated/simplify.qbk  
        doc/generated/simplify_douglas_peucker.qbk  
        doc/generated/spherical.qbk  
        doc/generated/spherical_equatorial.qbk  
        doc/generated/srs_spheroid.qbk  
        doc/generated/svg.qbk  
        doc/generated/svg_mapper.qbk  
        doc/generated/sym_difference.qbk  
        doc/generated/tag.qbk  
        doc/generated/tag_cast.qbk  
        doc/generated/touches.qbk  
        doc/generated/transform.qbk  
        doc/generated/transform_inverse_transformer.qbk  
        doc/generated/transform_map_transformer.qbk  
        doc/generated/transform_matrix_transformer.qbk  
        doc/generated/transform_rotate_transformer.qbk  
        doc/generated/transform_scale_transformer.qbk  
        doc/generated/transform_translate_transformer.qbk  
        doc/generated/union.qbk  
        doc/generated/unique.qbk  
        doc/generated/within.qbk  
        doc/generated/within_crossings_multiply.qbk  
        doc/generated/within_franklin.qbk  
        doc/generated/within_winding.qbk  
        doc/generated/wkt.qbk  
        doc/html/geometry/  
        doc/html/geometry_HTML.manifest  
        doc/index/generated/adaptors.qbk  
        doc/index/generated/equal_to.qbk  
        doc/index/generated/indexable.qbk  
        doc/index/generated/inserters.qbk  
        doc/index/generated/predicates.qbk  
        doc/index/generated/rtree.qbk  
        doc/index/generated/rtree_dynamic_linear.qbk  
        doc/index/generated/rtree_dynamic_quadratic.qbk  
        doc/index/generated/rtree_dynamic_rstar.qbk  
        doc/index/generated/rtree_functions.qbk  
        doc/index/generated/rtree_linear.qbk  
        doc/index/generated/rtree_quadratic.qbk  
        doc/index/generated/rtree_rstar.qbk  
        doc/index/html_by_doxygen/  
        doc/index/xml/  
  
no changes added to commit (use "git add" and/or "git commit -a")  
$  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2020-03-03 13:51:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/674#issuecomment-593960181  

>Any reason we do not to add those to .gitignore?  
  
I do not see any.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-05 21:48:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/674#issuecomment-595464517  

@vissarion Thanks, I will add `.gitignore`-s
