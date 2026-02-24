# #406 - Cray compile error with version /opt/cray/nvidia/319.37-1_1.0402.1285.1.2.gem [Closed]

> Username: Belcourt  
> Created at: 2015-01-02 22:20:41 UTC  
> Updated at: 2017-04-25 15:04:59 UTC  
> Closed at: 2017-04-25 15:04:59 UTC  
> Url: https://github.com/boostorg/compute/issues/406  

On Tue, Dec 30, 2014 at 4:15 PM, Belcourt, Kenneth kbelco@sandia.gov wrote:  
I ran into a compilation problem on another Cray system:  
  
"boost/include/boost/compute/type_traits/type_name.hpp", line 79: error:  
         class  
         "boost::compute::detail::type_name_traitboost::compute::char_" has  
         already been defined  
 BOOST_COMPUTE_DEFINE_BUILTIN_TYPE_NAME_FUNCTION(char)  
 ^  
  
Commenting out line 79 got me past the error.  
  
Interesting. My best guess is that this occurs because "cl_char" and  
"char" are actually synonymous on that platform which leads us to  
specialize the same template twice. Would you mind submitting a bug to  
the issue tracker [1] for this?  
  
I didn't encounter this error on newer Cray systems.  
##

---

## Comment 1

> Username: robertdfrench  
> Created at: 2015-05-17 14:11:34 UTC  
> Url: https://github.com/boostorg/compute/issues/406#issuecomment-102806777  

Hi @Belcourt , is this still an ongoing issue? If so, which compiler are you using?

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-25 15:04:59 UTC  
> Url: https://github.com/boostorg/compute/issues/406#issuecomment-297059863  

It's very old. I'm closing it. Please reopen if it's still a problem.
