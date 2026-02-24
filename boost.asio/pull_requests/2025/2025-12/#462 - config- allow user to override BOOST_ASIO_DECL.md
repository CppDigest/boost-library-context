# #462 config: allow user to override BOOST_ASIO_DECL [Open]

> Username: jcelerier  
> Created at: 2025-12-13 23:11:30 UTC  
> Updated at: 2025-12-28 16:02:20 UTC  
> Url: https://github.com/boostorg/asio/pull/462  

Currently, BOOST_ASIO_DECL is set by the library, with the logic:  
- Header-only => inline  
- Not header-only =>  
	Win32?  
		=> dllexport / dllimport.  
  
In some cases it is important to have more control over this.  
For instance:  
 - To be able to set visibility attributes under ELF platform  
 - To be able to disable visibility under DLL platform.  
  
etc.

---
