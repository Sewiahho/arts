## Algorithm

[LeetCode 253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

**题目解析**：

题目描述是这样的：给定一堆会议的起始和终止时间，问最少需要多少间会议室，比如输入为 `[[0, 30],[5, 10],[15, 20]]`，输出为 2，输入为 `[[7,10],[2,4]]`，输出为 1。

拿到一道题先不要急忙着去找最优解，想一想可能的思路有哪些。**随着我们做题数量的增加，往往我们会存在固定思维，习惯用之前的经验快速理解并解决一道题，但是这样其实并不能很好地练习思维发散，我们更应该关注的是一个思路是如何一步步想到的，而不是为了赶紧快速地解决这道题**。一个最直观但是往往不会尝试去想的思路是，取出这里面出现的最大值还有最小值，然后根据这两个值之差建立一个数组，然后计算每个时间点会被多少个会议涵盖，找出最大值即可。当然上面提到的这种解法在这道题目上时间肯定是不允许的，因为最大值和最小值差距会很大，但是看一道题的时候可以不带着这些限制，这样你可以想出很多有趣的思路和想法。

还是回到这道题，我们现在以区间为基准点来看看怎么解决。一堆会议时间是杂乱无章的，为了让其有序，我们可以将其排序，那么问题是以起始时间排序还是以终止时间排序？对于这道题，我们需要知道的是，“**当一个会议要开始的时候，需不需要另外安排会议室？**”，你可以看到，按照这个思路来说，我们考虑的顺序是按照会议开始的时间，因此这里按照会议起始的时间来排序，排完序之后又遇到一个问题就是，有的会议长有的会议短，当新的一个会议开始的时候，我们要怎么确定这个时候是否有之前空出来的会议室？因此我们还要对会议的结束时间进行统计，每当一个会议开始，我们就去检查在这个会议之前开始的会议的结束时间的最小值，到这里，你应该能想到堆这个数据结构，没错，我们可以维护一个最小堆用于记录结束时间，这样可以保证整个解的时间复杂度是 **O(nlogn)** 的。

另外还有一种解法也是比较巧妙，没有用到什么特别的数据结构。这种解法是将所有会议的起始时间和终止时间拆分开来形成两个数组，分别排序，遍历起始时间数组，然后看终止时间数组中是否有结束的会议，记录即可。整个时间复杂度也是 O(nlogn) 的。


<br>

**参考代码（一）**：
```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals == null || intervals.length == 0) {
        return 0;
    }
    
    Arrays.sort(intervals, (int[] a, int[] b) -> (a[0] - b[0]));
    
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    
    pq.offer(intervals[0][1]);
    
    for (int i = 1; i < intervals.length; ++i) {
        if (pq.peek() <= intervals[i][0]) {
            pq.poll();
        }
        
        pq.offer(intervals[i][1]);
    }
    
    return pq.size();
}
```
<br>

**参考代码（二）**：
```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals == null || intervals.length == 0) {
        return 0;
    }
    
    int n = intervals.length;
    
    int[] start = new int[n];
    int[] end = new int[n];
    
    for (int i = 0; i < n; ++i) {
        start[i] = intervals[i][0];
        end[i] = intervals[i][1];
    }
    
    Arrays.sort(start);
    Arrays.sort(end);
    
    int s = 0, e = 0;
    for (; s < n; s++) {
        if (end[e] <= start[s]) {
            e++;
        }
    }
        
    return s - e;
}
```
<br>

## Review
[How HTTPS Work](https://howhttps.works/why-do-we-need-https/)

使用漫画的形式来讲解 HTTPS 的相关知识，挺生动有趣的，这里做一些摘录：

**为什么要使用 HTTPS**：
* 保护数据不被窃取（**Privacy**）
  * 使用 HTTPS 传输数据能够保证在传输过程中数据不会被人盗取
* 保证数据完整性（**integrity**）
  * 同样也是为了安全考虑，防止数据在传输过程中被篡改
* 能够验证发送者身份是否有效（**Identification**）
  * 因为有证书的加入，让接受双方可以验证彼此的身份是否有效，这样做的目的可以防止他人使用错误信息恶意攻击服务器

**两种加密算法**：
* 对称性加密
  * 简单来说就是一把钥匙开一把锁，加密解密都使用一把 key，这种加密算法简单直观，通常来说运行效率比较高，但是有一个问题就是 **key 的分享和传输因为安全性问题变得不太容易**
* 非对称性算法
  * 有一把 public key 和 private key，public key 用来加密，private key 用来解密，因为 public key 不涉及解密，因此可以被分享而不会产生安全问题，但是这种算法运行效率相对来说不高

**客户端和服务器端的五次握手**：
* 第一次握手，客户端这边会发送 SSL/TLS 版本以及支持的加密算法到服务器
* 第二次握手，服务器端在客户端发过来的列表中选择其支持的并且是最恰当的 SSL/TLS 版本和加密算法返还给客户端
* 第三次握手，还是服务器端到客户端，服务器端发送 public key 以及证书，对于客户端来说，其可以利用前者来加密要传输到服务器端的数据，利用后者来验证服务器端的身份是否有效
* 第四次握手，客户端验证服务器的身份成功后，会产生一个 “pre-master key”，其实就是对称加密算法中的 private key，然后使用之前服务器端发送过来的 public key 对这个 private key 进行加密后发送给服务器端
* 第五次握手，服务器端验证客户端发送过来的 “pre-master key” 后，返回告知，后面通信的数据传输都用 “pre-master key” 来加解密

**HTTPS、TLS、SSL 的区别**：
* HTTP + TLS/SSL = HTTPS
* TLS（Transportation Layer Security）其实是从 SSL（Secure Sockets Layer）继承过来的，本质上属于同一个东西的不同版本，SSL 目前已经不再使用，但是由于历史原因，我们还是叫 TLS 为 TLS/SSL

**证书（Certificate）**:
* CA（Certificate Authorities）是一个第三方组织，目标是
  * 签发证书
  * 确认证书拥有者的合法性
  * 提供证书的有效性提供证明
* CA 其实一条层级链，上一级的 CA 保证了下一级的 CA 的有效性，由于最高级的 CA 数量非常少，通常会在安装浏览器或者操作系统的时候就被预设在里面了，它们是被我们完全信任的，因此使得验证有了出口。

<br>

## Tip
这周学习了 JavaScript 中的 **微任务机制**，JavaScript 的异步机制的实现依靠的是 **事件循环** 和 **消息队列**，也就是说每遇到一个 I/O 请求事件，JavaScript 引擎都会将其放入到消息队列中，但是这里就会出现一个问题，任务的优先级没有办法保证，因此这里 JavaScript 引用了微任务的概念，首先区别一下 **宏任务** 和 **微任务**：

宏任务：
* 渲染事件（如解析 DOM、计算布局、绘制）
* 用户交互事件（如鼠标点击、滑动鼠标、放大缩小等）
* JavaScript 脚本执行事件
* 网络请求完成、文件读写完成事件

可以看到的是宏任务的颗粒一般比较大，**微任务** 是一个需要异步执行的函数，**执行时机是在主函数执行结束之后，当前宏任务结束之前**。

在现代浏览器里面，产生微任务有两种方式：
* MutationObserver 监控某个节点，然后用 JavaScript 来修改这个节点
* 使用 Promise，当调用 Promise.resolve() 和 Promise.reject() 的时候

JavaScript 通过 **异步** 解决了同步的性能问题，通过 **微任务** 解决了实时性的问题。

    
<br>

## Share

[What devs need to know about Encoding / Encryption / Hashing / Salting / Stretching](https://hackernoon.com/what-devs-need-to-know-about-encoding-encryption-hashing-salting-stretching-76a3da32e0fd)

分享一篇讲述加密算法的文章。

这段时间在弄 HTTPS 相关的东西，涉及了很多的加密算法，但是对这些算法的区别和功能不是特别的了解。在读了这篇文章后，大致对一般加密算法的种类和用途有了基本的了解。这里对上述文章中的主要内容做摘要

<br>

**Encoding & Encryption**：

上面两个单词用中文翻译出来就是 **编码** 和 **加密**，常常有人会把这两个词弄混淆，**加密** 其实是属于编码的范畴的，它们都是将一个字符串或者文件内容转换成另一种表示形式（字符串）。但是 **编码** 这个词往往比较笼统，比如 UTF-8，ASCII 码等等都可以说成是编码，但是加密和这些编码不同的是，其往往会和安全性问题挂钩，其目的不是为了兼容，而是为了安全。因此，下次提到加密的时候不要在用编码这个词，这会造成概念模糊不清。

<br>

**对称性加密和非对称性加密**：

在上面的 review 部分已经简单介绍过这两个概念的区别，这里就不再过多地赘述。不过紧接着作者给出了一个概念叫做 “**Entropy**” ，这个是用来表示数据的随机性的值，这个值越高表示数据越随机，当然也就越安全。但是问题是用户输入的密码往往 entropy 比较低，也就是随机度不高，很容易被破解，因此我们需要对用户输入的密码进行加密存储，这样一来保证了密码不会被数据库的管理者盗取，二来，即使数据泄漏，别人也不能直接拿到用户的密码。

<br>

**Hash 加密算法**：

这里主要说的是 **单向** 的 Hash 加密算法，比如 MD5、SHA-1，这种算法会将一个字串表示成一个固定长度的字串。这种算法对加密内容不设限，即使是一个 1TB 的文件，最后都会表示成一个固定长度的字串，这里有几个重点：
* Hash 加密算法是单向的，也就是说你无法通过最后加密生成的字串来反过来计算加密前的数据
* 多对一的关系，这一点其实涉及到了 Hash 冲突，相信懂得 Hash 表原理的朋友应该不会陌生，**就是有可能不同的数据最后通过 Hash 加密算法计算出来的结果是相同的，但是同样的数据只可能生成唯一的 Hash 值**

但是问题又来了，直接用 Hash 计算后的结果去表示用户的密码，存入数据库，这种做法靠谱吗？还是回到之前那个问题，往往用户的密码都不会太长，20 个字符已经算非常多了，像微信支付和支付宝的密码就只有 6 位，而且都是数字。如果不法分子盗取了 Hash 加密算法生成的数据，然后他又知道你所使用的加密算法，那么他可以通过组合加试验的方式来还原密码，虽然说这里没有一个机制可以从 Hash 结果 -> 原数据，但是别忘了，数据和 Hash 结果是多对一的关系，也就是说一个数据只会对应一个 Hash 结果，我们可以用可能的数据去试，看是否可能生成同样的 Hash 结果，而且居然还有帮助解密的网站，比如 [md5 解密](https://cmd5.org/)。看来直接存 Hash 结果也是不靠谱的。

对原数据进行 salt，也就是加盐，可以提升 Hash 加密的安全性，**加盐的意思就是我们在用户输入的数据的前面或者后面再加多一个字符串（通常来说比较长）新生成一个字符串，然后将这个新字符串进行加密**。其实就是用户输入的数据太过于简单，我们补充一下，让数据复杂些，虽然说加盐后的数据依然可以通过组合加试验的方式破解，但是这个难度就大大提升了，主要是数据比较长，组合的方式特别多，没法在短时间内计算得出。

另外为了保证加盐的随机性和安全性，由于盐是和 Hash 结果同时被存在 DB 中的，通常盐不能是静态不变的，这样一来，如果数据泄密，他人就可以基于一个盐来组合破解用户的密码，这里作者给了两点建议：
* 动态产生盐
* 使用 **CSPRNG** (Cryptographically Secure Pseudo Random Number Generator) 算法去产生高 entropy 的盐.

还有就是，我们可以对一个密码重复多次 Hash 加密：
```
password + salt -> hash result -> hash result + salt -> hash result2 -> ...
```

当然，通常来说选择一个冲突率小的 Hash 算法也会有帮助，作者不建议使用 MD5，建议使用 SHA-2。

上面这些做法的目的都是为了安全，基于 Hash 加密算法，增加破解密码的难度。之前看了一篇文章说到，如果量子计算机出来，那么这些加密算法会被很快破解掉，因为量子计算机的计算速度惊人，如果真的到了那个量子计算机普及的时候，这个世界会是什么样的呢，是不是有其他的加密方式呢？这真是个不错的期待：）