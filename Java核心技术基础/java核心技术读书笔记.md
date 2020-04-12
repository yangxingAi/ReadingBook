# Java核心技术读书笔记

## 一、Java程序基本设计结构

1. integer的范围不与机器是32位还是64位有关。

2. 特殊转义字符:

   | 转义序列 |  名称  |
   | :------: | :----: |
   |   `\b`   |  退格  |
   |   `\t`   |  制表  |
   |   `\n`   |  换行  |
   |   `\r`   |  回车  |
   |   `\\`   | 反斜杠 |

3. 要注意注释中的转义字符，有可能会报错。

4. 强烈建议不使用char，最好将字符串作为抽象数据类型处理。有些字符占两个char。会导致程序报错。

5. 整形和布尔类型不能进行转换。

6. 整数除0将会产生异常，而浮点数除0将会得到无穷大或者NAN结果。

   ```java
   System.out.println(2.0F/0);
   结果为:System.out.println(2.0F/0);
   ```

7. 二元运算时候，如果不进行类型转换将会有以下的情况：

   * 如果两个操作数有一个是double类型，那么另一个将会转成double。
   * 否则，如果有一个为float，那么另一个会被转成float。
   * 否则，如果有一个为long，那么另一个会被转成long。
   * 否则，两个操作数都会被转成int。

8. 一旦创建了数组就不能再改变其大小，如果需要在运行过程中改变数组的大小，则需要使用另外一种数据结构---数组列表array list.

9. for each循环

   >增强for循环得格式为： for(var :collection) { .....}
   >
   >collection这一集合得表达式必须是一个数组或者是一个实现了Iterable接口的类对象。

10. Java中允许数组长度为0，数组长度为0与null不同。

11. 数组拷贝

    ```java
    int[] smallPriems={2,3,5,7,11,12};
    int[] luckNumbers = smallPriems;
    luckNumbers[5] = 12;//现在smallPriems[5] 也是12
    //如果希望将一个数组的所有制拷贝到新数组里就要使用Arrays的copyOf()
    int[] copiedLuckyNumbers = Arrays.copyOf(luckNumbers,luckNumbers.length);
    //第二个参数是数组的长度，通常用来扩容数组,如果是数值类型那么多余的元素就是0，如果是布尔类型就是false
    int[] copiedLuckyNumbers = Arrays.copyOf(luckNumbers,luckNumbers.length);
    ```

12. String[] args

    >Java程序main方法里的String[] args参数代表main方法接收一个字符串数组，也就是命令行参数。
    >
    >比如看下面这个程序:

    ```java
    public class Message{
        public static void main(String[] args){
            if(ars.length == 0 || args[0].equals("-h")){
                System.out.print("Hello");
            }else if(args[0].equals("-g")){
                System.out.print("GoodBye");
            }else{
                for(int i = 1;i<args.length;i++){
                     System.out.print("" + args[i]);
                     System.out.println("!");
                }
            }
        }
    }
    //使用java Message -g cruel world 运行程序
    //args[0]:"-g"    args[1]:"cruel"   args[2]:world
    //因此输出GoodBye,cruel world!
    ```

13. 数组排序

    >要相对数值型数组进行排序，可以使用Arrays类中的sort方法:这个方法使用了优化的快速排序算法。
    
14. 二维数组

    要想快速打印二维数组的数据元素列表可以使用`Arrarys.deepToString(arr);`

15. Java实际上没有多维数组，只有一维数组。多维数组被解释为"数组的数组".

## 二、对象与类

1.注意不要编写返回引用可变对象的访问器方法。例如:

```java
class Employee{
    private Date hireDay;
    public Date getHireDay(){
        return hireDay;
    }
}
```

Date对象是可变的，这一点就破坏了封装性。比如我们这样操作:

```java
Employee harry = ....;
Date d = harry.getHireDay();
d.setTime(d.getTime());
```

此时d和hireDay指向同一个Date对象，对d调用更改器方法就可以改变这个私有变量的值。

正确的做法应该是返回一个Date对象的克隆。比如:

```java
class Employee{
    private Date hireDay;
    public Date getHireDay(){
        return (Date)hireDay.clone();//返回一个克隆
    }
}
```

