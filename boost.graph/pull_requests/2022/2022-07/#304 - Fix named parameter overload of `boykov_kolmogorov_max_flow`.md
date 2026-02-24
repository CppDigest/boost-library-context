# #304 Fix named parameter overload of `boykov_kolmogorov_max_flow` [Merged]

> Username: sebrockm  
> Created at: 2022-07-25 00:14:08 UTC  
> Updated at: 2022-07-25 07:56:50 UTC  
> Merged at: 2022-07-25 01:44:49 UTC  
> Closed at: 2022-07-25 01:44:49 UTC  
> Url: https://github.com/boostorg/graph/pull/304  

Fixes #232

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-07-25 01:43:34 UTC  
> Url: https://github.com/boostorg/graph/pull/304#issuecomment-1193464557  

So I take it that test of the bundled properties failed until you fixed the return type of the named parameter overload?   
I just want to understand exactly what the change fixes. And why does it need to use `put` instead of `operator[]` now?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2022-07-25 01:45:05 UTC  
> Url: https://github.com/boostorg/graph/pull/304#issuecomment-1193465275  

And... thank you!

---

## Comment 3

> Username: sebrockm  
> Created_at: 2022-07-25 07:56:49 UTC  
> Url: https://github.com/boostorg/graph/pull/304#issuecomment-1193712746  

@jeremy-murphy Yes, the return type was the only error. It always expected the value type of the interior capacity property map, never the one provided via named parameters. (I sometimes hear people saying that they don't like `auto` as return type. From now on I will point them to this error 😅 )  
`put` instead of `operator[]` was honestly just a leftover from some early trials of investigation of this error. At some point I had a test that used a property map that didn't have `operator[]` and compilation failed. The test itself turned out to be non-constructive for identifying this issue, so I removed it. But I kept the `put` because I think writable property maps are not required to support `operator[]`, only `put`.

---
