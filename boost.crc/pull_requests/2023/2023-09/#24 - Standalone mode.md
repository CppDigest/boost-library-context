# #24 Standalone mode [Closed]

> Username: YarikTH  
> Created at: 2023-09-23 10:30:57 UTC  
> Updated at: 2023-09-27 17:24:06 UTC  
> Closed at: 2023-09-27 17:24:06 UTC  
> Url: https://github.com/boostorg/crc/pull/24  

# Standalone mode  
  
Boost.Crc has a few boost dependencies, most of which can be substituted with STD library's analogs. So it worth make the library more portable by implementing self-sufficient standalone mode in which Boost.Crc effectively becomes single-header library. In this mode library header can be easily copy-pasted where needed and used as is without boost baggage (bcp copies 425 headers with total of 93718 LOC in boost v1.82.0).  
  
CAUTION: In this mode most of platform specific workarounds and tweaks are not ported, so it might work worse that in normal mode on some exotic platforms.  
  
TODO:  
  
- [X] Add standalone mode in the header  
- [X] Add option in CMakeLists.txt using Boost.Math as a reference  
- [ ] Somehow test it  
- [ ] Update documentation according to the new changes  
  
## Testing  
  
Testing part is tricky. There are several complications at once:  
1. this project is not set up to be modularly autotested on CI. It is tested only as part of full boost bundle in a single configuration  
2. new BOOST_CRC_STANDALONE alters existing Boost::crc target, so new mode can't be tested along with normal mode, separate configuration is needed  
3. existing tests are heavily depend on boost libraries and Jamfile configuration  
  
I think testing part should be skipped for now and be done in the separate PR. And I need to research infrastructure of other libraries to find tips how it can be done.  
  
To ensure it works, I suppose some not over-complicated test on godbolt should be enough.  
  
# Documentation  
  
Documentation is very tricky because it is done in quickbook format which I don't know and I have no idea how building and commit of resulting documentation is done. Is it somehow automated or I have to manually run some jam target and then commit the result? It seems I need to find documentation related commits for hints.

---

## Comment 1

> Username: YarikTH  
> Created_at: 2023-09-23 13:10:17 UTC  
> Updated_at: 2023-09-23 13:16:57 UTC  
> Url: https://github.com/boostorg/crc/pull/24#issuecomment-1732315424  

Test under godbolt https://godbolt.org/z/c6P7Pa4K5  
  
