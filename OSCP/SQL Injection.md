- https://defendtheweb.net/article/blind-sql-injection
- https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html
- https://pentestmonkey.net/category/cheat-sheet/sql-injection

### DUMP DB STRUCTURE

```mysql
SELECT table_name FROM information_schema.tables WHERE table_schema=database();
SELECT column_name FROM information_schema.columns WHERE table_schema=database() AND table_name="something";
```