# #250 - libs/filesystem/src/operations.cpp:3867:7: error: '::utimbuf' has not been declared [Closed]

> Username: jschueller  
> Created at: 2022-08-12 08:25:52 UTC  
> Updated at: 2022-08-12 10:47:10 UTC  
> Closed at: 2022-08-12 10:01:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/250  

since 1.80 the build fails on centos:  
```  
2022-08-11T11:14:44.3215140Z gcc.compile.c++ bin.v2/libs/filesystem/build/gcc-10/release/threading-multi/visibility-hidden/directory.o  
2022-08-11T11:14:44.4034841Z gcc.compile.c++ bin.v2/libs/filesystem/build/gcc-10/release/threading-multi/visibility-hidden/operations.o  
2022-08-11T11:14:44.4038901Z libs/filesystem/src/operations.cpp: In function 'void boost::filesystem::detail::last_write_time(const boost::filesystem::path&, time_t, boost::system::error_code*)':  
2022-08-11T11:14:44.4040025Z libs/filesystem/src/operations.cpp:3867:7: error: '::utimbuf' has not been declared  
2022-08-11T11:14:44.4040489Z  3867 |     ::utimbuf buf;  
2022-08-11T11:14:44.4040772Z       |       ^~~~~~~  
2022-08-11T11:14:44.4041361Z libs/filesystem/src/operations.cpp:3868:5: error: 'buf' was not declared in this scope  
2022-08-11T11:14:44.4042088Z  3868 |     buf.actime = st.st_atime; // utime() updates access time too :-(  
2022-08-11T11:14:44.4042480Z       |     ^~~  
2022-08-11T11:14:44.4042782Z In file included from ./boost/config.hpp:39,  
2022-08-11T11:14:44.4043171Z                  from ./boost/filesystem/config.hpp:19,  
2022-08-11T11:14:44.4043578Z                  from libs/filesystem/src/platform_config.hpp:81,  
2022-08-11T11:14:44.4044250Z                  from libs/filesystem/src/operations.cpp:14:  
2022-08-11T11:14:44.4044976Z libs/filesystem/src/operations.cpp:3870:26: error: '::utime' has not been declared; did you mean 'stime'?  
2022-08-11T11:14:44.4045505Z  3870 |     if (BOOST_UNLIKELY(::utime(p.c_str(), &buf) < 0))  
```  
  
it seems some posix *at functions detection was introduced but the fallback does not compile on that platform (too old?):  
```  
- has POSIX *at APIs       : no [2]  
```  
  
https://github.com/boostorg/filesystem/blob/develop/src/operations.cpp#L3843

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-12 10:02:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/250#issuecomment-1212942046  

Thank you for the report, please test if https://github.com/boostorg/filesystem/commit/5864f397ccad30f6e73221b90bdac57a303b9752 fixes the problem.

---

## Comment 2

> Username: jschueller  
> Created at: 2022-08-12 10:47:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/250#issuecomment-1212977905  

its ok; thanks
