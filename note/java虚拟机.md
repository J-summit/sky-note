## 1.java对象的内存布局

### 1.1 五种创建对象方式

新建对象方式,new命令，反射，clone，反序列化，Unsafe.allocateInstance

反序列化和clone直接复制现有的数据

Unsafe.allocateInstance方法则没有初始化实例字段

new和反射通过构造器来初始化实例字段

### 1.2 构造器的约束

a.如果一个类没有定义任何构造器，java编译器会自动添加一个无参的构造器;

b.子类构造器需要调用父类构造器，如果父类存在无参构造器的话，该调用可以是隐式，也就是Java编译器会自动添加对父类构造器的调用，但是，如果父类中没有无参构造器，那么子类的构造器中需要显示地调用父类带参数的构造器。

注:显示调用 1.super关键字调用父类构造器;2.this调用同类中其他构造器;而且，必须是构造器中第一条语句（优先初始化继承而来的父类的字段）

那么new指令新建的对象，它的内存中涵盖了所有父类中的实例字段，虽然子类无法访问父类的私有实例字段，或者子类的实例字段隐藏了父类同实例字段，但是子类的实例中还是会为这些父类实例字段分配内存空间

## 2.垃圾回收

