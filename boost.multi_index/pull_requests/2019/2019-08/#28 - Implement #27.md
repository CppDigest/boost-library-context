# #28 Implement #27. [Closed]

> Username: redboltz  
> Created at: 2019-08-05 03:36:31 UTC  
> Updated at: 2019-08-05 07:56:22 UTC  
> Closed at: 2019-08-05 07:56:21 UTC  
> Url: https://github.com/boostorg/multi_index/pull/28  

Add an overload of `erase()` with position and PreErase handler.

---

## Comment 1

> Username: redboltz  
> Created_at: 2019-08-05 03:45:51 UTC  
> Updated_at: 2019-08-05 05:18:20 UTC  
> Url: https://github.com/boostorg/multi_index/pull/28#issuecomment-518075211  

I implemented one overload of `erase()` that has `PreErase` handler. It gives a chance to move the element before erasing. I sent this PR for discussion. I respect `modify()` way to implement the `erase()` overload. The handler might be able to call at earlier phase.  
  
We can use this overload as follows:  
  
```cpp  
int main() {  
    mi_trace mi;  
    mi.insert(trace());  
    mi.insert(trace());  
    mi.insert(trace());  
  
    auto it = mi.begin();  
    std::optional<trace> target;  
  
    BOOST_ASSERT(mi.size() == 3);  
    mi.erase(  
        it,  
        [&](auto& e) {  
            target.emplace(std::move(e));  
        }  
    );  
    BOOST_ASSERT(mi.size() == 2);  
}  
```  
  
Any ideas?   
  
I am ready to refine the pull request. After implementation policy will be decided, I will add tests.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2019-08-05 07:56:21 UTC  
> Url: https://github.com/boostorg/multi_index/pull/28#issuecomment-518127929  

Closing as per https://github.com/boostorg/multi_index/issues/27#issuecomment-518127806

---
