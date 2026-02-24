# #11 - b2 build warning in Jamfile [Closed]

> Username: jeking3  
> Created at: 2018-10-27 13:24:32 UTC  
> Updated at: 2019-01-06 15:49:16 UTC  
> Closed at: 2019-01-06 15:49:16 UTC  
> Url: https://github.com/boostorg/contract/issues/11  

Probably been there a long time...  
  
```  
../libs/contract/test/Jamfile.v2:417: Unescaped special character in argument <define>BOOST_CONTRACT_ON_MISSING_CHECK_DECL="{ throw err(); }"  
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-10-27 16:21:18 UTC  
> Updated at: 2018-12-22 14:25:17 UTC  
> Url: https://github.com/boostorg/contract/issues/11#issuecomment-433634346  

Yes, this has been there since the release in Boost (it was introduced by a fix to bjam done around the same time as boost.contract release). The warning doesn't do anything and only applies to the tests, it can be safely ignored.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-27 17:14:00 UTC  
> Url: https://github.com/boostorg/contract/issues/11#issuecomment-433638438  

Warning-free builds are better, though.  If it's a simple fix, please consider it.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2018-10-30 17:42:58 UTC  
> Url: https://github.com/boostorg/contract/issues/11#issuecomment-434401144  

I will not generate a new release just for this, but I will consider to include this improvement in the next release when/if that is needed.

---

## Comment 4

> Username: lcaminiti  
> Created at: 2018-12-22 14:02:35 UTC  
> Url: https://github.com/boostorg/contract/issues/11#issuecomment-449572554  

I will fix this as suggested in pull request #12.

---

## Comment 5

> Username: lcaminiti  
> Created at: 2019-01-06 15:49:16 UTC  
> Url: https://github.com/boostorg/contract/issues/11#issuecomment-451751779  

fixed in develop branch (will merge in master for next boost release).
