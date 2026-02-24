# #84 - BOOST_ASSERT [Closed]

> Username: breese  
> Created at: 2018-09-16 14:55:01 UTC  
> Updated at: 2018-09-16 17:02:51 UTC  
> Closed at: 2018-09-16 17:02:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/84  

Building develop (at 3af87d79c5ee97bad07a1da13e921d489e8377ae) causes compilation error about missing `BOOST_ASSERT` in `boost/histogram/storage/array_storage.hpp`. This is easily solved by including `<boost/assert.hpp>`.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-16 17:02:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/84#issuecomment-421796667  

Thanks, this is now fixed on master (and develop)
