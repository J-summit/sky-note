### 1.对象的创建

```mermaid
graph TB;
a("new类")-->b("根据new的参数在常量池中定位一个类的符号引用");
b-->c("如果没有找到这个符号引用，说明该类该没有被加载则进行类的加载、解析和初始化")
c-->d(虚拟机为对象分配内存)
d-->e(将分配的内存初始化为灵值)
e-->f(执行构造方法)
```

