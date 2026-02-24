# #15 Fixed bug with crc_optimal computes wrong checksum. [Merged]

> Username: te2ikd  
> Created at: 2021-01-24 06:31:42 UTC  
> Updated at: 2023-09-27 18:13:54 UTC  
> Merged at: 2023-09-27 18:13:53 UTC  
> Closed at: 2023-09-27 18:13:53 UTC  
> Url: https://github.com/boostorg/crc/pull/15  

With some template parameters (such as CRC-14/DARC, CRC-24/BLE), crc_optimal computes wrong checksum.  
  
```C++  
char data[9] = { '1', '2', '3', '4', '5', '6', '7', '8', '9' };  
boost::crc_basic  <14> crc14_darc_basic(0x805, 0x0000, 0x0000, true, true);  
boost::crc_optimal<14,                  0x805, 0x0000, 0x0000, true, true> crc14_darc_optimal;  
crc14_darc_basic  .process_bytes(data, sizeof(data));  
crc14_darc_optimal.process_bytes(data, sizeof(data));  
auto crc14_darc_value1 = crc14_darc_basic  .checksum(); // 0x082D  
auto crc14_darc_value2 = crc14_darc_optimal.checksum(); // 0x3FF4 (!)  
```

---

## Review 1 [Approved]

> Username: vincenttl  
> Created_at: 2022-11-01 15:48:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/crc/pull/15#pullrequestreview-1163585337  

It fixed my issue too

---
