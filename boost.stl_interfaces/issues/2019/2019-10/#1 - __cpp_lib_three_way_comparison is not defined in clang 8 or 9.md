# #1 - __cpp_lib_three_way_comparison is not defined in clang 8 or 9 [Closed]

> Username: grattle  
> Created at: 2019-10-22 11:07:58 UTC  
> Updated at: 2019-12-29 19:54:20 UTC  
> Closed at: 2019-12-29 19:54:20 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/1  

iterator_interface.hpp doesn't compile on my clang 8.  
  
As a local kludge, I just renamed all references  to __cpp_lib_three_way_comparison with a legal #define variable set to 0  
  
Great iterator interface, I love it!  
  
Arthur

---

## Comment 1

> Username: tzlaine  
> Created at: 2019-12-17 00:46:10 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/1#issuecomment-566319326  

Thanks!  The pattern __foo is reserved by the standard, and in particular a macro that starts with that (or is it "__cpp" -- I forget) is supposed to evaluate to false if the compiler has never seen that macro.  This is to make checks for such macros well-formed under versions of C++ from before they were introduced.  This also passes the tests in Travis.  Are you possibly running an oddly=configured build?

---

## Comment 2

> Username: grattle  
> Created at: 2019-12-17 23:26:39 UTC  
> Updated at: 2019-12-19 09:35:08 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/1#issuecomment-566795312  

On Tuesday, 17 December 2019 11:46:11 AM AEDT Zach Laine wrote:  
  
Yes, others have said I am odd...  
  
../stl_interfaces/iterator_interface.hpp:13:16: error: '__cpp_lib_three_way_comparison' is   
not defined, evaluates to 0 [-Werror,-Wundef]  
  
So, I could add -Wno_undef to my compile line. But that error line perhaps implies a naked   
__cpp_lib_three_way_comparison may be deprecated, or it could be a clang thing.  
  
In my little world, I'm lucky the only macro I need is #if NDEBUG, so I have thankfully   
forgotten all about them and where they fit in the evolving Standard.  
  
Again, these are all tiny issues. I do love how you have factored out much of the drudgery   
in making iterators. It is now permanently in my toolkit.  
  
Many thanks,  
  
  
Arthur

---

## Comment 3

> Username: tzlaine  
> Created at: 2019-12-18 02:14:37 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/1#issuecomment-566835431  

Ok, thanks for clarifying.  I'll probably just add a defined() around those so that users with those flags are not caught in pointless compilation errors.
