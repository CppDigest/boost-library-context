# #146 - Serialization for 2 D vector throws `std::bad_alloc ` [Closed]

> Username: sajedehkebriti  
> Created at: 2023-07-05 04:22:36 UTC  
> Updated at: 2023-08-02 17:24:29 UTC  
> Closed at: 2023-08-02 17:24:29 UTC  
> Url: https://github.com/boostorg/mpi/issues/146  

Hello,   
I've a problem in serialization of attached simple class having a `std::vector<std::vector<int>>` as a member. This problem doesn't exit in the following scenarios:   
1. I won't get this error when running with only two processors, but as long as four processors at the same time are trying to communicate a `std::vector` of the object, it breaks.   
2. Still running with four processors, I won't get this error when size of `std::vector<Dummy>` is 100, it breaks with higher size like 200.   
In summary, `req.test() ` throws `std::bad_alloc` when I'm running with more than two processors and larger size of std::vector of say 200 (still not large enough for real applications)   
I'm attaching the code, I'm sure that something is happening with the way that vector serialization is occurring since if I get rid of serialization of `array`, I won't face this issue. I'd highly appreciate help on this matter.   
I'm compiling the code `mpic++ -g code.cpp -o output -lboost_mpi -lboost_serialization`  
  
```  
#include <iostream>  
#include <boost/mpi/datatype.hpp>  
#include "boost/multi_array.hpp"  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/serialization/vector.hpp>  
#include <boost/mpi.hpp>  
#include <boost/version.hpp>  
class Dummy  
{  
private:  
  
	  
	std::vector<unsigned int> array;  
	friend class boost::serialization::access;  
	template <class T>   
	inline void serialize(T & ar, const unsigned int /*version*/)  
	{ ar & array;}  
public:  
	Dummy()  
	{};  
	Dummy(const int nDivs)   
	{  
		//array.resize(1);   
		array.resize(nDivs);  
  	}  
	~Dummy() {};   
};  
int main(int argc, char* const argv[])   
{  
 	boost::mpi::environment   env;   
        boost::mpi::communicator  world;  
        boost::mpi::request req;   
  
	std::cout << "Using Boost "       
          << BOOST_VERSION / 100000     << "."  // major version  
          << BOOST_VERSION / 100 % 1000 << "."  // minor version  
          << BOOST_VERSION % 100                // patch level  
          << std::endl;  
  
	int MASTER = 0;   
	int tag    = 0;  
	std::size_t size = 1000000;    
	  
  
	std::vector<unsigned int> neighbrs = {0, 1, 2, 3};  
	std::vector<Dummy> TobeSend (size, Dummy(2));   
	std::vector<Dummy> TobeRecvd (size ,Dummy(1));  
  
	for(auto itri:neighbrs)  
	{  
        int target = itri;   
		if(world.rank()!= target)  
		{  
			  
			world.isend(target, tag, TobeSend);  
			  
		}  
	}  
	for(auto isource:neighbrs)  
	{  
		int source = isource;  
  
		if(world.rank()!= source)  
		{  
			  
			req= world.irecv(source, tag, TobeRecvd);   
			req.test();   
  
			  
		}  
	}  
	return 0;   
}   
```

---

## Comment 1

> Username: aminiussi  
> Created at: 2023-07-06 10:19:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1623415452  

Hi,   
  
I cannot reproduce the problem with:   
std::size_t size= 200;   
and 4 mpi process with 1.82 and intel one api 2023.1.0.   
  
Note that there might be an issue with the life span of TobeSend which could be destroyed before serialization is over.   
  
A++   
  
----   
Alain Miniussi   
DSI, Pôles Calcul et Genie Log.   
Observatoire de la Côte d'Azur   
Tél. : +33609650665   
  
----- On Jul 5, 2023, at 6:22 AM, sajedehkebriti ***@***.***> wrote:   
  
> Hello,  
> I've a problem in serialization of attached simple class having a  
> std::vector<std::vector<int>> as a member. This problem doesn't exit in the  
> following scenarios:  
  
