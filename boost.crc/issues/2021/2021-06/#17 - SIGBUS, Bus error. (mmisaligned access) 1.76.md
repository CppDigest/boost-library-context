# #17 - SIGBUS, Bus error. (mmisaligned access) 1.76 [Closed]

> Username: arturbac  
> Created at: 2021-06-24 22:38:36 UTC  
> Updated at: 2021-06-25 09:30:58 UTC  
> Closed at: 2021-06-25 09:30:58 UTC  
> Url: https://github.com/boostorg/crc/issues/17  

With boost 1.76 I encountered error with misaligned access sigbus.  
  
with code that works on varius cpu for years it stared with 1.76 generating SIGBUS on a server machine with Xeon E5-2630   
my code  
compiler used clang 12 on linux  
```  
      uint8_t const * fdata = fop.data();  
      size_t layer_data_size = fop.size() ;  
      boost::crc_32_type crc32_result;  
      crc32_result.process_bytes( fdata, layer_data_size );  
```  
causes sometimes SIGBUS  
```  
Thread 1887 "mapgen" received signal SIGBUS, Bus error.  
[Switching to Thread 0x7fffbbfff640 (LWP 31854)]  
0x00007ffff77a4a31 in boost::detail::reflected_byte_table_driven_crcs<32, 79764919ul>::crc_update (remainder=234194926, new_dividend_bytes=<optimized out>, new_dividend_byte_count=<optimized out>) at /usr/include/boost/crc.hpp:1249  
1249                    remainder ^= table.elems[ index ];  
(gdb) back  
#0  0x00007ffff77a4a31 in boost::detail::reflected_byte_table_driven_crcs<32, 79764919ul>::crc_update (remainder=234194926, new_dividend_bytes=<optimized out>,   
    new_dividend_byte_count=<optimized out>) at /usr/include/boost/crc.hpp:1249  
#1  boost::crc_optimal<32ul, 79764919u, 4294967295u, 4294967295u, true, true>::process_bytes (this=<optimized out>, buffer=0x7fffba1cad72, byte_count=<optimized out>)  
    at /usr/include/boost/crc.hpp:2098  
#2  mm::archive_io_manager_t::load_appending_internal (this=<optimized out>, sector_loader=..., layer_type=<optimized out>, digest=mm::digests_e::BuiltupAreas, source=<optimized out>)  
```

---

## Comment 1

> Username: arturbac  
> Created at: 2021-06-25 09:30:58 UTC  
> Url: https://github.com/boostorg/crc/issues/17#issuecomment-868367962  

It looks like It is not a problem in bost but with data processed
