# #763 - Fuzz testing seems to be running without a seed corpus [Closed]

> Username: anarthal  
> Created at: 2023-07-06 17:31:33 UTC  
> Updated at: 2023-07-27 18:47:55 UTC  
> Closed at: 2023-07-27 18:47:55 UTC  
> Url: https://github.com/boostorg/url/issues/763  

If I've read the code correctly, fuzz tests attempt to construct a seed corpus from json files in the `test` folder, but there are none (seems to be a left-over from Boost.Json). Fuzzing without a seed corpus may reduce the effectiveness of fuzzing.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-07-07 16:29:04 UTC  
> Updated at: 2023-07-07 16:29:46 UTC  
> Url: https://github.com/boostorg/url/issues/763#issuecomment-1625659980  

Yes. That's correct. That was some bad copy/pasting from Boost.Json. This b2 infrastructure is also kind of hard to maintain because it's doing a lot of the work ad hoc instead of through the build system. That is besides the problem of the build system used for that being b2, which is unproductive for us since we have to keep copy/pasting patterns from other places. It was something to get started but we ended up moving to other things as usual and never having the time to improve it. Even the things being tested in the fuzz tests themselves are somewhat silly, although they did help us find a few bugs.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-07-18 19:42:33 UTC  
> Url: https://github.com/boostorg/url/issues/763#issuecomment-1640898537  

@anarthal Do you have fuzz tests in boost.mysql or do you know of a pattern of fuzz tests that would be a good idea to replicate?  
  
The pattern is got from Boost.JSON is quite problematic. I want to revisit this.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-07-19 09:15:13 UTC  
> Url: https://github.com/boostorg/url/issues/763#issuecomment-1641726968  

Yes, I have. You can have a look at MySQL's fuzzing, or to the new contributor guide page which explains more or less what I did.  
  
What you're missing is a seed corpus. It should contain a variety of samples (in your case, URLs) that guide the fuzzer about how a URL looks like, so it doesn't have to start guessing from zero.  
  
In MySQL, I created the seed corpus "by hand": I've got a thorough suite of integration tests; I ran them, captured the packets with Wireshark, and transformed them into binary files that matched what the deserialization functions expected.  
  
In your case, there are two options: 1) look in the Internet for a corpus of URLs for testing and use that. URLs are much more common than MySQL packets, so you should be able to find some. You need to produce a set of files, one per URL. 2) Take all the URLs you use in unit tests (may be a time-consuming job) and put them into files.  
  
If you can, create files with some ill-formed URLs, too.  
  
JSON uses all JSON files used during unit testing as seed corpus. IIRC, these files come from an external JSON corpus designed to exercise JSON parsers.  
  
Looking into URLs fuzzer, I'd also suggest to split what you have in several fuzzers. That would give you 5 fuzzing executables:  
```  
fuzz_absolute_uri  
fuzz_origin_form  
fuzz_relative_ref  
fuzz_uri  
fuzz_uri_reference  
```  
  
This is specially important if each parser considers valid a different set of values. In MySQL, there are like 10 fuzzers, since the protocol is shit and message types are inferred from the context, and not from a message type. I've got different seed corpus for each fuzzer.  
  
If I can help anyhow, please let me know =}
