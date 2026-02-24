# #144 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:59:20 UTC  
> Updated at: 2020-05-24 23:00:35 UTC  
> Closed at: 2020-05-24 23:00:35 UTC  
> Url: https://github.com/boostorg/container/issues/144  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/container/pmr/monotonic_buffer_resource.hpp:163:18: warning: ‘virtual void* boost::container::pmr::monotonic_buffer_resource::do_allocate(std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/monotonic_buffer_resource.hpp:170:17: warning: ‘virtual void boost::container::pmr::monotonic_buffer_resource::do_deallocate(void*, std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/monotonic_buffer_resource.hpp:174:17: warning: ‘virtual bool boost::container::pmr::monotonic_buffer_resource::do_is_equal(const boost::container::pmr::memory_resource&) const’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/synchronized_pool_resource.hpp:106:18: warning: ‘virtual void* boost::container::pmr::synchronized_pool_resource::do_allocate(std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/synchronized_pool_resource.hpp:109:17: warning: ‘virtual void boost::container::pmr::synchronized_pool_resource::do_deallocate(void*, std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/synchronized_pool_resource.hpp:112:17: warning: ‘virtual bool boost::container::pmr::synchronized_pool_resource::do_is_equal(const boost::container::pmr::memory_resource&) const’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/unsynchronized_pool_resource.hpp:137:18: warning: ‘virtual void* boost::container::pmr::unsynchronized_pool_resource::do_allocate(std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/unsynchronized_pool_resource.hpp:144:17: warning: ‘virtual void boost::container::pmr::unsynchronized_pool_resource::do_deallocate(void*, std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
./boost/container/pmr/unsynchronized_pool_resource.hpp:148:17: warning: ‘virtual bool boost::container::pmr::unsynchronized_pool_resource::do_is_equal(const boost::container::pmr::memory_resource&) const’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:33:18: warning: ‘virtual void* boost::container::pmr::new_delete_resource_imp::do_allocate(std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:36:17: warning: ‘virtual void boost::container::pmr::new_delete_resource_imp::do_deallocate(void*, std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:39:17: warning: ‘virtual bool boost::container::pmr::new_delete_resource_imp::do_is_equal(const boost::container::pmr::memory_resource&) const’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:51:18: warning: ‘virtual void* boost::container::pmr::null_memory_resource_imp::do_allocate(std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:58:17: warning: ‘virtual void boost::container::pmr::null_memory_resource_imp::do_deallocate(void*, std::size_t, std::size_t)’ can be marked override [-Wsuggest-override]  
libs/container/src/global_resource.cpp:61:17: warning: ‘virtual bool boost::container::pmr::null_memory_resource_imp::do_is_equal(const boost::container::pmr::memory_resource&) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-08 16:33:01 UTC  
> Url: https://github.com/boostorg/container/issues/144#issuecomment-625900312  

See fix in #147.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-05-24 23:00:35 UTC  
> Url: https://github.com/boostorg/container/issues/144#issuecomment-633313128  

Closing as #147 was applied
