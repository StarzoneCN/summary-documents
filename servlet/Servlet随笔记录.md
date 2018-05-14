# Servlet随笔记录
## 知识点
* **@HandlesTypes**
  * 根据`Servelt3.0`规范，容器要负责以`Set集合`的方式传递注解中指定的类型的所有子类(包括子接口、实现类等)的class对象,比如：  
  `SpringServletContainerInitializer `