## 类/接口
* **注解**
  * 获取注解对象
    ```
    xxAnnotation annotationObject = AnnotationUtils.findAnnotation(targetObject.getClass(), xxAnnotation.class);
    ```
  * 获取注解属性
    ```
    Map<String, Object> annotaionAttributes = AnnotationUtils.getAnnotationAttributes(annotationObject);
    ```
    还可以借鉴`WebSecurityConfiguration`类，通过实现`ImportAware#setImportMetadata`获取到注解属性；