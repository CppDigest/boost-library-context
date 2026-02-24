# #88 - Function to source SQL scripts [Closed]

> Username: anarthal  
> Created at: 2022-05-18 21:46:05 UTC  
> Updated at: 2024-08-10 16:19:19 UTC  
> Closed at: 2024-08-10 16:19:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/88  

Implement a function to source a SQL script, given a file path. Requires #8.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:19:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/88#issuecomment-2282199097  

This would require parsing the actual file, as things like `DELIMITER` aren't SQL commands, but instructions for the `mysql` command line. I don't think there's any value in re-creating the command line parser.