>    1. I won't get this error when running with only two processors, but as long as  
>    four processors at the same time are trying to communicate a std::vector of the  
>     object, it breaks.  
>    2. Still running with four processors, I won't get this error when size of  
>    std::vector<Dummy> is 100, it breaks with higher size like 200. In summary,  
>    req.test() throws std::bad_alloc when I'm running with more than two processors  
>    and larger size of std::vector of say 200 (still not large enough for real  
>    applications) I'm attaching the code, I'm sure that something is happening with  
>    the way that vector serialization is occurring since if I get rid of  
>    serialization of array , I won't face this issue. I'd highly appreciate help on  
>    this matter. I'm compiling the code mpic++ -g code.cpp -o output -lboost_mpi  
>     -lboost_serialization  
> #include <iostream>  
> #include <boost/mpi/datatype.hpp>  
> #include <boost/mpi/environment.hpp>  
> #include <boost/mpi/communicator.hpp>  
> #include <boost/serialization/vector.hpp>  
> #include <boost/mpi.hpp>  
> class Dummy  
> {  
> private:  
  
> 	int N ;  
> 	std::vector<std::vector<int>> array;  
> 	friend class boost::serialization::access;  
> 	template <class Archive>  
> 	void serialize(Archive &ar, const unsigned int /*version*/)  
> 	{  
> 		ar &N;  
> 		ar &array;  
> 	}  
> public:  
> 	Dummy()  
> 	{  
> 		N= 1;  
> 		array.resize(N + 1);  
> 		for (int i = 0; i <N + 1 ; ++i)  
> 		{  
>     		array[i].resize(N + 1);  
> 		}  
> 	};  
> 	Dummy(const int nDivs) :  N(nDivs)  
> 	{  
> 		array.resize(N + 1);  
> 		for (int i = 0; i <N + 1 ; ++i)  
> 		{  
>     		array[i].resize(N + 1);  
> 		}  
>   	}  
> 	~Dummy() {};  
> };  
> int main(int argc, char* const argv[])  
> {  
>  	boost::mpi::environment   env;  
  
> 	boost::mpi::communicator  world;  
  
> 	boost::mpi::request req;  
  
> 	int MASTER = 0;  
> 	int tag    = 0;  
> 	std::size_t size= 100;  
  
> 	std::vector<int> neighbrs = {0,1,2,3};  
> 	for(auto itri:neighbrs)  
> 	{  
>         int target= itri;  
> 		if(world.rank()!=target)  
> 		{  
> 			std::vector<Dummy> TobeSend (size, Dummy(1));  
> 			world.isend(target,tag,TobeSend) ;  
> 		}  
> 	}  
> 	for(auto isource:neighbrs)  
> 	{  
> 		int source= isource;  
  
> 		if(world.rank()!=source)  
> 		{  
> 			std::vector<Dummy> TobeRecvd (size ,Dummy(1));  
> 			req= world.irecv(source,tag,TobeRecvd);  
> 			req.test();  
> 		}  
  
> 	}  
> 	return 0;  
> }  
  
> —  
> Reply to this email directly, [ https://github.com/boostorg/mpi/issues/146 |  
> view it on GitHub ] , or [  
> https://github.com/notifications/unsubscribe-auth/AAEGZDEJAJUWYNDW7UEU4ODXOTTZNANCNFSM6AAAAAAZ6MQD5Q  
> | unsubscribe ] .  
> You are receiving this because you are subscribed to this thread. Message ID:  
> <boostorg/mpi/issues/146 @ github . com>

---

## Comment 2

> Username: aminiussi  
> Created at: 2023-07-06 12:15:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1623577919  

If you enabled core dumps (`ulimit -c ulimited` might be the command depending on the OS) you should get some and be able to get a stack trace.

---

## Comment 3

> Username: sajedehkebriti  
> Created at: 2023-07-07 01:58:05 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1624528938  

