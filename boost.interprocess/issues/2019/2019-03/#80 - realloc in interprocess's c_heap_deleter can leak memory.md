# #80 - realloc in interprocess's c_heap_deleter can leak memory [Closed]

> Username: mscottmueller  
> Created at: 2019-03-12 19:23:31 UTC  
> Updated at: 2019-03-13 21:55:26 UTC  
> Closed at: 2019-03-13 21:55:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/80  

In the following code, located in boost/interprocess/detail/win32_api.hpp, ::realloc may move the referenced memory block to a new location. If this happens, the memory is lost.  
  
```  
class c_heap_deleter  
{  
public:  
explicit c_heap_deleter(std::size_t size)  
   : m_buf(::malloc(size))  
{}  
  
~c_heap_deleter()  
{  
   if(m_buf) ::free(m_buf);  
}  
  
void realloc_mem(std::size_t num_bytes)  
{  
   void *buf = ::realloc(m_buf, num_bytes); // problem is here. If the memory is moved, the buffer is lost.  
   if(!buf){  
      ::free(m_buf);  
      m_buf = 0;  
   }  
}  
  
void *get() const  
{  return m_buf;  }  
  
   private:  
   void *m_buf;  
;  
```  
  
I would suggest re-writing the method like so:  
```  
void realloc_mem(std::size_t num_bytes)  
{  
   void *oldBuf = m_buf;  
   m_buf = ::realloc(m_buf, num_bytes);  
   if (!m_buf){  
      free(oldBuf);  
   }  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-13 21:55:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/80#issuecomment-472621542  

Many thanks for the report.