```c++  
#ifdef BOOST_CRC_STANDALONE  
#include <https://raw.githubusercontent.com/YarikTH/boost.crc/standalone/include/boost/crc.hpp>  
#else  
#include <boost/crc.hpp>  
#endif  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
#include <string>  
  
// The type of CRC parameters that can go in a template should be related  
// on the CRC's bit count.  This macro expresses that type in a compact  
// form, but also allows an alternate type for compilers that don't support  
// dependent types (in template value-parameters).  
#if !(defined(BOOST_NO_DEPENDENT_TYPES_IN_TEMPLATE_VALUE_PARAMETERS))  
#define BOOST_CRC_PARM_TYPE typename ::boost::uint_t<Bits>::fast  
#else  
#define BOOST_CRC_PARM_TYPE unsigned long  
#endif  
  
static const std::string checkString = "123456789";  
  
template <std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
          BOOST_CRC_PARM_TYPE InitRem, bool ReflectIn, bool ReflectRem,  
          BOOST_CRC_PARM_TYPE FinalXor>  
uint64_t check_crc_basic() {  
    boost::crc_basic<Bits> crc(TruncPoly, InitRem, FinalXor, ReflectIn, ReflectRem);  
    crc.process_bytes(checkString.data(), checkString.size());  
    return crc.checksum();  
}  
  
template <std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
          BOOST_CRC_PARM_TYPE InitRem, bool ReflectIn, bool ReflectRem,  
          BOOST_CRC_PARM_TYPE FinalXor>  
uint64_t check_crc_optimal() {  
    boost::crc_optimal<Bits, TruncPoly, InitRem, FinalXor,  
                                         ReflectIn, ReflectRem> crc;  
    crc.process_bytes(checkString.data(), checkString.size());  
    return crc.checksum();  
}  
  
template <std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
          BOOST_CRC_PARM_TYPE InitRem, bool ReflectIn, bool ReflectRem,  
          BOOST_CRC_PARM_TYPE FinalXor>  
uint64_t check_crc_func() {  
    return boost::crc<Bits, TruncPoly, InitRem, FinalXor, ReflectIn,  
                      ReflectRem>(checkString.data(), checkString.size());  
}  
  
template <std::size_t Bits>  
std::string to_hex(const uint64_t value) {  
    std::ostringstream ss;  
    ss << "0x" << std::hex << std::setfill('0') << std::uppercase  
       << std::setw(sizeof(typename boost::uint_t<Bits>::fast));  
    ss << value;  
    return ss.str();  
}  
  
template <std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
          BOOST_CRC_PARM_TYPE InitRem, bool ReflectIn, bool ReflectRem,  
          BOOST_CRC_PARM_TYPE FinalXor>  
void check_crc(const char* name, const uint64_t check) {  
    const uint64_t crcBasicResult =  
        check_crc_basic<Bits, TruncPoly, InitRem, ReflectIn, ReflectRem,  
                        FinalXor>();  
    const uint64_t crcOptimalResult =  
        check_crc_optimal<Bits, TruncPoly, InitRem, ReflectIn, ReflectRem,  
                          FinalXor>();  
    const uint64_t crcFuncResult =  
        check_crc_func<Bits, TruncPoly, InitRem, ReflectIn, ReflectRem,  
                       FinalXor>();  
  
    if (crcBasicResult != check) {  
        std::cerr << name << ": result of crc_basic "  
                  << to_hex<Bits>(crcBasicResult) << " doesn't match expected "  
                  << to_hex<Bits>(check) << "\n";  
    }  
  
    if (crcOptimalResult != check) {  
        std::cerr << name << ": result of crc_optimal "  
                  << to_hex<Bits>(crcOptimalResult)  
                  << " doesn't match expected " << to_hex<Bits>(check) << "\n";  
    }  
  
    if (crcFuncResult != check) {  
        std::cerr << name << ": result of crc<>() "  
                  << to_hex<Bits>(crcFuncResult) << " doesn't match expected "  
                  << to_hex<Bits>(check) << "\n";  
    }  
}  
  
template <class CrcType>  
void check_predefined_crc_optimal(const char* name, const uint64_t check) {  
    CrcType crc;  
    crc.process_bytes(checkString.data(), checkString.size());  
    const uint64_t result = crc.checksum();  
    const size_t Bits = CrcType::bit_count;  
    if (result != check) {  
        std::cerr << name << ": result of crc_optimal " << to_hex<Bits>(result)  
                  << " doesn't match expected " << to_hex<Bits>(check) << "\n";  
    }  
}  
  
void check_predefined()  
{  
    // Computation type for ARC|CRC-16|CRC-IBM|CRC-16/ARC|CRC-16/LHA standard  
    // "CRC-16/ARC" in table below  
    check_predefined_crc_optimal<boost::crc_16_type>("crc_16_type", 0xBB3D);  
  
    // Computation type for CRC-16/CCITT-FALSE standard  
    // "CRC-16/CCITT-FALSE" in table below  
    check_predefined_crc_optimal<boost::crc_ccitt_false_t>("crc_ccitt_false_t",  
                                                           0x29B1);  
  
    // Computation type for the actual  
    // KERMIT|CRC-16/CCITT|CRC-16/CCITT-TRUE|CRC-CCITT standard "CRC-16/KERMIT"  
    // in table below  
    check_predefined_crc_optimal<boost::crc_ccitt_true_t>("crc_ccitt_true_t",  
                                                          0x2189);  
  
    // Computation type that I mistakenly called the XMODEM standard; it inverts  
    // both reflection parameters and reflects the truncated divisor (Don't  
    // use?!) Not in table below, so I just hardcoded 0x0C73 as an actual  
    // calculation result  
    check_predefined_crc_optimal<boost::crc_xmodem_type>("crc_xmodem_type",  
                                                         0x0C73);  
  
    // Computation type for the actual XMODEM|ZMODEM|CRC-16/ACORN standard  
    // "CRC-16/XMODEM" in table below  
    check_predefined_crc_optimal<boost::crc_xmodem_t>("crc_xmodem_t", 0x31C3);  
  
    // Computation type for CRC-32|CRC-32/ADCCP|PKZIP standard  
    // "CRC-32/zlib" in table below  
    check_predefined_crc_optimal<boost::crc_32_type>("crc_32_type", 0xCBF43926);  
}  
  
void check_crc_catalog()  
{  
#define CHECK_CRC(Name, Width, Poly, Init, RefIn, RefOut, XorOut, Check) \  
    check_crc<Width, Poly, Init, RefIn, RefOut, XorOut>(#Name, Check)  
  
    // It seems that crc_optimal has a bug, that doesn't allow to calculate  
    // "CRC-14/DARC" and "CRC-24/BLE" properly, while crc_basic calculates result correctly  
  
    // From https://reveng.sourceforge.io/crc-catalogue/all.htm  
    //         Name                     Width Poly                      Init                      RefIn  RefOut XorOut                    Check  
    CHECK_CRC("CRC-3/GSM",                3,  0x3,                      0x0,                      false, false, 0x7,                      0x4);  
    CHECK_CRC("CRC-3/ROHC",               3,  0x3,                      0x7,                      true,  true,  0x0,                      0x6);  
    CHECK_CRC("CRC-4/G-704",              4,  0x3,                      0x0,                      true,  true,  0x0,                      0x7);  
    CHECK_CRC("CRC-4/INTERLAKEN",         4,  0x3,                      0xF,                      false, false, 0xF,                      0xB);  
    CHECK_CRC("CRC-5/EPC-C1G2",           5,  0x09,                     0x09,                     false, false, 0x00,                     0x00);  
    CHECK_CRC("CRC-5/G-704",              5,  0x15,                     0x00,                     true,  true,  0x00,                     0x07);  
    CHECK_CRC("CRC-5/USB",                5,  0x05,                     0x1F,                     true,  true,  0x1F,                     0x19);  
    CHECK_CRC("CRC-6/CDMA2000-A",         6,  0x27,                     0x3F,                     false, false, 0x00,                     0x0D);  
    CHECK_CRC("CRC-6/CDMA2000-B",         6,  0x07,                     0x3F,                     false, false, 0x00,                     0x3B);  
    CHECK_CRC("CRC-6/DARC",               6,  0x19,                     0x00,                     true,  true,  0x00,                     0x26);  
    CHECK_CRC("CRC-6/G-704",              6,  0x03,                     0x00,                     true,  true,  0x00,                     0x06);  
    CHECK_CRC("CRC-6/GSM",                6,  0x2F,                     0x00,                     false, false, 0x3F,                     0x13);  
    CHECK_CRC("CRC-7/MMC",                7,  0x09,                     0x00,                     false, false, 0x00,                     0x75);  
    CHECK_CRC("CRC-7/ROHC",               7,  0x4F,                     0x7F,                     true,  true,  0x00,                     0x53);  
    CHECK_CRC("CRC-7/UMTS",               7,  0x45,                     0x00,                     false, false, 0x00,                     0x61);  
    CHECK_CRC("CRC-8/AUTOSAR",            8,  0x2F,                     0xFF,                     false, false, 0xFF,                     0xDF);  
    CHECK_CRC("CRC-8/BLUETOOTH",          8,  0xA7,                     0x00,                     true,  true,  0x00,                     0x26);  
    CHECK_CRC("CRC-8/CDMA2000",           8,  0x9B,                     0xFF,                     false, false, 0x00,                     0xDA);  
    CHECK_CRC("CRC-8/DARC",               8,  0x39,                     0x00,                     true,  true,  0x00,                     0x15);  
    CHECK_CRC("CRC-8/DVB-S2",             8,  0xD5,                     0x00,                     false, false, 0x00,                     0xBC);  
    CHECK_CRC("CRC-8/GSM-A",              8,  0x1D,                     0x00,                     false, false, 0x00,                     0x37);  
    CHECK_CRC("CRC-8/GSM-B",              8,  0x49,                     0x00,                     false, false, 0xFF,                     0x94);  
    CHECK_CRC("CRC-8/HITAG",              8,  0x1D,                     0xFF,                     false, false, 0x00,                     0xB4);  
    CHECK_CRC("CRC-8/I-432-1",            8,  0x07,                     0x00,                     false, false, 0x55,                     0xA1);  
    CHECK_CRC("CRC-8/I-CODE",             8,  0x1D,                     0xFD,                     false, false, 0x00,                     0x7E);  
    CHECK_CRC("CRC-8/LTE",                8,  0x9B,                     0x00,                     false, false, 0x00,                     0xEA);  
    CHECK_CRC("CRC-8/MAXIM-DOW",          8,  0x31,                     0x00,                     true,  true,  0x00,                     0xA1);  
    CHECK_CRC("CRC-8/MIFARE-MAD",         8,  0x1D,                     0xC7,                     false, false, 0x00,                     0x99);  
    CHECK_CRC("CRC-8/NRSC-5",             8,  0x31,                     0xFF,                     false, false, 0x00,                     0xF7);  
    CHECK_CRC("CRC-8/OPENSAFETY",         8,  0x2F,                     0x00,                     false, false, 0x00,                     0x3E);  
    CHECK_CRC("CRC-8/ROHC",               8,  0x07,                     0xFF,                     true,  true,  0x00,                     0xD0);  
    CHECK_CRC("CRC-8/SAE-J1850",          8,  0x1D,                     0xFF,                     false, false, 0xFF,                     0x4B);  
    CHECK_CRC("CRC-8/SMBUS",              8,  0x07,                     0x00,                     false, false, 0x00,                     0xF4);  
    CHECK_CRC("CRC-8/TECH-3250",          8,  0x1D,                     0xFF,                     true,  true,  0x00,                     0x97);  
    CHECK_CRC("CRC-8/WCDMA",              8,  0x9B,                     0x00,                     true,  true,  0x00,                     0x25);  
    CHECK_CRC("CRC-10/ATM",               10, 0x233,                    0x000,                    false, false, 0x000,                    0x199);  
    CHECK_CRC("CRC-10/CDMA2000",          10, 0x3D9,                    0x3FF,                    false, false, 0x000,                    0x233);  
    CHECK_CRC("CRC-10/GSM",               10, 0x175,                    0x000,                    false, false, 0x3FF,                    0x12A);  
    CHECK_CRC("CRC-11/FLEXRAY",           11, 0x385,                    0x01A,                    false, false, 0x000,                    0x5A3);  
    CHECK_CRC("CRC-11/UMTS",              11, 0x307,                    0x000,                    false, false, 0x000,                    0x061);  
    CHECK_CRC("CRC-12/CDMA2000",          12, 0xF13,                    0xFFF,                    false, false, 0x000,                    0xD4D);  
    CHECK_CRC("CRC-12/DECT",              12, 0x80F,                    0x000,                    false, false, 0x000,                    0xF5B);  
    CHECK_CRC("CRC-12/GSM",               12, 0xD31,                    0x000,                    false, false, 0xFFF,                    0xB34);  
    CHECK_CRC("CRC-12/UMTS",              12, 0x80F,                    0x000,                    false, true,  0x000,                    0xDAF);  
    CHECK_CRC("CRC-13/BBC",               13, 0x1CF5,                   0x0000,                   false, false, 0x0000,                   0x04FA);  
    CHECK_CRC("CRC-14/DARC",              14, 0x0805,                   0x0000,                   true,  true,  0x0000,                   0x082D);  
    CHECK_CRC("CRC-14/GSM",               14, 0x202D,                   0x0000,                   false, false, 0x3FFF,                   0x30AE);  
    CHECK_CRC("CRC-15/CAN",               15, 0x4599,                   0x0000,                   false, false, 0x0000,                   0x059E);  
    CHECK_CRC("CRC-15/MPT1327",           15, 0x6815,                   0x0000,                   false, false, 0x0001,                   0x2566);  
    CHECK_CRC("CRC-16/ARC",               16, 0x8005,                   0x0000,                   true,  true,  0x0000,                   0xBB3D);  
    CHECK_CRC("CRC-16/CDMA2000",          16, 0xC867,                   0xFFFF,                   false, false, 0x0000,                   0x4C06);  
    CHECK_CRC("CRC-16/CMS",               16, 0x8005,                   0xFFFF,                   false, false, 0x0000,                   0xAEE7);  
    CHECK_CRC("CRC-16/DDS-110",           16, 0x8005,                   0x800D,                   false, false, 0x0000,                   0x9ECF);  
    CHECK_CRC("CRC-16/DECT-R",            16, 0x0589,                   0x0000,                   false, false, 0x0001,                   0x007E);  
    CHECK_CRC("CRC-16/DECT-X",            16, 0x0589,                   0x0000,                   false, false, 0x0000,                   0x007F);  
    CHECK_CRC("CRC-16/DNP",               16, 0x3D65,                   0x0000,                   true,  true,  0xFFFF,                   0xEA82);  
    CHECK_CRC("CRC-16/EN-13757",          16, 0x3D65,                   0x0000,                   false, false, 0xFFFF,                   0xC2B7);  
    CHECK_CRC("CRC-16/GENIBUS",           16, 0x1021,                   0xFFFF,                   false, false, 0xFFFF,                   0xD64E);  
    CHECK_CRC("CRC-16/GSM",               16, 0x1021,                   0x0000,                   false, false, 0xFFFF,                   0xCE3C);  
    CHECK_CRC("CRC-16/IBM-3740",          16, 0x1021,                   0xFFFF,                   false, false, 0x0000,                   0x29B1);  
    CHECK_CRC("CRC-16/IBM-SDLC",          16, 0x1021,                   0xFFFF,                   true,  true,  0xFFFF,                   0x906E);  
    CHECK_CRC("CRC-16/ISO-IEC-14443-3-A", 16, 0x1021,                   0xC6C6,                   true,  true,  0x0000,                   0xBF05);  
    CHECK_CRC("CRC-16/KERMIT",            16, 0x1021,                   0x0000,                   true,  true,  0x0000,                   0x2189);  
    CHECK_CRC("CRC-16/LJ1200",            16, 0x6F63,                   0x0000,                   false, false, 0x0000,                   0xBDF4);  
    CHECK_CRC("CRC-16/M17",               16, 0x5935,                   0xFFFF,                   false, false, 0x0000,                   0x772B);  
    CHECK_CRC("CRC-16/MAXIM-DOW",         16, 0x8005,                   0x0000,                   true,  true,  0xFFFF,                   0x44C2);  
    CHECK_CRC("CRC-16/MCRF4XX",           16, 0x1021,                   0xFFFF,                   true,  true,  0x0000,                   0x6F91);  
    CHECK_CRC("CRC-16/MODBUS",            16, 0x8005,                   0xFFFF,                   true,  true,  0x0000,                   0x4B37);  
    CHECK_CRC("CRC-16/NRSC-5",            16, 0x080B,                   0xFFFF,                   true,  true,  0x0000,                   0xA066);  
    CHECK_CRC("CRC-16/OPENSAFETY-A",      16, 0x5935,                   0x0000,                   false, false, 0x0000,                   0x5D38);  
    CHECK_CRC("CRC-16/OPENSAFETY-B",      16, 0x755B,                   0x0000,                   false, false, 0x0000,                   0x20FE);  
    CHECK_CRC("CRC-16/PROFIBUS",          16, 0x1DCF,                   0xFFFF,                   false, false, 0xFFFF,                   0xA819);  
    CHECK_CRC("CRC-16/RIELLO",            16, 0x1021,                   0xB2AA,                   true,  true,  0x0000,                   0x63D0);  
    CHECK_CRC("CRC-16/SPI-FUJITSU",       16, 0x1021,                   0x1D0F,                   false, false, 0x0000,                   0xE5CC);  
    CHECK_CRC("CRC-16/T10-DIF",           16, 0x8BB7,                   0x0000,                   false, false, 0x0000,                   0xD0DB);  
    CHECK_CRC("CRC-16/TELEDISK",          16, 0xA097,                   0x0000,                   false, false, 0x0000,                   0x0FB3);  
    CHECK_CRC("CRC-16/TMS37157",          16, 0x1021,                   0x89EC,                   true,  true,  0x0000,                   0x26B1);  
    CHECK_CRC("CRC-16/UMTS",              16, 0x8005,                   0x0000,                   false, false, 0x0000,                   0xFEE8);  
    CHECK_CRC("CRC-16/USB",               16, 0x8005,                   0xFFFF,                   true,  true,  0xFFFF,                   0xB4C8);  
    CHECK_CRC("CRC-16/XMODEM",            16, 0x1021,                   0x0000,                   false, false, 0x0000,                   0x31C3);  
    CHECK_CRC("CRC-17/CAN-FD",            17, 0x1685B,                  0x00000,                  false, false, 0x00000,                  0x04F03);  
    CHECK_CRC("CRC-21/CAN-FD",            21, 0x102899,                 0x000000,                 false, false, 0x000000,                 0x0ED841);  
    CHECK_CRC("CRC-24/BLE",               24, 0x00065B,                 0x555555,                 true,  true,  0x000000,                 0xC25A56);  
    CHECK_CRC("CRC-24/FLEXRAY-A",         24, 0x5D6DCB,                 0xFEDCBA,                 false, false, 0x000000,                 0x7979BD);  
    CHECK_CRC("CRC-24/FLEXRAY-B",         24, 0x5D6DCB,                 0xABCDEF,                 false, false, 0x000000,                 0x1F23B8);  
    CHECK_CRC("CRC-24/INTERLAKEN",        24, 0x328B63,                 0xFFFFFF,                 false, false, 0xFFFFFF,                 0xB4F3E6);  
    CHECK_CRC("CRC-24/LTE-A",             24, 0x864CFB,                 0x000000,                 false, false, 0x000000,                 0xCDE703);  
    CHECK_CRC("CRC-24/LTE-B",             24, 0x800063,                 0x000000,                 false, false, 0x000000,                 0x23EF52);  
    CHECK_CRC("CRC-24/OPENPGP",           24, 0x864CFB,                 0xB704CE,                 false, false, 0x000000,                 0x21CF02);  
    CHECK_CRC("CRC-24/OS-9",              24, 0x800063,                 0xFFFFFF,                 false, false, 0xFFFFFF,                 0x200FA5);  
    CHECK_CRC("CRC-30/CDMA",              30, 0x2030B9C7,               0x3FFFFFFF,               false, false, 0x3FFFFFFF,               0x04C34ABF);  
    CHECK_CRC("CRC-31/PHILIPS",           31, 0x04C11DB7,               0x7FFFFFFF,               false, false, 0x7FFFFFFF,               0x0CE9E46C);  
    CHECK_CRC("CRC-32/AIXM",              32, 0x814141AB,               0x00000000,               false, false, 0x00000000,               0x3010BF7F);  
    CHECK_CRC("CRC-32/AUTOSAR",           32, 0xF4ACFB13,               0xFFFFFFFF,               true,  true,  0xFFFFFFFF,               0x1697D06A);  
    CHECK_CRC("CRC-32/BASE91-D",          32, 0xA833982B,               0xFFFFFFFF,               true,  true,  0xFFFFFFFF,               0x87315576);  
    CHECK_CRC("CRC-32/BZIP2",             32, 0x04C11DB7,               0xFFFFFFFF,               false, false, 0xFFFFFFFF,               0xFC891918);  
    CHECK_CRC("CRC-32/CD-ROM-EDC",        32, 0x8001801B,               0x00000000,               true,  true,  0x00000000,               0x6EC2EDC4);  
    CHECK_CRC("CRC-32/CKSUM",             32, 0x04C11DB7,               0x00000000,               false, false, 0xFFFFFFFF,               0x765E7680);  
    CHECK_CRC("CRC-32/ISCSI",             32, 0x1EDC6F41,               0xFFFFFFFF,               true,  true,  0xFFFFFFFF,               0xE3069283);  
    CHECK_CRC("CRC-32/ISO-HDLC",          32, 0x04C11DB7,               0xFFFFFFFF,               true,  true,  0xFFFFFFFF,               0xCBF43926);  
    CHECK_CRC("CRC-32/JAMCRC",            32, 0x04C11DB7,               0xFFFFFFFF,               true,  true,  0x00000000,               0x340BC6D9);  
    CHECK_CRC("CRC-32/MEF",               32, 0x741B8CD7,               0xFFFFFFFF,               true,  true,  0x00000000,               0xD2C22F51);  
    CHECK_CRC("CRC-32/MPEG-2",            32, 0x04C11DB7,               0xFFFFFFFF,               false, false, 0x00000000,               0x0376E6E7);  
    CHECK_CRC("CRC-32/XFER",              32, 0x000000AF,               0x00000000,               false, false, 0x00000000,               0xBD0BE338);  
    CHECK_CRC("CRC-40/GSM",               40, 0x0004820009,             0x0000000000,             false, false, 0xFFFFFFFFFF,             0xD4164FC646);  
    CHECK_CRC("CRC-64/ECMA-182",          64, 0x42F0E1EBA9EA3693,       0x0000000000000000,       false, false, 0x0000000000000000,       0x6C40DF5F0B497347);  
    CHECK_CRC("CRC-64/GO-ISO",            64, 0x000000000000001B,       0xFFFFFFFFFFFFFFFF,       true,  true,  0xFFFFFFFFFFFFFFFF,       0xB90956C775A41001);  
    CHECK_CRC("CRC-64/MS",                64, 0x259C84CBA6426349,       0xFFFFFFFFFFFFFFFF,       true,  true,  0x0000000000000000,       0x75D4B74F024ECEEA);  
    CHECK_CRC("CRC-64/REDIS",             64, 0xAD93D23594C935A9,       0x0000000000000000,       true,  true,  0x0000000000000000,       0xE9C6D914C4B8D9CA);  
    CHECK_CRC("CRC-64/WE",                64, 0x42F0E1EBA9EA3693,       0xFFFFFFFFFFFFFFFF,       false, false, 0xFFFFFFFFFFFFFFFF,       0x62EC59E3F1A4F00A);  
    CHECK_CRC("CRC-64/XZ",                64, 0x42F0E1EBA9EA3693,       0xFFFFFFFFFFFFFFFF,       true,  true,  0xFFFFFFFFFFFFFFFF,       0x995DC9BBDF1939FA);  
  
    // boost::uint_t can't handle uint128_t needed for such calculation  
    //CHECK_CRC("CRC-82/DARC",              82, 0x0308C0111011401440411,  0x000000000000000000000,  true,  true,  0x000000000000000000000,  0x09EA83F625023801FD612);  
}  
  
int main() {  
    check_predefined();  
    check_crc_catalog();  
}  
```  
  
Test highlights the fact, that https://github.com/boostorg/crc/pull/15 is not fixed yet, despite PR that fix it was added 2 years ago.

---