I'm using 1.74 on Linux. Is it possible it's something related to the boost version?   
How is it possible that `TobeSend` become destroyed, and how should I control that if it's the case?   
This is where this exception occurs :   
```  
	if (__builtin_expect(__n > this->_M_max_size(), false))  
	  {  
	    // _GLIBCXX_RESOLVE_LIB_DEFECTS  
	    // 3190. allocator::allocate sometimes returns too little storage  
	    if (__n > (std::size_t(-1) / sizeof(_Tp)))  
	      std::__throw_bad_array_new_length();  
	    std::__throw_bad_alloc();  
	  }  
```

---

## Comment 4

> Username: aminiussi  
> Created at: 2023-07-07 13:42:18 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1625435833  

As a general rule, you should try the last version to see if the problem is still there.  
  
As for the location, the interesting part is the stack trace from the core dump or debugger (compiling with -O0 might help to cancel inlining), to see where it occurs in boost.   
  
Another possibility is to run the code through a memory check tool like valgrind.  
  
You could also try declaring the buffer at the top level top check if this is a scope issue.  
```  
	std::vector<int> neighbrs = {0,1,2,3};  
	std::vector<Dummy> TobeSend (size, Dummy(1));   
        std::vector<Dummy> TobeRecvd (size ,Dummy(1));  
```

---

## Comment 5

> Username: sajedehkebriti  
> Created at: 2023-07-11 03:28:23 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1630050186  

I've simplified the code more to only have one member as one dimensional `std::vector`, but still I'm getting the same thing with the difference that the code crashes in larger `size`.  Here are a few things that I've figured while debugging that might give a clue to resolve the issue.   
  
1. The way that I'm initializing the array size in constructor have an effect on at which `size` crashes. I've tried doing `reserve` or `push_back` and both crashed in larger `size`.   
2. I've been able to produce this error in another machine, but not been able to do so in a supercomputer with the same number of processors (4)  
3. I've tried different containers like `boost::multi_array ` or c like array but no difference in code behaviour.   
  
I wonder how is this possible that the same code crashes in two different machines and goes well in the third machine?  Do I necessarily need to run test on request?

---

## Comment 6

> Username: aminiussi  
> Created at: 2023-07-11 05:59:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1630190143  

Was it still with the 1.74 ?

---

## Comment 7

> Username: sajedehkebriti  
> Created at: 2023-07-11 06:02:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1630192517  

No. I've installed the latest version that's 1.82

---

## Comment 8

> Username: sajedehkebriti  
> Created at: 2023-07-17 00:36:23 UTC  
> Updated at: 2023-07-17 15:28:59 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1637235886  

Adding this piece of code to my problem resolved the issue related to having a 1D array in relatively small size.    
```  
namespace boost { namespace mpi  
{  
      template<> struct is_mpi_datatype<Dummy>  
        : public mpl::true_ { };  
      
}   }  
```  
However, from documentation, I thought when I have a std:::vector which is pointer internally, I shouldn't use this `mpl::true_ { }`.

---

## Comment 9

> Username: aminiussi  
> Created at: 2023-07-17 09:14:23 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1637661934  

That is weird. Do you actually have the correct results ?  
  
Are the nested vectors in the received objects of he correct sizes ?

---

## Comment 10

> Username: sajedehkebriti  
> Created at: 2023-07-17 15:28:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1638377825  

Haven't checked yet whether I will get correct values or not. Just stuck on being able to transfer data for three weeks now. I'm editing my comment since that code ONLY dealt with 1 D case of array as soon as I come back to my real case for a 2D vector with more members, it failed again.

---

## Comment 11

> Username: sajedehkebriti  
> Created at: 2023-08-02 17:22:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1662650477  

This issue can be closed. There is no problem with serialization at all. The problem arises because of the way that I had written non-blocking sends and recvs. I should not have used one variable inside the loop, since it's not safe to be reused when doing non-blocking. That's why, we saw different behaviour out of this code and different error messages ranging from segmentation fault and std::bad_alloc, since it's not guaranteed every time whether receiver has correct size or not.

---

## Comment 12

> Username: aminiussi  
> Created at: 2023-08-02 17:24:29 UTC  
> Url: https://github.com/boostorg/mpi/issues/146#issuecomment-1662654800  

Ok then!
