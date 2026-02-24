# #462 - Potential optimization: try to avoid memmoves with long reads [Open]

> Username: anarthal  
> Created at: 2025-03-28 19:11:02 UTC  
> Updated at: 2025-03-28 19:11:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/462  

If we perform a long read (we read packet N, and part of packet N+1), when we read packet N+1, we always memmove what we got before engaging into further reading. This has a noticeable cost if we read a considerable amount of data for packet N+1. We could probably save such memmoves under certain circumstances (not always, since we might run out of buffer space otherwise). This requires more investigation and some trial-and-error.
