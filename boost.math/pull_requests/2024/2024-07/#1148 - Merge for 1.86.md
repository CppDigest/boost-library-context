# #1148 Merge for 1.86 [Merged]

> Username: mborland  
> Created at: 2024-07-02 14:11:00 UTC  
> Updated at: 2024-07-03 19:47:47 UTC  
> Merged at: 2024-07-03 19:47:46 UTC  
> Closed at: 2024-07-03 19:47:47 UTC  
> Url: https://github.com/boostorg/math/pull/1148  



---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-02 19:43:42 UTC  
> Url: https://github.com/boostorg/math/pull/1148#issuecomment-2204231290  

@sdarwin Can you please enable priviliged access in drone for this repo? TSAN seems to have issues with memory mapping

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-07-02 20:41:37 UTC  
> Url: https://github.com/boostorg/math/pull/1148#issuecomment-2204377183  

- boostorg/math was already set to "Trusted: Enables privileged container settings."  
- In .drone.star or .drone.jsonnet, the step also needs to use `privileged=True` or the equivalent. However that seems to be set.  
    
Try this in drone.sh:  
  
```  
if [[ $(uname) == "Linux" ]]; then  
    echo 0 | sudo tee /proc/sys/kernel/randomize_va_space  
fi  
  
```

---

## Comment 3

> Username: sdarwin  
> Created_at: 2024-07-03 11:00:30 UTC  
> Url: https://github.com/boostorg/math/pull/1148#issuecomment-2205802287  

And https://stackoverflow.com/questions/77850769/fatal-threadsanitizer-unexpected-memory-mapping-when-running-on-linux-kernels  
  
`sudo sysctl vm.mmap_rnd_bits=28` or `sudo sysctl vm.mmap_rnd_bits=30`

---
