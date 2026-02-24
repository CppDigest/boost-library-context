# #81 Decoupling the tensor into three different tensor types [Merged]

> Username: amitsingh19975  
> Created at: 2020-04-21 20:18:10 UTC  
> Updated at: 2020-05-03 22:09:12 UTC  
> Merged at: 2020-05-03 14:14:23 UTC  
> Closed at: 2020-05-03 14:14:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/81  

Disintegrating the tensor into `static_tensor`, `dynamic_tensor` and `fixed_rank_tensor` and also refactoring code.

---

## Comment 1

> Username: bassoy  
> Created_at: 2020-04-22 08:05:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-617620969  

Could you please rerun travis. One of the build jobs could not execute  
`sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test`

---

## Comment 2

> Username: amitsingh19975  
> Created_at: 2020-04-22 10:55:56 UTC  
> Updated_at: 2020-04-22 10:56:24 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-617705710  

> Could you please rerun travis. One of the build jobs could not execute  
> `sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test`  
  
I don't have write access to the repo so I can not rerun it without commit or pull request.

---

## Comment 3

> Username: bassoy  
> Created_at: 2020-04-22 14:02:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-617798168  

> > Could you please rerun travis. One of the build jobs could not execute  
> > `sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test`  
>   
> I don't have write access to the repo so I can not rerun it without commit or pull request.  
  
Yes. Could you include separate examples for `static_tensor` and `fixed_rank_tensor` ?

---

## Comment 4

> Username: amitsingh19975  
> Created_at: 2020-04-22 15:51:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-617864153  

> > > Could you please rerun travis. One of the build jobs could not execute  
> > > `sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test`  
> >   
> >   
> > I don't have write access to the repo so I can not rerun it without commit or pull request.  
>   
> Yes. Could you include separate examples for `static_tensor` and `fixed_rank_tensor` ?  
  
I just added and separated the examples.

---

## Review 5 [Changes requested]

> Username: bassoy  
> Created_at: 2020-04-22 17:52:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-398406214  

Minor changes required.  
Please review all 17 comments and reply to them.

📁 examples/tensor/fixed_rank_prod_expressions.cpp

```diff
  30 |+     {
  31 |+ 
  32 |+         auto n = dynamic_extents<3>{3,4,2};
```

> Username: bassoy  
> Created_at: 2020-04-22 17:16:37 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413167534  

Recommend to place  
```cpp  
using extents_3_t = dynamic_extents<3>;  
using extents_4_t = dynamic_extents<4>;  
```  
after line 27 and use those types.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:06:07 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413203324  

ok

---

📁 examples/tensor/fixed_rank_prod_expressions.cpp

```diff
  37 |+         // C1(j,k) = T2(j,k) + A(i,j,k)*T1(i);
  38 |+         q = 1u;
  39 |+         tensor_t C1 = matrix_t(n[1],n[2],2) + prod(A,vector_t(n[q-1],1),q);
```

> Username: bassoy  
> Created_at: 2020-04-22 17:17:25 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413168107  

Can we use `auto` instead of `tensor_t` and get rid of the `using` expression in line 34?

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:08:15 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413204745  

this returns a tensor_expression which I don't think without casting it back to tensor we can use `std::cout`.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:46:38 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413231389  

can we have `operator<<` overloading for printing `tensor_expression`?

> Username: bassoy  
> Created_at: 2020-04-22 20:07:45 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413292000  

> this returns a tensor_expression which I don't think without casting it back to tensor we can use `std::cout`.  
  
Yes, I forgot. I am okay with that then.  
  
> can we have `operator<<` overloading for printing `tensor_expression`?  
  
Not sure if we its overloaded for `tensor_expression`. Lets mark it as an enhancment issue for later.

> Username: amitsingh19975  
> Created_at: 2020-04-22 20:47:28 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413320649  

I think Ashar's expression template will enhance it.

---

📁 examples/tensor/fixed_rank_prod_expressions.cpp

```diff
  79 |+         // C1(l,j,k) = T2(l,j,k) + A(i,j,k)*T1(l,i);
  80 |+         q = 1u;
  81 |+         tensor_t C1 = fixed_rank_tensor( dynamic_extents<3>{m,n[1],n[2]},value_t(2) ) + prod(A,matrix_t(m,n[q-1],1),q);
```

> Username: bassoy  
> Created_at: 2020-04-22 17:18:12 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413168668  

See previous comments.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:09:06 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413205269  

same as above.

---

📁 examples/tensor/fixed_rank_prod_expressions.cpp

```diff
 127 |+         auto na = dynamic_extents<3>{3,4,5};
 128 |+         auto nb = dynamic_extents<4>{4,6,3,2};
 129 |+         auto A = fixed_rank_tensor(na,value_t(2));
```

> Username: bassoy  
> Created_at: 2020-04-22 17:18:26 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413168849  

See previous comments.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:09:17 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413205473  

done.


📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
  22 |+ #include <stdexcept>
  23 |+ #include <vector>
  24 |+ #include <array>
