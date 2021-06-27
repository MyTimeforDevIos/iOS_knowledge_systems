![wwdc2021-10256_hd-0007.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624790682092-7d7182e5-64fb-4a66-b979-b794a822a97a.png#clientId=u51da3c5f-96d3-4&from=drop&id=uc064635e&margin=%5Bobject%20Object%5D&name=wwdc2021-10256_hd-0007.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=2&size=359350&status=done&style=none&taskId=u3bb574d2-b280-4ee6-a621-6355c9e5bc2) 
# 初探 Swift 算法和集合

- [前言](#%E5%89%8D%E8%A8%80)
- [Algorithms](#Algorithms) 
   - [分块算法函数 chunks](#%E5%88%86%E5%9D%97%E7%AE%97%E6%B3%95%E5%87%BD%E6%95%B0chunks)
   - [窗口算法函数 windows(ofCount:)](#%E7%AA%97%E5%8F%A3%E7%AE%97%E6%B3%95%E5%87%BD%E6%95%B0windows(ofCount%3A))
   - [CompactMap](#CompactMap)
   - [FlatMap](#FlatMap)
   - [lazy 算法链](#lazy%E7%AE%97%E6%B3%95%E9%93%BE)
- [Collection](#Collection) 
   - [Deque](#Deque)
   - [OrderedSet](#OrderedSet)
   - [OrderedDictionary](#OrderedDictionary)
- [总结](#%E6%80%BB%E7%BB%93)
# 前言
程序 = 算法 + 数据结构。Swift 的标准库实现了三种通用的数据结构：Array，Set，Dictionary。与这些集合相匹配的  `sort`，`map`， `fliter` 等多个算法函数。使用这些内置算法，能够使得代码更加简洁，易读，而且性能更好，也成为了 Swift 最强大特性之一。今年更是发布了一个 Swift 算法和集合的开源包，在其中更新了更多的算法函数和数据结构
​

本文基于 [Session  10256](https://developer.apple.com/videos/play/wwdc2021/10256/) 梳理，文章简单介绍其中一些算法和数据结构。需要提前了解的有：

1. 内联闭包的参数名称缩写（跳转 [SwiftGG - 闭包](https://swiftgg.gitbook.io/swift/swift-jiao-cheng/07_closures#shorthand-argument-names) 了解）
1. Copy On Write 特性
# Algorithms
到目前为止，开源包里的算法可以分为几下几个大类：

1. 集合组合和排列
1. 集合位置改变
1. 集合链接
1. 子集操作
1. 集合部分排序
1. 其他

具体用法和效果可查阅开源地址：[Swift Algorithms](https://github.com/apple/swift-algorithms)。下面是算法列表的截图
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624782083765-cdc2177d-e649-4c7b-936f-a68c1127d35a.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=sv0cu&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=424104&status=done&style=none&taskId=u8aae34d5-a4bd-48ff-8d10-1b537c7fa02&width=656)
下面列举几个 session 里印象比较深刻，或者平时业务开发可能使用得上的方法。
### 分块算法函数 `chunks`
根据函数的参数，当前元素与前一个元素“不同”时，这时候就需要分块。比如前后元素的某个值不相等：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624783764017-012e54d8-c129-4d2d-bb26-ada978db58d3.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=b7JpO&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=381380&status=done&style=none&taskId=uade10f9b-889e-490e-9cd1-029ab294114&width=656)
或者是元素的类型不同：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624783558932-8a133b8e-8d83-4806-860e-9c40aafb819c.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=TBG5V&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=224139&status=done&style=none&taskId=u98b58e05-1698-4d01-963a-9be3cc044db&width=656)
而当分块的参数可以是是块大小，此时就会出现“余数”块的情况：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624783352021-6f966e99-53cd-44f6-a829-68b8ba025db0.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=OVkTI&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=166050&status=done&style=none&taskId=ua250d39e-2835-4462-82e0-e70e4e269d6&width=656)
在 seesion 中，假设了有一个消息的列表。现在需要将信息列表根据每一个小时进行分割，并显示对应的时间戳信息。下图就是实现的算法链：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624784878728-b2e82ea6-a907-4e0d-9bba-0edbf4782d97.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=Nw3xi&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=309620&status=done&style=none&taskId=uff687f8d-97f4-4cfe-b32a-57016590fd1&width=656)
### 窗口算法函数 `windows(ofCount:)` 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624782986103-8b0acbe4-b06f-4c09-84c5-ab37f83bfb8d.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=FOHxR&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=206477&status=done&style=none&taskId=u8c549ff0-29aa-4de6-b1b1-2ce32921715&width=656)
`adjacentPairs` 函数和 `windows(ofCount: 2)` 函数的效果几乎一致。苹果开发人员考虑到一对数字这种特殊情况，而重新写了一个返回元组类型的方法。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624783226919-c71553ff-ad98-46f2-ad38-40e914548ee2.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=XDuNf&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=175178&status=done&style=none&taskId=uee045e71-27cb-402a-8e43-cde113e793b&width=656)
### CompactMap 
`compactMap` 方法用来过滤集合中的 nils 并映射到解包之后结果集合。`compactMap` 方法是 `filter` 和 `map` 两个方法的合体，其中 `filter` 方法筛选符合条件的元素集合，`map` 是映射出一个新的集合。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698875064-c794ae66-3010-40d8-84f1-ba6413a5d064.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=u6b834560&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=208406&status=done&style=none&taskId=u35b919aa-ae74-4345-8ead-33833dca063&width=656)
上面图中的代码等同于
```
messages
	.filter { $0.attachment != nil }
  .map { $0.attachment! }
```
业务场景：图片列表按从到旧排列，最大个数为 6 个
### ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698747515-f2cbf238-8806-416f-9f22-f5b2911471bc.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=HSD5S&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=336287&status=done&style=none&taskId=uf3dca274-9ce3-462a-98a5-3e7d492de36&width=656)
### FlatMap
`FlatMap` 将集合中的元素都映射到单层的集合中。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698823022-d139d2e4-4426-40d3-b4b1-7bc1256620d5.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=nBgK8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=197015&status=done&style=none&taskId=u996e0304-c37c-42b2-9439-e0b32c2fb8b&width=656)
上面图中的代码等同于
```
messages
	.map { $0.makeMessageParts() }
  .joined()
```
### lazy 算法链
在下图中的注释会发现 `join` 方法并没有返回一个分配了新内存并初始化的数组，而是返回一个 FlattenSequence：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698678994-6caab919-db21-46b7-af26-b824942ad90f.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=uffa6e607&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=205793&status=done&style=none&taskId=ua34a5b13-d0ad-4995-a122-24704e2f28d&width=656)
FlattenSequence 被称作 “lazy adapter” -- 惰性适配器。在多数情况下，FlattenSequence 的表现和 Array 一致，用视频中的话形容是一个 “thin wrapper”，占用内存较小可以比较随意的生成。惰性适配器，指带有 `lazy` 特性，像是 Swift 中的 Copy On Write ，也就是说是按需处理元素，而不是预先处理好所有的工作。
​

上面提到 `compactMap` 函数里，直接返回的是 Array 类型。那么这时候可以手动加上 lazy 的特性么？
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698632694-b2703021-6b21-4e64-84fa-65448cd1697a.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=u47e75755&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=189858&status=done&style=none&taskId=u5a3d5c8c-205f-4bfa-b591-15c552d493c&width=656)
当然也是可以的，在算法链的开头添加 .lazy ，那么在链上任何采用闭包的算法拥有 `lazy` 特性：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624698555232-b308211f-9036-415e-b8f6-32dd2caa802c.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=u626413ff&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=173196&status=done&style=none&taskId=u89460f7b-8eb8-481f-80d3-38329745be7&width=656)
而如果需要最终的结果也是一个 Array 类型，只需要将算法链放在 Array 初始化方法里：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624699097246-119cdfdc-16e9-4d34-9b72-64537a3acd82.png#clientId=ud72134d0-2595-4&from=paste&height=373&id=u11021eb0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=746&originWidth=1312&originalType=binary&ratio=2&size=164107&status=done&style=none&taskId=u833929a3-0b6c-4a48-b408-c2a024239fa&width=656)
需要注意的是：将 Array 类型转化成惰性适配器是是不可以的。`lazy` 不是灵丹妙药，在序列只做一次迭代时，利用lazy 可以节省一些不需要工作。但是需要多次迭代序列的时候就不要适合。所以就像一般的懒加载属性一样，懒加载之后并保存结果。
# Collection
开源包里新增的三种数据结构：

- Deque 双端队列
- OrderedSet 有序集合
- OrderedDictionary 有序字典

这三种也是常见的数据结构类型，而且也是标准集合类型的变体。开源地址：[Swift Collection](https://github.com/apple/swift-collections)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624785923385-4213e504-2ad2-48d9-81ce-c58f2b34217d.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=u7d1a5061&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=448329&status=done&style=none&taskId=uc7f0fbec-4301-41ca-b091-2ada8f1ae18&width=656)
### Deque
"double-ended queue" 双端队列，苹果工程师在项目中缩写为了 Deque。双端队列相比较于一般队列的先进先出单个方向的操作，提供了对称性的操作。而在 LeetCode 上就有一道算法题 [设计循环双端队列](https://leetcode-cn.com/problems/design-circular-deque/) 。![wwdc2021-10256_hd-0008.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624802129968-479290a7-89df-425e-92b6-d82a03a75f53.png#clientId=uf00e3866-b63f-4&from=drop&id=u2ae54b9c&margin=%5Bobject%20Object%5D&name=wwdc2021-10256_hd-0008.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=2&size=207432&status=done&style=none&taskId=u971d46ab-7630-4c14-806c-a9bbf32e15f)
双端队列 Deque 在使用上和 Array 比较接近。在一些方法的底层实现上就大有不同，例如在数组和双端队列中插入数据或翻转数据上，性能有很大的差距。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624786648704-62fa541e-288b-438e-a653-4d67cb373a15.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=ub7a3b75a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=207445&status=done&style=none&taskId=ua3fbe1a2-d0bd-4adb-ae72-b291f4a5833&width=656)
下面的图片可以看出，在插入新元素的时候，ABC 的位置其实是从后插入的，而改变的是元素对应的索引。在删除中间元素的时候，选择移动前的元素而不是后面的元素，随机删除元素的平均速度提高了一倍。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624786575056-02c80ef0-f14c-4a16-82d4-b3c6fe7d54ab.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=KbmsD&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=161428&status=done&style=none&taskId=u9b0e7ca8-9f9b-4061-9cb4-33c76091693&width=656)


### OrderedSet
一般的 Set 通过 hash 函数将元素直接存储在哈希表里，这样能保证元素的唯一性，在查找元素上也有很好的性能，但是元素之间的顺序却是不确定的。而在某些特别的情况下，需要对 Set 中的元素设置一定的循序。
​

Swift 实现的 OrderedSet，具体的元素放到了数组里，而原来的哈希表用来存储元素在数组中的索引。索引的范围受哈希表的大小限制，所以通过将整数值换成二进制的方法来压缩表。
![wwdc2021-10256_hd-0010.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624803245463-6c583707-916b-4888-8237-1347df7e544a.png#clientId=uf00e3866-b63f-4&from=drop&id=uae12e754&margin=%5Bobject%20Object%5D&name=wwdc2021-10256_hd-0010.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=2&size=90943&status=done&style=none&taskId=ucf409ac0-65e3-4d4d-aa30-f1e7a5ff867)
Array，Set，OrderedSet 三者在查找，添加，随机删除上的性能比较：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624788690998-8295da9f-7be7-4957-9f0f-6addbc2c6654.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=ubfc5eac7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=257301&status=done&style=none&taskId=uf1fb7e41-0465-4968-84e3-605a23a3155&width=656)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624788726200-b5e35e03-f679-402a-bfde-fecf25dfb1ff.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=u993b9442&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=243211&status=done&style=none&taskId=u8637d12e-0ce1-4f12-bc01-2417efb7774&width=656)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624788906710-be23f998-0ff6-4d14-a03d-42792cf274f5.png#clientId=u51da3c5f-96d3-4&from=paste&height=372&id=u7d392e02&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1312&originalType=binary&ratio=2&size=258773&status=done&style=none&taskId=uac876860-4ce2-4773-9e7d-9687be0e7b9&width=656)
### OrderedDictionary
常规的字典使用两个单独的哈希表来存储键值对，而有序字典则是使用单个哈希表和两个数组。![wwdc2021-10256_hd-0011.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624803538547-e74cb598-2411-46d6-986d-4df903fbe932.png#clientId=uf00e3866-b63f-4&from=drop&id=u5b5c8128&margin=%5Bobject%20Object%5D&name=wwdc2021-10256_hd-0011.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=2&size=238092&status=done&style=none&taskId=u819a7b4a-b486-45a5-8c21-7de17697338)


Swift 实现有序字典使用类似数组的整数索引，但是这样会引入一个问题。当索引和 key 值冲突的时候，应该这么处理呢？Swift 开发工程师认为基于 key 取值是字典的常用操作，所以有序字典里没有提供直接使用索引的操作，而且有序字典也只符合了序列的协议。而符合集合协议的下标操作，通过了 elements 来实现![wwdc2021-10256_hd-0004.png](https://cdn.nlark.com/yuque/0/2021/png/1274781/1624790014264-476d9f44-e395-450f-b8fe-dcabef81d5e3.png#clientId=u51da3c5f-96d3-4&from=drop&id=u43090f68&margin=%5Bobject%20Object%5D&name=wwdc2021-10256_hd-0004.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=2&size=218245&status=done&style=none&taskId=u6c254b84-c57f-4232-a1fc-2726ffb13e5)
# 总结
在文章中介绍数据结构的时候，更多的去阐述了数据结构的底层实现。
