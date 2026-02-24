# #74 - Reading file in chunks using boost::iostreams::mapped_file_source crashes on Windows [Open]

> Username: BullyWiiPlaza  
> Created at: 2018-11-30 19:35:38 UTC  
> Updated at: 2018-11-30 19:35:38 UTC  
> Url: https://github.com/boostorg/iostreams/issues/74  

I wasted a few hours wondering and trying to fix a bug which caused my application to crash the moment I tried to read the 2nd chunk of a file. The same code runs just fine on `Linux` but does not on `Windows`.  
  
Here is the code snippet I'm using:  
  
```  
boost::iostreams::mapped_file_source read_bytes(const char *file_path,  
unsigned_value offset, unsigned_value length) {  
	iostreams::mapped_file_params parameters;  
	parameters.path = file_path;  
	parameters.length = static_cast<size_t>(length);  
	parameters.flags = iostreams::mapped_file::mapmode::readonly;  
	parameters.offset = static_cast<boost::iostreams::stream_offset>(offset);  
  
	boost::iostreams::mapped_file_source file;  
  
	file.open(parameters);  
  
	if (file.is_open()) {  
		return file;  
	}  
  
	printf("Failed to open file\n");  
	exit(EXIT_FAILURE);  
}  
```  
  
This is the exception `Visual Studio` showed me:  
  
`Unhandled exception at 0x00007FFD7AFBA388 in PointerSearcher.exe: Microsoft C++ exception: boost::wrapexcept<std::ios_base::failure> at memory location 0x0000003034FCF270.`  
  
I'm pretty sure I didn't mess anything up since the behavior of `Boost` should be identical on all platforms and I'm using an `offset` and a `length` to read chunks from a file.  
  
Thank you.
