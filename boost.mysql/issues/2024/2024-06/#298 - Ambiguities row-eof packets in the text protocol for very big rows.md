# #298 - Ambiguities row/eof packets in the text protocol for very big rows [Open]

> Username: anarthal  
> Created at: 2024-06-30 11:49:35 UTC  
> Updated at: 2024-06-30 11:49:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/298  

The following fails:  
  
```  
SELECT REPEAT('a', 0x1000000)  
```  
  
Because a `string_lenenc` with such size starts with the EOF packet header. This looks like a design issue in the protocol. Other language drivers solve this by first attempting to parse the packet as a row, and then attempting it as an EOF packet on failure.
