# <div style="text-align:center;color:#FF9900">踩坑-Java</div>
### 1. Arrays.asList
猜猜这个结果：
```Java
int[] arr = new int[]{1,2,3};
System.out.println(Arrays.asList(arr).contains(1));
```
在猜猜这个结果：
```Java
Integer[] arr = new Integer[]{1,2,3};
System.out.println(Arrays.asList(arr).contains(1));
```
提示：
> 主要问题出在可变长参数上，`Arrays.asList`的参数定义就是可变长参数`asList(T... a)`

[参考](https://suigara.iteye.com/blog/1623427 '编号2')

### 2. 诡异的三元运算符
猜猜结果：
```Java
char x = 'X';
int i = 0;
System.out.println(true ? x : 0);
System.out.println(false ? i : x);
```
参考：[Link](https://suigara.iteye.com/blog/1623427 '搜索“诡异的三元表达式”')

### 3. +=
[参考](https://suigara.iteye.com/blog/1623427 '#5')

### 4. readResolve
提示：解决反序列化对单例的破坏；更详细的解释请自行搜索；

### 5. `e:`和`e:\\`
[参考](https://blog.csdn.net/u011004037/article/details/45369887)
