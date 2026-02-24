# #718 - Managing memory when R-Tree bulk loading [Closed]

> Username: Caian  
> Created at: 2020-05-26 16:37:02 UTC  
> Updated at: 2020-08-04 22:34:48 UTC  
> Closed at: 2020-08-04 22:34:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/718  

Greetings, I am using R-Tree + shared memory file to store / query a dataset of around 400 million 4D points and I'm relying on bulk loading to favor construction time. But unfortunately I'm projecting that the main memory consumption (currently at 27GB) will be too large for some larger cases.  
  
The consumption above does not happen when calling `insert(begin, end)` from an empty tree, which led me to believe that it is related to the bulk loading process.  
  
If storage is not a limiting factor, is there a way to make the shared memory file larger and use the allocator passed to the constructor for the intermediate results as well?  
  
Thank you,  
  
Caian

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-05-26 17:41:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-634172560  

Hi, yes bulk loading algorithm creates a temporary vector for all elements and currently the standard allocator is used for that so elements are stored in-memory which indeed can cause problems if many elements are in the range:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/pack_create.hpp#L177  
  
So the answer is: currently it is not possible, at least not officially.  
  
A workaround could be to modify your local version of Boost at the line I mentioned above passing e.g. the same allocator that was passed into the R-tree. This would however pollute your shared memory with the temporary objects. So something like this (untested):  
```  
typedef typename boost::container::allocator_traits  
        <  
            typename allocators_typa::allocator_type  
        >::template rebind_alloc<entry_type> temp_allocator_type;  
temp_allocator_type temp_allocator(allocators.allocator());  
std::vector<entry_type, temp_allocator_type> entries(temp_allocator);  
```  
  
I agree that it should be possible to specify how the temporaries are created. It's not clear to me what would be the beast way of doing it. Using the same allocator doesn't seem to be correct approach, plus it could break some existing programs relying on a specific size of shared file, because the memory consumption would be bigger with temporaries stored together with the rtree data.  
  
Another possibilities are e.g.:  
- passing special Boost.Geometry-specific kind of compound_allocator, allocator_set, storage_definition, etc. into the rtree, optionally instead of allocator  
- passing additional allocator for temporary data into the rtree constructor  
  
So I have to think about it and choose what's best. I also have to look at the newer standards because maybe there is a preferred way of solving this problem. If you have some ideas I'm open to suggestion.

---

## Comment 2

> Username: Caian  
> Created at: 2020-05-26 18:36:24 UTC  
> Updated at: 2020-05-26 18:54:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-634202943  

Thank you @awulkiew. I stumbled upon this vector and tried to use the shmem allocator, but I think `std::vector`, but it caused problems with `offset_ptr`.   
  
Changing it to `boost::interprocess::vector` would introduce an explicit dependency of `interprocess` that I don't know if it something desirable.  
  
The idea of using the same allocator is indeed not the best solution, I though of it because it was the most direct one.  
  
I will do some experiments, I though about refactoring `std::vector` as a generic container, but the dependency on the iterator type `InIt` complicates things.  
  
