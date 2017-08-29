---
title: python3 socket文件传输
date: 2017-08-29 18:50:33
tags: [python,小工具]
---

# 场景：
CMS系统部署，当时采用Nginx和Tomcat架构，即静态文件放在Nginx部署的服务器上，后台动态代码（cms后台管理系统class）部署在另一台Tomcat服务器上。Tomcat部署的系统，在文章发布后生成的静态页面文件（HTML文件等），需要拷贝到Nginx服务器上（最佳方案是两台服务器共享存储）。要求：
1. 在Tomcat服务生成的静态文件实时（时间间隔5分钟以内）同步到Tomcat服务中。
2. 自动监控、7*24运行
3. 部署时方便快捷
<!-- more -->

# 解决思路：
1. 使用python3 开发，方便快捷，主要的服务器都是Linux版本
2. Tomcat服务中需要同步的根文件夹，只要有文件改动（新增、修改、删除）都发起同步

# 遇到问题：
1. 客户端怎样实时监控文件变化
2. 在创建文件时，监控发现触发两次文件修改方法
3. 文件内容较大时，还没有复制完，就开始触发socket传递，使得文件发送失败（因为文件还没有修改完）
4. 服务器端和客户端都能实现循环等待


# 解决方案：
1. 客户端监控文件变化是根据系统不同，使用的插件不同。在Windows系统中使用win32file
2. 设置可以传递文件的大小
3. 设置等待时间，即等待文件创建或者修改完后，才能socket传递
4. 服务器端：接收文件名称、创建文件、保存文件、继续等待..

# 代码实现
客户端代码
```
#!/user/bin/python
# -*- coding: utf-8 -*-
'''
python3 socket 文件传输----客户端（Windows版本）：
v1.5:
1、监控指定文件夹变化
2、瓶颈在文件大小：即写文件时间，如果读取文件时，文件没有写入完成，就会报错。
    解决方法：添加等待时间time.sleep(3)#等待3秒
'''
import socket, os,time,logging
import zipfile
import sys
import win32file
import win32con

socket = socket.socket()
socket.connect(("127.0.0.1", 9999))
SIZE = 1024 * 1024 * 2000

print(socket.recv(SIZE))
print("sending please wait for a second....")

ACTIONS = {
    1: "Created",
    2: "Deleted",
    3: "Updated",
    4: "Renamed from something",
    5: "Renamed to something"
}
FILE_LIST_DIRECTORY = 0x0001

path_to_watch = 'E:\\temp'
print('Watching changes in', path_to_watch)
hDir = win32file.CreateFile(
    path_to_watch,
    FILE_LIST_DIRECTORY,
    win32con.FILE_SHARE_READ | win32con.FILE_SHARE_WRITE,
    None,
    win32con.OPEN_EXISTING,
    win32con.FILE_FLAG_BACKUP_SEMANTICS,
    None
)

COUNT = 0

while 1:
    results = win32file.ReadDirectoryChangesW(
        hDir,
        1024,
        True,
        win32con.FILE_NOTIFY_CHANGE_FILE_NAME |
        win32con.FILE_NOTIFY_CHANGE_DIR_NAME |
        win32con.FILE_NOTIFY_CHANGE_ATTRIBUTES |
        win32con.FILE_NOTIFY_CHANGE_SIZE |
        win32con.FILE_NOTIFY_CHANGE_LAST_WRITE |
        win32con.FILE_NOTIFY_CHANGE_SECURITY,
        None,
        None)
    COUNT = COUNT + 1
    print(results)
    print(COUNT)
    print("--------")
    for action, filename in results:
        full_filename = os.path.join(path_to_watch, filename)
        print(full_filename, ACTIONS.get(action, "Unknown"))
        if action == 3:
            print("permission =======")
            print(full_filename)
            time.sleep(3) #睡眠时间：等待文件复制完成
            try:
                f2 = open(full_filename, 'rb')
                socket.sendall(bytes(f2.name, encoding="utf-8"))
                data = f2.read(SIZE)
                socket.sendall(data)
                f2.close()
            except Exception as e:
                logging.error("文件打开异常...")
                logging.exception(e)
            finally:
                pass

print("sended!")
socket.close()
print("connection closed")

```
***
服务端代码

```
#!/user/bin/python
# -*- coding: utf-8 -*-
'''
python3 socket 文件传输--服务端（Windows版本）：
v1.5:
1、接收客户端传递过来的文件

'''
import socket, os,logging
from datetime import datetime

socket = socket.socket()
socket.bind(("127.0.0.1", 9999))
socket.listen(20)
SIZE = 1024*1024*2000
savepath = "D:\\workspace\\python\\demo\\sc1\\py_s\\ss"

def Service():
    while True:
        conn, addr = socket.accept()
        print('Accept new connection from %s:%s...' % addr)
        conn.sendall(bytes("Welcome from server!", encoding="utf-8"))
        print(conn)
        try:
            while True:
                fpath = str(conn.recv(1024), encoding="utf-8")
                f_dir = os.path.split(fpath)[0]
                fname = os.path.split(fpath)[1]
                fnameSave = os.path.join(savepath,fname)
                if not os.path.isdir(savepath):
                    os.makedirs(savepath)
                ff = open(fnameSave, 'wb') # 按照配置的路径进行存储
                starttime = datetime.now()
                print("start...")
                recvdata = conn.recv(SIZE)
                if not recvdata:
                    print("reach the end of file")
                    break
                else:
                    ff.write(recvdata)
                ff.close()
                endtime = datetime.now()
                print("end...花费时间(s)",(endtime-starttime).seconds)
        except Exception as e:
            logging.error("服务器异常...")
            logging.exception(e)
        finally:
            conn.close()

    print("receive finished")
    print("connection from %s:%s closed." % addr)

if __name__ == '__main__':
    Service()


```


# 总结

- 文件操作和操作系统有关，不同系统调用底层的库是不一样的
- 现在只实现了windows 版本，且只是在win10系统测试通过
- 单个文件传输有大小限制
- 监控文件变化时，与文件修改写入的时间有冲突，socket发起传递时，一定是文件操作关闭了才可以，不然会报错误。
- 后续计划：
    * 实现Linux版本
    * 实现打包传递
    * 实现超大文件传递

