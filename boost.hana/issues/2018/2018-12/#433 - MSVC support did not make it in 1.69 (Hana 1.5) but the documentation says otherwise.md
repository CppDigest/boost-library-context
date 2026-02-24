# #433 - MSVC support did not make it in 1.69 (Hana 1.5) but the documentation says otherwise [Closed]

> Username: sdebionne  
> Created at: 2018-12-21 09:19:15 UTC  
> Updated at: 2019-05-10 15:53:44 UTC  
> Closed at: 2019-02-11 21:59:17 UTC  
> Url: https://github.com/boostorg/hana/issues/433  

For anyone wondering why Hana does not compile on MSVC while the documentation says it is now fully supported, it's just that the documentation if slightly "ahead of time". The `develop` branch works fine.

---

## Comment 1

> Username: sdebionne  
> Created at: 2019-05-09 09:51:55 UTC  
> Url: https://github.com/boostorg/hana/issues/433#issuecomment-490837611  

TBH, I was disappointed to see that MSVC support did not make it to Boost 1.70 neither. Any chance to get Hana 1.5.1 with the patch from MS in 1.71?

---

## Comment 2

> Username: ldionne  
> Created at: 2019-05-10 15:53:44 UTC  
> Url: https://github.com/boostorg/hana/issues/433#issuecomment-491338346  

Sorry, I got very busy and missed the deadline 🤦‍♂️. I'm tagging 1.6.0 and merging to master right now to make sure I don't miss 1.71.0