```

> Username: bassoy  
> Created_at: 2020-04-22 17:23:27 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413172325  

Can you please check if we need that many headers?

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:14:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413209438  

the only redundant headers `string` and `numeric`

> Username: bassoy  
> Created_at: 2020-04-22 20:09:23 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413293030  

why does `fixed_rank_extents` require `std::vector` ?

> Username: amitsingh19975  
> Created_at: 2020-04-22 20:11:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413294057  

it is to convert `fixed_rank_extents` to `dynamic_extents`.

> Username: bassoy  
> Created_at: 2020-04-22 20:25:04 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413304524  

I recommend not to put a conversion operation inside `fixed_rank_extents` and define the free function where you need it. I remember that you needed it for some stride computations. I would put it in there as a free function or even just use `std::copy` if it is needed at that place.

> Username: amitsingh19975  
> Created_at: 2020-04-22 20:44:15 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413316969  

I removed it because I forgot I added a constructor that could accept any extents.


📁 include/boost/numeric/ublas/tensor/fixed_rank_strides.hpp

```diff
  22 |+ #include <initializer_list>
  23 |+ #include <algorithm>
  24 |+ #include <cassert>
```

> Username: bassoy  
> Created_at: 2020-04-22 17:24:15 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413172873  

Do we need that many headers here?

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:39:37 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413226666  

I removed them as they are included through `fixed_rank_exents.hpp`


📁 include/boost/numeric/ublas/tensor/fixed_rank_tensor.hpp

```diff
 253 |+     struct result_tensor< V, basic_fixed_rank_extents<T,R>, F >{
 254 |+         using type = fixed_rank_tensor< V, R, F >;
 255 |+     };
```

> Username: bassoy  
> Created_at: 2020-04-22 17:26:27 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413174558  

Could this be placed in a different file with all tensor traits ?  
Also I would like to see it in `boost::numeric::ublas` not in detail namespace.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:43:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413229077  

This style allows us to easily remove and add *.hpp or makes code more module so if you do  not include then you do not need to know about it.

> Username: bassoy  
> Created_at: 2020-04-22 20:54:13 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413325492  

Ok.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_tensor.hpp

```diff
 100 |+             for(auto i = size_type{}; i < sz; ++i){
 101 |+                 super_type::data_[i] = std::move(v.data()[i]);
 102 |+             }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:49:19 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413191204  

1. I recommend to use the `std::move` algorithm  
2. I recommend to immediately initialize the `super_type` with the data in the initialization list `:`.

> Username: amitsingh19975  
> Created_at: 2020-04-22 20:45:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413317700  

1. Done

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_tensor.hpp

```diff
 109 |+                                             " : rank of extents are not correct, please check!");
 110 |+ 
 111 |+             std::copy(v.data().begin(), v.data().end(), super_type::begin());
```

> Username: bassoy  
> Created_at: 2020-04-22 17:50:32 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413192067  

1. I recommend to immediately initialize the `super_type` with the data in the initialization list `:`.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:32:35 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413221944  

Then I have to do the same this in `basic_tensor` and then pass the vector or matrix to it.

> Username: bassoy  
> Created_at: 2020-04-22 20:11:47 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413294598  

Understand. I am okay with your approach. We can think of it later on.  
I was thinking of decoupling the interoperability between matrix/vector and tensor using free function like `to_tensor` or `make_tensor` which would be perhaps more generic?

> Username: amitsingh19975  
> Created_at: 2020-04-22 20:46:19 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413319398  

ok.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_tensor.hpp

```diff
 123 |+             for(auto i = size_type{}; i < sz; ++i){
 124 |+                 super_type::data_[i] = std::move(v.data()[i]);
 125 |+             }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:50:48 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413192242  

1. I recommend to use the `std::move` algorithm  
2. I recommend to immediately initialize the `super_type` with the data in the initialization list `:`.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:33:43 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413222722  

1. Ok  
2. Same as above.


📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
 177 |+ struct is_static_rank< basic_static_extents<T, E...> > : std::true_type {};
 178 |+ 
 179 |+ } // namespace boost::numeric::ublas
```

> Username: bassoy  
> Created_at: 2020-04-22 17:30:12 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413177263  

1. I recommend not to put in `detail` namespace.  
2. Could we place it in a file called `extents_traits.hpp` with all the other extents types ?

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:43:44 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413229442  

1. Ok  
2. same as above


📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 306 |+ struct is_static_rank< basic_static_strides< basic_static_extents<T, E...>, L > > : std::true_type {};
 307 |+ 
 308 |+ } // namespace boost::numeric::ublas
```

> Username: bassoy  
> Created_at: 2020-04-22 17:30:36 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413177632  

1. I recommend not to put in `detail` namespace.  
2. Could we place it in a file called `strides_traits.hpp` with all the other strides traits types ?

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:44:24 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413229877  

1. Ok  
2. same as above


📁 include/boost/numeric/ublas/tensor/static_tensor.hpp

```diff
 265 |+     };
 266 |+ 
 267 |+ } // namespace boost::numeric::ublas::detail
