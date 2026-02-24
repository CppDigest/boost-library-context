# #8 - left operand of shift expression  ‘(-1 << 11)’ is negative [Closed]

> Username: tendollargold  
> Created at: 2020-03-27 19:22:41 UTC  
> Updated at: 2024-04-13 17:42:46 UTC  
> Closed at: 2024-04-13 17:42:45 UTC  
> Url: https://github.com/boostorg/crc/issues/8  

Hi,  
So my colleagues I support state their is an issue with the boost crc.  I've found two other instances of this error.  The solution found in GitHub is to use -fpermissive or -std=gnu++98.  By doing so, the code compiles and executes.    
  
RHEL 8.1, boost 1.66  
g++ g++ (GCC) 8.3.1 20190507 (Red Hat 8.3.1-4)  
  
The test code:  
#include <boost/crc.hpp>  
#include <iostream>  
  
int main()  
{  
    boost::crc_basic<11> a_crc(0x017F);  
    a_crc.process_bit(0x8000);  
    std::cout << "Checksum = " << a_crc.checksum() << std::ends;  
    a_crc.reset();  
}  
  
gives this output when using no options: g++  -o testcrc testcrc.C  
  
...  
/usr/include/boost/crc.hpp:350:9: error: left operand of shift expression ‘(-1 << 11)’ is negative [-fpermissive]  
         BOOST_STATIC_CONSTANT( least, sig_bits = (~( ~(least( 0u )) << Bits )) );  
  
Is this a bug or expected behavior?  
Thank you.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-13 17:42:45 UTC  
> Url: https://github.com/boostorg/crc/issues/8#issuecomment-2053712771  

I don't see this line in `crc.hpp`, so I assume this is no longer relevant.
