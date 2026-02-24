# #256 - Checkpoint feature? [Open]

> Username: vinniefalco  
> Created at: 2020-03-01 18:05:14 UTC  
> Updated at: 2020-03-04 16:21:42 UTC  
> Url: https://github.com/boostorg/test/issues/256  

It might be nice to have a "checkpoint" function or macro which records the current location as an integer, incremented from a global counter. And then provide a means for the program to break when a particular checkpoint number is reached. An example of this feature can be found in Boost.JSON's "medium-weight test" framework:  
https://github.com/vinniefalco/json/commit/66246351ac67fb6c256ab99dc22f3c95cb8657f0

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-04 16:21:41 UTC  
> Url: https://github.com/boostorg/test/issues/256#issuecomment-594640649  

Turns out that the checkpoint feature can be entirely self-contained:  
  
```  
struct checkpoint  
{  
    char const* const file;  
    int const line;  
    std::size_t const id;  
  
    static  
    checkpoint*&  
    current() noexcept  
    {  
        static checkpoint* p = nullptr;  
        return p;  
    }  
  
    checkpoint(  
        char const* file_,  
        int line_,  
        std::size_t id_ = 0)  
        : file(file_)  
        , line(line_)  
        , id([]  
        {  
            static std::size_t n = 0;  
            return ++n;  
        }())  
        , prev_(current())  
    {  
        current() = this;  
        if(id_ == id)  
            debug_break();  
    }  
  
    ~checkpoint()  
    {  
        current() = prev_;  
    }  
  
private:  
    checkpoint* prev_;  
};  
  
#define BOOST_TEST_CHECKPOINT(...) ::checkpoint \  
        _BOOST_TEST_CHECKPOINT ## __LINE__ ( \  
            __FILE__, __LINE__, __VA_ARGS__ + 0)  
```  
  
The only part missing here is printing the current checkpoint on a failure.
