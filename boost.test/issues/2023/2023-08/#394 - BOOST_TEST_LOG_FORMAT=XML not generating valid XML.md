# #394 - BOOST_TEST_LOG_FORMAT=XML not generating valid XML [Open]

> Username: agarwalneetu  
> Created at: 2023-08-18 05:26:20 UTC  
> Updated at: 2023-08-18 05:28:34 UTC  
> Url: https://github.com/boostorg/test/issues/394  

I'm running some ctests in my project and I want to generate test output in an XML file, for that I am providing BOOST_TEST_LOG_FORMAT=XML as env variable before running my testcase. Testcase runs successfully but generates an invalid XML  
![image](https://github.com/boostorg/test/assets/122016933/58f3aef0-ac35-4396-9ab2-c5545f17bd84)

---

## Comment 1

> Username: agarwalneetu  
> Created at: 2023-08-18 05:27:22 UTC  
> Url: https://github.com/boostorg/test/issues/394#issuecomment-1683372066  

XML generated is unable to be read by my azure pipeline too.  
![image](https://github.com/boostorg/test/assets/122016933/f8c22ba9-20c2-4e41-819a-a3a0f15eac4c)

---

## Comment 2

> Username: agarwalneetu  
> Created at: 2023-08-18 05:28:34 UTC  
> Url: https://github.com/boostorg/test/issues/394#issuecomment-1683372892  

@raffienficiaud please look into it. This is a critical issue for us
