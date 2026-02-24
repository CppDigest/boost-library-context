# #987 Two GC-related utilities [Merged]

> Username: awulkiew  
> Created at: 2022-03-18 22:22:11 UTC  
> Updated at: 2022-05-24 14:42:51 UTC  
> Merged at: 2022-05-24 14:42:50 UTC  
> Closed at: 2022-05-24 14:42:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/987  

- a view flattening a GC and allowing to access it randomly  
- an algorithm merging geometries stored in a GC into corresponding multi-geometries (related to: https://github.com/boostorg/geometry/discussions/947)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2022-04-08 08:45:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1092610793  

Is this in review? There are no reviewers added.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2022-04-08 09:24:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1092645569  

@barendgehrels Yes, I've forgotten to add reviewers, sorry about that.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-04-08 09:55:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1092687131  

> @barendgehrels Yes, I've forgotten to add reviewers, sorry about that.  
  
No problem, thanks!

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2022-04-18 08:42:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/987#pullrequestreview-944208149  

LGTM, thanks! How is this compared to the two implementations (iterative and recursive) mentioned in https://github.com/boostorg/geometry/discussions/947 performance-wise?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2022-04-19 20:46:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1103142035  

@vissarion I'm testing 4 implementations:  
- **naiive** - iterative algorithm working on polygons (no GC nor dynamic geometries)  
- **recursive** - recursive algroithm working on polygons (no GC nor dynamic geometries)  
- **dfs** - pseudo-recursive algorithm with stacks managed manually working on polygons (no GC nor dynamic geometries)  
- **merge_elements** - algorithm implements in this PR (same as above but taking a GCs)  
  
7 kinds of data with various numbers of polygons (N squares):  
  
**0**. distributed diagonally from bottom-left to top-right  
**1**. distributed diagonally from top-right to bottom-left  
**2**. distributed diagonally randomly  
**3**. distributed along Y axis randomly with X slightly incremented (each by 0.00001)  
**4**. distributed along X axis randomly with Y slightly incremented (each by 0.00001)  
**5**. distributed along Y axis randomly with X = 0  
**6**. distributed along X axis randomly with Y = 0  
  
The same random number is used for placement to allow comparison. For **N = 10** the sets look like this:  
![image832](https://user-images.githubusercontent.com/1226951/164085092-7076e624-022a-42f3-9d96-f4f150582639.png)  
  
With msvc-14.1 x64 Release.  
  
Results for **N = 10**:  
|           | naiive [us] | recursive [us] | dfs [us] | merge_elements [us] |  
| ---------- | ---------- | --------- | -------- | ------------ |  
| **set 0** | 254 | 136 | 103 | 121 |   
| **set 1** | 113 | 118 | 93  | 107 |   
| **set 2** | 315 | 179 | 164 | 163 |   
| **set 3** | 231 | 128 | 124 | 127 |   
| **set 4** | 213 | 121 | 129 | 118 |   
| **set 5** | 188 | 154 | 141 | 143 |   
| **set 6** | 196 | 157 | 135 | 159 |   
  
Results for **N = 100**:  
|           | naiive [us] | recursive [us] | dfs [us] | merge_elements [us] |  
| ---------- | ---------- | --------- | -------- | ------------ |  
| **set 0** | 4808 | 1002 | 995 | 979 |   
| **set 1** | 3712 | 962 | 964 | 921 |   
| **set 2** | 17025 | 2662 | 2480 | 2468 |   
| **set 3** | 11141 | 2163 | 1739 | 1725 |   
| **set 4** | 10250 | 1609 | 1655 | 1591 |   
| **set 5** |  4304 | 1296 | 1344 | 1361 |   
| **set 6** | 3910 | 1245 | 1280 | 1280 |   
  
Results for **N = 1000**:  
|           | naiive [ms] | recursive [ms] | dfs [ms] | merge_elements [ms] |  
| ---------- | ---------- | --------- | -------- | ------------ |  
| **set 0** | 228 | 10 | 10 | 10 |  
| **set 1** | 149 | 10 | 10 | 10 |  
| **set 2** | 1581 | 33 | 33 | 33 |  
| **set 3** | 1091 | 21 | 21 | 22 |  
| **set 4** | 903 | 21 | 20 | 20 |  
| **set 5** | 390 | 13 | 14 | 13 |  
| **set 6** | 333 | 13 | 13 | 13 |  
  
Results for **N = 10000**:  
|           | naiive [ms] | recursive [ms] | dfs [ms] | merge_elements [ms] |  
| ---------- | ---------- | --------- | -------- | ------------ |  
| **set 0** | 25317 | 115 | 118 | 120 |  
| **set 1** | 23595 | 116 | 123 | 120 |  
| **set 2** | 728205 | 976 | 977 | 976 |  
| **set 3** | 516424 | 494 | 497 | 496 |  
| **set 4** | 262716 | 427 | 429 | 436 |  
| **set 5** | 142511 | 183 | 186 | 195 |  
| **set 6** | 76114 | 173 | 180 | 181 |  
  
As predicted the latter three algorithms are a lot faster than the iterative algorithm, they all have the same performance but the call tree is obviously smaller for non-recursive version. The additional handling of GCs and dynamic geometries seems to be irrelevant.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2022-04-19 20:50:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1103149562  

In case you were wondering what I'm comparing against exactly I'm pasting the implementations of the algorithms:  
  
**naiive**  
```  
mpolygon_t naiive_merge(std::vector<polygon_t> const& polygons)  
{  
    mpolygon_t result;  
    for (auto const& p : polygons)  
    {  
        mpolygon_t m;  
        bg::union_(result, p, m);  
        result = std::move(m);  
    }  
    return result;  
}  
```  
  
**recursive**  
```  
template <typename It>  
mpolygon_t recursive_merge(std::vector<polygon_t> const& polygons, It first, It last)  
{  
    auto s = last - first;  
    mpolygon_t result;  
    if (s == 1)  
    {  
        bg::range::push_back(result, polygons[first->second]);  
    }  
    else if (s == 2)  
    {  
        bg::union_(polygons[first->second], polygons[(first + 1)->second], result);  
    }  
    else if (s > 2)  
    {  
        It mid = first + s / 2;  
        std::nth_element(first, mid, last, [](auto const& l, auto const& r)  
        {  
            return bg::less<>()(l.first, r.first);  
        });  
        mpolygon_t m1 = recursive_merge(polygons, first, mid);  
        mpolygon_t m2 = recursive_merge(polygons, mid, last);  
        bg::union_(m1, m2, result);  
    }  
  
    return result;  
}  
  
mpolygon_t recursive_merge(std::vector<polygon_t> const& polygons)  
{  
    std::vector<std::pair<point_t, size_t>> points;  
    points.reserve(polygons.size());  
    for (size_t i = 0 ; i < polygons.size() ; ++i)  
    {  
        point_t point;  
        if (bg::point_on_border(point, polygons[i]))  
        {  
            points.push_back(std::make_pair(point, i));  
        }  
    }  
  
    return recursive_merge(polygons, points.begin(), points.end());  
}  
```  
  
**dfs**, basically `bg::detail::merge_elements::merge` without `traits::iter_visit` calls  
```  
template <typename It>  
struct merge_polygons_data  
{  
    merge_polygons_data(It first_, It last_)  
        : first(first_), last(last_)  
    {}  
    It first, last;  
    bool merge_results = false;  
};  
  
template <typename MultiPolygon, typename RandomIt, typename Strategy>  
MultiPolygon merge_polygons(RandomIt const first, RandomIt const last, Strategy const& strategy)  
{  
    auto const size = last - first;  
    if (size <= 0)  
    {  
        return MultiPolygon();  
    }  
  
    auto const less = [](auto const& l, auto const& r)  
    {  
        return bg::less<void, -1, typename Strategy::cs_tag>()(l.first, r.first);  
    };  
  
    std::vector<merge_polygons_data<RandomIt>> stack_in;  
    std::vector<MultiPolygon> stack_out;  
    stack_in.reserve(size / 2 + 1);  
    stack_out.reserve(size / 2 + 1);  
  
    stack_in.emplace_back(first, last);  
  
    while (! stack_in.empty())  
    {  
        auto & b = stack_in.back();  
        if (! b.merge_results)  
        {  
            auto const s = b.last - b.first;  
            if (s > 2)  
            {  
                RandomIt const mid = b.first + s / 2;  
                std::nth_element(b.first, mid, b.last, less);  
                RandomIt const fir = b.first;  
                RandomIt const las = b.last;  
                b.merge_results = true;  
                stack_in.emplace_back(fir, mid);  
                stack_in.emplace_back(mid, las);  
            }  
            else if (s == 2)  
            {  
                MultiPolygon result;  
                bg::union_(*(b.first->second), *((b.first + 1)->second), result, strategy);  
                stack_out.push_back(std::move(result));  
                stack_in.pop_back();  
            }  
            else if (s == 1)  
            {  
                MultiPolygon result;  
                bg::range::push_back(result, *(b.first->second));  
                stack_out.push_back(std::move(result));  
                stack_in.pop_back();  
            }  
        }  
        else if (b.merge_results)  
        {  
            MultiPolygon m2 = std::move(stack_out.back());  
            stack_out.pop_back();  
            MultiPolygon m1 = std::move(stack_out.back());  
            stack_out.pop_back();  
            MultiPolygon result;  
            bg::union_(m1, m2, result, strategy);  
            stack_out.push_back(std::move(result));  
            stack_in.pop_back();  
        }  
    }  
  
    return std::move(stack_out.back());  
}  
  
template <typename MultiPolygon, typename Polygons, typename Strategy>  
MultiPolygon merge_polygons(Polygons const& polygons, Strategy const& strategy)  
{  
    using polygon_t = typename boost::range_value<Polygons>::type;  
    using polygon_point_t = typename geometry::point_type<polygon_t>::type;  
    using iterator_t = typename boost::range_const_iterator<Polygons const>::type;  
    using coordinate_t = typename geometry::coordinate_type<polygon_point_t>::type;  
    using cs_t = typename geometry::coordinate_system<polygon_point_t>::type;  
    using point_t = model::point<coordinate_t, 2, cs_t>;  
  
    std::vector<std::pair<point_t, iterator_t>> points;  
    points.reserve(polygons.size());  
    for (auto it = boost::begin(polygons) ; it != boost::end(polygons) ; ++it)  
    {  
        polygon_point_t point;  
        if (bg::point_on_border(point, *it))  
        {  
            coordinate_t const& x = geometry::get<0>(point);  
            coordinate_t const& y = geometry::get<1>(point);  
            // Alternatively use expand() with strategy but then below  
            // we have to take into account cooridnates outside min/max.  
            points.emplace_back(point_t(x, y), it);  
        }  
    }  
  
    return merge_polygons<MultiPolygon>(points.begin(), points.end(), strategy);  
}  
```

---

## Comment 7

> Username: awulkiew  
> Created_at: 2022-05-18 20:48:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1130531250  

@barendgehrels Are you ok with merging this PR?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2022-05-22 07:15:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/987#issuecomment-1133834653  

>   
  
Hi @awulkiew , yes, sorry I was a bit delayed. I'm fine.

---