1. final定义的变量必须被初始化，一旦赋值就不能被更改。final定义的引用变量呢？

   ```java
   public class fianlDemo {
       private final StringBuilder s = new StringBuilder();
       @Test
       public void testFinal(){
           s.append("222");
           System.out.println(s.toString());
       }
   }
   ```

   依然会输出`222`，final定义的s引用变量指向StringBuilder对象，且不可被更改，但是s还是可以添加值得。

2. Static静态域

   > 如果将域定义为static，每个类中只有一个这样得域。静态域里的值被所有对象共享，它属于类，但是不属于任何独立的对象。

3. 静态方法

   > 静态方法是一种不能向对象实施操作的方法。比如Math的pow方法就是一个静态方法，表达式Math.pow(a,2)，计算a的平方。在运算的时候不使用任何Math对象。
   >
   > 静态方法不能访问实例域因为不能操作对象，但是静态方法可以访问自己类里的静态域。

   在下面两种情况下使用静态方法:

   - 一个方法不需要访问对象状态，其所需参数都是显示参数提供。
   - 一个方法只需要访问类的静态域。

4. ###### 工厂方法

   > 静态方法还有另一种常见的用途。比如NumberFormat类使用工厂方法生成不同风格的格式化对象。

   ```java
       public void testFactory(){
           NumberFormat currencyInstance = NumberFormat.getCurrencyInstance();
           NumberFormat percentInstance = NumberFormat.getPercentInstance();
           double x = 1.0;
           System.out.println(currencyInstance.format(x));//￥1.00
           System.out.println(percentInstance.format(x));//100%
       }
   ```

   为什么NumberFormat不使用构造器完成这些操作呢？主要由两个原因：

   - 无法命名构造器。构造器的名字与类名相同。但是这里希望将得到的货币实例和百分比实例有不同的名字。
   - 当使用构造器时，无法改变所构造的对象类型。而Factory方法返回一个DecimalFormat对象，这个是NumberFormat的子类。

5. 方法参数

   > Java程序设计语言总是按值调用。也就是说，方法得到 的是一个参数值的拷贝，特别是，方法不能修改传递给它任何参数变量的内容。

   ```java
   package com.seanyang.finalDemo;
   
   public class TestParam {
   
       public static void main(String[] args) {
           /**
            * 方法不能修改基本数据类型的参数
            */
           System.out.println("Testing tripleValue:");
           double percent = 10;
           System.out.println("Before: percent = " + percent);
           tripleValue(percent);
           System.out.println("After: percent = "+ percent);
           /**
            * 方法可以改变对象参数的状态
            */
           System.out.println("Testing triple salary");
           Employee  harry = new Employee("harry",50000);
           System.out.println("Before : salary  =" + harry.getSalary());
           tripleSalary(harry);
           System.out.println("After : salary =" + harry.getSalary());
           /**
            *一个方法不能让对象参数引用一个新的对象参数
            */
           System.out.println("Testing swap");
           Employee a = new Employee("alice",70000);
           Employee b = new Employee("bob",60000);
           System.out.println("Before a = " + a.getName());
           System.out.println("Before b = " + b.getName());
           swap(a,b);
           System.out.println("After: a = "+a.getName());
           System.out.println("After: b = "+b.getName());
   
   
       }
   
       private static void swap(Employee x, Employee y) {
           Employee temp =x ;
           x= y;
           y = temp;
           System.out.println("end of method : x= "+x.getName());
           System.out.println("end of method : y= "+y.getName());
       }
   
       private static void tripleSalary(Employee x) {
           x.raiseSalary(200);
           System.out.println("end of mehtod : salary" + x.getSalary());
       }
   
       private static void tripleValue(double x) {
           x = x *3;
           System.out.println("end of method : percent = " + x);
       }
   }
   
   class  Employee{
       private String name;
       private double salary;
       public Employee(String name, double salary) {
           this.name = name;
           this.salary = salary;
       }
       public String getName() {
           return name;
       }
       public double getSalary() {
           return salary;
       }
   
       public void raiseSalary(double byPercent){
           double raise = salary * byPercent /100;
           salary += raise;
       }
   }
   
   ```

   输出结果为:

   >Testing tripleValue:
   >Before: percent = 10.0
   >end of method : percent = 30.0
   >After: percent = 10.0
   >Testing triple salary
   >Before : salary  =50000.0
   >end of mehtod : salary150000.0
   >After : salary =150000.0
   >Testing swap
   >Before a = alice
   >Before b = bob
   >end of method : x= bob
   >end of method : y= alice
   >After: a = alice
   >After: b = bob

