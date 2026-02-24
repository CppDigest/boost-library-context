# #11 add hmac-sha512 [Merged]

> Username: cmazakas  
> Created at: 2024-10-10 20:03:19 UTC  
> Updated at: 2024-10-11 18:11:20 UTC  
> Merged at: 2024-10-11 17:00:31 UTC  
> Closed at: 2024-10-11 17:00:31 UTC  
> Url: https://github.com/boostorg/hash2/pull/11  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-10-11 00:01:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/11#pullrequestreview-2361642752  

📁 test/hmac_sha2.cpp

```diff
 142 |+     BOOST_TEST_EQ( digest<hmac_sha2_512>( from_hex( "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" ), from_hex( "dddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddd" ) ), std::string( "2ee7acd783624ca9398710f3ee05ae41b9f9b0510c87e49e586cc9bf961733d8623c7b55cebefccf02d5581acc1c9d5fb1ff68a1de45509fbe4da9a433922655" ) );
 143 |+ 
 144 |+     // skip this test case from https://www.rfc-editor.org/rfc/rfc4868#section-2.7.2.3, the key seems wrong as it's larger than 64 bytes
```

> Username: pdimov  
> Created_at: 2024-10-11 00:01:18 UTC  
> Updated_at: 2024-10-11 00:01:19 UTC  
> Url: https://github.com/boostorg/hash2/pull/11#discussion_r1796238562  

The key isn't wrong. Does the test fail?

> Username: cmazakas  
> Created_at: 2024-10-11 15:05:51 UTC  
> Url: https://github.com/boostorg/hash2/pull/11#discussion_r1797090840  

This test case does seem to fail for me.  
  
Unfortunately, it seems like I accidentally pushed up an attempt at fixing an issue I had run into from the RFC I'm pulling test cases from.  
  
In https://www.rfc-editor.org/rfc/rfc4868#section-2.7.2.3:  
```  
   Test Case AUTH512-4:  
   Key =          0a0b0c0d0e0f10111213141516171819  
                  0102030405060708090a0b0c0d0e0f10  
                  1112131415161718191a1b1c1d1e1f20  
                  2122232425262728292a2b2c2d2e2f30  
                  3132333435363738393a3b3c3d3e3f40  (64 bytes)  
```  
  
This is actually 80 bytes, not 64. Each row is 16 bytes, and we have 5 rows here.  
  
Using this key (or truncated versions of it, as I had pushed) doesn't seem to create the corresponding MAC.  
  
I kept this test commented out just in case anyone else reads the RFC and tries to add it in.

> Username: pdimov  
> Created_at: 2024-10-11 15:12:25 UTC  
> Url: https://github.com/boostorg/hash2/pull/11#discussion_r1797098711  

See https://www.rfc-editor.org/errata/rfc4868


---
