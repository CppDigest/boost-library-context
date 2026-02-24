# #119 - date_time memory leak [Closed]

> Username: Nazario-DApote-Kyklos  
> Created at: 2019-11-29 14:41:04 UTC  
> Updated at: 2020-02-11 05:41:11 UTC  
> Closed at: 2020-02-11 05:41:11 UTC  
> Url: https://github.com/boostorg/date_time/issues/119  

Hi,  
incidentally I ran into this memory leak.   
Here the simple code:  
  
try {  
       //Let's send in month 25 by accident and create an exception  
       string bad_date("20012509"); //2001-??-09  
       date wont_construct(from_undelimited_string(bad_date));  
}  
catch (std::exception& e) {  
  cout << "  Exception: " << e.what() << endl; // memory leak!!!!!!!!  
}  
  
Exception thrown at 0x751A35D2 in TestCpp.exe: Microsoft C++ exception: **boost::wrapexcept<boost::gregorian::bad_month>** at memory location 0x0117E61C.  
Detected memory leaks!  
Dumping objects ->  
{742} normal block at 0x014E1CA0, 4096 bytes long.  
 Data: <                > CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD   
{714} normal block at 0x014CF068, 32 bytes long.  
 Data: <  L   L   L     > D8 EE 4C 01 D8 EE 4C 01 D8 EE 4C 01 01 00 CD CD   
{713} normal block at 0x014DA330, 20 bytes long.  
 Data: <  M   M   N p M > F0 A4 4D 01 F0 A4 4D 01 E0 0B 4E 01 70 A7 4D 01   
{712} normal block at 0x014DA770, 16 bytes long.  
 Data: < 6            N > C8 36 B2 00 01 00 00 00 01 00 00 00 E0 0B 4E 01   
{703} normal block at 0x014DBD88, 8 bytes long.  
 Data: <  N     > FC 0F 4E 01 00 00 00 00   
{702} normal block at 0x014D9F08, 72 bytes long.  
 Data: <  M   M   M     > 08 9F 4D 01 08 9F 4D 01 08 9F 4D 01 01 01 CD CD   
{701} normal block at 0x014DBCE0, 8 bytes long.  
 Data: <  N     > F0 0F 4E 01 00 00 00 00   
{700} normal block at 0x014C6840, 48 bytes long.  
 Data: <@hL @hL @hL     > 40 68 4C 01 40 68 4C 01 40 68 4C 01 01 01 CD CD   
{699} normal block at 0x014DB848, 8 bytes long.  
 Data: <  N     > E4 0F 4E 01 00 00 00 00   
{698} normal block at 0x014C76F0, 48 bytes long.  
 Data: < vL  vL  vL     > F0 76 4C 01 F0 76 4C 01 F0 76 4C 01 01 01 CD CD   
{695} normal block at 0x014DB8B8, 8 bytes long.  
 Data: < L      > 9C 4C B4 00 00 00 00 00   
{694} normal block at 0x014E0BE0, 1072 bytes long.  
 Data: <                > 10 04 00 00 00 00 00 00 00 00 00 00 00 00 1A 00   
{693} normal block at 0x014DB9D0, 8 bytes long.  
 Data: <`L      > 60 4C B4 00 00 00 00 00   
{692} normal block at 0x014CEED8, 32 bytes long.  
 Data: <h L h L h L     > 68 F0 4C 01 68 F0 4C 01 68 F0 4C 01 01 01 CD CD   
{691} normal block at 0x014DBC00, 8 bytes long.  
 Data: <TL  | L > 54 4C B4 00 7C F0 4C 01   
{690} normal block at 0x014DA4F0, 20 bytes long.  
 Data: <0 M 0 M         > 30 A3 4D 01 30 A3 4D 01 CD CD CD CD CD CD CD CD   
{160} normal block at 0x014C74A0, 16 bytes long.  
 Data: <            hvL > 0C A5 B0 00 02 00 00 00 01 00 00 00 68 76 4C 01   
{159} normal block at 0x014C7668, 44 bytes long.  
 Data: <        8       > FC A3 B0 00 00 00 00 00 38 A4 B0 00 04 A3 B0 00   
{158} normal block at 0x014C78E0, 16 bytes long.  
 Data: <            HbM > E8 A4 B0 00 02 00 00 00 01 00 00 00 48 62 4D 01   
{157} normal block at 0x014D6248, 44 bytes long.  
 Data: <        P       > CC A2 B0 00 00 00 00 00 50 A3 B0 00 04 A3 B0 00   
Object dump complete.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2019-12-17 20:23:06 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-566733786  

This is surprising as bad_month is a trivial constructor that I can't see how it's leaking -- here it is:  
  
```  
  struct BOOST_SYMBOL_VISIBLE bad_month : public std::out_of_range  
  {  
    bad_month() : std::out_of_range(std::string("Month number is out of range 1..12")) {}  
  };  
```  
do you think the leak could actually be in the parsing code itself?

---

## Comment 2

> Username: Nazario-DApote-Kyklos  
> Created at: 2019-12-19 09:12:21 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-567403073  

I think you're right.  The problem should be in my test environment.

---

## Comment 3

> Username: mclow  
> Created at: 2019-12-19 14:35:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-567514486  

Ok, here's a actual test case:  
```  
#include <string>  
#include <iostream>  
  
#include "boost/date_time/gregorian/gregorian.hpp"  
  
int main () {  
	using namespace boost::gregorian;  
  
	try {  
		//Let's send in month 25 by accident and create an exception  
		std::string bad_date("20012509"); //2001-??-09  
		date wont_construct(from_undelimited_string(bad_date));  
		}  
	catch (std::exception& e) {  
		std::cout << " Exception: " << e.what() << std::endl; // memory leak!!!!!!!!  
		}  
}  
```  
  
This has no leaks for me. (using leaksanitizer)

---

## Comment 4

> Username: JeffGarland  
> Created at: 2019-12-19 15:16:54 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-567531560  

Marshall -- I assume that's clang on Linux?  Which would make it maybe microsoft specific or a tool false positive.

---

## Comment 5

> Username: mclow  
> Created at: 2019-12-23 15:22:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-568502907  

Both on Linux and Mac OS.

---

## Comment 6

> Username: JeffGarland  
> Created at: 2019-12-23 19:10:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/119#issuecomment-568556623  

@Nazario-DApote-Kyklos - given your last comment and Marshall not finding a leak I'm inclined to close this as not a defect.
