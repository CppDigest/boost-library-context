# #136 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:47:16 UTC  
> Updated at: 2019-12-19 04:30:03 UTC  
> Closed at: 2019-12-19 04:29:54 UTC  
> Url: https://github.com/boostorg/container/issues/136  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/container/stable_vector.hpp:      #define STABLE_VECTOR_CHECK_INVARIANT \  
./boost/container/stable_vector.hpp:      #define STABLE_VECTOR_CHECK_INVARIANT  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:         STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:            STABLE_VECTOR_CHECK_INVARIANT  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:      STABLE_VECTOR_CHECK_INVARIANT;  
./boost/container/stable_vector.hpp:#undef STABLE_VECTOR_CHECK_INVARIANT  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_ALL_CONTIGUOUS:  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_ALL_CONTIGUOUS:  
./boost/container/detail/alloc_lib.h:#define DL_MULTIALLOC_ALL_CONTIGUOUS        ((size_t)(-1))  
[rleahy@dev-hq1-sv001 boost_1_72_0]$ find -name "*.*" -path "*/container/*" | xargs -d '\n' -I{} grep -H -P "[^A-Za-z0-9_]DL_MULTIALLOC_(ALL|DEFAULT)_CONTIGUOUS[^A-Za-z0-9_]" {}  
./libs/container/src/dlmalloc_ext_2_8_6.c:              ((contiguous_elements + 1) > (DL_MULTIALLOC_DEFAULT_CONTIGUOUS + 1) && n_elements < contiguous_elements) ||  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_DEFAULT_CONTIGUOUS:  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_ALL_CONTIGUOUS:  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_DEFAULT_CONTIGUOUS:  
./libs/container/src/dlmalloc_ext_2_8_6.c:      case DL_MULTIALLOC_ALL_CONTIGUOUS:  
./libs/container/test/alloc_full_test.cpp:         dlmalloc_multialloc_nodes((i+1)*2, i+1, DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &chain);  
./libs/container/test/alloc_full_test.cpp:         dlmalloc_multialloc_arrays(ArraySize, requested_sizes, 1, DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &chain);  
./libs/container/test/alloc_full_test.cpp:      dlmalloc_multialloc_arrays(ArraySize, requested_sizes, 1, DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &chain);  
./libs/container/test/alloc_full_test.cpp:      dlmalloc_multialloc_nodes(ArraySize, i*4+1, DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &chain);  
./boost/container/detail/alloc_lib.h:#define DL_MULTIALLOC_ALL_CONTIGUOUS        ((size_t)(-1))  
./boost/container/detail/alloc_lib.h:#define DL_MULTIALLOC_DEFAULT_CONTIGUOUS    ((size_t)(0))  
./boost/container/adaptive_pool.hpp:      if(BOOST_UNLIKELY(!dlmalloc_multialloc_nodes(n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch))){  
./boost/container/adaptive_pool.hpp:            (n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain)))){  
./boost/container/adaptive_pool.hpp:      if(BOOST_UNLIKELY(!dlmalloc_multialloc_arrays(n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch))){  
./boost/container/adaptive_pool.hpp:         (n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain)))){  
./boost/container/adaptive_pool.hpp:            (n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain)))){  
./boost/container/adaptive_pool.hpp:         (n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain)))){  
./boost/container/allocator.hpp:      if(!dlmalloc_multialloc_nodes(n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch)){  
./boost/container/allocator.hpp:      if(!dlmalloc_multialloc_nodes(n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain))){  
./boost/container/allocator.hpp:      if(!dlmalloc_multialloc_arrays(n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch)){  
./boost/container/allocator.hpp:      if(!dlmalloc_multialloc_arrays(n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, reinterpret_cast<dlmalloc_memchain *>(&chain))){  
./boost/container/node_allocator.hpp:      if(BOOST_UNLIKELY(!dlmalloc_multialloc_nodes(n_elements, elem_size*sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch))){  
./boost/container/node_allocator.hpp:      dlmalloc_multialloc_arrays(n_elements, elem_sizes, sizeof(T), DL_MULTIALLOC_DEFAULT_CONTIGUOUS, &ch);  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-12-19 04:30:03 UTC  
> Url: https://github.com/boostorg/container/issues/136#issuecomment-567327438  

Thanks for the report!
