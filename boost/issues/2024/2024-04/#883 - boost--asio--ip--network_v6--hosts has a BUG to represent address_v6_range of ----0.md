# #883 - boost::asio::ip::network_v6::hosts has a BUG to represent address_v6_range of "::/0" [Open]

> Username: bcdong  
> Created at: 2024-04-10 02:42:51 UTC  
> Updated at: 2024-04-10 02:42:51 UTC  
> Url: https://github.com/boostorg/boost/issues/883  

code position: https://github.com/boostorg/asio/blob/e65367991cb5fbdb8a7cf218ae38f69ca9a0a9f5/include/boost/asio/ip/impl/network_v6.ipp#L78  
  
```cpp  
address_v6_range network_v6::hosts() const noexcept  
{  
  address_v6::bytes_type begin_bytes(address_.to_bytes());  
  address_v6::bytes_type end_bytes(address_.to_bytes());  
  for (std::size_t i = 0; i < 16; ++i)  
  {  
    if (prefix_length_ <= i * 8)  
    {  
      begin_bytes[i] = 0;  
      end_bytes[i] = 0xFF;  
    }  
    else if (prefix_length_ < (i + 1) * 8)  
    {  
      begin_bytes[i] &= 0xFF00 >> (prefix_length_ % 8);  
      end_bytes[i] |= 0xFF >> (prefix_length_ % 8);  
    }  
  }  
  return address_v6_range(  
      address_v6_iterator(address_v6(begin_bytes, address_.scope_id())),  
      ++address_v6_iterator(address_v6(end_bytes, address_.scope_id())));  
}  
```  
  
The above code returns an address_v6_range with the end being `++address_v6_iterator(address_v6(end_bytes, address_.scope_id())));`.  
If the network CIDR is `::/0`, which should contains all valid ipv6 addresses, the `end_bytes` will be `0xFFF...FFF`.   
And the `++` operator will make the address_v6_range [0x00..00, ***\+\+ 0xFF...FF***) overflow to [0x00..00, 0x00..00). This network range contains no ipv6 addresses.
