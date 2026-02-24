# #2749 - base64::decoded_size improvement to avoid UB on malformed data [Closed]

> Username: denyskozyr  
> Created at: 2023-10-17 11:50:52 UTC  
> Updated at: 2023-10-22 13:41:39 UTC  
> Closed at: 2023-10-22 13:41:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2749  

base64 implementation has `decoded_size` function with comment about requirement to input data size:  
```  
/// Returns max bytes needed to decode a base64 string  
inline  
std::size_t constexpr  
decoded_size(std::size_t n)  
{  
    return n / 4 * 3; // requires n&3==0, smaller  
}  
```  
and `base64::decode` function requires valid memory:  
```  
    The memory pointed to by `out` points to valid memory  
    of at least `decoded_size(len)` bytes.  
 ```  
Basic base64-decoding function looks like this (copied from `base64_test`):  
```  
std::string  
base64_decode(string_view data)  
{  
	std::string dest;  
	dest.resize(base64::decoded_size(data.size()));  
	auto const result = base64::decode(  
		&dest[0], data.data(), data.size());  
	dest.resize(result.first);  
	return dest;  
}  
```  
There could be UB on malformed data. For example, for data with length 43 we allocate 30 bytes estimated by `base64::decoded_size`, then `base64::decode` outputs 32 bytes.  
  
Despite `decoded_size` in `detail` namespace, would be great to fix it and avoid such issues. Please consider to update it as fix looks trivial and removes requirement on input size:  
```  
/// Returns max bytes needed to decode a base64 string  
inline  
std::size_t constexpr  
decoded_size(std::size_t n)  
{  
    return (n + 3) / 4 * 3;  
}  
```

---

## Comment 1

> Username: fpelliccioni  
> Created at: 2023-10-17 12:34:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2749#issuecomment-1766328215  

I understand the concern. The comment `// requires n&3==0` is a precondition, meaning the function expects `n` to be a multiple of 4. Any Base64 string not meeting this is malformed.  
  
Violating this precondition can lead to unpredictable outcomes, known in C++ as "undefined behavior". This is problematic since it's not safe.  
  
While the suggested change to decoded_size might handle bad data better, it can also hide the fact the data is wrong. If we aim to handle wrong Base64 strings, the decoding function should detect and address these cases, not just work around them.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-10-17 14:14:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2749#issuecomment-1766509846  

The correct action is to change the comment to an assert.

---

## Comment 3

> Username: fpelliccioni  
> Created at: 2023-10-18 22:03:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2749#issuecomment-1769450354  

> The correct action is to change the comment to an assert.  
  
To add the assert, we'd have to make one of the following changes:  
  
- Remove `constexpr` from the function: This doesn't seem like a viable option.   
- Compile using the C++14 standard: This isn't a feasible option either.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-10-22 04:38:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2749#issuecomment-1773991925  

Then it is correct as-is
