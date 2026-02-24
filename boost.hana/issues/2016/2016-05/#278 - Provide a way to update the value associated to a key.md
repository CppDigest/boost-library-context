# #278 - [map] Provide a way to update the value associated to a key [Open]

> Username: ldionne  
> Created at: 2016-05-24 21:57:33 UTC  
> Updated at: 2016-05-25 16:18:51 UTC  
> Url: https://github.com/boostorg/hana/issues/278  

See [this](http://stackoverflow.com/q/37386345/627587) StackOverflow question for details.

---

## Comment 1

> Username: vittorioromeo  
> Created at: 2016-05-25 08:17:30 UTC  
> Url: https://github.com/boostorg/hana/issues/278#issuecomment-221504488  

Continuing from StackOverflow:  
  
> Do you need to change the type of the value? If not, and if your value can be assigned to, you can use map[key] = new_value or equivalently hana::at_key(map, key) = new_value, as hana::at_key returns a reference.   
  
Yes, I needed to change the type of the value.   
  
> hana::map could be made a Functor, but I'm not sure that transform could touch the keys of the map while still respecting the Functor laws.  
  
You could take a look at [Data.Map.Lazy](http://hackage.haskell.org/package/containers-0.5.5.1/docs/Data-Map-Lazy.html#t%3aMap) from Haskell.  
  
Its `map` is defined as:  
  
```  
map (++ "x") (fromList [(5,"a"), (3,"b")]) == fromList [(3, "bx"), (5, "ax")]  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2016-05-25 15:20:36 UTC  
> Url: https://github.com/boostorg/hana/issues/278#issuecomment-221610391  

Correct, but the function being mapped is only mapped on the values, and the keys are not provided to it. Hence, you still wouldn't be able to achieve what you want to achieve.

---

## Comment 3

> Username: vittorioromeo  
> Created at: 2016-05-25 15:51:16 UTC  
> Updated at: 2016-05-25 15:52:20 UTC  
> Url: https://github.com/boostorg/hana/issues/278#issuecomment-221619986  

@ldionne How about calling the function being mapped on the pairs?  
  
``` cpp  
// Pseudocode.  
template<typename TMap, typename TKey, typename TNewValue>  
auto replace(TMap m, TKey k, TNewValue nv)  
{  
    return hana::transform(m, [&](auto pair)  
        {  
            static_if(hana::first(pair) == k)   
            {  
                return hana::make_pair(hana::first(pair), nv));  
            }  
            static_else  
            {  
                return pair;  
            }  
        });  
}  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2016-05-25 16:18:51 UTC  
> Url: https://github.com/boostorg/hana/issues/278#issuecomment-221628269  

This is what I think would break the Functor laws, but I have failed to come up with an example so far. But my intuition tells me that if you can modify the keys, you can break the laws because you could take a map with n elements and make it into a 1-element map by mapping all the keys to the same thing. That doesn't seem very Functorial.