Another alternative that I would like to know your opinion about (because I don't know if this is a C++ idiom) is to carry a generator object capable of instancing a container given an element type:  
  
```  
class pack {  
     
    ...  
  
    struct default_generator  
    {  
        template<typename T> struct generated_type { typedef std::vector<T> type; };  
  
        // TODO move semantics  
        template <typename T>  
        typename generated_type<T>::type generate()  
        {  
            return typename generated_type<T>::type();  
        }  
    };  
  
   // Arbitrary iterators  
    template <typename InIt> inline static  
    node_pointer apply(InIt first, InIt last, size_type & values_count, size_type & leafs_level,  
                       parameters_type const& parameters, Translator const& translator, Allocators & allocators)  
    {  
        // Revert back to std::vector  
        default_generator generator;  
        return apply(first, last, values_count, leafs_level, parameters, translator, allocators, generator);  
    }  
  
    // Arbitrary iterators  
    template <typename InIt, typename Generator> inline static  
    node_pointer apply(InIt first, InIt last, size_type & values_count, size_type & leafs_level,  
                       parameters_type const& parameters, Translator const& translator, Allocators & allocators,  
                       Generator& generator)  
    {  
        typedef typename std::iterator_traits<InIt>::difference_type diff_type;  
  
        diff_type diff = std::distance(first, last);  
        if ( diff <= 0 )  
            return node_pointer(0);  
  
        typedef std::pair<point_type, InIt> entry_type;  
        typedef typename Generator::template generated_type<entry_type>::type entry_container;  
        // TODO move semantics  
        entry_container entries(generator.template generate<entry_type>());  
  
        values_count = static_cast<size_type>(diff);  
  
        ...  
    }  
};  
```  
  
That would be much simpler to carry up.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-05-26 19:40:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-634235962  

> (...) I think std::vector, but it caused problems with `offset_ptr`.  
  
Out of curiosity, what compiler and version are you using? I thought the Interprocess allocators would work out of the box with containers supporting stateful allocators, so C++11 std::vector should be fine, I think.  
  
> Changing it to `boost::interprocess::vector` (...)  
  
Have you tried `boost::container::vector`? This container is used if you pass dynamic parameters into the `rtree`, so e.g. `bgi::dynamic_rstar`. So this is already a dependency and according to our tests it works with Interprocess allocators.  
  
> Another alternative that I would like to know your opinion about (...)  
  
The `default_generator` like that would work in general but there are several problems with it.  
  
The first one is that `entry_type` would have to be exposed for the user somehow, probably accessible from the `rtree` type, because it would depend on the allocator and the pack algorithm. I'd prefer to keep the implementation details hidden.  
  
Another one is that it would be specialized for a type and nothing could be passed into it by the user in place of the call. So the problem is that only one could be used per translation unit / cpp file. It wouldn't be possible to use different rtrees with different allocators for temporaries at the same time. And it would not work well with stateful allocators like the ones in Interprocess since in general they have to be passed into the container because the user has to have control over the creation and destruction of such allocator. You could work around it I guess but it would be inconsistent with the rest of the rtree which supports stateful allocators in general. Here you'd have to hook in a different allocator "from the back" and to make sure it's created at the right moment, the correct way in a specialization far far away. I'd prefer a solution where the allocator is passed by the user into the R-tree. We only have to figure out the correct interface.  
  
For now I'm leaning towards a straightforward solution of adding something like an optional support for tuple of allocators:  
```  
bgi::rtree_allocators<Alloc, TempPackAlloc = [default], TempBalancingAlloc = [default]>  
```  
passed into the rtree.  
  
Side note. I was thinking about optionally passing a storage definition instead of an allocator into the `rtree` anyway. This would be an object at the higher level of abstraction than the allocator allowing e.g. to implement `insert`/`remove` with strong exception guarantees, so the state of the tree would be preserved even after an exception is thrown by the operation.

---

## Comment 4

> Username: Caian  
> Created at: 2020-05-26 21:13:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-634282293  

> > (...) I think std::vector, but it caused problems with `offset_ptr`.  
>   
> Out of curiosity, what compiler and version are you using? I thought the Interprocess allocators would work out of the box with containers supporting stateful allocators, so C++11 std::vector should be fine, I think.  
  
We always compile in C++98 or GNU++98 due to several compatibility constraints, even on modern gcc and icc compilers. The one I tested this is gcc 7.5.0.  
  
> > Changing it to `boost::interprocess::vector` (...)  
>   
> Have you tried `boost::container::vector`? This container is used if you pass dynamic parameters into the `rtree`, so e.g. `bgi::dynamic_rstar`. So this is already a dependency and according to our tests it works with Interprocess allocators.  
  
It seems to work just fine!  
  
> > Another alternative that I would like to know your opinion about (...)  
>   
> The `default_generator` like that would work in general but there are several problems with it.  
>   
> The first one is that `entry_type` would have to be exposed for the user somehow, probably accessible from the `rtree` type, because it would depend on the allocator and the pack algorithm. I'd prefer to keep the implementation details hidden.  
  
The idea was having a nested template in the `default_generator` to overcome these details, but personally I never liked the idea of going back and forth with templates like this.  
  
> Another one is that it would be specialized for a type and nothing could be passed into it by the user in place of the call. So the problem is that only one could be used per translation unit / cpp file. It wouldn't be possible to use different rtrees with different allocators for temporaries at the same time. And it would not work well with stateful allocators like the ones in Interprocess since in general they have to be passed into the container because the user has to have control over the creation and destruction of such allocator. You could work around it I guess but it would be inconsistent with the rest of the rtree which supports stateful allocators in general. Here you'd have to hook in a different allocator "from the back" and to make sure it's created at the right moment, the correct way in a specialization far far away. I'd prefer a solution where the allocator is passed by the user into the R-tree. We only have to figure out the correct interface.  
  
Here is the generator that I plug to my tree after the modification to test a replacement for `std::vector`.  
```  
    template <typename A>  
    struct another_generator  
    {  
        template<typename T> struct generated_type  
        {  
            typedef typename A::template rebind<T>::other allocator_type;  
            typedef boost::container::vector<T, allocator_type> type;  
        };  
  
        const A* _allocator;  
  
        another_generator(  
            const A* allocator  
        ) :  
            _allocator(allocator)  
        {  
        }  
  
        // TODO move semantics  
        template <typename T>  
        typename generated_type<T>::type generate() const  
        {  
            typedef typename generated_type<T>::allocator_type O;  
            O allocator(*_allocator);  
            return typename generated_type<T>::type(allocator);  
        }  
    };  
```  
And then:  
```  
RTree rtree(begin, end, default_generator<ScratchFileAllocator>(&scratch_allocator), params_t(), indexable_t(), equal_to_t(), index_file_allocator);  
```  
Unfortunately I never took the time to fully understand stateful allocators beyond what Boost has taught me by example (including this module, so thank you very much :) ).  
  
