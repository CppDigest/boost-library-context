# #118 - fast variadic string-concat [Open]

> Username: gpeterhoff  
> Created at: 2023-06-15 21:51:56 UTC  
> Updated at: 2023-06-15 21:51:56 UTC  
> Url: https://github.com/boostorg/algorithm/issues/118  

Hello,  
often string functions of the form  
str = str + concat(strings...)  
or  
str = concat(strings...)  
are needed. But so far there is no variadic std::concat or boost::algorithm::concat. I would like to make up for that here.  
The goal of course must be to limit memory operations to the most necessary, i.e. to avoid temporary strings and thus (superfluous) new/delete etc.  
My current implementations [concat.hpp.txt](https://github.com/boostorg/algorithm/files/11763557/concat.hpp.txt)  
This also works with very old compilers - of course C++11 is generally required https://godbolt.org/z/cvWrzxr1e  
  
thx  
Gero
