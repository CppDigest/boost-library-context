# #61 - Error under msvc-8.0 [Closed]

> Username: pdimov  
> Created at: 2018-10-08 17:53:54 UTC  
> Updated at: 2018-10-08 20:05:06 UTC  
> Closed at: 2018-10-08 20:05:06 UTC  
> Url: https://github.com/boostorg/log/issues/61  

The program  
  
```  
#include <boost/log/trivial.hpp>  
  
int main()  
{  
    BOOST_LOG_TRIVIAL(info) << "An informational message";  
}  
```  
  
under msvc-8.0 yields  
  
```  
1>c:\boost-git\develop\boost\parameter\aux_\arg_list.hpp(227) : error C2064: term does not evaluate to a function taking 1 arguments  
1>        c:\boost-git\develop\boost\parameter\aux_\arg_list.hpp(390) : see reference to class template instantiation 'boost::parameter::aux::arg_list<TaggedArg,Next>' being compiled  
```  
  
No idea if anyone does, or ought to, care about msvc-8.0 at this point; I just came across this and thought I'd mention it.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-10-08 20:05:06 UTC  
> Url: https://github.com/boostorg/log/issues/61#issuecomment-427962098  

The error comes from Boost.Parameter, so you might want to report the problem there. On Boost.Log side, I'm just using the API Boost.Parameter provides, I don't see what I can do.  
  
PS: I don't have access to MSVC 8 but just from looking at the code I don't quite understand how it gets to an `arg_list` since the macro only unfolds to a single named parameter `severity`, so it should only create `tagged_argument` but not `arg_list`. Either I'm missing something or something fishy is going on.
