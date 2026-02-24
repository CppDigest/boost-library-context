# #448 - Remove UB when an operation is initiated while a multi-function operation is in progress [Closed]

> Username: anarthal  
> Created at: 2025-02-19 13:02:26 UTC  
> Updated at: 2025-02-22 18:15:04 UTC  
> Closed at: 2025-02-22 18:15:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/448  

This is, after `start_execution` and before the final EOF is read. Currently, this causes packet mismatches.
