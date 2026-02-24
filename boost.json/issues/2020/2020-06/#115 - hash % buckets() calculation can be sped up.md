# #115 - hash % buckets() calculation can be sped up [Closed]

> Username: joaquintides  
> Created at: 2020-06-21 09:55:02 UTC  
> Updated at: 2020-07-16 21:40:16 UTC  
> Closed at: 2020-07-16 21:40:16 UTC  
> Url: https://github.com/boostorg/json/issues/115  

Your internal hash table uses a prime number table `lists` for bucket array sizes and calculates the position of a value `hash` within the bucket array as `hash % buckets()`. Modulo calculation is an expensive operation involving CPU integer division: turns out this can be _significantly_ sped up by eliminating run-time  modulo ops altogether with the following technique:  
  
* Make `detail::object_impl::table` store the index into the prime number table rather than the prime itself (member `buckets`).  
* Replace `hash%buckets()` by a call to a function of the form:  
```  
std::size_t position(std::size_t hash,std::size_t buckets_size_index)  
{  
  switch(buckets_size_index){  
    default:  
    case 0: return 0;  
    case 1: return hash%list[1];  
    case 2: return hash%list[2];  
    case 3: return hash%list[3];  
    case 4: return hash%list[4];  
    case 5: return hash%list[5];  
    case 6: return hash%list[6];  
    case 7: return hash%list[7];  
    case 8: return hash%list[8];  
    case 9: return hash%list[9];  
    case 10: return hash%list[10];  
    case 11: return hash%list[11];  
    case 12: return hash%list[12];  
    case 13: return hash%list[13];  
    case 14: return hash%list[14];  
    case 15: return hash%list[15];  
    case 16: return hash%list[16];  
    case 17: return hash%list[17];  
    case 18: return hash%list[18];  
    case 19: return hash%list[19];  
    case 20: return hash%list[20];  
    case 21: return hash%list[21];  
    case 22: return hash%list[22];  
    case 23: return hash%list[23];  
    case 24: return hash%list[24];  
    case 25: return hash%list[25];  
    case 26: return hash%list[26];  
    case 27: return hash%list[27];  
    case 28: return hash%list[28];  
    case 29: return hash%list[29];  
    case 30: return hash%list[30];  
    case 31: return hash%list[31];  
    case 32: return hash%list[32];  
    case 33: return hash%list[33];  
    case 34: return hash%list[34];  
    case 35: return hash%list[35];  
    case 36: return hash%list[36];  
    case 37: return hash%list[37];  
    case 38: return hash%list[38];  
    case 39: return hash%list[39];  
    case 40: return hash%list[40];  
    case 41: return hash%list[41];  
    case 42: return hash%list[42];  
    case 43: return hash%list[43];  
    case 44: return hash%list[44];  
    case 45: return hash%list[45];  
    case 46: return hash%list[46];  
    case 47: return hash%list[47];  
    case 48: return hash%list[48];  
    case 49: return hash%list[49];  
    case 50: return hash%list[50];  
    case 51: return hash%list[51];  
    case 52: return hash%list[52];  
    case 53: return hash%list[53];  
    case 54: return hash%list[54];  
    case 55: return hash%list[55];  
    case 56: return hash%list[56];  
    case 57: return hash%list[57];  
    case 58: return hash%list[58];  
    case 59: return hash%list[59];  
    case 60: return hash%list[60];  
    case 61: return hash%list[61];  
    case 62: return hash%list[62];  
    case 63: return hash%list[63];  
    case 64: return hash%list[64];  
    case 65: return hash%list[65];  
    case 66: return hash%list[66];  
  }  
}  
```  
Note that each `case` in `position` calculates the modulo of `hash` for a compile-time constant: the compiler knows how to implement this, in each `case`, as an operation _not_ involving integer division. For more context, look for "Integer division by a constant" in [this article](https://queue.acm.org/detail.cfm?id=3372264) by Matt Godbolt.

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-07-07 08:22:15 UTC  
> Url: https://github.com/boostorg/json/issues/115#issuecomment-654684100  

Have you measured how much performance this gains you?

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-07-10 22:51:37 UTC  
> Updated at: 2020-07-10 22:52:01 UTC  
> Url: https://github.com/boostorg/json/issues/115#issuecomment-656928284  

@joaquintides Sorry for the late response.   
  
I'll post complete numbers in the PR later, but we saw a performance gain around +10% for benchmarks that are object heavy (citm_catalog, citm_catalog_nws & random). Thank you for the suggestion, it gave a very significant improvement :)