6. 重载

   > 如果多个方法有相同的名字不同的参数，便产生了重载。
   >
   > 编译器必须挑选出具体执行哪个方法 ，它通过用各个方法给出的参数类型与特定方法调用所使用的值类型进行匹配来挑选出相应的方法。
   >
   > Java允许重载任何方法，不只是构造器方法，因此要完整描述方法需要指出方法名以及参数类型。

7. 良好的编程习惯：显示初始化域。

8. 注意域隐藏问题

   ```java
   class Employee{
       private String name;
       private int age;
       //参数是name，和成员变量名字相同。
       public Employee(String name, int age) {
           name = name;
           age = age;
       }
       public Employee(){}
   
       public String getName() {
           return name;
       }
   
       public int getAge() {
           return age;
       }
   }
   ```

9. 类设计技巧：

   - 一定要保证数据私有。
   - 一定要对数据进行初始化。
   - 不要在类中使用过多的基本类型。
   - 不是所有的域都需要独立的域访问器和域更改器。
   - 将职责过多的类进行分解。
   - 类名和方法名要能体现他们的职责。
   - 优先使用不可变的类。

## 三、继承

1. spuer并不是一个对象的引用，不能将super赋给另一个对象变量，它只是一个指示编译器调用超类方法的特殊关键字。

2. 由于子类的构造器不能访问父类的私有域，所以必须利用父类的构造器对这部分进行初始化，我们可以利用super实现对超类构造器的调用。使用super调用构造器必须在方法的第一行。

   如果子类的构造器没有显示的调用超类的构造器，则将自动调用超类默认（无参）构造器。如果超类没有无参构造器，并且在子类中没有显示调用超类其他构造，则编译器出错。

3. 一个对象变量可以指示多种实际类型的现象被称为多态。在运行时能够自动调用哪个方法的现象被称为动态绑定。

4. 可协变的返回类型

   > 如果在子类中定义了一个与超类签名相同的方法，那么子类中的方法就覆盖了超类中的这个相同签名的方法。不过返回类型不是签名的一部分，因此在覆盖方法时，一定要保证返回类型的兼容性。允许子类返回父类返回类型的子类型。

5. 每次动态绑定调用方法时间开销很大，因此虚拟机创建了一个方法表，其中列出了所有方法的签名和实际调用的方法。这样一来实际调用这个方法的时候虚拟机仅查找这个表就好了。

6. 关键字为 private，static，final的方法采用静态绑定。编译器可以准确知道调用哪个方法。 

7. 将一个类声明为final，只有其中的方法自动转换成final，而不包括域。

8. 如果方法很简短且被频繁调用，也没有真正被覆盖，那么即时编译器就会将这个方法进行内联处理。

9. 将一个子类引用赋值给超类，编译器是允许的，而将一个超类引用赋值给子类，必须进行强制类型转换。

10. 访问修饰符

   > 仅对本类可见---private
   >
   > 对所有类可见---public
   >
   > 对本包和所有子类可见---protected
   >
   > 对本包可见---（默认）不需要修饰符

