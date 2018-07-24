# 通过request获取用户ip

```java
package com.example.demo.multi.springBoot.controller;

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;

@RestController
@RequestMapping("hello")
public class HelloController {

   @RequestMapping("{id}")
    public String index(HttpServletRequest request, @PathVariable Integer id) {
       String ip = request.getHeader("x-forwarded-for");
       if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
           ip = request.getHeader("Proxy-Client-IP");
       }
       if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
           ip = request.getHeader("WL-Proxy-Client-IP");
       }
       if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
           ip = request.getHeader("HTTP_CLIENT_IP");
       }
       if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
           ip = request.getHeader("HTTP_X_FORWARDED_FOR");
       }
       if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
           ip = request.getRemoteAddr();
       }
       System.out.println(ip);
       return "Conquer the world from here!";
    }
}
```







*先记录方法，每一项解释需要好好了解一系 // todo*