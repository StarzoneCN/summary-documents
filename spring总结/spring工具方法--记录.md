## 1. 类/接口

### 1.1 注解

* 获取注解对象
  ```
  xxAnnotation annotationObject = AnnotationUtils.findAnnotation(targetObject.getClass(), xxAnnotation.class);
  ```
* 获取注解属性
  ```
  Map<String, Object> annotaionAttributes = AnnotationUtils.getAnnotationAttributes(annotationObject);
  ```
  还可以借鉴`WebSecurityConfiguration`类，通过实现`ImportAware#setImportMetadata`获取到注解属性；

### 1.2 StopWatch

计时小工具，内部隐藏使用**System.currentTimeMillis()**方法。