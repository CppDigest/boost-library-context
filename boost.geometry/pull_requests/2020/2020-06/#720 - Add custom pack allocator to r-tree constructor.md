# #720 Add custom pack allocator to r-tree constructor [Merged]

> Username: Caian  
> Created at: 2020-06-02 12:48:49 UTC  
> Updated at: 2020-06-08 16:33:42 UTC  
> Merged at: 2020-06-08 16:33:42 UTC  
> Closed at: 2020-06-08 16:33:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/720  

As discussed in #718, it's not currently possible to specify an allocator for the packing process used during the construction of the r-rtee. This is desirable in some situations when dealing with constrained memory systems.  
  
This PR adds overloads for both `bgi::detail::index::rtree::pack` and `bgi::rtree` classes to support the aforementioned allocator without interfering with the existing interfaces.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-06-02 14:56:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-637597119  

Thanks for this PR!  
  
There is one thing I'd like to discuss though, i.e. the number and the order of constructor's arguments.  
  
You placed the temporary allocator just after the range but the tree allocator at the end. I wonder why? My guess would be that if the user wants to pass a custom allocator then he'd either pass the TreeAllocator only and default-construct the TemporaryAllocator or pass both of them. Does your use case require to pass only the TemporaryAllocator but to use the standard allocator as the TreeAllocator?  
  
