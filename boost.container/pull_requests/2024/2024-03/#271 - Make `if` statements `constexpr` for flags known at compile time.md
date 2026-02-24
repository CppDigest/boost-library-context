# #271 Make `if` statements `constexpr` for flags known at compile time [Closed]

> Username: AndreyKulikov2022  
> Created at: 2024-03-28 18:03:25 UTC  
> Updated at: 2024-06-02 13:41:59 UTC  
> Closed at: 2024-05-23 21:30:55 UTC  
> Url: https://github.com/boostorg/container/pull/271  

I am trying to move-assign one container to the other:  
```  
void func(boost::container::deque<T>& deque1){  
boost::container::deque<T> deque2;  
// Fill deque2 with elements.  
...  
deque1 = std::move(deque2);  
}  
```  
If `T` is neither move nor copy constructable, this code does not compile. However, it should and compiles for `std` containers, because it is possible to just pass memory ownership from `deque2` to `deque1` since their allocators are the same. The problem is that the choice between transferring resources or moving the elements one by one is written using ordinary `if`. The compiler generates the error when checking the second option even though it is not used. With `if constexpr` instead of `if` the statement is resolved at compile time and no error is generated.  
  
I propose changing `if` statements for flags known at compile time to `if constexpr` using `BOOST_IF_CONSTEXPR`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2024-05-23 21:30:55 UTC  
> Url: https://github.com/boostorg/container/pull/271#issuecomment-2128051147  

Thanks for the report. Reviewing this patch I've found that many containers don't support non move-assignable types when allocator support is appropiate. I created an issue with the general problem and resolved it adding required container changes and modified tests. See:  
  
https://github.com/boostorg/container/issues/280

---

## Comment 2

> Username: AndreyKulikov2022  
> Created_at: 2024-06-02 13:41:58 UTC  
> Url: https://github.com/boostorg/container/pull/271#issuecomment-2143858966  

Thank you!

---
