# #120 - Transpose for Rank One Tensors [Open]

> Username: amitsingh19975  
> Created at: 2021-05-28 11:31:30 UTC  
> Updated at: 2023-07-18 15:58:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/120  

As of now, the rank one tensor does not allow to be transposed.  
  
```cpp  
auto const n = extents<>{3};  
auto a = tensor_dynamic{n, 1.f};  
auto c = tensor_dynamic{n};  
std::vector<std::size_t> pi{3};  
ublas::trans( 1ul, n.data(), pi.data(), c.data(), c.strides().data(),    a.data(), res.strides().data() ); // c = [0,0,0]  
```

---

## Comment 1

> Username: WilliamTambellini  
> Created at: 2023-07-18 15:58:05 UTC  
> Url: https://github.com/boostorg/ublas/issues/120#issuecomment-1640505330  

same request here.  
Would also call for handling rank n tensors by transposing per slice.
