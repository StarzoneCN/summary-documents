* `printf`函数中`%n`和`\n`都表示换行；
* 关于断言：
  - 断言的形式：
    - `asser condition[:expr]`,例：`assert(name!=null):"变量name为空null"`;
  - `-ea`选项使断言有效，也可以使用`-da`选项使断言无效（默认为无效）；
  - 可以通过在-ea或-da后面指定包名来使一个包的断言有效或无效  
    `-da:com.test`
  - 要使一个包中的所有子包中的断言能够有效或无效，在包名后加上三个点  
    `-da:com.test...`
* `spring`中的`Assert`类并不是`java`断言机制的使用，而是一种检查参数有效性的便捷手段，运行时是有效的；
* 踩坑：注意`ArrayList`中的`remove(int)`和`remove(Object)`的区别；在`ArrayList<Interger>`情况下，小心出错；    
