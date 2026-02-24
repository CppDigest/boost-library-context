# #25 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:07:02 UTC  
> Updated at: 2019-12-17 00:28:06 UTC  
> Closed at: 2019-12-17 00:28:06 UTC  
> Url: https://github.com/boostorg/multi_array/issues/25  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./libs/multi_array/test/generative_tests.hpp:#ifndef GENERATIVE_TESTS_RG072001_HPP  
./libs/multi_array/test/generative_tests.hpp:#define GENERATIVE_TESTS_RG072001_HPP  
./libs/multi_array/test/generative_tests.hpp:#endif // GENERATIVE_TESTS_RG072001_HPP  
./boost/multi_array/base.hpp:#ifndef BASE_RG071801_HPP  
./boost/multi_array/base.hpp:#define BASE_RG071801_HPP  
./boost/multi_array/base.hpp:#endif // BASE_RG071801_HPP  
./boost/multi_array/collection_concept.hpp:#ifndef COLLECTION_CONCEPT_RG103101_HPP  
./boost/multi_array/collection_concept.hpp:#define COLLECTION_CONCEPT_RG103101_HPP  
./boost/multi_array/collection_concept.hpp:#endif // COLLECTION_CONCEPT_RG103101_HPP  
./boost/multi_array/range_list.hpp:#ifndef RANGE_LIST_RG072501_HPP  
./boost/multi_array/range_list.hpp:#define RANGE_LIST_RG072501_HPP  
./boost/multi_array/range_list.hpp:#endif // RANGE_LIST_RG072501_HPP  
./boost/multi_array/iterator.hpp:#ifndef ITERATOR_RG071801_HPP  
./boost/multi_array/iterator.hpp:#define ITERATOR_RG071801_HPP  
./boost/multi_array/iterator.hpp:#endif // ITERATOR_RG071801_HPP  
./boost/multi_array/copy_array.hpp:#ifndef COPY_ARRAY_RG092101_HPP  
./boost/multi_array/copy_array.hpp:#define COPY_ARRAY_RG092101_HPP  
./boost/multi_array/copy_array.hpp:#endif // COPY_ARRAY_RG092101_HPP  
./boost/multi_array/subarray.hpp:#ifndef SUBARRAY_RG071801_HPP  
./boost/multi_array/subarray.hpp:#define SUBARRAY_RG071801_HPP  
./boost/multi_array/subarray.hpp:#endif // SUBARRAY_RG071801_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-17 00:28:06 UTC  
> Url: https://github.com/boostorg/multi_array/issues/25#issuecomment-566315222  

Resolved in e6f210c7ca4b66a74f8a8f3336f6578094b7ed3d.
