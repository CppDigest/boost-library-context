# #21 Additional examples [Merged]

> Username: teeks99  
> Created at: 2016-06-01 02:18:40 UTC  
> Updated at: 2021-03-27 14:38:54 UTC  
> Merged at: 2018-03-22 15:29:59 UTC  
> Closed at: 2018-03-22 15:29:59 UTC  
> Url: https://github.com/boostorg/program_options/pull/21  

Added a little documentation and several examples of using config files and env options.

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2017-09-20 22:02:13 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-330993708  

Is it still alive? Can you please merge it or decline?

---

## Comment 2

> Username: teeks99  
> Created_at: 2017-10-08 18:19:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-335027231  

These are just documentation changes and additional example files. This shouldn't impact the library.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-03-06 14:23:27 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-370796871  

I agree this looks useful.  Squashing the PR to a single commit would be better for future reference.

---

## Comment 4

> Username: vprus  
> Created_at: 2018-03-22 15:30:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-375348772  

Thanks for the PR, I have squashed and applied it.

---

## Comment 5

> Username: vprus  
> Created_at: 2018-03-23 09:12:08 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-375588692  

It seems that this patch causes build errors in the form of   
```  
libs\program_options\example\config_file_types.cpp(167) : error C2059: syntax error : 'bad suffix on number'  
libs\program_options\example\config_file_types.cpp(167) : error C2146: syntax error : missing ';' before identifier 'b101010'  
libs\program_options\example\config_file_types.cpp(167) : error C2065: 'b101010' : undeclared identifier  
```  
See, for example, this: https://ci.appveyor.com/project/vprus/program-options/build/1.0.14-develop/job/6nuivuhqbpfoxapj  
@teeks99, is this something you could fix?

---

## Comment 6

> Username: teeks99  
> Created_at: 2018-03-23 11:18:16 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-375627588  

Hmmm, it looks like this (and [other errors](https://travis-ci.org/boostorg/program_options/jobs/357282772#L1165)?) are because the examples were written in C++11, or in this case C++14 (binary literals).   
  
I'm hesitant to go back and C++03ize it, since it is just an example and I think an example should lead people forward. If they want the backwards compatibility, it is there, they just have to work for it....  
  
That said, could this be fixed by applying some Jamfile rule that prevents the build of the failing examples on older compilers? I'm not a b2 expert, so I don't know what that would be though.

---

## Comment 7

> Username: vprus  
> Created_at: 2018-03-24 11:03:44 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-375872455  

There are also errors like  
```  
/usr/lib/gcc/x86_64-linux-gnu/7.2.0/../../../../include/c++/7.2.0/bits/basic_ios.tcc:178:25: error: base class 'std::ios_base' has private copy constructor  
  extern template class basic_ios<char>;  
```  
  
Are these also C++03 vs C++11 vs C++14?

---

## Comment 8

> Username: teeks99  
> Created_at: 2018-03-24 12:09:17 UTC  
> Url: https://github.com/boostorg/program_options/pull/21#issuecomment-375881331  

Maybe? I'm not sure exactly where that error comes from, but the C++11 build that [travis ran](https://travis-ci.org/boostorg/program_options/builds/356935667) against that commit did succeed.

---
