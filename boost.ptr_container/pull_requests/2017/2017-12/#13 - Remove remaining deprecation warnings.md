# #13 Remove remaining deprecation warnings. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2017-12-09 23:16:59 UTC  
> Updated at: 2017-12-10 01:56:42 UTC  
> Merged at: 2017-12-10 01:56:41 UTC  
> Closed at: 2017-12-10 01:56:41 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/13  

Work in progress: I will grep all deprecation warning from the travis output that are emitted directly by the the public includes, not the ones from the test themselves.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2017-12-10 00:23:58 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/13#issuecomment-350515723  

@pdimov Checked with travis logs for gcc 6 and gcc 7: no more deprecation warnings except from tests directly:  
  
`curl -L 'https://api.travis-ci.org/jobs/314132423/log.txt?deansi=true' | grep -- "-Wdeprecated-declarations" |sort -u|grep -v "^libs/ptr_container/test"`  
`curl -L 'https://api.travis-ci.org/jobs/314132424/log.txt?deansi=true' | grep -- "-Wdeprecated-declarations" |sort -u|grep -v "^libs/ptr_container/test"`

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-10 00:32:08 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/13#issuecomment-350516105  

I wonder whether it's worth fixing `test/sequence_test_data.hpp` and `test/associative_test_data.hpp` while we're at it. On one hand, many test .cpp files have plenty of `auto_ptr` too. On the other, it will silence quite a lot.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2017-12-10 01:31:21 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/13#issuecomment-350518445  

I just did the same for the two test header files you mentioned.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-10 01:55:16 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/13#issuecomment-350519355  

Perfect, thanks.

---
