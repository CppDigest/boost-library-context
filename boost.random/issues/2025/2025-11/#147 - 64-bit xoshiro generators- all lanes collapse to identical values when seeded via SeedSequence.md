# #147 - 64-bit xoshiro generators: all lanes collapse to identical values when seeded via SeedSequence [Closed]

> Username: Eg0r1337  
> Created at: 2025-11-11 21:35:25 UTC  
> Updated at: 2025-11-14 14:33:43 UTC  
> Closed at: 2025-11-12 10:25:32 UTC  
> Url: https://github.com/boostorg/random/issues/147  

xoshiro256mm (and other 64-bit xoshiro) seeded via SeedSequence collapse all lanes to the same 64-bit value due to two-at-a-time generate pattern.  
  
When seeding 64-bit xoshiro engines with a SeedSequence, the current code requests two 32-bit words per lane in a loop, which is permitted to return the same first two words on every call (In the boost::random::seed_seq implementation it does); as a result, all 64-bit lanes in the state become identical for std::seed_seq and boost::random::seed_seq, which causes the state to look similar to this:  
  
```  
    [0]: 3a 46 19 3d db 6d 2d 37  
    [1]: 3a 46 19 3d db 6d 2d 37  
    [2]: 3a 46 19 3d db 6d 2d 37  
    [3]: 3a 46 19 3d db 6d 2d 37  
```  
  
The 32-bit xoshiro128mm path is not affected because it fills the entire state via a single generate call, so it does not suffer from per-call repetition.  
  
Code in [xoshiro_base.hpp](https://github.com/boostorg/random/blob/develop/include/boost/random/detail/xoshiro_base.hpp#L54):  
  
```cpp  
    template <typename Sseq>  
    inline void sseq_seed_64(Sseq& seq)  
    {  
        for (auto& i : state_)  
        {  
            std::array<std::uint32_t, 2> seeds;  
            seq.generate(seeds.begin(), seeds.end());  
  
            i = concatenate(seeds[0], seeds[1]);  
        }  
    }  
```  
  
Code to reproduce the issue:  
  
```cpp  
#include <array>  
#include <cstdint>  
#include <iomanip>  
#include <iostream>  
#include <iterator>  
#include <numeric>  
#include <random>  
#include <vector>  
  
#include <boost/random/seed_seq.hpp>  
#include <boost/random/xoshiro.hpp>  
  
template <class Arr>  
bool all_words_equal(const Arr& a) {  
  for (std::size_t i = 1; i < a.size(); ++i) if (a[i] != a[0]) return false;  
  return true;  
}  
  
template <class Arr>  
void print_state_hex(const Arr& a, const char* label) {  
  std::cout << label << ":\n";  
  std::cout << "  words:";  
  for (auto w : a) {  
    if constexpr (sizeof(typename Arr::value_type) == 4) {  
      std::cout << " 0x" << std::hex << std::setw(8) << std::setfill('0')  
                << static_cast<std::uint32_t>(w) << std::dec;  
    } else {  
      std::cout << " 0x" << std::hex << std::setw(16) << std::setfill('0')  
                << static_cast<std::uint64_t>(w) << std::dec;  
    }  
  }  
  std::cout << "\n";  
  
  std::cout << "  bytes per word (LE):\n";  
  for (std::size_t i = 0; i < a.size(); ++i) {  
    std::cout << "    [" << i << "]:";  
    auto w = a[i];  
    for (unsigned b = 0; b < sizeof(typename Arr::value_type); ++b) {  
      uint8_t byte = static_cast<uint8_t>((w >> (8 * b)) & 0xFFu);  
      std::cout << " " << std::hex << std::setw(2) << std::setfill('0')  
                << static_cast<unsigned>(byte) << std::dec;  
    }  
    std::cout << "\n";  
  }  
}  
  
template <class Engine, class SSeq>  
void test_engine_with_sseq(const char* title, SSeq& sseq) {  
  Engine eng(sseq);  
  auto st = eng.state();  
  print_state_hex(st, title);  
  bool words_equal = all_words_equal(st);  
  std::cout << "  all words equal? " << (words_equal ? "YES" : "NO") << "\n";  
}  
  
int main() {  
  std::vector<std::uint32_t> seed_words = {  
      0x12345678u, 0x9abcdef0u, 0xc0ffee12u, 0xdeadbeefu  
  };  
  
  std::seed_seq stdseq(seed_words.begin(), seed_words.end());  
  boost::random::seed_seq bseq(seed_words.begin(), seed_words.end());  
  
  // Demonstrate small-generate repetition  
  {  
    std::array<std::uint32_t, 2> a{}, b{};  
    stdseq.generate(a.begin(), a.end());  
    stdseq.generate(b.begin(), b.end());  
    std::cout << "std::seed_seq two-at-a-time demo:\n";  
    std::cout << "  first 2: 0x" << std::hex << std::setw(8) << std::setfill('0') << a[0]  
              << " 0x" << std::setw(8) << a[1] << std::dec << "\n";  
    std::cout << "  next  2: 0x" << std::hex << std::setw(8) << std::setfill('0') << b[0]  
              << " 0x" << std::setw(8) << b[1] << std::dec << "\n\n";  
  }  
  
  // xoshiro128mm (4 x 32-bit state)  
  test_engine_with_sseq<boost::random::xoshiro128mm>("xoshiro128mm seeded with std::seed_seq", stdseq);  
  
  std::seed_seq stdseq2(seed_words.begin(), seed_words.end());  
  test_engine_with_sseq<boost::random::xoshiro128mm>("xoshiro128mm seeded with boost::seed_seq", bseq);  
  
  // xoshiro256mm (4 x 64-bit state)  
  std::seed_seq stdseq3(seed_words.begin(), seed_words.end());  
  boost::random::seed_seq bseq2(seed_words.begin(), seed_words.end());  
  test_engine_with_sseq<boost::random::xoshiro256mm>("xoshiro256mm seeded with std::seed_seq", stdseq3);  
  test_engine_with_sseq<boost::random::xoshiro256mm>("xoshiro256mm seeded with boost::seed_seq", bseq2);  
  
  return 0;  
}  
```  
  
Proposed fix:  
  
```cpp  
template <typename Sseq>  
inline void sseq_seed_64(Sseq& seq)  
{  
    std::array<std::uint32_t, state_.size() * 2> seeds;  
    seq.generate(seeds.begin(), seeds.end());  
  
    for (std::size_t i = 0; i < state_.size(); ++i)  
    {  
        state_[i] = concatenate(seeds[2*i], seeds[2*i + 1]);  
    }  
}  
```

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-14 14:33:43 UTC  
> Url: https://github.com/boostorg/random/issues/147#issuecomment-3533074651  

FYI this fix will ship with Boost 1.90 in a few weeks. Thanks for the diff and solid reproducer
