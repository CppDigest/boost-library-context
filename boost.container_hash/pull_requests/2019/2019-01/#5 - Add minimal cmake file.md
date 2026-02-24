# #5 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-02 22:06:28 UTC  
> Updated at: 2019-05-01 08:01:13 UTC  
> Merged at: 2019-01-23 17:23:08 UTC  
> Closed at: 2019-01-23 17:23:08 UTC  
> Url: https://github.com/boostorg/container_hash/pull/5  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
    add_subdirectory( <path-to-boost_container_hash> )  
    target_link_libraries( <my_lib> PUBLIC Boost::container_hash )  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.

---
