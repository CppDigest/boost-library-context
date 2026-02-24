# #206 - Use of uninitialized value caught by clang memory sanitizer [Closed]

> Username: correaa  
> Created at: 2020-06-08 19:36:24 UTC  
> Updated at: 2020-07-15 23:05:28 UTC  
> Closed at: 2020-07-15 23:05:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/206  

On the spirit of clang sanitizers... I see that the are variables that are used uninitialized as caught by clang's memory sanitizer:  
  
This simple program  
  
```c++  
#include<boost/archive/text_oarchive.hpp>  
#include<boost/serialization/vector.hpp>  
  
#include<iostream>  
#include<vector>  
  
int main(){  
	std::vector<double> v(10, 99);  
	boost::archive::text_oarchive toa{std::cout};  
	toa << v;  
}  
```  
compiled with `clang++ -fsanitize=memory test.cpp -g -o test.x -lboost_serialization` and run produces the runtime error below.  
  
I could find a pattern, but if I believe that the error happens really at the line reported by the error.   
I looks like it is the use of initialized `const T& t` repeatedly (likely) or the use of the fail bit in the `ostream` (?).  
  
Again, I don't know if this is a false positive, but since `clang` is so popular, perhaps it is worth to whitelist this UB with some clang pragma.  
  
Error when using the command above:  
```  
 #0 0x4a76c7 in void boost::archive::basic_text_oprimitive<std::ostream>::save_impl<boost::serialization::collection_size_type>(boost::serialization::collection_size_type const&, mpl_::bool_<false>&) /usr/include/boost/archive/basic_text_oprimitive.hpp:126:12  
    #1 0x4a747e in void boost::archive::basic_text_oprimitive<std::ostream>::save<boost::serialization::collection_size_type>(boost::serialization::collection_size_type const&) /usr/include/boost/archive/basic_text_oprimitive.hpp:179:9  
    #2 0x4a73ea in void boost::archive::text_oarchive_impl<boost::archive::text_oarchive>::save<boost::serialization::collection_size_type>(boost::serialization::collection_size_type const&) /usr/include/boost/archive/text_oarchive.hpp:65:46  
    #3 0x4a733f in void boost::archive::save_access::save_primitive<boost::archive::text_oarchive, boost::serialization::collection_size_type>(boost::archive::text_oarchive&, boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/oserializer.hpp:93:12  
    #4 0x4a72a7 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::save_primitive::invoke<boost::serialization::collection_size_type>(boost::archive::text_oarchive&, boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/oserializer.hpp:237:13  
    #5 0x4a7224 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::invoke<boost::serialization::collection_size_type>(boost::archive::text_oarchive&, boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/oserializer.hpp:315:9  
    #6 0x4a7197 in void boost::archive::save<boost::archive::text_oarchive, boost::serialization::collection_size_type const>(boost::archive::text_oarchive&, boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/oserializer.hpp:539:5  
    #7 0x4a711e in void boost::archive::detail::common_oarchive<boost::archive::text_oarchive>::save_override<boost::serialization::collection_size_type const>(boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/common_oarchive.hpp:71:9  
    #8 0x4a7077 in void boost::archive::basic_text_oarchive<boost::archive::text_oarchive>::save_override<boost::serialization::collection_size_type const>(boost::serialization::collection_size_type const&) /usr/include/boost/archive/basic_text_oarchive.hpp:83:39  
    #9 0x4a6f19 in boost::archive::text_oarchive& boost::archive::detail::interface_oarchive<boost::archive::text_oarchive>::operator<<<boost::serialization::collection_size_type>(boost::serialization::collection_size_type const&) /usr/include/boost/archive/detail/interface_oarchive.hpp:70:23  
    #10 0x4a6e59 in void boost::serialization::nvp<boost::serialization::collection_size_type>::save<boost::archive::text_oarchive>(boost::archive::text_oarchive&, unsigned int) const /usr/include/boost/serialization/nvp.hpp:67:12  
    #11 0x4a6d7f in void boost::serialization::access::member_save<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> const>(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type> const&, unsigned int) /usr/include/boost/serialization/access.hpp:91:11  
    #12 0x4a6cdf in boost::serialization::detail::member_saver<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> >::invoke(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type> const&, unsigned int) /usr/include/boost/serialization/split_member.hpp:43:13  
    #13 0x4a6c3f in void boost::serialization::split_member<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type>&, unsigned int) /usr/include/boost/serialization/split_member.hpp:69:5  
    #14 0x4a6b9f in void boost::serialization::nvp<boost::serialization::collection_size_type>::serialize<boost::archive::text_oarchive>(boost::archive::text_oarchive&, unsigned int) /usr/include/boost/serialization/nvp.hpp:76:5  
    #15 0x4a6aff in void boost::serialization::access::serialize<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type>&, unsigned int) /usr/include/boost/serialization/access.hpp:116:11  
    #16 0x4a6a5f in void boost::serialization::serialize<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type>&, unsigned int) /usr/include/boost/serialization/serialization.hpp:68:5  
    #17 0x4a69c1 in void boost::serialization::serialize_adl<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type>&, unsigned int) /usr/include/boost/serialization/serialization.hpp:126:5  
    #18 0x4a6861 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::save_only::invoke<boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/detail/oserializer.hpp:246:13  
    #19 0x4a67d4 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::invoke<boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/detail/oserializer.hpp:315:9  
    #20 0x4a6747 in void boost::archive::save<boost::archive::text_oarchive, boost::serialization::nvp<boost::serialization::collection_size_type> const>(boost::archive::text_oarchive&, boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/detail/oserializer.hpp:539:5  
    #21 0x4a66ce in void boost::archive::detail::common_oarchive<boost::archive::text_oarchive>::save_override<boost::serialization::nvp<boost::serialization::collection_size_type> const>(boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/detail/common_oarchive.hpp:71:9  
    #22 0x4a6627 in void boost::archive::basic_text_oarchive<boost::archive::text_oarchive>::save_override<boost::serialization::nvp<boost::serialization::collection_size_type> const>(boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/basic_text_oarchive.hpp:83:39  
    #23 0x4a5a09 in boost::archive::text_oarchive& boost::archive::detail::interface_oarchive<boost::archive::text_oarchive>::operator<<<boost::serialization::nvp<boost::serialization::collection_size_type> >(boost::serialization::nvp<boost::serialization::collection_size_type> const&) /usr/include/boost/archive/detail/interface_oarchive.hpp:70:23  
    #24 0x4a52e9 in void boost::serialization::stl::save_collection<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&, boost::serialization::collection_size_type) /usr/include/boost/serialization/collections_save_imp.hpp:43:8  
    #25 0x4a4f4f in void boost::serialization::stl::save_collection<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&) /usr/include/boost/serialization/collections_save_imp.hpp:76:5  
    #26 0x4a4dc0 in void boost::serialization::save<boost::archive::text_oarchive, double, std::allocator<double> >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&, unsigned int, mpl_::bool_<false>) /usr/include/boost/serialization/vector.hpp:66:5  
    #27 0x4a4d1c in void boost::serialization::save<boost::archive::text_oarchive, double, std::allocator<double> >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&, unsigned int) /usr/include/boost/serialization/vector.hpp:145:5  
    #28 0x4a4c71 in boost::serialization::free_saver<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >::invoke(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&, unsigned int) /usr/include/boost/serialization/split_free.hpp:45:9  
    #29 0x4a4b0f in void boost::serialization::split_free<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> >&, unsigned int) /usr/include/boost/serialization/split_free.hpp:74:5  
    #30 0x4a4a1f in void boost::serialization::serialize<boost::archive::text_oarchive, double, std::allocator<double> >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> >&, unsigned int) /usr/include/boost/serialization/vector.hpp:176:5  
    #31 0x4a4781 in void boost::serialization::serialize_adl<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> >&, unsigned int) /usr/include/boost/serialization/serialization.hpp:126:5  
    #32 0x4a4374 in boost::archive::detail::oserializer<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >::save_object_data(boost::archive::detail::basic_oarchive&, void const*) const /usr/include/boost/archive/detail/oserializer.hpp:153:5  
    #33 0x7fe8d7d9994a  (/lib/x86_64-linux-gnu/libboost_serialization.so.1.71.0+0x1894a)  
    #34 0x4a22d3 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::save_standard::invoke<std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/oserializer.hpp:258:16  
    #35 0x4a21f7 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::save_conditional::invoke<std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/oserializer.hpp:275:17  
    #36 0x4a2174 in void boost::archive::detail::save_non_pointer_type<boost::archive::text_oarchive>::invoke<std::vector<double, std::allocator<double> > >(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/oserializer.hpp:315:9  
    #37 0x4a20e7 in void boost::archive::save<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > const>(boost::archive::text_oarchive&, std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/oserializer.hpp:539:5  
    #38 0x4a206e in void boost::archive::detail::common_oarchive<boost::archive::text_oarchive>::save_override<std::vector<double, std::allocator<double> > const>(std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/common_oarchive.hpp:71:9  
    #39 0x4a1fc7 in void boost::archive::basic_text_oarchive<boost::archive::text_oarchive>::save_override<std::vector<double, std::allocator<double> > const>(std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/basic_text_oarchive.hpp:83:39  
    #40 0x49a039 in boost::archive::text_oarchive& boost::archive::detail::interface_oarchive<boost::archive::text_oarchive>::operator<<<std::vector<double, std::allocator<double> > >(std::vector<double, std::allocator<double> > const&) /usr/include/boost/archive/detail/interface_oarchive.hpp:70:23  
    #41 0x499a4b in main /tmp/./test.cpp:14:6  
    #42 0x7fe8d78350b2 in __libc_start_main /build/glibc-YYA7BZ/glibc-2.31/csu/../csu/libc-start.c:308:16  
    #43 0x41e7bd in _start (/tmp/test.cppx+0x41e7bd)  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-06-08 19:55:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/206#issuecomment-640853129  

I looked at some of the code as you suggested.  Honestly I don't see any uninitialized data there at all.

---

## Comment 2

> Username: correaa  
> Created at: 2020-06-08 20:47:54 UTC  
> Updated at: 2020-06-08 21:40:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/206#issuecomment-640878224  

I think you are right, there is no uninitialized variable.   
Valgrind doesn't detect this for example.  
  
Actually, reading about MSan, it looks like the single point is this one (the rest is a stack trace)  
  
```  
 #0 0x4a76c7 in void boost::archive::basic_text_oprimitive<std::ostream>::save_impl<boost::serialization::collection_size_type>(boost::serialization::collection_size_type const&, mpl_::bool_<false>&) /usr/include/boost/archive/basic_text_oprimitive.hpp:126:12  
