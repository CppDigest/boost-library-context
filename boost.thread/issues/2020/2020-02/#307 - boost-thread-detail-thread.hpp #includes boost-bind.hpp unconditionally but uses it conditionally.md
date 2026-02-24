# #307 - boost/thread/detail/thread.hpp #includes boost/bind.hpp unconditionally but uses it conditionally [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2020-02-10 17:42:22 UTC  
> Updated at: 2026-02-06 11:04:42 UTC  
> Closed at: 2026-02-06 11:04:42 UTC  
> Url: https://github.com/boostorg/thread/issues/307  

I can see boost::bind() used only if BOOST_THREAD_PROVIDES_VARIADIC_THREAD is *not* defined  
  
Could the #include <boost/bind.hpp> be moved inside a #ifndef BOOST_THREAD_PROVIDES_VARIADIC_THREAD section? In fact I'd move it inside an #else section under line 50  
  
Boost.Bind brings in a lot of cruft, that is, increased compile times and funny warnings `boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled`  
  
Happy to provide a PR
