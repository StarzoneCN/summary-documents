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