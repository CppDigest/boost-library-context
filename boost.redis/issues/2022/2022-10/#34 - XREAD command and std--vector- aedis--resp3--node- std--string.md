# #34 - XREAD command and std::vector< aedis::resp3::node< std::string > > [Closed]

> Username: nejati-deriv  
> Created at: 2022-10-13 14:30:10 UTC  
> Updated at: 2022-10-15 19:10:31 UTC  
> Closed at: 2022-10-15 19:10:31 UTC  
> Url: https://github.com/boostorg/redis/issues/34  

As you know `XREAD` command can return `nil` when there is no messages. using ` std::vector< aedis::resp3::node< std::string > >` for response `nil` would lead to error in read operation and cancel the connection.  
  
And is there a possibility for a more ergonomic type as a container for the result of `XREAD` and `XREADGROUP` commands?  
  
Considering that they have a fixed structure:  
```bash  
> XREAD COUNT 2 STREAMS mystream writers 0-0 0-0  
1) 1) "mystream"  
   2) 1) 1) 1526984818136-0  
         2) 1) "duration"  
            2) "1532"  
            3) "event-id"  
            4) "5"  
            5) "user-id"  
            6) "7782813"  
      2) 1) 1526999352406-0  
         2) 1) "duration"  
            2) "812"  
            3) "event-id"  
            4) "9"  
            5) "user-id"  
            6) "388234"  
2) 1) "writers"  
   2) 1) 1) 1526985676425-0  
         2) 1) "name"  
            2) "Virginia"  
            3) "surname"  
            4) "Woolf"  
      2) 1) 1526985685298-0  
         2) 1) "name"  
            2) "Jane"  
            3) "surname"  
            4) "Austen"  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-10-13 19:50:12 UTC  
> Url: https://github.com/boostorg/redis/issues/34#issuecomment-1278107277  

Fixed in commit https://github.com/mzimbres/aedis/commit/8566745d83741cd86fa8fab1da1af232d6e0b24b.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-10-13 19:55:44 UTC  
> Url: https://github.com/boostorg/redis/issues/34#issuecomment-1278112165  

> And is there a possibility for a more ergonomic type as a container for the result of XREAD and XREADGROUP commands?  
  
Yes, we would have to first define a data structure and then write the adapter. I have been considering adding a section about how to do this. It will be however difficult to incorporate it in Aedis as it will generate an amount of work for which I currently don't have: Document, unit-test etc. It is something I will let for users to do.

---

## Comment 3

> Username: nejati-deriv  
> Created at: 2022-10-14 11:18:05 UTC  
> Url: https://github.com/boostorg/redis/issues/34#issuecomment-1278870429  

Thanks, it seems [check_resp3_handshake_failed](https://github.com/mzimbres/aedis/blob/8566745d83741cd86fa8fab1da1af232d6e0b24b/include/aedis/detail/connection_ops.hpp#L407) should be marked as inline.

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-10-15 11:09:51 UTC  
> Url: https://github.com/boostorg/redis/issues/34#issuecomment-1279722589  

Fixed: https://github.com/mzimbres/aedis/commit/6dce1a92264debfa967de9fa09bf4c2f1a9bf798
