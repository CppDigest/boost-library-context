# #28 Fix compat with boost/integer [Closed]

> Username: georgthegreat  
> Created at: 2024-09-03 14:14:15 UTC  
> Updated at: 2024-12-13 11:25:00 UTC  
> Closed at: 2024-12-13 11:25:00 UTC  
> Url: https://github.com/boostorg/crc/pull/28  

Fixes compatibility regression introduced in 470537f62b2dd74f882b25b0c9165ba5baef9e6f.

---

## Comment 1

> Username: FireBurn  
> Created_at: 2024-11-04 10:44:44 UTC  
> Url: https://github.com/boostorg/crc/pull/28#issuecomment-2454375162  

Thanks this fixes an error I was seeing:   
  
```  
error: implicit conversion loses integer precision: 'value_type' (aka 'unsigned long') to 'u32_le' (aka 'unsigned int') [-Werror,-Wshorten-64-to-32]  
   72 |     message.header.crc = crc.checksum();  
      |                        ~ ~~~~^~~~~~~~~~  
  
```

---

## Comment 2

> Username: georgthegreat  
> Created_at: 2024-11-04 14:30:03 UTC  
> Url: https://github.com/boostorg/crc/pull/28#issuecomment-2454866666  

@pdimov, why not merge this (simpler) variant instead of   
https://github.com/boostorg/crc/commit/bd22b63fd2593d615cd8f962950a3f51282761f6

---
