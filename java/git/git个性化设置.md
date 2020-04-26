### **1.给命令设置别名称**

```java
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.st status
git config --global alias.co checkout
```

### **2.创建.gitingore文件，忽略不想提交的文件**

### **3.忽略已经track的文件，修改了也不提交**

```java
git update-index --assume-unchanged  +文件或者文件夹
恢复
git update-index --no-assume-unchanged  +文件或者文件夹
```

### **4.ignored文件不生效的解决办法（亲测）**

```java
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```



