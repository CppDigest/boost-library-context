# #22 Reflect a8dd4858 to augmented_crc_modulo_word_update() too [Merged]

> Username: mpsuzuki  
> Created at: 2023-09-16 06:59:45 UTC  
> Updated at: 2023-09-27 18:09:37 UTC  
> Merged at: 2023-09-27 17:46:14 UTC  
> Closed at: 2023-09-27 17:46:15 UTC  
> Url: https://github.com/boostorg/crc/pull/22  

The commit a8dd4858 replaced the operator "not" by the conventional "!" in crc_modulo_word_update() for MSVC. But "not" is still used for augmented_crc_modulo_word_update(). This patch replaces the "not" in augmented_crc_modulo_word_update() by "!".

---

## Comment 1

> Username: mpsuzuki  
> Created_at: 2023-09-27 18:09:36 UTC  
> Url: https://github.com/boostorg/crc/pull/22#issuecomment-1737860325  

Thanks! 👍

---