```

> Username: bassoy  
> Created_at: 2020-04-22 17:32:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413179083  

1. I recommend not to put in `detail` namespace.  
2. Could we place it in a file called `tensor_traits.hpp` with all the other tensor traits types ?

---

📁 include/boost/numeric/ublas/tensor/static_tensor.hpp

```diff
 182 |+             for( auto i = size_type(0); i < other.size(); ++i ){
 183 |+                 super_type::data_[i] = std::move(other[i]);
 184 |+             }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:38:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413183270  

Instead of a `for` loop, I recommend to use `std::move` algorithm.

---

📁 include/boost/numeric/ublas/tensor/static_tensor.hpp

```diff
 138 |+             for(auto i = size_type{}; i < sz; ++i){
 139 |+                 super_type::data_[i] = std::move(v.data()[i]);
 140 |+             }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:38:22 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413183416  

Instead of a `for` loop, I recommend to use `std::move` algorithm.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:44:47 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413230137  

done.

---

📁 include/boost/numeric/ublas/tensor/static_tensor.hpp

```diff
 105 |+             for(auto i = size_type{}; i < sz; ++i){
 106 |+                 super_type::data_[i] = std::move(v.data()[i]);
 107 |+             }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:38:30 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413183518  

Instead of a `for` loop, I recommend to use `std::move` algorithm.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:26:04 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413217581  

done.


📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 610 |+     void serialize(Archive & ar, const unsigned int /* file_version */){
 611 |+         ar & serialization::make_nvp("data",data_);
 612 |+     }
```

> Username: bassoy  
> Created_at: 2020-04-22 17:42:06 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413186031  

I recommend to delete this serialization function.  
It should be first defined as a project.

> Username: amitsingh19975  
> Created_at: 2020-04-22 18:27:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413218190  

Ok.


---

## Review 6 [Approved]

> Username: bassoy  
> Created_at: 2020-04-22 21:09:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-398587192  

I have a small nitpicking remark regarding your `std::cout` usage in the tensor example.  
Otherwise, code looks even much cleaner than your previous pull request.  
Good job.

📁 examples/tensor/fixed_rank_tensor.cpp

```diff
  85 |+     std::cout<<"( t4 == t3 ) => "<<( t4 == t3 ? "True" : "False")<<'\n';
  86 |+     
  87 |+     std::cout<<"( t5 == dt1 ) => "<<( t5 == dt1 ? "True" : "False")<<'\n';
```

> Username: bassoy  
> Created_at: 2020-04-22 21:03:13 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413331012  

I would use `std::endl` and `std::boolalpha` and rewrite  
```cpp  
std::cout<< "(t4== t3) => "<< std::boolalpha << (t4==t3) << std::endl;  
```  
Its more readable and there is really big difference in the assembler output:  
See [godbolt](https://godbolt.org/z/N3xEQ4)

> Username: amitsingh19975  
> Created_at: 2020-04-23 11:55:04 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413751606  

I have changed it. I was using `'\n'` because `std::endl` does two tasks the first one is new line and the second one is flush and I did not want to use flush and in case of `std::boolalpha` it will change of the `std::cout` and if you want to revert back you again have to set the state. I think `std::format` will be a better replacement for formating than `std::cout`.

> Username: bassoy  
> Created_at: 2020-04-23 12:56:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413792096  

It am nitpicking here. I would prefer `std::boolalpha`. This  
```cpp  
    std::cout << std::boolalpha;  
    std::cout << "(t4==t3)" << (t4==t3) << std::endl;  
    std::cout << "(t5==t2)" << (t5==t2) << std::endl;  
```  
easier to read. We can still rewrite it at a later time using `std::format`.


📁 examples/tensor/static_tensor.cpp

```diff
  75 |+     std::cout<<"( t4 == t3 ) => "<<( t4 == t3 ? "True" : "False")<<'\n';
  76 |+     
  77 |+     std::cout<<"( t5 == dt1 ) => "<<( t5 == dt1 ? "True" : "False")<<'\n';
```

> Username: bassoy  
> Created_at: 2020-04-22 21:03:47 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413331337  

Same comment as in `examples/tensor/fixed_rank_tensor.cpp`.

> Username: amitsingh19975  
> Created_at: 2020-04-23 11:55:52 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r413752110  

Same as [here](https://github.com/boostorg/ublas/pull/81#discussion_r413331012).


---

## Review 7 [Approved]

> Username: bassoy  
> Created_at: 2020-04-23 12:57:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-399071580  

Thanks for the change.  IMO, code looks better.

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2020-04-24 16:42:05 UTC  
> Updated_at: 2020-05-02 14:49:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-619122351  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=h1) Report  
> Merging [#81](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/e68819c645ae85aa3d432269edf467024d2793f8&el=desc) will **increase** coverage by `1.09%`.  
> The diff coverage is `64.11%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/81/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #81      +/-   ##  
===========================================  
+ Coverage    66.47%   67.57%   +1.09%       
===========================================  
  Files           29       33       +4       
  Lines         1414     1437      +23       
  Branches       544      534      -10       
