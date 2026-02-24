# #171 - LZMA threads: is 1000 block size too small, and is the dictionary too big? [Closed]

> Username: Artoria2e5  
> Created at: 2024-03-12 05:05:34 UTC  
> Updated at: 2024-03-12 05:10:07 UTC  
> Closed at: 2024-03-12 05:10:06 UTC  
> Url: https://github.com/boostorg/iostreams/issues/171  

In https://github.com/boostorg/iostreams/pull/95, boost iostreams received the ability to call `lzma_stream_encoder_mt`. However, boost currently uses a chunk size of 1000 *bytes* in:  
  
```C++  
    const lzma_mt opt = { 0, threads_, 0, 1000, level_, NULL, LZMA_CHECK_CRC32 };  
```  
  
This is about 1000 times smaller than what the liblzma documentation recommends:  
https://github.com/frida/xz/blob/e70f5800ab5001c9509d374dbf3e7e6b866c43fe/src/liblzma/api/lzma/container.h#L82  
  
Because of how xz multithreading works, we are basically chunking the input down to 1000 byte fragments and feeding them to separate compressors instances here. I expect this to cause some serious issues with the compression ratio, though I haven't written any code to confirm it yet.  
  
In addition, because `lzma_stream_encoder_mt` (chunking) is used even with `threads_` set to 1, this compression ration regression should hold for all builds that don't have `BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED` enabled.  
  
### What to do?  
  
Can we increase the chunk size to a reasonable minimum, such as 1 MiB? What was the small chunk size trying to avoid, and is it that big of a concern? Surely lzma does its own buffering, right?  
  
### Tangential: dictionary size, passing options  
At the same time, there is also *no* point in using a dictionary size bigger than the chunk or source-data size, whichever is smaller. If we are chunking, then the maximum reasonable dictionary size simply is the chunk size.  
  
Right now we just let people choose their xz preset, but that's causes basically useless scaling of dictionary size, and with that RAM usage. The only part of the preset that matters are the cpu-effort parameters; the dictionary SHOULD NOT be scaled. If we look at the [xz manpage](https://manned.org/xz.1), we can see a suggestion for the exact thing:  
  
> Sometimes the compression time doesn’t matter, but the decompressor memory usage has to be kept low, for example, to make it possible to decompress the file on an embedded system.  The following command uses `-6e` (`-6 --extreme`) as a base and sets the dictionary to only 64 KiB.  The resulting file can be decompressed with XZ Embedded (that’s why there is `--check=crc32`) using about 100 KiB of memory.  
>```  
>xz --check=crc32 --lzma2=preset=6e,dict=64KiB foo  
>```  
  
Boost should, ideally, do something similar to parse the level and cap the dictionary size --- once we figure out the chunk size thing.

---

## Comment 1

> Username: Artoria2e5  
> Created at: 2024-03-12 05:10:06 UTC  
> Url: https://github.com/boostorg/iostreams/issues/171#issuecomment-1990479001  

.... I am an idiot, the 1000 is the timeout for liblzma's internal buffering. That's a reasonable amount. The actual `block_size` is 0, "let liblzma decide". The dictionary part must also not be too bad, considering a lot of data can happen in one second.
