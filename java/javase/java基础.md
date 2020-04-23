#### 1.内部类

https://www.cnblogs.com/dolphin0520/p/3811445.html

##### 1.1 成员内部类

​	就像类的一个成员一样

##### 1.2 局部内部类

​	类就像方法的成员一样

##### 1.3匿名内部类

​	唯一没有构造器的类（线程的实现方式类似）

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("aaa");
    }
}).start();
```

#### 2.创建Java对象的方式

##### 1.1 使用new关键字

采用new关键字实例化对象是Java中最为常见的方法，下面是采用new关键字实例化对象的示例。在开始之前，我们先准备好一个Java类：Student.java。

```java
package com.ramostear.oops;
public class Student{    
    private String name;    
    private String nickname;    
    public Student(String name,String nickname){        
        this.name = name;        
        this.nickname = nickname;    
    }    
    public String getName(){        
        return name;    
    }    
    public void setName(String name){        
        this.name = name;    
    }    
    public String getNickname(){        
        return nickname;    
    }    
    public void setNickname(String nickname){        
        this.nickname = nickname;    
    }
}
```

接下来，我们通过new关键字来创建几个学生：

```java
package com.ramostear.oops;
public class TestNewStudent{    
    public static void main(String[] args){        
        Student zhangsan = new Student("Zhangsan","张三");        
        Student lisi = new Student();        
        lisi.setName("Lisi");        
        lisi.setNickname("李四")；    
    }
}
```

##### 1.2 使用Class类的newInstance()方法

```java
package com.ramostear.oops;
public class Student{    
    private String name;    
    private String nickname;    
    public Student(String name,String nickname){        
        this.name = name;        
        this.nickname = nickname;    
    }    
    public String getName(){        
        return name;    
    }    
    public void setName(String name){        
        this.name = name;    
    }    
    public String getNickname(){        
        return nickname;    
    }    
    public void setNickname(String nickname){        
        this.nickname = nickname;    
    }
}
```

接下来，将演示如何通过newInstance()方法来创建新的对象：

```java
package com.ramostear.oops;
public class TestUseNewInstanceMethod{    
    public static void main(String[] args){        
        try{            
            String className = "com.ramostear.oops.Student";            
            Class clz = Class.forName(className);            
            Student student = (Student) clz.newInstance();            			                     System.out.println("student name :"+ student.getName()+", nickname: "+student.getNickname());        
        }catch(InstantiationException | IllegalAccessException | ClassNotFoundException e){            
            e.printStackTrace();        
        }    
    }
}
```

##### 1.3 使用Constructor.newInstance()方法

与Class类的newInstance()方法相似，我们还可以使用java.lang.reflect.Constructor类的newInstance()方法来创建新的对象。同样，我们先准备一个目标类：

```java
package com.ramostear.oops;

public class Student{    
    private String name = "Constructor";    
    private String nickname = "构造器";    
    public Student(){        
        super();    
    }    
    public Student(String name,String nickname){        
        super();        
        this.name = name;        
        this.nickname = nickname;    
    }    
    public String getName(){        
        return name;    
    }    
    public void setName(String name){        
        this.name = name;    
    }    
    public String getNickname(){        
        return nickname;    
    }    
    public void setNickname(String nickname){        
        this.nickname = nickname;    
    }
}
```

接下来，我们看看如何使用Constructor的newInstance()方法创建新的对象：

```java
package com.ramostear.oops;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class UseConstructorNewInstanceMethod{    
    public static void main(String[] args){        
        Constructor<Student> studentConstructor;        
        try{            
            studentConstructor = Student.class.getConstructor();            
            Student student = studentConstructor.newInstance();                                       System.out.println("student name :"+ student.getName()+", nickname: "+student.getNickname());        
        }catch(InstantiationException | IllegalAccessException | IllegalArgumentException | InvocationTargetException |               NoSuchMethodException | SecurityException e){            e.printStackTrace();        
        }    
    } 
}
student name:Constructor, nickname:构造器
```

```java

```

##### 1.4 使用反序列化

如果要使用反序列化创建对象，则目标类首先要实现Serializable接口。Serializable是一个标记接口。在本次示例中，我们先创建一个Student对象，并将其保存到data.txt文件中，然后在通过反序列化操作，读取data.txt中的数据，并创建新的对象。

```java
package com.ramostear.oops;

import java.io.Serializable;
public class Student implements Serializable{ 
    private String name; 
    private String nickname; 
    public Student() {  
        super(); 
    } 
    public Student(String name, String nickname) {  
        super();  
        this.name = name;  
        this.nickname = nickname; 
    } 
    public String getName() {  
        return name; 
    } 
    public void setName(String name) {  
        this.name = name; 
    } 
    public String getNickname() {  
        return nickname; 
    } 
    public void setNickname(String nickname) {  
        this.nickname = nickname; 
    }
}
```

接下来，将通过一个简单的示例，演示如何使用反序列化的方式创建新的对象：

```java
package com.ramostear.oops;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class StudentDemo {    
    public static void main(String[] args) {        
        String filePath = "data.txt";        
        Student student1 = new Student("Deserialization", "反序列化");        
        try {            
            FileOutputStream fileOutputStream = new FileOutputStream(filePath);                       ObjectOutputStream outputStream = new ObjectOutputStream(fileOutputStream);               outputStream.writeObject(student1);            
            outputStream.flush();            
            outputStream.close();            
            FileInputStream fileInputStream = new FileInputStream(filePath);                         ObjectInputStream inputStream = new ObjectInputStream(fileInputStream);                   Student student2 = (Student) inputStream.readObject();                                   inputStream.close();            
            System.out.println("Student name:"+student2.getName()+",            nickname:"+student2.getNickname());        
        } catch (Exception ee) {            
            ee.printStackTrace();        
        }    
    }
}
Student name:Deserialization,nickname:反序列化
```

##### 1.5 使用对象克隆创建新的对象

clone()方法可以创建现有对象的副本，但在使用clone()方法前，需保证模板类实现了Cloneable接口，Cloneable接口也是一个标记类接口。下面是使用克隆方式创建新对象的完整示例：

```java
package net.javaguides.corejava.oops;

import java.io.Serializable;

public class Student implements Cloneable {    
    private String name;    
    private String nickname;    
    public Student() {        
        super();    
    }    
    public Student(String name, String nickname) {        
        super();        
        this.name = name;        
        this.nickname = nickname;    
    }    
    public String getName() {        
        return name;    
    }    
    public void setName(String name) {        
        this.name = name;    
    }    
    public String getNickname() {        
        return nickname;    
    }    
    public void setNickname(String nickname) {        
        this.nickname = nickname;    
    }    
    public static void main(String args[]) {        
        Student stu1 = new Student("Clone", "克隆");        
        try {            
            Student stu2 = (Student) stu1.clone();            
            System.out.println("Student name :"+stu2.getName()+",nickname:"+stu2.getNickanme());        
        } catch (CloneNotSupportedException e) {            
            e.printStackTrace();        
        }    
    }
}
Student name： Clone,nickname:克隆
```
