# #83 - Doc request: indicate constexpr support for algorithms [Open]

> Username: hadrielk  
> Created at: 2021-01-06 13:57:56 UTC  
> Updated at: 2021-01-06 13:57:56 UTC  
> Url: https://github.com/boostorg/algorithm/issues/83  

Hi,  
I believe most if not all of the algorithms in `boost` are `constexpr` if the compiler supports it and C++14 or greater is used. (yes?)  
  
That's not indicated in the docs, as far as I can tell. I recently implemented a couple myself because I assumed the `boost` ones were not, from the docs; and at the time I didn't go look in the source code to see that they actually were. (yeah, I was being an idiot)
