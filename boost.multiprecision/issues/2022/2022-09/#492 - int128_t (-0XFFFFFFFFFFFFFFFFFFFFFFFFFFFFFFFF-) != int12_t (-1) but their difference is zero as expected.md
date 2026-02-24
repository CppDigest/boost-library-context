# #492 - int128_t ("0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF") != int12_t (-1) but their difference is zero as expected [Closed]

> Username: CatrielCarbonera  
> Created at: 2022-09-02 12:34:41 UTC  
> Updated at: 2022-09-02 17:54:16 UTC  
> Closed at: 2022-09-02 17:54:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/492  

The value int128_t (-1) is not equal to int128_t ("0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF") , but their difference is zero as expected. The following program illustrates the issue:  
  
```  
void TestEqualityof_int128_t ()  
{  
    using namespace boost::multiprecision;  
  
    const int128_t minusOne ("0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF");  
  
    // Using difference to detect equality  
    if ((minusOne - int128_t (-1)) == 0)  
        std::cout << "Using test (minusOne - int128_t (-1)), are equal." << std::endl;  
    else  
        std::cout << "Using test (minusOne - int128_t (-1)), are not equal." << std::endl;  
  
    // Using equality  
    if (minusOne == int128_t (-1))  
        std::cout << "Using test (minusOne == int128_t (-1)), are equal." << std::endl;  
    else  
        std::cout << "Using test (minusOne == int128_t (-1)), are not equal." << std::endl;  
}  
```  
  
Which produces the following output:  
  
```  
Using test (minusOne - int128_t (-1)), are equal.  
Using test (minusOne == int128_t (-1)), are not equal.  
```  
  
The same problem can be found with every int.

---

## Comment 1

> Username: CatrielCarbonera  
> Created at: 2022-09-02 12:38:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/492#issuecomment-1235453155  

I'm using Visual Studio 2022.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-09-02 17:54:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/492#issuecomment-1235767749  

This is to be expected as these are signed-magnitude integers, NOT 2's complement integers.  So 0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF is a positive value which does fit inside an int128_t (because it has 128-bits magnitude PLUS a sign).  
  
Then also note that `x-y == 0` does NOT imply that `x == y`, in this case we are adding 1 to 0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF which then overflows the representation and wraps around to zero.  
  
Note that signed integer overflow is undefined behaviour in C/C++, likewise you are not allowed to rely on signed-integers being 2's complement in C99 - I have a hunch that C++20 (or maybe 2b) does make that guarantee though.  Either way assigning an overflowing bit-value to a signed integer is undefined behaviour which is allowed to shoot you in both feet ;)