Back to the subject. I think that the TemporaryAllocator should not be passed right after the range. It should rather be passed at the end. But I know this may be inconvenient, so we could do something different, i.e. mimic the C++14 interface of `std::set` where it is possible to pass only the `Allocator` and `Compare` is default-constructed (see: https://en.cppreference.com/w/cpp/container/set/set). In the case of the R-tree all three `Parameters`, `IndexableGetter` and `EqualTo` could be ommitted and default-constructed, allowing to pass the Allocator and optionally TempAllocator. So:  
  
Right now these ctors are defined (simplified code):  
```  
rtree(Par = Par(), InGet = InGet(), EqTo = EqTo())  
rtree(Par, InGet, EqTo, All)  
rtree(It, It, Par = Par(), InGet= InGet(), EqTo = EqTo(), All = All())  
rtree(Rng, Par = Par(), InGet= InGet(), EqTo = EqTo(), All = All())  
```  
  
So I propose to add these:  
```  
rtree(It, It, Par, InGet, EqTo, All, TempAll)  
rtree(Rng, Par, InGet, EqTo, All, TempAll)  
rtree(It, It, All)  
rtree(Rng, All)  
rtree(It, It, All, TempAll)  
rtree(Rng, All, TempAll)  
```  
  
What do you think?

---

## Comment 2

> Username: Caian  
> Created_at: 2020-06-02 18:50:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-637740504  

Thank you for the feedback @awulkiew,  
  
> Thanks for this PR!  
>   
> There is one thing I'd like to discuss though, i.e. the number and the order of constructor's arguments.  
>   
> You placed the temporary allocator just after the range but the tree allocator at the end. I wonder why? My guess would be that if the user wants to pass a custom allocator then he'd either pass the TreeAllocator only and default-construct the TemporaryAllocator or pass both of them. Does your use case require to pass only the TemporaryAllocator but to use the standard allocator as the TreeAllocator?  
  
You are right, I either specify both allocators or just the tree allocator.   
  
I tried to keep the interface as closest as possible to the original overload without reordering arguments. I would put the temporary allocator after the tree allocator for consistency, but C++ would not allow template type inference from the default value of `pack_allocator`.  
  
> Back to the subject. I think that the TemporaryAllocator should not be passed right after the range. It should rather be passed at the end. But I know this may be inconvenient, so we could do something different, i.e. mimic the C++14 interface of `std::set` where it is possible to pass only the `Allocator` and `Compare` is default-constructed (see: https://en.cppreference.com/w/cpp/container/set/set). In the case of the R-tree all three `Parameters`, `IndexableGetter` and `EqualTo` could be ommitted and default-constructed, allowing to pass the Allocator and optionally TempAllocator. So:  
>   
> Right now these ctors are defined (simplified code):  
>   
> ```  
> rtree(Par = Par(), InGet = InGet(), EqTo = EqTo())  
> rtree(Par, InGet, EqTo, All)  
> rtree(It, It, Par = Par(), InGet= InGet(), EqTo = EqTo(), All = All())  
> rtree(Rng, Par = Par(), InGet= InGet(), EqTo = EqTo(), All = All())  
> ```  
>   
> So I propose to add these:  
>   
> ```  
> rtree(It, It, Par, InGet, EqTo, All, TempAll)  
> rtree(Rng, Par, InGet, EqTo, All, TempAll)  
> rtree(It, It, All)  
> rtree(Rng, All)  
> rtree(It, It, All, TempAll)  
> rtree(Rng, All, TempAll)  
> ```  
>   
> What do you think?  
  
Sound good, no arguments reordered.

---

## Comment 3

> Username: Caian  
> Created_at: 2020-06-04 12:56:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-638829623  

Constructors modified. Here's a proposal for testing:  
  
```c++  
// Adapted from:  
// https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/spatial_indexes/rtree_examples/index_stored_in_shared_memory_using_boost_interprocess.html  
//  
// TODO other authors?  
// Copyright (c) 2011-2014 Adam Wulkiewicz.  
// Copyright (c) 2020 Caian Benedicto, Campinas, Brazil.  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/geometries/box.hpp>  
  
#include <boost/geometry/index/rtree.hpp>  
  
#include <boost/foreach.hpp>  
  
#include <boost/interprocess/managed_mapped_file.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
  
#include <vector>  
#include <string>  
  
typedef boost::geometry::model::point<float, 2, boost::geometry::cs::cartesian> P;  
typedef boost::geometry::model::box<P> B;  
  
template <class Tree>  
void query_test(const Tree* tree)  
{  
    namespace bg = boost::geometry;  
    namespace bgi = bg::index;  
  
    std::vector<B> expected;  
    expected.push_back(B(P(45, 45), P(45.5, 45.5)));  
    expected.push_back(B(P(46, 46), P(46.5, 46.5)));  
    expected.push_back(B(P(47, 47), P(47.5, 47.5)));  
    expected.push_back(B(P(48, 48), P(48.5, 48.5)));  
    expected.push_back(B(P(49, 49), P(49.5, 49.5)));  
    expected.push_back(B(P(50, 50), P(50.5, 50.5)));  
    expected.push_back(B(P(51, 51), P(51.5, 51.5)));  
    expected.push_back(B(P(52, 52), P(52.5, 52.5)));  
    expected.push_back(B(P(53, 53), P(53.5, 53.5)));  
    expected.push_back(B(P(54, 54), P(54.5, 54.5)));  
    expected.push_back(B(P(55, 55), P(55.5, 55.5)));  
  
    std::vector<B> result; // Yes, could have been done with iterators...  
    unsigned k = tree->query(bgi::intersects(B(P(45, 45), P(55, 55))),  
        std::back_inserter(result));  
  
    // TODO ASSERT  
    if (k != 11) throw 0;  
  
    BOOST_FOREACH(B const& b, result)  
    {  
        // TODO ASSERT  
        // TODO box comparison?  
  
        bool found = false;  
  
        BOOST_FOREACH(B const& e, expected)  
        {  
            if (bg::equals(b, e)) { found = true; break; }  
        }  
  
        if (!found) throw 0;  
    }  
}  
  
template <class Par, class I, class E>  
void test_tree()  
{  
    using namespace boost::interprocess;  
  
    namespace bg = boost::geometry;  
    namespace bgi = bg::index;  
  
    typedef allocator<B, managed_mapped_file::segment_manager> Alloc;  
    typedef bgi::rtree<B, Par, I, E, Alloc> Rtree;  
  
    const char* tree_filename = "RRreeFile";  
    const size_t tree_filesize = 10*1024*1024; // 10MB should be more than enough  
  
    const char* pack_filename = "CacheFile";  
    const size_t pack_filesize = 10*1024*1024; // 10MB should be more than enough  
  
    // Create the tree  
  
    {  
        file_mapping::remove(tree_filename);  
        managed_mapped_file tree_segment(create_only, tree_filename, tree_filesize);  
  
        file_mapping::remove(pack_filename);  
        managed_mapped_file pack_segment(create_only, pack_filename, pack_filesize);  
  
        std::vector<B> objects;  
  
        for ( float i = 0 ; i < 100 ; i += 1 )  
            objects.push_back(B(P(i, i), P(i+0.5f, i+0.5f)));  
  
        Rtree* tree;  
  
        // rtree(It, It, Par, InGet, EqTo, All, TempAll)  
  
        tree = tree_segment.construct<Rtree>("Rtree1")(  
            objects.begin(), objects.end(),  
            Par(), I(), E(),  
            Alloc(tree_segment.get_segment_manager()),  
            Alloc(pack_segment.get_segment_manager()));  
  
        query_test(tree);  
  
        // rtree(Rng, Par, InGet, EqTo, All, TempAll)  
  
        tree = tree_segment.construct<Rtree>("Rtree2")(  
            objects,  
            Par(), I(), E(),  
            Alloc(tree_segment.get_segment_manager()),  
            Alloc(pack_segment.get_segment_manager()));  
  
        query_test(tree);  
  
        // rtree(It, It, All)  
  
        tree = tree_segment.construct<Rtree>("Rtree3")(  
            objects.begin(), objects.end(),  
            Alloc(tree_segment.get_segment_manager()));  
  
        query_test(tree);  
  
        // rtree(Rng, All)  
  
        tree = tree_segment.construct<Rtree>("Rtree4")(  
            objects,  
            Alloc(tree_segment.get_segment_manager()));  
  
        query_test(tree);  
  
        // rtree(It, It, All, TempAll)  
  
        tree = tree_segment.construct<Rtree>("Rtree5")(  
            objects.begin(), objects.end(),  
            Alloc(tree_segment.get_segment_manager()),  
            Alloc(pack_segment.get_segment_manager()));  
  
        query_test(tree);  
  
        // rtree(Rng, All, TempAll)  
  
        tree = tree_segment.construct<Rtree>("Rtree6")(  
            objects,  
            Alloc(tree_segment.get_segment_manager()),  
            Alloc(pack_segment.get_segment_manager()));  
  
        query_test(tree);  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree1").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree1");  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree2").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree2");  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree3").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree3");  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree4").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree4");  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree5").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree5");  
    }  
  
    {  
        managed_mapped_file segment(open_only, tree_filename);  
        Rtree* tree = segment.find<Rtree>("Rtree6").first;  
        query_test(tree);  
        segment.destroy<Rtree>("Rtree6");  
    }  
  
    file_mapping::remove(tree_filename);  
    file_mapping::remove(pack_filename);  
};  
  
int main(int argc, char *argv[])  
{  
    namespace bg = boost::geometry;  
    namespace bgi = bg::index;  
  
    test_tree<  
        bgi::linear<32, 8>,  
        bgi::indexable<B>,  
        bgi::equal_to<B>  
    >();  
  
    return 0;  
}  
```

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-06-06 01:30:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-639943204  

Thanks! I'll review it tomorrow.  
  
Regarding the testing. Here are the tests for Rtree+interprocess:  
https://github.com/boostorg/geometry/tree/develop/index/test/rtree/interprocess  
These constructors could either be added there somewhere or a separate file could be created. The latter is probably simpler. Have in mind we use Boost.Test.

---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2020-06-07 19:02:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/720#pullrequestreview-425845039  

📁 include/boost/geometry/index/rtree.hpp

```diff
 441 |+     \param equal             The function object comparing Values.
 442 |+     \param tree_allocator    The allocator object for persistent data in the tree..
 443 |+     \param pack_allocator    The temporary allocator object used when packing
```

> Username: awulkiew  
> Created_at: 2020-06-07 19:02:46 UTC  
> Updated_at: 2020-06-08 12:54:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#discussion_r436392070  

I think for consistency with other constructors I'd prefer keeping the old name `allocator` instead of `tree_allocator`. I think it's clear enough.  
  
What do you think about changing the name of `pack_allocator` to `temp_allocator`? I think it would be more clear what is happening there, i.e. it's an allocator used in construction of temporary data.

> Username: Caian  
> Created_at: 2020-06-08 00:22:36 UTC  
> Updated_at: 2020-06-08 12:54:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#discussion_r436416083  

I agree.


---

## Comment 6

> Username: awulkiew  
> Created_at: 2020-06-07 19:05:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640264501  

Looks good. I only had one remark regarding naming.  
Btw, if you like please add yourself to the list of contributors: https://github.com/boostorg/geometry/blob/develop/doc/geometry.qbk#L109

---

## Comment 7

> Username: Caian  
> Created_at: 2020-06-08 00:38:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640305298  

> Looks good. I only had one remark regarding naming.  
> Btw, if you like please add yourself to the list of contributors: https://github.com/boostorg/geometry/blob/develop/doc/geometry.qbk#L109  
  
I'll be glad. Should I add copyrights to lines 8 and 17 as well?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-06-08 12:01:31 UTC  
> Updated_at: 2020-06-08 12:02:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640560007  

> Should I add copyrights to lines 8 and 17 as well?  
  
No, AFAIU the authors section is reserved for maintainers. Regarding the line 8, that would technically be needed. So to avoid further confusion I'll do it after the PR is merged.

---

## Comment 9

> Username: Caian  
> Created_at: 2020-06-08 12:52:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640583818  

Updated

---

## Comment 10

> Username: awulkiew  
> Created_at: 2020-06-08 15:52:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640715481  

Would you like me to merge this now or do you plan to add a test file for this PR?

---

## Comment 11

> Username: Caian  
> Created_at: 2020-06-08 15:58:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640719184  

I prefer to add it to a next PR.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2020-06-08 16:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/720#issuecomment-640737570  

Ok, thanks!

---
