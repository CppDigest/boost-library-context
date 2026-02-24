# #812 - Load json::value from unsigned char pointer memory [Closed]

> Username: DanielLazarHTDM  
> Created at: 2022-11-19 11:35:11 UTC  
> Updated at: 2022-11-22 06:00:17 UTC  
> Closed at: 2022-11-22 06:00:17 UTC  
> Url: https://github.com/boostorg/json/issues/812  

### Version of Boost 1.80  
Hello it is just a question. I have following code which is using memory pointer as a storage of json data. **jv** object is printed correctly. Also I printed out memory where (after external asci convert) are strings visible, so great it is filling my buffer as expected. But I am somehow missing how to get this pointer again and initialize second jsdon data **jv2** which wil use same memory. My intention is to create shared memory between two processes and one will fill it by json data and second will read it. Can't find it in your documentation how to "parse" it back from memory buffer. Thank you very much.  
  
```  
#include <boost/json.hpp>  
#include <iostream>  
#include <string>  
  
int main(){  
  unsigned char buf[ 4096 ];  
  memset(&buf, 0, 4096);  
  boost::json::static_resource mr( buf );  
  boost::json::value jv (&mr);  
  jv = {  
    { "pi", 3.141 },  
    { "happy", true },  
    { "name", "Boost" },  
    { "nothing", nullptr },  
    { "answer", {  
        { "everything", 42 } } },  
    {"list", {1, 0, 2}},  
    {"object", {  
        { "currency", "USD" },  
        { "value", 42.99 }  
            } }  
    };  
    
  boost::json::static_resource mr2( buf );  
  boost::json::value jv2 (&mr2);  
  std::cout << jv << std::endl;  
  std::cout << jv2 << std::endl;  
  
  for(unsigned char &c : buf) {  
    printf("%d, ", c);  
  }  
  printf("\n");  
}  
```  
  
#### Output  
jv: {"pi":3.141E0,"happy":true,"name":"Boost","nothing":null,"answer":{"everything":42},"list":[1,0,2],"object":{"currency":"USD","value":4.299E1}}  
jv2: null

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-19 11:45:14 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1320867041  

1) Using the same buffer for 2 different memory resources will almost certainly lead to undefined behaviour.  
2) jv2 is just a new null value that uses mr2 memory resource (it doesn't allocate, though, because there's nothing to allocate).  
3) It seems you are trying to somehow share the implementation details between 2 value objects. If you had succeeded, you would certainly get undefined behaviour.  
4) Why are you doing this? What's the goal?

---

## Comment 2

> Username: DanielLazarHTDM  
> Created at: 2022-11-19 12:01:13 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1320870037  

Thanks for your reply.   
  
Bassicaly I would like to use it in IPC as a container for data. Let's assume we have two processes.   
  
- First is writer and second readed.   
- Between them we have shared memory.  
  
1. So first process will take this shared memory and use it to fill it by some json data.  
2. Then using some IPC mutex or semaphore I will notify reader process => hey data is ready.  
3. Reader will take this pointer (const pointer to avoid writing into by this process) and I would like to wrap it by boost::json to have all possibilities to get data just using ["key"]. I will not write or push back or something. Just reading.  
4. So origin question is how to wrap this pointer into boost::json and use it in convience

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-11-19 15:07:11 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1320904421  

I don't think that's possible. The memory addresses wiill be different in different processes. You'd have to use something like [boost::interprocess::offset_ptr](https://www.boost.org/doc/libs/1_80_0/doc/html/interprocess/offset_ptr.html), but JSON does not work with fancy pointers. I can suggest either serialise the new state each time, or, if that's too costly, serialise _changes_ (e.g. look at [JSON Patch](https://datatracker.ietf.org/doc/html/rfc6902/)).

---

## Comment 4

> Username: DanielLazarHTDM  
> Created at: 2022-11-20 21:41:04 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1321249938  

So at the end. There is no possibility to reconstruct or parse boost::json::value from const unsigned char created previously by boost::json::value?  
I still don`t understand why it is not possible. I mean I used that buffer for creating json value inside. The buffer is somehow structured  => Again if I print out memory each byte I can see strings and some metadata I wrote there.   
So I would like to just do pseudo code:  
boost::json::value jv2 = parse_from_buffer(<const unsigned char*>);

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-11-21 08:45:52 UTC  
> Updated at: 2022-11-21 09:46:03 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1321704319  

Let's say your value has an array inside. Some memory storage was allocated for the array, and the address of the beginning of that storage is stored inside the array object. Now, this is an address in the virtual space of your process. Even if it's shared memory. Shared memory is mapped to the address space of the process and the mapping may not be identical. In another process the beginning address of the shared memory region may very well be completely different.   
  
So, returning to the address strored in the array. In one process it points to the storage that was allocated for array elements. In another it most likely points to something completely unrelated. For this reason exactly `boost::interprocess::offset_ptr` was created. But, as I've said before, Boost.JSON doesn't work with fancy pointers.  
  
If you still want to try it out, here's some (UNTESTED!) code that does it, assuming that the shared memory has the same starting address in both processes: https://godbolt.org/z/ddEW3z68j.

---

## Comment 6

> Username: DanielLazarHTDM  
> Created at: 2022-11-21 14:07:23 UTC  
> Url: https://github.com/boostorg/json/issues/812#issuecomment-1322118761  

Well, yeah you were right. Anyways thanks for the code at least your example is working in single process so json is "loaded" correctly. I did the example with two processes and shared memory and each process have different virtual address so I got segmentation fault on the reader process once I try to recast it. So at the end I will end up with serialise. I though that memory could be reconstructed only from buffer data (somehow mapping is stored here) I was not aware that it depends on addresses also.  
Again thank you for you effort.
