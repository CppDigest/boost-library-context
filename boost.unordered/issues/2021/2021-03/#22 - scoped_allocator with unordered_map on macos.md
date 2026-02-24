# #22 - scoped_allocator with unordered_map on macos [Closed]

> Username: kleunen  
> Created at: 2021-03-20 07:22:30 UTC  
> Updated at: 2021-12-23 06:06:06 UTC  
> Closed at: 2021-12-23 06:06:06 UTC  
> Url: https://github.com/boostorg/unordered/issues/22  

I have a user-defined type which is allocator aware:  
https://github.com/kleunen/tilemaker/blob/win_ci_build/include/osm_store.h#L108-L122  
  
This user-defined type I would like to store in an unordered_map, which is allocated in an mmap file using boost interprocess:  
https://github.com/kleunen/tilemaker/blob/win_ci_build/include/osm_store.h#L331-L337  
  
Finally I use emplace to store an entry in this map:  
https://github.com/kleunen/tilemaker/blob/win_ci_build/include/osm_store.h#L375-L380  
  
On MSVC and linux/g++ this works fine. However, if I compile on MacOS, with g++ and probably libc++ as STL, the unordered_map construct seems to default initialize the VarIntVector and not pass the scoped allocator. Which fails, because the interprocess allocator is not default constructable, it needs a reference to a segment manager.   
  
  
````  
2021-03-20T07:14:56.5388750Z /usr/local/share/vcpkg/installed/x64-osx/include/boost/container/scoped_allocator.hpp:443:32: error: constructor for 'boost::container::dtl::scoped_allocator_adaptor_base<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64>>' must explicitly initialize the base class 'boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64>' which does not have a default constructor  
2021-03-20T07:14:56.5391710Z    BOOST_CONTAINER_FORCEINLINE scoped_allocator_adaptor_base()  
2021-03-20T07:14:56.5392270Z                                ^  
2021-03-20T07:14:56.5395420Z /usr/local/share/vcpkg/installed/x64-osx/include/boost/container/scoped_allocator.hpp:644:32: note: in instantiation of member function 'boost::container::dtl::scoped_allocator_adaptor_base<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64>>::scoped_allocator_adaptor_base' requested here  
2021-03-20T07:14:56.5397550Z    BOOST_CONTAINER_FORCEINLINE scoped_allocator_adaptor()  
2021-03-20T07:14:56.5398080Z                                ^  
2021-03-20T07:14:56.5400950Z /Users/runner/work/tilemaker/tilemaker/include/osm_store.h:120:25: note: in instantiation of member function 'boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >::scoped_allocator_adaptor' requested here  
2021-03-20T07:14:56.5402840Z         VarIntVector(A alloc = A())  
2021-03-20T07:14:56.5403260Z                                ^  
2021-03-20T07:14:56.5413170Z /usr/local/share/vcpkg/installed/x64-osx/include/boost/unordered/detail/implementation.hpp:2040:11: note: in instantiation of function template specialization 'boost::unordered::detail::func::construct_from_args<boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<boost::unordered::detail::node<boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >, std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >, std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > >, const std::__1::piecewise_construct_t &, std::__1::tuple<unsigned long long &>, std::__1::tuple<> >' requested here  
2021-03-20T07:14:56.5420390Z           construct_from_args(  
2021-03-20T07:14:56.5420750Z           ^  
2021-03-20T07:14:56.5428320Z /usr/local/share/vcpkg/installed/x64-osx/include/boost/unordered/detail/implementation.hpp:3774:54: note: in instantiation of function template specialization 'boost::unordered::detail::func::construct_node_from_args<boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<boost::unordered::detail::node<boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >, std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >, const std::__1::piecewise_construct_t &, std::__1::tuple<unsigned long long &>, std::__1::tuple<> >' requested here  
2021-03-20T07:14:56.5434010Z           node_tmp b(boost::unordered::detail::func::construct_node_from_args(  
2021-03-20T07:14:56.5434560Z                                                      ^  
2021-03-20T07:14:56.5441480Z /usr/local/share/vcpkg/installed/x64-osx/include/boost/unordered/unordered_map.hpp:226:23: note: in instantiation of function template specialization 'boost::unordered::detail::table<boost::unordered::detail::map<boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> >, const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > >, std::__1::hash<unsigned long long>, std::__1::equal_to<unsigned long long> > >::emplace_unique<const std::__1::piecewise_construct_t &, std::__1::tuple<unsigned long long &>, std::__1::tuple<> >' requested here  
2021-03-20T07:14:56.5446600Z         return table_.emplace_unique(  
2021-03-20T07:14:56.5447020Z                       ^  
2021-03-20T07:14:56.5453500Z /Users/runner/work/tilemaker/tilemaker/include/osm_store.h:377:30: note: in instantiation of function template specialization 'boost::unordered::unordered_map<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > >, std::__1::hash<unsigned long long>, std::__1::equal_to<unsigned long long>, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<std::__1::pair<const unsigned long long, VarIntVector<unsigned long long, boost::container::scoped_allocator_adaptor<boost::interprocess::node_allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0>, 0>, iset_index>, 64> > >::emplace<const std::__1::piecewise_construct_t &, std::__1::tuple<unsigned long long &>, std::__1::tuple<> >' requested here  
````  
  
https://pipelines.actions.githubusercontent.com/ya42YO45OlA9LAnHTfxsjT6uj2KbvrHrStfya2AgUYklRTQFd7/_apis/pipelines/1/runs/255/signedlogcontent/8?urlExpires=2021-03-20T07%3A21%3A48.5176130Z&urlSigningMethod=HMACV1&urlSignature=mLfAstpJX4HmQ9g2S%2FnGVYNFIkD9E6hB399%2B2xhuwDs%3D  
https://github.com/kleunen/tilemaker/runs/2154401042?check_suite_focus=true

---

## Comment 1

> Username: kleunen  
> Created at: 2021-03-20 07:59:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-803268639  

I think I figured it out myself now, it seems unordered_map does not detect by itself it can use allocator traits and cxx11 construction with libc++. I had to add these defines:  
  
````  
#define BOOST_UNORDERED_USE_ALLOCATOR_TRAITS 1  
#define BOOST_UNORDERED_CXX11_CONSTRUCTION 1  
#include <boost/unordered_map.hpp>  
````  
  
I think it would be good to add detection of llvm libc++ to unordered_map, now that it is used more often

---

## Comment 2

> Username: cmazakas  
> Created at: 2021-09-17 18:19:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-921992401  

Hi kleuen!  
  
Unfortunately, I tried recreating your issue but everything compiles:  
https://godbolt.org/z/hePrb5YGq  
  
It's not a perfect recreation of your exact setup but should be similar enough so as to trigger an error.  
  
Could I possibly get some more information about your setup?  
  
My question is: what compiler are you using? I'm guessing you're using Apple Clang which unfortunately I can't easily reproduce.  
  
I'm also wondering, what C++ version? If I have more information around your compiler version and the C++ standard, I might have better luck recreating the issue.

---

## Comment 3

> Username: kleunen  
> Created at: 2021-09-17 18:24:32 UTC  
> Updated at: 2021-09-17 18:50:21 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-921995290  

The issue occured on the Github CI setup, so the virtual environment of Github CI with macos was used.   
  
I am not sure what version that was, possibly this is already been updated.

---

## Comment 4

> Username: cmazakas  
> Created at: 2021-09-17 18:52:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922010742  

Would you be able to provide a smaller, minimal example that causes the failure you experienced?

---

## Comment 5

> Username: kleunen  
> Created at: 2021-09-17 18:56:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922012909  

I have to think about that. Actually, i am not using the scoped_allocator anymore. It was easier to use a stateless allocator, so I made a workaround with a global allocator.   
  
The example you showed was similar to the code I was using.

---

## Comment 6

> Username: cmazakas  
> Created at: 2021-09-17 19:06:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922018442  

Ha ha, no worries.  
  
You made me realize, Unordered actually doesn't have a test case like what you mentioned. So I'm going to work on adding it to the CI test matrix and then hopefully, we'll be able to trigger a failure.

---

## Comment 7

> Username: kleunen  
> Created at: 2021-09-17 19:12:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922021207  

If i have time, i will have a look if i can break it. I have some old test code lying around.

---

## Comment 8

> Username: kleunen  
> Created at: 2021-09-17 19:26:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922029744  

This was a code sample i made:   
https://godbolt.org/z/9snoYPM8q  
  
On godbolt it seems to compile fine, so maybe really related to macos ?

---

## Comment 9

> Username: cmazakas  
> Created at: 2021-09-17 19:33:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922033606  

Yeah, I'm guessing it's probably some weirdness surrounding Apple Clang specifically. Maybe just not a robust enough check on the side of Unordered.  
  
Thank you for the godbolt link!

---

## Comment 10

> Username: kleunen  
> Created at: 2021-09-17 20:00:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-922047846  

My guess was that libc++ on macos was missing certain defines.

---

## Comment 11

> Username: cmazakas  
> Created at: 2021-09-26 17:59:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-927345522  

Hi @kleunen, I have a PR up: https://github.com/boostorg/unordered/pull/31  
  
Would you mind taking a look and seeing if this mirrors your use-case sufficiently simplified?  
  
The Github Actions test matrix _does_ include OS X and unfortunately, a compiler error doesn't seem to have been triggered.

---

## Comment 12

> Username: kleunen  
> Created at: 2021-09-26 18:02:20 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-927345877  

I will have a look.   
  
Yes, I think the macos virtual environment is probably upgraded now.   
Also ubuntu 16.04 was removed recently, so.

---

## Comment 13

> Username: kleunen  
> Created at: 2021-09-26 18:07:42 UTC  
> Updated at: 2021-09-26 18:10:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-927346774  

The PR looks good. You are checking now the stateful scoped allocator is passed to the underlying vector inside the map. So, at least you have a test for this now, to make sure the C++ environment is handling this correctly.

---

## Comment 14

> Username: cmazakas  
> Created at: 2021-09-27 14:41:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-927941910  

Awesome, then I'll work on fixing the msvc compilation failures (so hey, at least we found one failure in the test matrix) and then we'll get this bad boy merged!

---

## Comment 15

> Username: cmazakas  
> Created at: 2021-12-22 23:25:24 UTC  
> Url: https://github.com/boostorg/unordered/issues/22#issuecomment-999938375  

@kleunen Can I go ahead and close this issue or is there anything else you'd like to add/ask about?
