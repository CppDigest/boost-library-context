# #1060 - Pool size is doubled on each ordered_malloc call [Open]

> Username: ptlopez  
> Created at: 2025-07-07 09:09:39 UTC  
> Updated at: 2025-07-16 06:25:33 UTC  
> Url: https://github.com/boostorg/boost/issues/1060  

Encountered a similar issue with: https://github.com/boostorg/pool/issues/54  when I upgraded from boost 1.70 to 1.88. Repeated use of purge_memory() results in pool size extension with two times the previous size when ordered_malloc is called.  
  
Sample Code:  
```  
int main()  
{  
    boost::pool<> bufferMemPool(sizeof(WCHAR), 32768);  
    for (int i = 0; i < 10; i++)  
    {  
        LPWSTR buffer = static_cast<LPWSTR>(bufferMemPool.ordered_malloc());  
        if (buffer) {  
            std::wcout << "get_next_size(): " << bufferMemPool.get_next_size() << std::endl;  
            bufferMemPool.ordered_free(buffer);  
        }  
        else {  
            std::wcerr << L"Memory allocation failed." << std::endl;  
        }  
  
        bufferMemPool.purge_memory();  
    }  
  
    return 0;  
}  
```  
  
Output:  
```  
get_next_size(): 65536  
get_next_size(): 131072  
get_next_size(): 262144  
get_next_size(): 524288  
get_next_size(): 1048576  
get_next_size(): 2097152  
get_next_size(): 4194304  
get_next_size(): 8388608  
get_next_size(): 16777216  
get_next_size(): 33554432  
```

---

## Comment 1

> Username: Lyeuiechang  
> Created at: 2025-07-16 06:25:33 UTC  
> Url: https://github.com/boostorg/boost/issues/1060#issuecomment-3077055498  

Cool stuff.😎  Wandering the best practices when using purge_memory() versus free().
