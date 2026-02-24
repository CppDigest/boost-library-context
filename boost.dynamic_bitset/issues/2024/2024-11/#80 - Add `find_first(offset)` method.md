# #80 - Add `find_first(offset)` method [Closed]

> Username: Siapran  
> Created at: 2024-11-18 22:46:21 UTC  
> Updated at: 2024-11-26 18:59:19 UTC  
> Closed at: 2024-11-26 18:59:19 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/80  

Motivating example:  
  
```cpp  
void put_chunk(Packet const& chunk)  
{  
    std::memcpy(  
        message.buffer + chunk.position * CHUNK_SIZE,  
        chunk.data, CHUNK_SIZE);  
    missing.reset(chunk.position);  
}  
  
bool complete()  
{  
    received =  
        (received == 0 ? missing.find_first()  
                       : missing.find_next(received - 1));  
    return received == boost::dynamic_bitset<>::npos;  
}  
```  
with `find_first(offset)`, `complete()` becomes:  
```cpp  
bool complete()  
{  
    received = missing.find_first(received);  
    return received == boost::dynamic_bitset<>::npos;  
}  
```
