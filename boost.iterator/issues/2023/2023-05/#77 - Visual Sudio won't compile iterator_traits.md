# #77 - Visual Sudio won't compile iterator_traits [Closed]

> Username: OriolFreixa  
> Created at: 2023-05-01 21:33:27 UTC  
> Updated at: 2023-05-03 20:42:58 UTC  
> Closed at: 2023-05-03 20:16:06 UTC  
> Url: https://github.com/boostorg/iterator/issues/77  

I'm using boost with the Visual Studio 2023's compiler (v143) and c++ 20. I haven't touched any of the provided code, I just included `<boost/algorithm/string/split.hpp>` and `<boost/algorithm/string/classification.hpp>` to use the metods `split` and `is_any_of` in my own code like so:  
`boost::algorithm::split(strings, payload, boost::algorithm::is_any_of(':'));`.  
  
When trying to compile I get these errors:  
![imagen](https://user-images.githubusercontent.com/16169690/235532442-ccb68d2d-ebf0-4d24-b6e1-04d3f80b2c41.png)  
  
I'm taking a look at the code to see if I can solve the problem myself

---

## Comment 1

> Username: Lastique  
> Created at: 2023-05-01 22:15:41 UTC  
> Url: https://github.com/boostorg/iterator/issues/77#issuecomment-1530422996  

There's too little information to give a useful comment, but I don't see how Boost.Iterator is involved. Please provide a compilable small reproducer and compiler error (not a screenshot).

---

## Comment 2

> Username: OriolFreixa  
> Created at: 2023-05-03 19:12:29 UTC  
> Url: https://github.com/boostorg/iterator/issues/77#issuecomment-1533564280  

Sorry, here's more detailed information:  
Code to reproduce the compiling error:  
```  
#include <string>  
#include <vector>  
#include <boost/algorithm/string/split.hpp>  
#include <boost/algorithm/string.hpp>  
int main()  
{  
    std::vector<std::string> strings;  
    std::string payload = "test:test2:test3";  
  
    //SPLIT THE STRINGS MANUALLY  
    boost::split(strings, payload, boost::is_any_of(':'));  
}  
```  
I'm compiling on visual studio with c++17 and the error output is the following  
  
```  
Build started...  
1>------ Build started: Project: Project1, Configuration: Debug x64 ------  
1>Main.cpp  
1>C:\Repos\boostForks\boost\boost\range\value_type.hpp(26,70): error C2039: 'type': is not a member of 'boost::range_iterator<T,void>'  
1>        with  
1>        [  
1>            T=char  
1>        ]  
1>C:\Repos\boostForks\boost\boost\range\value_type.hpp(26,51): message : see declaration of 'boost::range_iterator<T,void>'  
1>        with  
1>        [  
1>            T=char  
1>        ]  
1>C:\Repos\boostForks\Project1\Main.cpp(11,56): message : see reference to class template instantiation 'boost::range_value<char>' being compiled  
1>C:\Repos\boostForks\boost\boost\algorithm\string\classification.hpp(203,9): message : see reference to function template instantiation 'boost::algorithm::detail::is_any_ofF<range_value<T>::type> boost::algorithm::is_any_of(const RangeT &)' being compiled  
1>C:\Repos\boostForks\boost\boost\range\value_type.hpp(26): error C2146: syntax error: missing '>' before identifier 'type'  
1>C:\Repos\boostForks\boost\boost\range\value_type.hpp(27): error C3770: 'unknown-type': is not a valid base class  
1>C:\Repos\boostForks\Project1\Main.cpp(11,43): error C2672: 'boost::algorithm::is_any_of': no matching overloaded function found  
1>C:\Repos\boostForks\boost\boost\algorithm\string\classification.hpp(203,9): message : could be 'boost::algorithm::detail::is_any_ofF<range_value<T>::type> boost::algorithm::is_any_of(const RangeT &)'  
1>C:\Repos\boostForks\Project1\Main.cpp(11,43): message : Failed to specialize function template 'boost::algorithm::detail::is_any_ofF<range_value<T>::type> boost::algorithm::is_any_of(const RangeT &)'  
1>C:\Repos\boostForks\Project1\Main.cpp(11,43): message : With the following template arguments:  
1>C:\Repos\boostForks\Project1\Main.cpp(11,43): message : 'RangeT=char'  
1>C:\Repos\boostForks\Project1\Main.cpp(11,12): error C2672: 'boost::algorithm::split': no matching overloaded function found  
1>C:\Repos\boostForks\boost\boost\algorithm\string\split.hpp(148,35): message : could be 'SequenceSequenceT &boost::algorithm::split(SequenceSequenceT &,RangeT &&,PredicateT,boost::algorithm::token_compress_mode_type)'  
1>C:\Repos\boostForks\Project1\Main.cpp(11,12): message : 'SequenceSequenceT &boost::algorithm::split(SequenceSequenceT &,RangeT &&,PredicateT,boost::algorithm::token_compress_mode_type)': expects 4 arguments - 2 provided  
1>Done building project "Project1.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
========== Build started at 8:46 PM and took 01,696 seconds ==========  
 ```  
I posted the issue in this subrepository because the compiler errors are about the iterator files. I'm not sure in which repo the issue should go though (looks like an update on one end made the code on the another end stop working).

---

## Comment 3

> Username: Lastique  
> Created at: 2023-05-03 20:16:06 UTC  
> Url: https://github.com/boostorg/iterator/issues/77#issuecomment-1533676439  

`is_any_of` should be provided with a range, not a single character. A C-style string is also acceptable, so you can do this:  
  
```  
boost::split(strings, payload, boost::is_any_of(":"));  
```  
  
Note the double quotes around the colon.

---

## Comment 4

> Username: OriolFreixa  
> Created at: 2023-05-03 20:42:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/77#issuecomment-1533718779  

Thank you very much, and sorry for the confusion!
