```cmd
host路径:
c:\windows\system32\drivers\etc

查看占用端口进程：
netstat -ano | findstr 8081
查看PID对应的进程：
tasklist | findstr “8080”
杀死占用端口：
taskkill /F /PID 8081
taskkill /f /im nginx.exe
taskkill /f /im java.exe


进入目录
cd /d d:\APP\mysql-8.0.31-winx64
```

