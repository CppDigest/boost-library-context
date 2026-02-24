# #30 - singleton_pool::release_memory does not release chunks if free() is used [Closed]

> Username: jensbjorgensen  
> Created at: 2019-07-28 08:16:02 UTC  
> Updated at: 2019-07-30 05:11:04 UTC  
> Closed at: 2019-07-30 05:11:04 UTC  
> Url: https://github.com/boostorg/pool/issues/30  

I'm using singleton_pool and also want to use valgrind to check for memory leaks in my application. Unfortunately if free() is used to deallocate then when release_memory is called it does not deallocate a chunk because it checks to see if the free pointer refers to the first item in the chunk so unless you deallocate in reverse order you cannot free the memory. Since you're not deallocating until program exit maybe nobody cares but the documentation for release_memory should perhaps at least warn you about this.  
  
The included demonstration will assert.  
```  
#include <string>  
#include <string_view>  
#include <boost/pool/singleton_pool.hpp>  
#include <cassert>  
  
struct SomeDataPool_tag {};  
struct SomeData {  
    double floatNumber;  
    int integerNumber;  
    std::string stringValue;  
  
    static SomeData* allocate(double fn, int in, std::string_view sv);  
    static void deallocate(SomeData* sd);  
    static bool releaseMemory();  
};  
  
typedef boost::singleton_pool<SomeDataPool_tag, sizeof(SomeData)> SomeDataPool;  
SomeData* SomeData::allocate(double fn, int in, std::string_view sv) {  
    auto memp = SomeDataPool::malloc();  
    return new (memp) SomeData{fn, in, std::string(sv)};  
}  
void SomeData::deallocate(SomeData *sd) {  
    sd->~SomeData();  
    SomeDataPool::free(sd);  
}  
bool SomeData::releaseMemory() {  
    return SomeDataPool::release_memory();  
}  
  
int main() {  
    auto s1 = SomeData::allocate(3.14, 42, "hello, pool");  
    auto s2 = SomeData::allocate(2.17, 7, "hello again");  
    auto s3 = SomeData::allocate(-1.0, 1, "hello for the last time");  
  
    SomeData::deallocate(s2);  
    SomeData::deallocate(s1);  
    SomeData::deallocate(s3);  
    assert(SomeData::releaseMemory());  
    return 0;  
}  
```

---

## Comment 1

> Username: jensbjorgensen  
> Created at: 2019-07-30 05:11:04 UTC  
> Url: https://github.com/boostorg/pool/issues/30#issuecomment-516264288  

Ok, I didn't see before in the documentation found for pool (thought not in singleton_pool, but yeah I get it the singleton_pool wraps pool in singleton-ness) that indicates 'pool must be ordered'.
