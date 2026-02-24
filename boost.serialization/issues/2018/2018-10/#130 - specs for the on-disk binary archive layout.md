# #130 - specs for the on-disk binary archive layout [Closed]

> Username: sbinet  
> Created at: 2018-10-18 09:41:34 UTC  
> Updated at: 2019-02-24 18:57:24 UTC  
> Closed at: 2019-02-24 18:57:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/130  

hi there,  
  
for interoperability with C++ programs that produce/consume Boost serialization archives, I had the need to implement a reader+writer, from Go.  
  
reverse engineering the on-disk data format got me this far:  
- https://github.com/go-boostio/boostio  
  
but, I'd sleep better if there were proper specs for how the binary data is laid out on disk.  
  
could such a thing be devised?

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-18 15:01:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/130#issuecomment-431043579  

This is a very interesting question which I've considered from time to time.  I'll post my thoughts here for posterity.  
  
a) Traditionally, we define a data layout and write code to fill/empty it.  This means that the layout is well defined and we can write programs in different languages to work with it.  It's a straight forward process but it does require that one invest in defining the layout and writing the code to match it. This is the approach taken by Google Protocol Buffers.  It has worked well for them.  
  
b) The serialization library is fundamentally different.  The layout is implicit in the serialization functions and sequence that these functions are called. This means no extra work in defining a layout and writing code to bridge the gap between one's data structures and the externally defined layout. The layout is generated automatically and can very even for the same program depending upon the actual data being serialized.  Of course this has the downside of making it difficult to access/update the data with a program which is not written in C++.  
  
c) Occasionally I have considered creating a special archive type for "export/import" which would include enough extra information to make it easier to write a program in other languages to access/update the data.  It would take significant effort to do such a thing in a way that would make it easy to use.   
  
d) Another idea would be an archive which would generate the source code in your favorite language to read/update the archive.     
  
e) Finally, another idea - which actually got started - is to automatically generate a windows dialog (using MFC) to edit the archive.  I became convinced that this could be made to work pretty well.  But the gap between an initial idea/prototype was just too wide to justify for such a niche product unless someone would be willing to pay for it.    
  
Let me know if you've managed to get any of the above accomplished - all interesting projects.

---

## Comment 2

> Username: sbinet  
> Created at: 2018-10-18 15:44:50 UTC  
> Updated at: 2018-10-18 15:54:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/130#issuecomment-431059944  

I guess "my" `boostio` package is a mixture of a) and b).  
  
right now, by inspecting the binary archive resulting from the execution of this C++ program:  
- https://github.com/go-boostio/boostio/blob/cef27aadea55302504fbdb840ee58f7dcbb9aba3/binser/testdata/gen-binary-archive.go#L57  
  
the generated archive is:  
```  
$> hexdump -C ./testdata/data/bin  
00000000  16 00 00 00 00 00 00 00  73 65 72 69 61 6c 69 7a  |........serializ|  
00000010  61 74 69 6f 6e 3a 3a 61  72 63 68 69 76 65 11 00  |ation::archive..|  
00000020  04 08 04 08 01 00 00 00  00 01 11 22 22 33 33 33  |...........""333|  
00000030  33 44 44 44 44 44 44 44  44 ff 22 22 33 33 33 03  |3DDDDDDDD.""333.|  
00000040  44 44 44 44 44 44 44 04  cd cc 0c 40 66 66 66 66  |DDDDDDD....@ffff|  
00000050  66 66 0a 40 00 00 00 00  00 03 00 00 00 00 00 00  |ff.@............|  
00000060  00 11 22 33 04 00 00 00  00 00 00 00 11 22 33 ff  |.."3........."3.|  
00000070  05 00 00 00 00 00 00 00  68 65 6c 6c 6f 05 00 00  |........hello...|  
00000080  00 00 00 00 00 68 65 6c  6c 6f 00 00 00 00 00 03  |.....hello......|  
00000090  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|  
000000a0  04 00 00 00 00 00 00 00  64 72 65 69 05 00 00 00  |........drei....|  
000000b0  00 00 00 00 74 72 6f 69  73 04 00 00 00 00 00 00  |....trois.......|  
000000c0  00 65 69 6e 73 02 00 00  00 00 00 00 00 75 6e 04  |.eins........un.|  
000000d0  00 00 00 00 00 00 00 7a  77 65 69 04 00 00 00 00  |.......zwei.....|  
000000e0  00 00 00 64 65 75 78 00  00 00 00 00 03 00 00 00  |...deux.........|  
000000f0  00 00 00 00 70 65 74 04  00 01 03 00 00 00 00 00  |....pet.........|  
00000100  00 00 70 65 74 04 00 01                           |..pet...|  
00000108  
```  
  
I inferred that:  
- an archive starts with the `"serialization::archive"` string  
- then, an archive header: `[archive-version (uint16)][size-of-int,long,float,double (4bytes), endianness (4bytes)]`  
- user payload  
  
built-in types: take their expected size, on-disk.  
  
- a `std::string` is encoded as `[size (int64)][bytes]`  
- a `std::vector<T>` is encoded as: `[size (int64)][values...]`  
- a `std::array<T, n>` is encoded as: `[type descriptor for (std::array<T, n>)][n (int64)][values...]`  
- a `std::map<K,V>` is encoded as: `[type descriptor for (std::map<K,V>)][size of map (int64)][9 bytes ???][key][value][key][value]...`  
- a `class T` is encoded as: `[type descriptor for T][fields]`   
  
type descriptors are only written once, upon the first occurrence of a value of such a type.  
type descriptors are pairs of `[version (uint32)][flags (uint8)]`.  
I couldn't figure out what really are those extra `uint8` values. (in my test file, it's just consistently `0`.)  
  
I also couldn't figure out what the extra `9` bytes are meant for and their use, in the `std::map` case.  
I haven't played (yet?) with polymorphic values, nor containers of polymorphic values, so I may miss a bunch of possible layouts.  
  
one thing that I don't know how to handle (and confused me) is that the on-disk layout of `std::vector<T>` is different from the `std::list<T>` one.  
naively I had assumed they'd be the same (at least on-disk): a size, then the values. but, in the `std::list` case, there are extra bytes before the payload for the values.  
why is that ?

---

## Comment 3

> Username: sbinet  
> Created at: 2018-10-22 07:58:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/130#issuecomment-431765687  

> naively I had assumed they'd be the same (at least on-disk): a size, then the values. but, in the std::list case, there are extra bytes before the payload for the values.  
> why is that ?  
  
figured it out: these bits are also there for `std::vector<T>` when `T` isn't a builtin.  
I still don't know what the `9 bytes` are for in the case of a `std::map<K,V>`...

---

## Comment 4

> Username: robertramey  
> Created at: 2018-10-22 14:09:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/130#issuecomment-431846421  

The easiest way to decipher the format of a particular archive is just output to an xml archive.  In this version all the fields are labeled.  Then you can easily match them up to a binary or text archive
