---
title: 性能测试小记（一）
date: 2017-09-08 16:38:06
tags:
     - Jmeter
     - 性能测试
---

[Dragon Wang]: https://github.com/wanghantong/ "wanghantong"

*—— 同时发表于[coding](http://noney.coding.me/)*

__作者：[Dragon Wang]__

### 1.sampler
性能测试中，向服务器发送请求、记录响应信息、响应时间的最小单元。
### 2.逻辑控制器
__[作用范围]: 只作用在线程组上__

+ 真正的控制逻辑： 
控制sampler节点发送请求的逻辑顺序，比如：`if，switch，Loop，Random`  

+ 业务的逻辑控制：
控制业务组合，组织和控制sampler节点：比如：`transaction，Throughput`

<!-- more -->

### 3.断言
+ __响应断言__
 __[作用描述]:__ 对响应体内容的判断 
 __[作用范围]:__ 
  >1. 仅主请求取样 (main sampler only)  
  >2. 仅子请求取样 (sub sampler only)  
  >3. 主请求取样&子请求 取样(main sampler & sub sampler) 这个一般指Ajax的多层请求 
  >4. Jmeter 变量 (Jmeter variables)
  
  __[响应的属性值]__：用来断言的字段/属性/等
  
  >1. 响应文本：即响应的正文-返回体
  >2. Documeng(text)：
  	  + 测试文件
  	  + URL样本  
  	  + 响应代码
  	  + 响应信息
  >3. Response Headers：响应头部
  >4. Ignore status：忽略返回的响应报文状态码
  
 __[用法与基本功能概述]__：  
  
  1. 函数选项：
  >contain   | : 采用Java API实现，会将整个响应的正文定义为一个字符串，然后在其中进行查找某个关键字  
  >subString | : 采用Java API实现，一般用于将某一段内容进行截取比对，不支持正则
  >Equals    | : 将响应体与期望值进行比较，如果相同则返回true,反之为false即请求失败  
  >Match     | : 此时可以使用正则表达式进行比较，一般用于对响应体的格式进行校验匹配
  >Not       | : 该选项可以与前四项混合使用，表示'非'的意思

  2. 可以进行断言的内容
  >Text Response | : 最常用的HTTP请求的响应体检测
  >Document (text) | : 对返回内容的探测，基于内存模式，对内存占用较大，有一定的失败率
  >URL Sampled | : 可能是一个URL也可能是2个URL，在请求状态码为302重定向时，此处会记录2个URL
  >Response code | : HTTP状态码
  >Response Message | : 200 OK | 302 Found
  >Response Headers | : 常见的响应头
  >Ignore status | : 如果要验证400，501等响应代码时需要勾选此处，因为默认Jmeter会默认这个请求为失败的  
  <p></p>
  >插入语：
  >Apache Tika :
  >主要作用：侦测文档。
  >功能概述：从不同的数据文档中（HTML | PDF | Doc）侦测和提取元数据和结构化内容。
  >项目背景：最开始是ApacheLucene项目的子项目，2010年5月成为Apache组织的顶级项目。
  >使用群体：搜索引擎索引和分析工具
  >项目特点：具有强大的解析类库，支持你能想到的全部格式
  >官方网址：<https://tika.apache.org>
  
  3. 操作细节:
  >在Patterns to Test 中添加要进行断言比对的文本，点击下方的**add**按钮，添加断言文本
  
  <p></p>
  **参考文献：**
  * 如何模拟(main sampler & sub sampler)场景
   <https://stackoverflow.com/questions/28214936/jmeter-in-which-scenario-i-can-use-main-sample-or-sub-sample-or-both-for-te>  
  * Ajax是如何发出重复请求的?  
    <https://www.zhihu.com/question/19805411>
    
  
      
 