===========================================  
+ Hits           940      971      +31       
+ Misses          70       69       -1       
+ Partials       404      397       -7       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/numeric/ublas/tensor/static\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9zdGF0aWNfZXh0ZW50cy5ocHA=) | `100.00% <ø> (+12.00%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `44.14% <44.14%> (+5.31%)` | :arrow_up: |  
| [.../numeric/ublas/tensor/detail/extents\_functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9kZXRhaWwvZXh0ZW50c19mdW5jdGlvbnMuaHBw) | `62.50% <46.15%> (-6.95%)` | :arrow_down: |  
| [...clude/boost/numeric/ublas/tensor/static\_tensor.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9zdGF0aWNfdGVuc29yLmhwcA==) | `50.00% <50.00%> (ø)` | |  
| [include/boost/numeric/ublas/tensor/algorithms.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9hbGdvcml0aG1zLmhwcA==) | `53.50% <50.92%> (ø)` | |  
| [...oost/numeric/ublas/tensor/operators\_comparison.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9vcGVyYXRvcnNfY29tcGFyaXNvbi5ocHA=) | `61.66% <61.01%> (ø)` | |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `65.00% <62.16%> (ø)` | |  
| [...e/boost/numeric/ublas/tensor/fixed\_rank\_tensor.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX3RlbnNvci5ocHA=) | `66.66% <66.66%> (ø)` | |  
| [...boost/numeric/ublas/tensor/multi\_index\_utility.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aV9pbmRleF91dGlsaXR5LmhwcA==) | `72.22% <66.66%> (ø)` | |  
| [include/boost/numeric/ublas/tensor/strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9zdHJpZGVzLmhwcA==) | `83.33% <66.66%> (+8.33%)` | :arrow_up: |  
| ... and [17 more](https://codecov.io/gh/boostorg/ublas/pull/81/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=footer). Last update [e68819c...cf01479](https://codecov.io/gh/boostorg/ublas/pull/81?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 9 [Changes requested]

> Username: bassoy  
> Created_at: 2020-04-29 08:08:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-402430037  

I think that the decoupling has become very valuable.  
I recommend to decouple (extents/strides) traits and their free functions by putting them in a separate file(s). This will help to speclialize those traits and functions.

📁 include/boost/numeric/ublas/tensor/dynamic_extents.hpp

```diff
 241 |- template <class T>
 242 |- struct is_dynamic_rank< basic_extents<T> > : std::true_type {};
 271 |+     } // namespace detail
```

> Username: bassoy  
> Created_at: 2020-04-29 07:43:24 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417124687  

I recommend to put the traits in a separate file?  
This would simplify the specialization of those traits for different types.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:21:30 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417211213  

ok, I will create separate files.

---

📁 include/boost/numeric/ublas/tensor/dynamic_extents.hpp

```diff
 214 |+             return false;
 215 |+         }else{
 216 |+             return std::equal(this->begin(), this->end(), rhs.begin());
```

> Username: bassoy  
> Created_at: 2020-04-29 07:52:48 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417129702  

I recommend to simplify the statement here:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:26:52 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417213997  

same as [here](https://github.com/boostorg/ublas/pull/81#discussion_r417212093)


📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
 242 |+         };
 243 |+         
 244 |+     } // detail
```

> Username: bassoy  
> Created_at: 2020-04-29 07:44:18 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417125121  

I recommend to put traits in a separate file.  
Reason is the same as for `dynamic_extents`

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:21:49 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417211404  

same as above.


📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
 222 |+         };
 223 |+ 
 224 |+     } // namespace detail
```

> Username: bassoy  
> Created_at: 2020-04-29 07:45:00 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417125442  

I recommend to put the traits in a separate file?  
This would simplify the specialization of those traits for different types.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:22:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417211528  

same as above.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
 184 |+             return false;
 185 |+         }else{
 186 |+             return std::equal(this->begin(), this->end(), rhs.begin());
```

> Username: bassoy  
> Created_at: 2020-04-29 07:48:33 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417127432  

I recommend to simplify the statement here:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:23:13 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417212135  

