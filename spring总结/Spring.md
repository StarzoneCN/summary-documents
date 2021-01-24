# <div style="text-align:center;color:#FF9900">Spring</div>

1. AOP几种通知类型执行位置：
    ```java
    try{
        try{
          // @Around 前半部分

          { // 此块相当于@Around中的joinpoint.proceed();
            //@Before
            method.invoke(..);
          }

          // @Around 后半部分
        }finally{
            //@After
        }
        //@AfterReturning
    }catch(){
        //@AfterThrowing
    }
    ```
