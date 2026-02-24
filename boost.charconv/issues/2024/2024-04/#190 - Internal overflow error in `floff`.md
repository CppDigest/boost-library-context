# #190 - Internal overflow error in `floff` [Closed]

> Username: mborland  
> Created at: 2024-04-22 09:34:49 UTC  
> Updated at: 2024-04-23 07:15:20 UTC  
> Closed at: 2024-04-23 07:15:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/190  

```  
====== BEGIN OUTPUT ======  
INFO: Running with entropic power schedule (0xFF, 100).  
INFO: Seed: 3779282785  
INFO: Loaded 1 modules   (15 inline 8-bit counters): 15 [0x5582e680f620, 0x5582e680f62f),   
INFO: Loaded 1 PC tables (15 PCs): 15 [0x5582e680f630,0x5582e680f720),   
INFO:        1 files found in seedcorpus/fuzz_to_chars_float  
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 20336 bytes  
INFO: seed corpus: files: 1 min: 20336b max: 20336b total: 20336b rss: 45Mb  
#2	INITED cov: 7 ft: 8 corp: 1/19Kb exec/s: 0 rss: 45Mb  
#3	NEW    cov: 7 ft: 9 corp: 2/36Kb lim: 20336 exec/s: 0 rss: 45Mb L: 17284/20336 MS: 1 EraseBytes-  
#60	REDUCE cov: 7 ft: 9 corp: 2/31Kb lim: 20336 exec/s: 0 rss: 47Mb L: 11489/20336 MS: 2 ChangeBit-CrossOver-  
#62	REDUCE cov: 7 ft: 9 corp: 2/25Kb lim: 20336 exec/s: 0 rss: 47Mb L: 5498/20336 MS: 2 CrossOver-CrossOver-  
#76	REDUCE cov: 7 ft: 9 corp: 2/23Kb lim: 20336 exec/s: 0 rss: 48Mb L: 3903/20336 MS: 4 InsertRepeatedBytes-ChangeBit-ChangeASCIIInt-EraseBytes-  
#81	REDUCE cov: 7 ft: 9 corp: 2/22Kb lim: 20336 exec/s: 0 rss: 48Mb L: 2523/20336 MS: 5 ChangeASCIIInt-InsertRepeatedBytes-InsertRepeatedBytes-ShuffleBytes-EraseBytes-  
#95	REDUCE cov: 7 ft: 9 corp: 2/21Kb lim: 20336 exec/s: 0 rss: 48Mb L: 2174/20336 MS: 4 ChangeASCIIInt-ShuffleBytes-InsertRepeatedBytes-EraseBytes-  
#114	REDUCE cov: 7 ft: 9 corp: 2/21Kb lim: 20336 exec/s: 0 rss: 48Mb L: 1677/20336 MS: 4 ShuffleBytes-InsertByte-ChangeByte-EraseBytes-  
#131	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 49Mb L: 775/20336 MS: 2 ChangeASCIIInt-CrossOver-  
#151	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 49Mb L: 634/20336 MS: 5 CopyPart-ChangeBit-ChangeBit-ChangeByte-EraseBytes-  
#168	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 49Mb L: 349/20336 MS: 2 InsertByte-EraseBytes-  
#169	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 49Mb L: 323/20336 MS: 1 EraseBytes-  
#185	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 50Mb L: 271/20336 MS: 1 EraseBytes-  
#207	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 50Mb L: 179/20336 MS: 2 InsertByte-EraseBytes-  
#253	REDUCE cov: 7 ft: 9 corp: 2/20Kb lim: 20336 exec/s: 0 rss: 51Mb L: 162/20336 MS: 1 EraseBytes-  
#257	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 51Mb L: 86/20336 MS: 4 ChangeByte-ChangeByte-ChangeBinInt-EraseBytes-  
#273	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 51Mb L: 57/20336 MS: 1 EraseBytes-  
#276	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 51Mb L: 40/20336 MS: 3 CrossOver-InsertByte-EraseBytes-  
#307	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 52Mb L: 39/20336 MS: 1 EraseBytes-  
#354	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 52Mb L: 32/20336 MS: 2 InsertByte-EraseBytes-  
#391	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 53Mb L: 28/20336 MS: 2 ChangeASCIIInt-EraseBytes-  
#462	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 53Mb L: 26/20336 MS: 1 EraseBytes-  
#474	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 54Mb L: 19/20336 MS: 2 CMP-EraseBytes- DE: "\001\000"-  
#500	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 54Mb L: 16/20336 MS: 1 EraseBytes-  
#511	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 54Mb L: 9/20336 MS: 1 EraseBytes-  
#532	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 54Mb L: 6/20336 MS: 1 EraseBytes-  
#584	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 55Mb L: 3/20336 MS: 2 CMP-EraseBytes- DE: "\001\000\000\000"-  
#636	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 55Mb L: 2/20336 MS: 2 InsertByte-EraseBytes-  
#647	REDUCE cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 0 rss: 56Mb L: 1/20336 MS: 1 EraseBytes-  
#65536	pulse  cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 32768 rss: 395Mb  
#131072	pulse  cov: 7 ft: 9 corp: 2/19Kb lim: 20336 exec/s: 32768 rss: 404Mb  
../../../boost/charconv/detail/dragonbox/floff.hpp:2425:58: runtime error: index 8 out of bounds for type 'const std::uint32_t[8]' (aka 'const unsigned int[8]')  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/charconv/detail/dragonbox/floff.hpp:2425:58 in   
MS: 4 InsertRepeatedBytes-PersAutoDict-ChangeBinInt-CrossOver- DE: "\001\000\000\000"-; base unit: 5ba93c9db0cff93f52b521d7420e43f6eda2784f  
0x34,0x34,0x34,0x34,0x34,0x34,0x36,0x35,0x35,0x36,0x36,0x38,0x38,0x35,0x2e,0x32,0x36,0x32,0x35,0x35,0x32,0xa,0x2d,0x33,0x32,0x37,0x31,0x39,0x34,0x37,0x30,0x37,0x35,0x2e,0x34,0x34,0x32,0x31,0x32,0x37,0xa,0x31,0x38,0x36,0x36,0x31,0x34,0x36,0x38,0x38,0x31,0x2e,0x32,0x38,0x32,0x33,0x34,0x36,0x37,0xa,0x39,0x39,0x32,0x32,0x33,0x31,0x38,0x36,0x38,0x37,0x2e,0x32,0x34,0x37,0x35,0x33,0x32,0xa,0x32,0x31,0x30,0x35,0x38,0x38,0x39,0x33,0x37,0x34,0x2e,0x39,0x35,0x34,0x33,0x34,0x37,0x36,0xa,0x31,0x34,0x31,0x32,0x31,0x39,0x31,0x31,0x34,0x35,0x2e,0x31,0x39,0x39,0x30,0x37,0x33,0x38,0xa,0x34,0x38,0x39,0x39,0x32,0x34,0x34,0x0,  
44444465566885.262552\012-3271947075.442127\0121866146881.2823467\0129922318687.247532\0122105889374.9543476\0121412191145.1990738\0124899244\000  
artifact_prefix='./'; Test unit written to ./crash-1ea458a26909c872f9c66cd50d1b0b8036674bb3  
Base64: NDQ0NDQ0NjU1NjY4ODUuMjYyNTUyCi0zMjcxOTQ3MDc1LjQ0MjEyNwoxODY2MTQ2ODgxLjI4MjM0NjcKOTkyMjMxODY4Ny4yNDc1MzIKMjEwNTg4OTM3NC45NTQzNDc2CjE0MTIxOTExNDUuMTk5MDczOAo0ODk5MjQ0AA==  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
Overflow is in this rounding check: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/dragonbox/floff.hpp#L2426

---

## Comment 1

> Username: jk-jeon  
> Created at: 2024-04-22 22:13:42 UTC  
> Url: https://github.com/boostorg/charconv/issues/190#issuecomment-2071040105  

> `fractional_part_rounding_thresholds32[digits_in_the_second_segment - 1]`  
  
This should be `fractional_part_rounding_thresholds32[digits_in_the_second_segment - 3]` indeed. Thanks for catching this!