> For now I'm leaning towards a straightforward solution of adding something like an optional support for tuple of allocators:  
>   
> ```  
> bgi::rtree_allocators<Alloc, TempPackAlloc = [default], TempBalancingAlloc = [default]>  
> ```  
>   
> passed into the rtree.  
>   
> Side note. I was thinking about optionally passing a storage definition instead of an allocator into the `rtree` anyway. This would be an object at the higher level of abstraction than the allocator allowing e.g. to implement `insert`/`remove` with strong exception guarantees, so the state of the tree would be preserved even after an exception is thrown by the operation.  
  
I think this is the simplest solution given that `boost::container::vector` works.

---

## Comment 5

> Username: Caian  
> Created at: 2020-05-29 12:35:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-635948261  

Just to clarify about the compiler version you asked, because I happened to see the issue about moving to a newer C++ standard. In my field we rely on legacy infrastructure as well as the software developed for it, so system-wide upgrades (packages and OS) are kept at minimum. Even if we have access to "newer" compilers like our ICC 11.1 (and hence the quotes around the word newer) that accept newer C++ standards, we lack OS support for it on the GNU toolchain used.

---

## Comment 6

> Username: Caian  
> Created at: 2020-05-31 13:25:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-636471215  

@awulkiew I did some experimenting with the proposed `bgi::rtree_allocators`, but I'm concerned in storing a temporary allocator that will not be available after the creation of the tree as I think it may lead to some undesirable side effect (e.g. the allocator's destructor trying to access a region that is not mapped anymore), could you clarify on this possibility?  
  
Also there is the question on whether the tree itself should be aware of the storage medium used to store intermediate data.  
  
Finally I can't seem to find a use for `TempBalancingAlloc`. Is it the one used by strategies?  
  
Right now I'm leaning towards passing a generic `PackAlloc` to the tree's contructor and let the `pack` class rebind it to the correct type, as adding a specialized allocator set is requiring extensive modifications to the code.  
  
Ideally I think that the `pack` class could be refactored out as a functor that guarantees optimum order of insertion / initialization, like a fancy `std::copy`.  
  
Cheers,

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-05-31 20:29:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-636525241  

> I did some experimenting with the proposed `bgi::rtree_allocators` (...) could you clarify on this possibility?  
  
