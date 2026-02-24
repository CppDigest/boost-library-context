# #748 Remove direct usage of Boost.MPL and Boost.TypeTraits from the library. [Merged]

> Username: awulkiew  
> Created at: 2020-08-29 16:28:56 UTC  
> Updated at: 2020-10-01 17:06:52 UTC  
> Merged at: 2020-10-01 17:06:51 UTC  
> Closed at: 2020-10-01 17:06:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/748  

Changes:  
- new files:  
   - `util/type_traits_std.hpp` - type traits not available in C++14 dependent only on standard headers, e.g. C++17 `bool_constant`, C++20 `remove_cvref`, non-standard `transcribe_const`, etc.  
   - `util/type_traits.hpp` - geometry-related type traits dependent on tags, e.g. `is_linear`, `enable_if_point`, etc.  
   - `util/sequence.hpp` - tools for manipulating `std::integer_sequence` and non-standard `type_sequence` (replacing `mpl::vector_c` and `mpl::vector` respectively), the interface is mimicking the one of `std::tuple` metafunctions, e.g. `sequence_element` replaces `mpl::at_c`  
- `add_const_if_c` moved to util/type_traits_std.hpp, all usage removed or replaced with newly added `transcribe_const`  
- `util::bare_type` moved to util/type_traits_std.hpp, all usage replaced with newly added `remove_cptrref`  
- `is_range` moved to `util/range.hpp`  
- added new tags:  
   - `polylinear_tag` - range-based linear geometry (Ls, MLs)  
   - `polysegmental_tag` - range-based linear or areal geometry (Ls, MLs, Ring, Poly, MPoly)  
- added variadic templates in utilities like `select_most_precise`, `select_calculation_type_alt`  
- `select_calculation_type` used instead of `select_most_precise` in `buffer::geographic_point_circle` to correctly handle `CalculationType`  
- Boost.TypeTraits and Boost.MPL replaced in the majority of the library, what's left is:  
   - `MPL_ASSERT` - I'd like to propose a separate PR for that  
   - `Boost.Fusion` adaptation  
   - in deprecated headers not included automatically like `util/compress_variant.hpp`  
   - in `util/parameter_type_of.hpp` because Boost.FunctionTypes is used there for now  