same as above.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
 184 |-     constexpr bool operator==(basic_fixed_rank_extents const &other) const {
 185 |-         return _base == other._base;
 180 |+     constexpr bool operator==(Extents const& rhs) const noexcept{
```

> Username: bassoy  
> Created_at: 2020-04-29 07:52:08 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417129354  

I recommend to have the comparison operation and other functions for extents (also for strides) in a separate file.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:26:05 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417213580  

That's what `extents.hpp` serving for. It has comparison operators in it.

> Username: bassoy  
> Created_at: 2020-04-29 10:50:05 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417225931  

Okay. But then I would call it `extents_functions` or something, not including any of the specific `extents_xxxx` header. I would rather include `extents_functions` header in `extents_xxxx` header.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:51:15 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417226523  

why not put it inside `extents_function.hpp`

> Username: bassoy  
> Created_at: 2020-04-29 11:23:19 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417242115  

Putting `static_extents.hpp` and `dynamic_extents.hpp`  in `extents_functions.hpp` requires `extents_functions.hpp` to be changed everytime you include a new `xxxx_extents.hpp`.  
  
By including `extents_functions.hpp` in `xxxx_extents.hpp` you do not need to change `extents_functions.hpp`, only `xxxx_extents.hpp` (which you added and changed anyways).  
  
Also if you include `xxxx_extents.hpp` you hope that you get all functionality (including functions in  `extents_functions.hpp`).

> Username: amitsingh19975  
> Created_at: 2020-04-29 11:27:03 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417243906  

I was thinking of putting `operator==(LExtents const&, RExtents const& )` which is templated so the only thing that is to be changed is `is_extents`.

> Username: bassoy  
> Created_at: 2020-04-29 12:38:55 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417281896  

okay. let's try it.  
do you still need to statically check for `is_extents` as you are using `enable_if`?

> Username: amitsingh19975  
> Created_at: 2020-04-29 12:49:23 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417288157  

nope, that was just there to remind that it can be replaced with `concepts`.

> Username: amitsingh19975  
> Created_at: 2020-04-29 12:52:58 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417290354  

should I combine both strides and extents `operator==` into one? In the `extents_functions.hpp`

> Username: bassoy  
> Created_at: 2020-04-30 12:59:56 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417990451  

I recommend to combine all required traits for `xxxx_extents` in one `extents_traits.hpp` file.  
I think you have provided separate classes. I would not put those in separate files.

> Username: amitsingh19975  
> Created_at: 2020-04-30 13:05:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417993632  

I have separated the traits for extents into individual files `xxxx_extents.hpp` and then included it in `type_traits.hpp`


📁 include/boost/numeric/ublas/tensor/fixed_rank_strides.hpp

```diff
 214 |+ 
 215 |+     template <class T, std::size_t R, class L>
 216 |+     struct is_static_rank< basic_fixed_rank_strides<T,R,L> > : std::true_type {};
```

> Username: bassoy  
> Created_at: 2020-04-29 07:45:31 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417125722  

I recommend to put the traits in a separate file.  
This would simplify the specialization of those traits for different types.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:22:11 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417211613  

same as above.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_strides.hpp

```diff
 188 |+             return std::equal(this->begin(), this->end(), rhs.begin());
 189 |+         }
 190 |+     }
```

> Username: bassoy  
> Created_at: 2020-04-29 07:47:04 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417126630  

I recommend to simplify the statement:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:23:07 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417212093  

I was going for readability.

> Username: bassoy  
> Created_at: 2020-04-29 10:47:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417224428  

I prefer one-liners with less control-flow.  
But this is preference.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:48:22 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417225051  

ok.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_strides.hpp

```diff
 187 |+         }else{
 188 |+             return std::equal(this->begin(), this->end(), rhs.begin());
 189 |+         }
```

> Username: bassoy  
> Created_at: 2020-04-29 07:53:09 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417129879  

I recommend to simplify the statement here:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:27:06 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417214101  

same as [here](https://github.com/boostorg/ublas/pull/81#discussion_r417212093)


📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
  99 |-       return std::equal(begin(), end(), rhs.begin());
 102 |+         return std::equal(this->begin(), this->end(), rhs.begin());
 103 |     }
```

> Username: bassoy  
> Created_at: 2020-04-29 07:53:39 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417130157  

I recommend to simplify the statement here:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:27:11 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417214152  

