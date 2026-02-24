# #132 - is_function_cxx_11.hpp(102): error C3193: '__clrcall': requires '/clr' or '/ZW' command line option [Closed]

> Username: Riadela  
> Created at: 2019-09-05 16:06:08 UTC  
> Updated at: 2019-09-13 08:12:32 UTC  
> Closed at: 2019-09-13 08:12:32 UTC  
> Url: https://github.com/boostorg/type_traits/issues/132  

I'm trying to build an unmanaged project where I can't enable /clr, I upgraded the Boost libraries from v1.67 to 1.71, and keep getting this error for some files that uses boost:  
`>c:\..\..\thirdparty\source\boost\boost_1_71_0\boost\type_traits\detail\is_function_cxx_11.hpp(102): error C3193: '__clrcall': requires '/clr' or '/ZW' command line option`  
I tried to turn on the /clr option only for those specific files, but it becomes a whole mess with some project dependencies ..  
Is there any solution or workaround for this?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-09-05 17:52:58 UTC  
> Url: https://github.com/boostorg/type_traits/issues/132#issuecomment-528497189  

If you look at the offending code you will see:  
  
```  
#ifdef __CLR_VER  
   template <class Ret, class...Args BOOST_TT_NOEXCEPT_PARAM>  
   struct is_function<Ret __clrcall(Args...)BOOST_TT_NOEXCEPT_DECL> : public true_type {};  
#endif  
```  
  
And the docs for `__CLR_VER`  say:  
  
"__CLR_VER Defined as an integer literal that represents the version of the Common Language Runtime (CLR) used to compile the app. The value is encoded in the form Mmmbbbbb, where M is the major version of the runtime, mm is the minor version of the runtime, and bbbbb is the build number. __CLR_VER is defined if the /clr compiler option is set. Otherwise, undefined."  
  
See https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=vs-2019  
  
So it looks to me like you have a stray #define in your project settings somewhere?  Certainly __CLR_VER should **not** be defined if you're building a native application.  In fact since it's a compiler predefine, it should never be set on the command line.

---

## Comment 2

> Username: Riadela  
> Created at: 2019-09-06 07:50:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/132#issuecomment-528751558  

Yeah, you are right, I've just found out there are 2 files that uses the /clr option, yet they're not using any of the boost libraries ..

---

## Comment 3

> Username: Riadela  
> Created at: 2019-09-13 08:12:32 UTC  
> Url: https://github.com/boostorg/type_traits/issues/132#issuecomment-531144101  

I solved it by undefining __CLR_VER in the files that were trying to include Boost
