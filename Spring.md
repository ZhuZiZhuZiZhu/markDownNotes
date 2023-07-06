# 1. 细碎的注解学习

https://www.cnblogs.com/wpbing/p/14370208.html

## 1.1 @ResponseBody

@ResponseBody这个注解的作用是将方法中的返回值写入请求的body中，一般用来返回xml或者json。

需要注意的是使用这个注解之后不会再走视图处理器，而是直接将数据写入流里，他的效果等同于通过response对象输出指定格式的数据。

​			1）@ResponseBody 也是可以直接作用在类上的 ，最典型的例子就是 @RestController 这个注解，它就包含了 @ResponseBody 这个注解

       2）在类上用@RestController，其内的所有方法都会默认加上@ResponseBody，也就是默认返回JSON格式。如果某些方法不是返回JSON的，就只能用@Controller了吧，这也是它们俩的区别。

不加@ResponseBody可能会出现一些小问题：

## 1.2 @RestController 和 @Controller

@RestController 等价于 @RespondBody + @ Controller

## 1.3 @Configration

## 1.4 @GetMapping, @PostMapping 和 @RequestMapping

@RequestMapping 详解：https://www.cnblogs.com/caoyc/p/5635173.html

首先来了解一下@RequestMapping, 首先这个注解有两个用法，一个是作用在类上，一个是作用在方法上。

@getMapping和@postMapping 都是复合注解，相当于

## 1.5 @RequestParam 和 @RequestBody

