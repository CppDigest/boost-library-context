# #270 Update github actions [Closed]

> Username: sdarwin  
> Created at: 2022-10-18 16:32:31 UTC  
> Updated at: 2023-08-24 18:59:43 UTC  
> Closed at: 2023-08-24 18:59:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/270  

If you're interested here is a newer Github Actions config, copied from boost-ci. It should finish successfully.   
  
During the tests, Windows is running out of disk space. All the jobs take a long time.  It would be good to reduce the number of tests and then re-enable windows.

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-24 18:59:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/270#issuecomment-1692250851  

Rather than reducing the number of tests, I'd rather just disable CI for the serialization library.  Right not, in spite of efforts expended by many, the CI tests are just heating up the planet.

---
