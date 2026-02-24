# #39 - boost::mpl::sequence_tag conflicts with mp11's one [Closed]

> Username: redboltz  
> Created at: 2019-08-23 05:56:09 UTC  
> Updated at: 2019-12-04 23:49:24 UTC  
> Closed at: 2019-12-04 23:49:23 UTC  
> Url: https://github.com/boostorg/mp11/issues/39  

It seems that  
https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/adapted/std_tuple/tag_of.hpp#L32  
conflicts with  
https://github.com/boostorg/mp11/blob/develop/include/boost/mp11/mpl.hpp#L148  
  
Running demo:  
https://wandbox.org/permlink/BoAQGjmPMWTW0Uwj  
  
See https://github.com/boostorg/fusion/issues/182#issue-332253287  
  
I'm not sure which library should define `sequence_tag`. Or some it should be defined in the common header?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-04 23:49:23 UTC  
> Url: https://github.com/boostorg/mp11/issues/39#issuecomment-561897126  

In https://github.com/boostorg/mp11/commit/164aa375f2463ef3d3bf717471b872ababf947f4, I've split `mpl.hpp` into `mpl_list.hpp` and `mpl_tuple.hpp`, so in case of a conflict with Fusion libraries can now include just `mpl_list.hpp` and leave `std::tuple` to Fusion.
