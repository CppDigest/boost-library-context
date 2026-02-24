# #85 - mt19937 default seed fails when language extensions disabled [Closed]

> Username: andres-alban  
> Created at: 2021-10-01 21:11:27 UTC  
> Updated at: 2021-11-17 20:07:51 UTC  
> Closed at: 2021-11-17 20:05:35 UTC  
> Url: https://github.com/boostorg/random/issues/85  

I tried to compile the following in Visual Studio 2017 disabling the language extensions (Project Properties -> C/C++ -> Language -> Disable Language Extensions -> Yes (/Za) )  
```  
#include <boost/random.hpp>  
boost::random::mt19937 rng;  
```  
I get error LNK2019   
> unresolved external symbol "public: static unsigned int const boost::random::mersenne_twister_engine<unsigned int,32,624,397,31,2567483615,11,4294967295,7,2636928640,15,4022730752,18,1812433253>::default_seed" (?default_seed@?$mersenne_twister_engine@I$0CA@$0CHA@$0BIN@$0BP@$0JJAILANP@$0L@$0PPPPPPPP@$06$0JNCMFGIA@$0P@$0OPMGAAAA@$0BC@$0GMAHIJGF@@random@boost@@2IB) referenced in function "public: void __cdecl boost::random::mersenne_twister_engine<unsigned int,32,624,397,31,2567483615,11,4294967295,7,2636928640,15,4022730752,18,1812433253>::seed(void)" (?seed@?$mersenne_twister_engine@I$0CA@$0CHA@$0BIN@$0BP@$0JJAILANP@$0L@$0PPPPPPPP@$06$0JNCMFGIA@$0P@$0OPMGAAAA@$0BC@$0GMAHIJGF@@random@boost@@QEAAXXZ)  
  
The program compiles properly if I use properties Disable Language Extensions -> No or if I pass a seed to the constructor, i.e., the following compiles properly regardless of the property Disable Language Extension:  
```  
#include <boost/random.hpp>  
boost::random::mt19937 rng{1};  
```  
It is an easy fix from the user side but I spent a few hours figuring it out.

---

## Comment 1

> Username: andres-alban  
> Created at: 2021-11-17 20:07:51 UTC  
> Url: https://github.com/boostorg/random/issues/85#issuecomment-971933907  

I found out that disabling language extensions is not recommended
