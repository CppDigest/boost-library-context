# #15 Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-12-30 14:58:51 UTC  
> Updated at: 2019-01-02 22:11:00 UTC  
> Merged at: 2018-12-30 18:25:40 UTC  
> Closed at: 2018-12-30 18:25:40 UTC  
> Url: https://github.com/boostorg/typeof/pull/15  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
 add_subdirectory( <path-to-boost_move> )  
 target_link_library( <my_lib> PUBLIC Boost::move )  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.

---
