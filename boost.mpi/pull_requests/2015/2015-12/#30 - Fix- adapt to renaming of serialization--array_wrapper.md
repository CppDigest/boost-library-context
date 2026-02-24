# #30 Fix: adapt to renaming of serialization::array_wrapper [Merged]

> Username: jhunold  
> Created at: 2015-12-07 06:45:16 UTC  
> Updated at: 2016-04-25 19:16:41 UTC  
> Merged at: 2016-04-24 19:59:36 UTC  
> Closed at: 2016-04-24 19:59:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/30  

Commit e3b67eb299b070edb32c2eb5da76653b4ed9cf5e in Boost.Serialization  
renames serialization::array to serialization::array_wrapper.

---

## Comment 1

> Username: vprus  
> Created_at: 2016-04-25 07:17:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/30#discussion_r60870196  

Missing 'r'? Is this code exercised by any tests?

---

## Comment 2

> Username: aminiussi  
> Created_at: 2016-04-25 07:41:47 UTC  
> Url: https://github.com/boostorg/mpi/pull/30#discussion_r60872309  

On 25/04/2016 09:17, Vladimir Prus wrote:  
  
> In include/boost/mpi/detail/forward_iprimitive.hpp   
> https://github.com/boostorg/mpi/pull/30#discussion_r60870196:  
>   
> > @@ -42,7 +42,7 @@ class forward_iprimitive  
> >   
> > ```  
> >  /// loading of arrays is forwarded to the implementation archive  
> >  template<class T>  
> > ```  
> > -    void load_array(serialization::array<T> & x, unsigned int file_version )  
> > -    void load_array(serialization::array_wrappe<T> & x, unsigned int file_version )  
>   
> Missing 'r'? Is this code exercised by any tests?  
  
Does not ring any bell. Wasn't even aware serialization arrays were   
renamed or handled.  
  
Alain

---

## Comment 3

> Username: jhunold  
> Created_at: 2016-04-25 09:29:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/30#discussion_r60885095  

The code is obviously not exercised. The tests are all green if the patch is applied. So I guess test is needed then.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2016-04-25 19:16:41 UTC  
> Url: https://github.com/boostorg/mpi/pull/30#issuecomment-214486944  

Applied patch e4be4411c8 to fix this as it could impact users.  I'll add a ticket to add a test to exercise this code.

---
