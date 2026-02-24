# #141 - Bug: consistency: missing BOOST_NORETURN of serialization-specific throw_exception() causes warning-as-error C6011 (NULL ptr access) [MSVC2015] [Closed]

> Username: andim2  
> Created at: 2019-01-02 16:31:44 UTC  
> Updated at: 2019-10-21 23:50:27 UTC  
> Closed at: 2019-10-21 23:50:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/141  

Hello all,  
  
using Boost 1.61.0.0 (HEAD appears to still have this issue!), we are getting  
  
...\extern\boost\1.61.0.0\out\v140-x64\include\boost-1_61\boost\serialization\shared_ptr_helper.hpp(160):  
     error C6011: Dereferencing NULL pointer 'true_type'.  
  
This happens despite boost/serialization/shared_ptr_helper.hpp already explicitly containing a true_type NULL ptr check!:  
  
    template<class T>  
    void reset(SPT< T > & s, T * t){  
        if(NULL == t){  
            s.reset();  
            return;  
        }  
        const boost::serialization::extended_type_info * this_type  
            = & boost::serialization::type_info_implementation< T >::type  
                    ::get_const_instance();  
  
        // get pointer to the most derived object's eti.  This is effectively  
        // the object type identifer  
        typedef typename mpl::if_<  
            is_polymorphic< T >,  
            polymorphic,  
            non_polymorphic  
        >::type type;  
  
        const boost::serialization::extended_type_info * true_type  
            = type::get_object_type(*t);  
  
        // note:if this exception is thrown, be sure that derived pointern  
        // is either registered or exported.  
        if(NULL == true_type)  
            boost::serialization::throw_exception(  
                boost::archive::archive_exception(  
                    boost::archive::archive_exception::unregistered_class,  
                    this_type->get_debug_info()  
                )  
            );  
        // get void pointer to the most derived type  
        // this uniquely identifies the object referred to  
        // oid = "object identifier"  
        const void * oid = void_downcast(  
            *true_type,  
            *this_type,  
            t  
        );  
  
I then relatively quickly realized that  
this is due to  
boost/serialization/throw_exception.hpp missing  
a noreturn function attribute (BOOST_NORETURN), which  
(consistency!!!?!?) *is* used in some other similar helpers, namely:  
- boost/throw_exception.hpp  
  
Given this missing noreturn attribute, the helper-internal "throw" (which disrupts code flow, i.e. is a properly valid cancellation point *prior* to execution of the NULL ptr deref!) will  
NOT be visible / shadowed to MSVC Code Analysis (as opposed to a directly code-inline "throw"), thus  
noreturn is required, else Code-Analysis-induced C6011 diagnosis.  
  
Note that  
some other locations might be affected by this kind of problem, too. Specifically:  
- asio/detail/throw_exception.hpp  
  
// Declare the throw_exception function for all targets.  
template <typename Exception>  
void throw_exception(const Exception& e);  
  
// Only define the throw_exception function when exceptions are enabled.  
// Otherwise, it is up to the application to provide a definition of this  
// function.  
# if !defined(BOOST_ASIO_NO_EXCEPTIONS)  
template <typename Exception>  
void throw_exception(const Exception& e)  
{  
  throw e;  
}  
  
  
See my code consistency bug fix patch in attachment  
[boost_serialization_throw_exception.hpp.diff.txt](https://github.com/boostorg/serialization/files/2721233/boost_serialization_throw_exception.hpp.diff.txt)  
which managed to get rid of this warning-as-error build fail here, in my serialization-restricted use case (my patch does NOT treat asio and potentially existing other locations yet).  
  
HTH,  
  
Andreas Mohr

---

## Comment 1

> Username: robertramey  
> Created at: 2019-02-24 18:43:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/141#issuecomment-466803951  

I've incorporated your change on my machine's develop branch.  Assuming nothing pops up, it will eventually arrive to the release