```  
that is when the failbit is checked, which is not a problem with Boost.Serialization but (at most) with iostream.  
  
Even a program like this generates a similar warning:  
  
```c++  
int main(){  
	std::vector<double> v(10, 99);  
	std::ofstream ofs{"bla.txt"};  
	if(ofs.fail()) return 1;  
}  
```  
  
Somewhere I read that that this is not even a problem in iostream but more a reflection that iostream needs to be compiled itself with clang's MSan to not report this false positive: https://stackoverflow.com/questions/20617788/using-memory-sanitizer-with-libstdc  
  
So I think this closes the Issue.  
  
However, if I might ask, does it really make sense to check the fail bit in the Serialization's `save` functions?  
  
After all, 1) it could be user's job to check iostream errors 2) user can activate exceptions (instead of fail bits) if he/she really wants exceptions on failure 3) ... in which case the check will be at best redundant   
  
That is, the checking can be passed as a responsibility of the iostream by the user or even by Boost.Serialization if needed:  
https://en.cppreference.com/w/cpp/io/basic_ios/exceptions  
  
Finally, 4) Checking .fail bit on streams can lead to checking almost everywhere with no end.  
  
Thank you for the great Boost.Serialization library.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-06-08 23:43:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/206#issuecomment-640944424  

"However, if I might ask, does it really make sense to check the fail bit in the Serialization's save functions?"  I don't trust the user. Actually, I don't trust anyone.  The user can insert his own check and activate exceptions.  Actually you do raise a good idea:  if I get fail() - raise exception.  I'm likely already doing this. 4) Right.  The serialization library checks everything because users don't and when things fail they blame me.  And when they fail it's 50 levels deep inside the library and no one can figure out.  You'd be amazed at the checking that is done - especially at compile time when it's "free".  I'm amazed that more people don't complain the library takes a long time to compile.

---

## Comment 4

> Username: correaa  
> Created at: 2020-06-09 21:29:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/206#issuecomment-641592311  

> Actually, I don't trust anyone.   
  
I guess you do trust iostreams.  
  
> The user can insert his own check and activate exceptions. Actually you do raise a good idea: if I get fail() - raise exception. I'm likely already doing this.  
  
Actually, the point of activating exception in iostream is that you don't need to check for a fail explictly.  
iostream throws as soon as it fails, and does not the depend on Serialization throwing, it still could.  
But I understand if you wanted to have control over errors (and even ignore them sometimes).   
  
While iostream can be used in two modes (error codes) and exceptions.   
I think the exception mode is ideal for a library like Serialization because the stream needs to be automatic and by design errors cannot change the path of execution but only fail upwards.   
After all iostreams error codes are probably there only 1) for  historic reasons (before exceptions) 2) to teach input output 3) to allow control flow 4) (mostly fruitless) recovery attempt.   
None of these apply to the internals of Serialization archives in my opinion.  
  
> The serialization library checks everything because users don't and when things fail they blame me.   
  
That is a shame, I agree.   
  
In part this could be because Serialization is trying to take ownership of the failure path so information about the error (if iostream provided) one is lost when Serialization throws a simple exception (with no info, not even whether it was a iostream error).   
Serialization could still collect the info from the exception thrown by iostream (at any level, `save`, `operator>>`, etc) and at least blame the error on it, so they don't blame you.   
  
> And when they fail it's 50 levels deep inside the library and no one can figure out. You'd be amazed at the checking that is done - especially at compile time when it's "free". I'm amazed that more people don't complain the library takes a long time to compile.  
  
Certainly not a complain I have.  
  
Thanks,  
Alfredo

---

## Comment 5

> Username: robertramey  
> Created at: 2020-06-10 01:27:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/206#issuecomment-641665582  

Truth is I don't think I ever new one could just activate exceptions in iostream.  Had I known that, I might have used it.  In fact the whole i/o thing in serialization is pretty ad hoc.  If you wanted to mostly re-do it it might be a nice summer project.  There are a couple of issues.  
  
1. serialization doesn't use the boost exception macros.  This is because some time ago they changed them and re-directed them to some developers library and I couldn't deal with an obligatory not trivial dependency which I was ambushed with.  But time has passed, they fixed up the library and it seems a lot of people are happy with it so perhaps it's time to revisit that decision.  
  
2. One needs to depend on a macro in order to support embedded systems.  This is already in there but would have to be looked at.  
  
3. There is one case where an exception is thrown from a destructors (xml archives) which has created problems.  Unfortunately, fixing this will create an API break.  Oh well.  
  
4. The documentation would have to be updated.  
  
5. and I'm not sure what else.  I would love to know how many actually use the library.  It seems I have steady stream of requests from "fans" such as yourself so that's an indicator.  It's amazing after more than 15 years!
