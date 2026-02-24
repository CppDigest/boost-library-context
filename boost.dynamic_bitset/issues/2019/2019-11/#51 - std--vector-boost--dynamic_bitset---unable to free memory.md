# #51 - std::vector<boost::dynamic_bitset<>>unable to free memory [Closed]

> Username: robot-kimbo  
> Created at: 2019-11-20 09:47:52 UTC  
> Updated at: 2019-12-01 07:46:21 UTC  
> Closed at: 2019-12-01 07:46:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51  

Hi，   
    I am using boost 1.66.0 version,  gcc/g++ -7, and I found when using std::vector<boost::dynamic_bitset<>>, it is unable to free memory when destruction, even using function swap(), resize(), reset(). it doesn't work.  
  
The test program is show below  
int main()  
{  
	cout << "[ memory start] "  << getMemoryUsage();  
	{  
		std::vector<boost::dynamic_bitset<>> m_nodes;  
		m_nodes.resize(1000000 + 1);  
		uint64_t descriptor[4] = {1, 2, 3, 4};  
		for(size_t i = 0; i < m_nodes.size(); i++)  
		{  
		    m_nodes[i] = boost::dynamic_bitset<>(descriptor, descriptor + 4);  
		}  
	}  
	cout << "[ memory end] "  << getMemoryUsage() << endl;  
}  
  
result is:  
[ memory start ] 13  
[ memory end ] 59  
  
As the result show:  
The memory of boost::dynamic_bitset can not release, so it is the problem.  
Could you please confirm it, and tell me how to solve it?

---

## Comment 1

> Username: mclow  
> Created at: 2019-11-28 04:08:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51#issuecomment-559333679  

I'm not seeing a memory leak here. What is `getMemoryUsage`, and what system are you running this on?  
My test program (attached) reports that every allocation is paired with a deallocation. (Boost trunk, on Mac OS 10.14)  
```  
#include <vector>  
#include <iostream>  
#include <cstdio>  
  
#include <boost/dynamic_bitset.hpp>  
  
void* operator new (std::size_t count)  
{  
    void *ptr = malloc(count);  
    ::printf ("Allocating %ld bytes at %p\n", count, ptr);  
      
    return ptr;  
}  
  
void* operator new[]( std::size_t count)  
{  
    void *ptr = malloc(count);  
    ::printf ("Allocating %ld bytes[] at %p\n", count, ptr);  
      
    return ptr;  
}  
  
void operator delete(void *ptr) throw()  
{  
    ::printf ("deleting %p\n", ptr);  
	free(ptr);  
}  
  
void operator delete[](void *ptr) throw()  
{  
    ::printf ("deleting[] %p\n", ptr);  
    free(ptr);  
}  
  
  
int main() {  
	{  
	std::vector<boost::dynamic_bitset<>> m_nodes;  
// 	m_nodes.resize(1000000 + 1);  
	m_nodes.resize(10 + 1);  
	uint64_t descriptor[4] = {1, 2, 3, 4};  
	for(size_t i = 0; i < m_nodes.size(); i++)  
		{  
		m_nodes[i] = boost::dynamic_bitset<>(descriptor, descriptor + 4);  
		}  
	::printf("Exiting scope\n");  
	}  
}  
```

---

## Comment 2

> Username: robot-kimbo  
> Created at: 2019-11-30 03:09:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51#issuecomment-559907888  

Hi, thanks for reply.   
1. system: ubuntu 18.04, boost 1.66.0 release version, gcc/g++ -7  
2. getMemoryUsage() is code of get the program memory, it like command "busybox top"  
here is the linux code of it:  
unsigned long getMemoryUsage()  
{  
    unsigned long MEM = 0;  
	FILE *f = ::fopen ("/proc/self/stat", "r");  
	if (!f) return 0;  
	if (!::fscanf(f,  
		"%*d %*s %*c %*d %*d %*d %*d %*d%*u %*u %*u %*u %*u %*u %*u%*d %*d %*d %*d %*d %*d %*u %lu", &MEM))  
	{  
		// Error parsing:  
		MEM=0;  
	}  
	::fclose (f);  
    return MEM/1024/1024;  
}  
3. For my test program, you can see after destruct of "std::vector<boost::dynamic_bitset<>>", the memory can not restore "13M",  that why I mean "std::vector<boost::dynamic_bitset<>>" has memory leak.

---

## Comment 3

> Username: robot-kimbo  
> Created at: 2019-11-30 03:20:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51#issuecomment-559908406  

Hi, I also test std::bitset, the memory of this has not meomory leak, here is the test program   
  
int main()  
{  
    cout << "[ memory start ] "  << getMemoryUsage() << std::endl;  
    {  
  
        std::vector<std::bitset <256> > m_nodes;  
        m_nodes.resize(1000000 + 1);  
        uint64_t descriptor[4] = {1, 2, 3, 4};  
        std::bitset <256> tmp(ULONG_MAX);  
        for(size_t i = 0; i < m_nodes.size(); i++)  
        {  
            m_nodes[i] = tmp;  
        }  
    }  
    cout << "[ memory end ] "  << getMemoryUsage() << endl;  
}  
  
The result is:  
[ memory start ] 13  
[ memory end ] 13

---

## Comment 4

> Username: swatanabe  
> Created at: 2019-11-30 20:49:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51#issuecomment-560020086  

AMDG  
  
This is not a bug.  It's the normal behavior of memory allocation.  
Allocators do not return memory to the system when it is deallocated,  
as that would be horribly inefficient for multiple reasons.  Instead,  
the freed memory will be reused on the next allocation.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: robot-kimbo  
> Created at: 2019-12-01 07:46:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/51#issuecomment-560070842  

Ok, got it, thank you very much.