same as [here](https://github.com/boostorg/ublas/pull/81#discussion_r417212093)


📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 222 |+       return;
 223 |+     }else{
 224 |+       static_assert( static_traits::is_valid_v<extents_type>, "Error in boost::numeric::ublas::basic_static_strides() : shape is not valid.");
```

> Username: bassoy  
> Created_at: 2020-04-29 07:59:53 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417133573  

I recommend to have the `static_assert`s not inside the `constexpr` statement.  
Better would be:  
```cpp  
static_assert(_size == 0 || static_traits::is_valid_v<extents_type>,   
"Error in boost::numeric::ublas::basic_static_strides() : shape is not valid.");  
```  
and  
```cpp  
static_assert(  
static_traits::is_vector_v<extents_type> || static_traits::is_scalar_v<extents_type> || _size >= 2,  
"Error in boost::numeric::ublas::basic_static_strides() : size of strides must be greater or equal 2."  
);  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:29:35 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417215371  

but the problem is if the size is 0 it will assert. That is the reason I have put it inside the constexpr if.

> Username: bassoy  
> Created_at: 2020-04-29 10:51:10 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417226465  

> but the problem is if the size is 0 it will assert. That is the reason I have put it inside the constexpr if.  
  
the `static_assert` respects that  
`_size == 0 || static_traits::is_valid_v<extents_type>`

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:52:38 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417227205  

ok.

---

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 276 |     }else{
 275 |-       return std::equal(begin(), end(), rhs.begin());
 277 |+         return std::equal(this->begin(), this->end(), rhs.begin());
```

> Username: bassoy  
> Created_at: 2020-04-29 08:00:18 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417133782  

I recommend to simplify the statement here:  
```cpp  
return this->size() == rhs.size() && return std::equal(this->begin(), this->end(), rhs.begin());  
```

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:29:42 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417215436  

same as [here](https://github.com/boostorg/ublas/pull/81#discussion_r417212093)

---

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 300 |+ 
 301 |+   template <class T, T... E, class L>
 302 |+   struct is_static_rank< basic_static_strides< basic_static_extents<T, E...>, L > > : std::true_type {};
```

> Username: bassoy  
> Created_at: 2020-04-29 08:01:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417134162  

I recommend to put the traits in a separate file.  
This would simplify the specialization of those traits for different types.

> Username: amitsingh19975  
> Created_at: 2020-04-29 10:29:52 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r417215512  

ok.


---

## Review 10 [Changes requested]

> Username: bassoy  
> Created_at: 2020-04-30 14:43:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-403533085  

There are some nitpicking change requests regarding formatting and (my personal opinion) better reading.  
The header files for extents type traits should be inside one single header file. However, I am not sure if we should have one single `type_traits` or not.

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 191 | inline
 192 | std::string to_string(T const &e) {
```

> Username: bassoy  
> Created_at: 2020-04-30 13:44:15 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418020309  

I recommend to use the following code:  
```cpp  
std::stringstream ss;  
ss << "[ ";   
std::copy(v.begin(),v.end(), std::ostream_iterator<typename T::value_type>(ss," ") );   
ss << "];";  
return ss.str();  
```

> Username: amitsingh19975  
> Created_at: 2020-05-01 13:46:22 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418547424  

do you want comma-separated or space-separated?

> Username: bassoy  
> Created_at: 2020-05-01 15:30:59 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418593246  

I do not care. Matlab eats both.

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:31:33 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418593515  

ok.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 163 | 
 164 |   using size_type = typename ExtentsType::size_type;
 165 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:03:06 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418034255  

I recommend to use the following code:  
```cpp  
if   ( e.size() ==1u) return e[0] == 1u;  
else ( e.size() > 1u) return std::all_of(e.begin(), e.end(), [](auto a) { return a > 0u; });  
else                  return false;  
```  
Why because it makes your code more readable.   
using `1u` instead of `size_type(1)` is save as we know that `deltype(e.size())` should be an unsigned type  
using `1u` instead of `value_type(1)` is save we know that `deltype(e.front())` should be an unsigned type  
etc.

> Username: amitsingh19975  
> Created_at: 2020-05-01 13:48:10 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418548154  

This was to support any type extents even if a user creates his own he/she might change the type of size.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:56:43 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418578086  

I can not use 1u because we do not know the type of extents.

> Username: bassoy  
> Created_at: 2020-05-01 15:30:29 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418593031  

but you know that (or we should guarantee that)  
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::size_type> == true  
```  
and  
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::value_type> == true  
```  
So comparing with values with `1u` should always work even if number of bits differ.

> Username: amitsingh19975  
> Created_at: 2020-05-02 09:01:14 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418933783  

Changed it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 223 | 
 224 |   using size_type = typename ExtentsType::size_type;
 225 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:07:13 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418037205  

I recommend to use:  
```cpp  
return !e.empty() && std::all_of(e.begin(), e.end(), [](auto a) { return a==1u; });  
```

> Username: amitsingh19975  
> Created_at: 2020-05-01 13:52:04 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418549770  

this was to make code more readable.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:55:32 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418577589  

and I can not use `1u` because we do not know the type of extents.

> Username: bassoy  
> Created_at: 2020-05-01 15:27:03 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418591492  

but you know that (or we should guarantee that)  
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::size_type> == true  
```  
and  
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::value_type> == true  
```  
So comparing with values with `1u` should always work even if number of bits differ.

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:29:39 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418592651  

I was also considering if a user created his own extents he might make `signed` extents.

> Username: bassoy  
> Created_at: 2020-05-01 16:13:52 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418612975  

Valid point. However, I see   
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::size_type> == true  
```  
a necessity, therefore a `static_assert`. If   
```cpp  
is_unsigned_v<typename extents_traits<extents_t>::size_type> == false  
```  
then the user should get a warning. Signed extents or negative extents do not make much sense. However, the user might have chosen signedness because of carelessness or special behavior for negative numbers.   
  
So I am okay with warnings for signed extents. Strides should be stricter and should enfore unsignedness of `value_type`

> Username: amitsingh19975  
> Created_at: 2020-05-02 09:00:57 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418933762  

I will change it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 243 | 
 244 |   using size_type = typename ExtentsType::size_type;
 245 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:15:40 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418043189  

I like the algorithm but I recommend stylisticly to have sth like.   
```cpp  
constexpr auto greater_one = [](auto a){ return a>1u; };   
constexpr auto equal_one   = [](auto a){ return a==1u; };   
  
if      (e.size() == 0u) return false;  
else if (e.size() == 1u) return e[0]>1u;  
return /* as you have */  
```

> Username: amitsingh19975  
> Created_at: 2020-05-02 05:05:13 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418900139  

ok

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 280 | 
 281 |   using size_type = typename ExtentsType::size_type;
 282 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:23:37 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418048975  

Same as above.

> Username: amitsingh19975  
> Created_at: 2020-05-02 05:05:17 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418900165  

ok

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 311 |   
 312 |   using size_type = typename ExtentsType::size_type;
 313 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:23:45 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418049072  

Same as above. Suggest one-liners.

> Username: amitsingh19975  
> Created_at: 2020-05-02 05:05:23 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418900208  

ok

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 357 |+   static_assert(is_extents_v<ExtentsType>, "boost::numeric::ublas::product() : invalid type, type should be an extents");
 358 |   
 359 |   using value_type = typename ExtentsType::value_type;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:24:01 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418049330  

Same as above. Suggest one-liners.

> Username: amitsingh19975  
> Created_at: 2020-05-02 05:09:01 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418901582  

ok


📁 include/boost/numeric/ublas/tensor/dynamic_tensor.hpp

```diff
 225 |+     struct result_tensor< V, dynamic_extents<>, F >{
 226 |+         using type = dynamic_tensor< V, F >;
 227 |+     };
```

> Username: bassoy  
> Created_at: 2020-04-30 14:27:46 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418052200  

Should be inside a `type_traits` header.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:24:19 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418563382  

will do once we have better type_traits structure. [Here](https://github.com/boostorg/ublas/pull/81#discussion_r418562362) is the question we need to answer.


📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  61 |+             auto ret = dynamic_extents< std::max( size_type( E::_size - 1 ), size_type(2) )>();
  62 |+             ret.fill(typename E::value_type(1));
  63 |+             return ret;
```

> Username: bassoy  
> Created_at: 2020-04-30 14:30:33 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418054191  

Why is the `extents_result_type_tensor_times_vector` returning a value instead of a type ?

> Username: bassoy  
> Created_at: 2020-04-30 14:32:46 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418055785  

Should be also considered for the following code lines.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:03:56 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418554720  

this was because `fixed rank extents` does not have a constructor that fill the extents but it does have `fill` function which is not in `dynamic extents` and I wanted the code clean where I'm calling it.

> Username: bassoy  
> Created_at: 2020-05-01 15:35:44 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418595402  

1. I would expect a function named `extents_result_type_tensor_times_vector` to return a `result_type` for `extents` in case of `tensor_times_vector`  
  
2. why does the `tensor_times_vector` function require a helper function like this?

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:38:12 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418596702  

It was to convert `static_extents` and `fixed_rank_extents` to convert `fixed_rank_extents ` and `dynamic_extents` to `dynamic_extents`

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:38:48 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418596971  

I will change the name to `extents_result_type_tensor_times_vector` to `extents_result_tensor_times_vector `

> Username: bassoy  
> Created_at: 2020-05-01 16:16:59 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418614306  

why does the tensor_times_vector function require a helper function like this?

> Username: amitsingh19975  
> Created_at: 2020-05-02 05:39:20 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418913329  

It is whether to choose `fixed_rank_extents` or `dynamic_extents` from a given extents type.

> Username: bassoy  
> Created_at: 2020-05-02 13:19:15 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418958214  

1. could you use `std::conditional_t` or `std::variant` inside `tensor_times_vector` ?  
2. could you use a free function template for deriving the result types for all kind of contraction operations?   
3. If point 2. is possible, I think it will be a second small project

> Username: amitsingh19975  
> Created_at: 2020-05-02 13:26:07 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418958880  

I'm not in favor of `std::variant` as it will introduce runtime overhead and the problem with `std::conditional_t ` when you access size, it will complain about a size not being the compile-time constant for dynamic extents. I will think of some kind of solution.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  52 |+         // cannot use ::_size inside constexpr function
  53 |+         // basic_extents does not contains _size member because of which
  54 |+         // it complains about it
```

> Username: bassoy  
> Created_at: 2020-04-30 14:31:42 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418055013  

Can you use a `constexpr` function, sth. like   
```cpp  
constexpr size_type size() const { return _size; }  
```

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:07:35 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418556211  

size is already `constexpr`. I think this is fixed in the compiler we are using.

> Username: bassoy  
> Created_at: 2020-05-01 15:38:34 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418596867  

I am not quite sure what you mean.  
You commented that due to a bug in MSVC 14.1, you cannot use the **public** member data `_size` directly. I am wondering why you do not make `_size` a **private** member data and use a constexpr function `size()`instead.

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:42:55 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418598877  

```cpp  
if constexpr( is_static_v<extents_type> ){  
   if constexpr ( extents_type::_size ){....}  
}else {  
  ....  
}  
```  
msvc 14.1 will say basic_extents<T> does not have `_size`. Which is not a proper behaviour of `constexpr`

> Username: bassoy  
> Created_at: 2020-05-01 16:19:06 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418615200  

So you cannot define  
```cpp  
constexpr size_type size() const { return _size; }  
```  
in `fixed_rank_extents` and use it later?

> Username: amitsingh19975  
> Created_at: 2020-05-01 16:25:29 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418617886  

it will then give error size() cannot be used at compile time. dynamic extents `size()` cannot be known at compile time. I think have used it before and the above error stopped me msvc 14.1 but I haven't checked for 14.2. That's why I took the alternative of defining functions.

> Username: bassoy  
> Created_at: 2020-05-01 16:30:59 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418620395  

Hmmm. Okay. Thanks for clarifying.


📁 include/boost/numeric/ublas/tensor/type_traits.hpp

```diff
  24 |+ 
  25 |+ #include <boost/numeric/ublas/tensor/detail/type_traits_tensor.hpp>
  26 |+ #include <boost/numeric/ublas/tensor/detail/storage_traits.hpp>
```

> Username: bassoy  
> Created_at: 2020-04-30 14:38:52 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418060362  

Please have the following headers due to separation of concerns:  
`type_traits_extents.hpp` that includes `type_traits` for `static_extents`, `dynamic_extents`, `fixed_rank_extents`  
`type_traits_strides.hpp` that includes `type_traits` for `static_strides`, `dynamic_strides`, `fixed_rank_strides`  
`type_traits_tensor.hpp`   
`type_traits_storage.hpp`

> Username: bassoy  
> Created_at: 2020-04-30 15:41:31 UTC  
> Updated_at: 2020-05-02 14:48:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418105494  

I am not sure if we should put   
`type_traits_extents.hpp`   
`type_traits_strides.hpp`  
` type_traits_tensor.hpp`  
and   
` type_traits_storage.hpp`   
into one `type_traits` header.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:13:40 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418558887  

I wanted it like std `type_traits.hpp` which contains every type of trait. If you want separate traits I will decouple them.

> Username: amitsingh19975  
> Created_at: 2020-05-01 14:21:52 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418562362  

should I put into separate folder `type_traits`? This will make it easier to maintain and make better folder structure which we are missing for clean and maintainable code.

> Username: bassoy  
> Created_at: 2020-05-01 15:42:51 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418598844  

Okay, what about this:  
Let's have:  
`details/type_traits_extents.hpp`  
`details/type_traits_strides.hpp`  
`details/type_traits_tensor.hpp`  
`details/type_traits_storage.hpp`  
and include them in  
`type_traits.hpp`  
Inside our code, we should only use `details` to have lesser inclusions.  
Users of our code can access `type_traits`.

> Username: amitsingh19975  
> Created_at: 2020-05-01 15:45:36 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418600098  

This is much better.

> Username: bassoy  
> Created_at: 2020-05-01 16:19:32 UTC  
> Updated_at: 2020-05-02 14:48:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#discussion_r418615375  

Then let's go with this version.


---

## Comment 11

> Username: amitsingh19975  
> Created_at: 2020-05-02 09:10:00 UTC  
> Updated_at: 2020-05-02 09:11:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-622920509  

> There are some nitpicking change requests regarding formatting and (my personal opinion) better reading.  
> The header files for extents type traits should be inside one single header file. However, I am not sure if we should have one single `type_traits` or not.  
  
I have included the all the extents into the `type_traits_extents.hpp` similarly all the strides have been included in `type_traits_strides.hpp`. Header file `type_traits.hpp` has all the traits for the user to use similar to standard type traits. Traits have modularized for easier maintenance and resides inside the `detail` folder.

---

## Review 12 [Approved]

> Username: bassoy  
> Created_at: 2020-05-02 13:23:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-404528165  

I like the changes made it here.

---

## Comment 13

> Username: bassoy  
> Created_at: 2020-05-02 13:29:50 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-622954038  

> > There are some nitpicking change requests regarding formatting and (my personal opinion) better reading.  
> > The header files for extents type traits should be inside one single header file. However, I am not sure if we should have one single `type_traits` or not.  
>   
> I have included the all the extents into the `type_traits_extents.hpp` similarly all the strides have been included in `type_traits_strides.hpp`. Header file `type_traits.hpp` has all the traits for the user to use similar to standard type traits. Traits have modularized for easier maintenance and resides inside the `detail` folder.  
  
Great.

---

## Review 14 [Changes requested]

> Username: bassoy  
> Created_at: 2020-05-02 14:44:39 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/81#pullrequestreview-404533738  

I am not in favor of my previous suggestion. The tensor multiplication functions were much cleaner before. They should not know different types of extents or only if desired and specialized for.  
Please reset your changes to the previous commit.  
  
On the long run, I think we need to find a way to find a common interface for all extent and layout types.

---

## Comment 15

> Username: amitsingh19975  
> Created_at: 2020-05-02 14:46:43 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-622965058  

> I am not in favor of my previous suggestion. The tensor multiplication functions were much cleaner before. They should not know different types of extents or only if desired and specialized for.  
> Please reset your changes to the previous commit.  
>   
> On the long run, I think we need to find a way to find a common interface for all extent and layout types.  
  
I will change it back then.

---

## Comment 16

> Username: bassoy  
> Created_at: 2020-05-03 14:14:08 UTC  
> Url: https://github.com/boostorg/ublas/pull/81#issuecomment-623116426  

I am merging this pull request into the develop branch.

---