I thought about storing it in the `rtree::members_holder` in particular in `detail::rtree::allocators`:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L198  
which is defined here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp#L112  
  
> Finally I can't seem to find a use for `TempBalancingAlloc`.  
  
During balancing process, so when a value is inserted or removed temporary containers can be created for whatever reason to store relatively small numbers of objects (size of a node), e.g.:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp#L364  
Having a separate allocator for this is not needed in general because nodes are small compared to the overall number of values stored in the whole tree but I proposed this for completeness. With this you could e.g. track all memory allocations done by the R-tree.  
  
> Right now I'm leaning towards passing a generic `PackAlloc` to the tree's contructor (...)  
  
Right, for now this allocator would only be used in this constructor anyway and not needed after the R-tree is created. Also for you it should be sufficient to pass it into the constructor, use it and do not store it anywhere. However in general case I can imagine that it may be possible to use it somehow even after the R-tree is constructed. For instance, I thought about building a subtree with pack-creation when a user passes a range of values into `insert()` and then inserting this whole subtree into existing R-tree. In such case this allocator could be used again for temporary data.  
  
> Ideally I think that the pack class could be refactored out as a functor that guarantees optimum order of insertion / initialization, like a fancy `std::copy`.  
  
Could you elaborate on that idea?

---

## Comment 8

> Username: Caian  
> Created at: 2020-06-01 20:52:46 UTC  
> Updated at: 2020-06-01 20:54:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/718#issuecomment-637095638  

> > I did some experimenting with the proposed `bgi::rtree_allocators` (...) could you clarify on this possibility?  
>   
> I thought about storing it in the `rtree::members_holder` in particular in `detail::rtree::allocators`:  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L198  
> which is defined here:  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp#L112  
>   
> > Finally I can't seem to find a use for `TempBalancingAlloc`.  
>   
> During balancing process, so when a value is inserted or removed temporary containers can be created for whatever reason to store relatively small numbers of objects (size of a node), e.g.:  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp#L364  
> Having a separate allocator for this is not needed in general because nodes are small compared to the overall number of values stored in the whole tree but I proposed this for completeness. With this you could e.g. track all memory allocations done by the R-tree.  
>   
> > Right now I'm leaning towards passing a generic `PackAlloc` to the tree's contructor (...)  
>   
> Right, for now this allocator would only be used in this constructor anyway and not needed after the R-tree is created. Also for you it should be sufficient to pass it into the constructor, use it and do not store it anywhere. However in general case I can imagine that it may be possible to use it somehow even after the R-tree is constructed. For instance, I thought about building a subtree with pack-creation when a user passes a range of values into `insert()` and then inserting this whole subtree into existing R-tree. In such case this allocator could be used again for temporary data.  
  
I agree with the consistency, but as stated before I'm worried that it may not be possible to carry certain types of allocators inside the tree. For instance, if a second shmem allocator is used for packing and balancing, I don't see how one could re-initialize the program in order to match the offset of the temporary memory region with respect to the memory region mapped for the tree. And even if this works, decision is carried as long as the tree "lives". Every instance of the program (or programs) will have to `.insert()` using the same allocation scheme, whether this makes sense or not for the hardware being used. As a disclaimer, I'm being biased by my work field, where heterogeneous infrastructures are common.  
  
> > Ideally I think that the pack class could be refactored out as a functor that guarantees optimum order of insertion / initialization, like a fancy `std::copy`.  
>   
> Could you elaborate on that idea?  
Having to pass 2 or 3 additional allocators, for me, in an indication that the responsibility of doing such tasks may not belong to the tree.  
  
Instead the ideia is to build functions (or functors) that talk to the tree through a clean interface (that is already being done internally). So instead of having additional allocators, those would belong to pack and bulk insertions functions and there would be a cleaner distinction of responsibilities for those allocators.  
  
For instance:   
```  
bgi::packed_insert(iti, ito, tree, OPTIONAL(my_allocator)); // Can either construct the tree packed (if empty) or insert a branch with the best internal structure  
```  
  
Perhaps the responsibility of maintaining the state of the tree in case of exceptions (as you mentioned way above) would belong to these functions.
