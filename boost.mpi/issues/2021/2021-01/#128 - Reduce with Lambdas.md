# #128 - Reduce with Lambdas [Open]

> Username: Lagrang3  
> Created at: 2021-01-22 14:18:59 UTC  
> Updated at: 2023-03-14 10:59:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/128  

In the standard library lambdas can be used for reduction operations in `std::accumulate`.  
Is there any reason why `boost::mpi::all_reduce` does not accept lambda functions as custom reduction operation?  
  
Example:  
```  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
namespace mpi = boost::mpi;  
  
#include <iostream>  
#include <algorithm>  
  
int main()  
{  
    mpi::environment env;  
    mpi::communicator world;  
  
    {  
        int a=1;  
        int s = mpi::all_reduce(world,a,std::plus<int>()); // ok  
          
        std::vector<int> v{1,2,3};  
        int s2 = std::accumulate(v.begin(),v.end(),0,std::plus<int>()); // ok  
    }  
      
    {  
        int a=1;  
        //int s = mpi::all_reduce(world,a,[](int x,int y){return x+y;}); // error: deleted default constructor for lambdas  
          
        std::vector<int> v{1,2,3};  
        int s2 = std::accumulate(v.begin(),v.end(),0,[](int x,int y){return x+y;}); // ok  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: aminiussi  
> Created at: 2021-01-22 14:25:34 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-765436487  

The choice was made to remain "old school" to accommodate older compilers.  
  
It's a choice that could be discussed tough.

---

## Comment 2

> Username: Lagrang3  
> Created at: 2021-01-22 14:35:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-765443912  

> The choice was made to remain "old school" to accommodate older compilers.  
  
That's a pity.

---

## Comment 3

> Username: aminiussi  
> Created at: 2021-01-22 14:41:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-765448306  

Yes.   
  
----   
Alain Miniussi   
DSI, Pôles Calcul et Genie Log.   
Observatoire de la Côte d'Azur   
Tél. : +33492003009 (Mont-Gros)   
+33483618544 (Sophia Antipolis)   
+33609650665   
  
----- On 22 Jan 21, at 15:35, Eduardo Quintana <notifications@github.com> wrote:   
  
>> The choice was made to remain "old school" to accommodate older compilers.  
> That's a pity.  
  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, [  
> https://github.com/boostorg/mpi/issues/128#issuecomment-765443912 | view it on  
> GitHub ] , or [  
> https://github.com/notifications/unsubscribe-auth/AAEGZDGSVF27VWZG32GVEU3S3GEMPANCNFSM4WORRK7Q  
> | unsubscribe ] .

---

## Comment 4

> Username: Lagrang3  
> Created at: 2021-01-22 14:41:28 UTC  
> Updated at: 2021-01-22 14:50:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-765448579  

@aminiussi How do you test changes in the repository? There's no makefile nor anything like it.  
I mean is there an automatic way to get your new changes immediately on you custom boost install.  
Or do you just copy everything from `include` into `boost_<version>/boost/mpi` and the rest into `boost_<version>/libs/mpi` and then run the `bootstraph.sh`?

---

## Comment 5

> Username: aminiussi  
> Created at: 2021-01-22 22:45:28 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-765728342  

@Lagrang3 I don't do that very often but yes, I do a bootstrap.sh (either with the --prefix option or I edit the bottom of the project bjam config, as the mpi section and python section usually need some manual editing) and then I call the ./b2 install (and the b2 test, although I usually go into the ./lib/mpi/tests directory and run ../../../b2 from there).

---

## Comment 6

> Username: Lagrang3  
> Created at: 2022-10-25 08:26:37 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-1290179358  

Can we keep this open as an improvement proposal? I would like to implement this sometime.

---

## Comment 7

> Username: aminiussi  
> Created at: 2023-03-14 10:59:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/128#issuecomment-1467874253  

There is now a cmake build available, ad I copied al the bjam tests into ctest tests.  
  
It's available on develop.