11. equals方法

    > Object中的equlas方法用来检测一个对象是否等于另一个对象。在object中这个方法判断两个对象是否具有相同的引用。如果两个对象的引用相同那么他们一定相等。
    >
    > 然而对于大多数类来说，这样得判断没有什么意义。
    >
    > 下面重写一个equals方法，如果雇员的姓名，工资和工龄相同就认为相等。
    
    ```java
     public boolean equals(Object object){
            //如果当前对象引用和传入对象引用相等，那这两个对象一定相等。
            if (this == object) return true;
            //如果传入对象为null则不相等
            if (object == null) return false;
            //如果两个对象类型不一样则肯定不相等，getClass()返回一个对象所属的类
            if (getClass() != object.getClass()) return false;
            //执行到这里肯定是一个同类型的非空对象了，那么强制转换
            Employee employee = (Employee)object;
         //为了防止name和hireDay都可能为null,如果都是null，object.equals（）会返回true,如果其中一个为null,则返回fasle,如果都不为null，则调用a.equals(b)
            return Object.equals(name,employee.name)&&
                salary == employee.salary && Object.equals(hireDay,employee.hireDay);
     		}
    ```
    
12. 在子类中定义equals方法，首先调用超类的equals。如果检测失败对象就不可能相等。如果超类的域都相等那么比较子类的域。

13. Java要求equals方法具有以下的特性：

    > 1)自反性：对于任何非空引用x, x.equals(x)应该返回true
    >
    > 2)对称性：对于任何引用x和y，x.equals(y)返回true那么y.equals(x)也应该返回true
    >
    > 3)传递性：对任何应用x,y,z,如果x.equals(y)返回true,y.equals(z)返回true,那么x.equals(z)也应该返回true
    >
    > 4)一致性：如果x和y引用的对象没有发生变化，反复调用x.equals(y)应该返回同样的结果。
    >
    > 5)对于任何非空引用x, x.equals(null)应该返回false

14. hashcode方法

    > 如果重新定义equals方法就必须重写hashcode方法。如果x.equals(y)返回true,那么x.hashcode()一定等于y.hashcode()

    ```java
    public int hashcode(){
        return Object.hash(name,salary,hireDay);
    }
    ```

15. toString方法

    > 随处可见toSting方法的原因是：只要一个对象与一个字符串通过操作符"+"连接起来，就会自动调用toString方法，以便获得这个对象的字符串描述。

16. ArrayList

    > ArrayList可以存储任意类型得对象，并且可以自动扩容。
    >
    > 一旦确定数组列表得长度不再发生变化，就可以调用trimToSize方法。
    >
    > 这个方法将存储区域得大小调整为当前元素数量所需要得存储空间数目。垃圾回收器回收多余的存储空间。

17. 有一个技巧可以灵活扩展数组，又可以方便访问数组元素

    ```java
    //首先创建一个数组，并添加所有的元素
    ArrayList<X> list = new ArrayList<>();
    while(...){
        x = ....;
        list.add(x);
    }
    //执行完所有操作后，使用toArray方法将数组元素拷贝到一个数组中
    x[] = new x[list.size()];
    list.toArray(a);
    ```

18. 频繁在中间删除和添加数组列表效率很低，应该考虑链表数组。

19. 装箱和拆箱是编译器认可的，而不是虚拟机。编译器在生成类的字节码文件中时，插入必要的方法调用。虚拟机只是执行这些字节码。

20. 反射的功能可以用来：

    - 在运行时分析类的能力
    - 在运行时查看对象，例如编写一个toString方法供所有类使用
    - 实现通用的数组操作代码
    - 利用Method对象。

21. Class类

    在程序运行期间，Java运行时系统为所有的对象维护一个被称为运行时的类型标识。这个信息跟踪着每个对象所属的类。虚拟机利用运行时类型信息选择相应的方法执行。

22. 利用反射分析类的能力

    Class类中的`getFileds`,`getMethods`,`getConstructors`方法将分别返回类提供的public域，方法和构造器数组，其中包括超类的公有成员。Class`getDeclareFileds`,`getDeclareMethods`,`getDeclareConstructors`将分别返回类中声明的全部域，方法和构造器，其中包括私有和受保护成员，但不包括超类的成员。

23. 查看对象域

    ```java
    //创建雇员对象
    Employee harry =  new Employee("harry hacker",35000,10,1,1989);
    //获取类信息
    Class cl= harry.getClass();
    //获取域对象
    Filed f = cl.getDeclareFiled("name");
    //因为name私有
    f.setAccessible(true);
    //返回harry里name的值
    Object v = f.get(harry);
    ```

24. 使用反射编写泛型数组代码