- `geometry::dimension<>` is derived from `std::integral_constant<std::size_t, trait::dimension<Geometry>::value>`, rationale:  
    - to guarantee the type (standard uses std::size_t for such things, e.g. `std::array`'s size, `std::tuple_size<>::value`)  
    - we already use `std::size_t` across the library for Dimensions  
    - to allow users to define `trait::dimension` however they like as long as there is `value` member  
    - this is a potentially breaking change  
- `distance_result` for variants is implemented differently, it no longer can be a variant type, it is always the most precise numeric type for any combination of variant geometries and strategy (so result of a strategy), rationale:  
   - simplicity  
   - in order to get variant result the input types had to be specific which makes it unlikely, i.e. variant types had to hold geometries using different coordinate types and at least one of these types had to be more precise than double (so e.g. user-defined or `long double` in a compiler actually providing this as separate type to `double`), because in distance strategies all fundamental result types are promoted to `double`  
   - still this is a potentially breaking change

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-08-29 16:31:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-478139473  

📁 include/boost/geometry/util/type_traits.hpp

```diff
 130 |+ struct is_polylinear
 131 |+     : std::is_base_of<polylinear_tag, typename tag<T>::type>
 132 |+ {};
```

> Username: awulkiew  
> Created_at: 2020-08-29 16:31:59 UTC  
> Updated_at: 2020-09-11 11:58:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r479666278  

I decided to use *polylinear* name for linear geometries which are not single segment. Similar to *polygonal* for areal geometries that are not a box. If you have a different idea feel free to propose a change.

> Username: barendgehrels  
> Created_at: 2020-09-16 08:36:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r489263035  

What is the difference with `linestring` ? Those are also linear geometries with not a single segment. But I assume you mean something else?

> Username: awulkiew  
> Created_at: 2020-09-16 10:50:26 UTC  
> Updated_at: 2020-09-16 10:50:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r489344914  

Yes, polylinear means linestring or multi_linestring. I added the tag in case someone wanted to use `tag_cast` instead and because we already have `polygonal_tag`.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-08-29 16:34:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-478139640  

📁 include/boost/geometry/util/type_traits.hpp

```diff
 172 |+ struct is_polysegmental
 173 |+     : bool_constant<is_polylinear<T>::value || is_polygonal<T>::value>
 174 |+ {};
```

> Username: awulkiew  
> Created_at: 2020-08-29 16:34:36 UTC  
> Updated_at: 2020-09-11 11:58:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r479666532  

Here is another group of geometries, i.e. geometries based on ranges of segments which are not a single segment, both linear and areal. I named it `polysegmental` feel free to suggest a different name.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-08-29 16:36:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-478139768  

📁 include/boost/geometry/util/type_traits.hpp

```diff
 184 |+ struct is_multi_element
 185 |+     : bool_constant<is_point<T>::value || is_linestring<T>::value || is_polygon<T>::value>
 186 |+ {};
```

> Username: awulkiew  
> Created_at: 2020-08-29 16:36:36 UTC  
> Updated_at: 2020-09-11 11:58:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r479666734  

Here is another group, single geometries which can be elements of a multi geometry. I didn't have an idea about a name so the one I used is more straightforward than the other ones I mentioned. I'm open to suggestion.

> Username: barendgehrels  
> Created_at: 2020-09-16 08:35:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r489261903  

Fine for me, it's clear.

> Username: vissarion  
> Created_at: 2020-09-23 12:33:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#discussion_r493534723  

The names are fine by me too. I do not know if they make a lot of geometric sense but they are consistent and clear. So I have no objection.


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2020-09-10 08:21:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-485695834  

I have not reviewed the code changes, but I like the general direction (removal of legacy libraries).  
So, thank you @awulkiew and I give 👍  assuming tests and examples compile and pass, examples compile.

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2020-09-10 15:36:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-486064285  

Looks good to me, thanks!

---

## Comment 6

> Username: awulkiew  
> Created_at: 2020-09-11 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-691054424  

I decided to move type_traits and sequence utilities from `detail` namespace to `util` namespace because:  
- it's shorter  
- it's easier to use in deeper `detail` namespaces like `strategies::detail`, e.g. `util::enable_if_linear` vs `geometry::detail::enable_if_linear`  
Please let me know if you're ok with it.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2020-09-11 12:10:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-691056099  

@mloskot I'll put it this way: the number of passing tests and compiling examples remains the same. ;)

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-09-11 12:29:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-691065031  

I'll wait with merging this for @barendgehrels merging https://github.com/boostorg/geometry/pull/752.

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-09-11 17:28:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-691221266  

> I decided to move type_traits and sequence utilities from `detail` namespace to `util` namespace because  
> it's shorter  
> it's easier to use  
  
I'd just point out that semantic of the `detail` namespace is "not a public interface, can change/deprecate/remove w/o notice".  
The proposed simplification breaks that notion and it may hit back with users complaining about when things have been removed from the `util` what I see likely.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2020-09-11 21:32:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-691321186  

@mloskot yes, I'm aware of that. I find it unlikely that these type traits will be modified in the future. The only candidate I can think of are metafunctions for sequences (`merge`, `combine`, etc.) which could have names changed to names consistent with other utils like `sequence_element`, `sequence_size`, etc. (named after metafunctions for `std::tuple`). So they could be named `sequence_merge`, `sequence_combine` or `merge_sequences`, `combine_sequences`, etc. Other than that I think the interface is ok.  
With that said I wouldn't hesitate much if we wanted to do any change in the future because these tools are undocumented and AFAIU anything that is undocumented may be changed as well. I think it was already done in the past with algorithms in `boost::geometry` namespace. An example can be `sectionalize` which is in `algorithms/detail` directory but the function is in the `boost::geometry` namespace and at some point an additional `DimensionVector` template argument was added to it. I'm not endorsing this practice in general. I'm only pointing out that the code that is not documented is probably rarely used.

---

## Review 11 [Approved]

> Username: barendgehrels  
> Created_at: 2020-09-16 08:32:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/748#pullrequestreview-489396322  

I glanced through the changes, and it looks good to me, thanks!  
But it's too much to review it in detail.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2020-09-16 08:33:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/748#issuecomment-693260078  

> I decided to move type_traits and sequence utilities from `detail` namespace to `util` namespace because:  
>   
>     * it's shorter  
>   
>     * it's easier to use in deeper `detail` namespaces like `strategies::detail`, e.g. `util::enable_if_linear` vs `geometry::detail::enable_if_linear`  
>       Please let me know if you're ok with it.  
  
Fine for me.

---
