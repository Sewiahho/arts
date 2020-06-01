> ARTS是什么？<br>
**Algorithm**：每周至少做一个leetcode的算法题；<br>
**Review**：阅读并点评至少一篇英文技术文章；<br>
**Tip**：学习至少一个技术技巧；<br>
**Share**：分享一篇有观点和思考的技术文章。

## Algorithm

[LC 174. Dungeon Game](https://leetcode.com/problems/dungeon-game/)

**题目解析**：

题意是给定一个矩阵，公主在右下角的格子，然后有一个骑士在左上角的格子，每个格子里面有一个数字，表示加血和减血，在进入到每个格子，骑士的血量不能低于 0，骑士需要去到公主所在的格子，并且每步只能向下或者向右，问骑士的 **最低起始血量** 是多少。

这道题目第一次做觉得和很多题目类似，比如 Unique Path 一类的题目，而且显然是一道动态规划的题。但是如果你按常规去思考的话，你会觉得状态貌似并没有那么好表示，比如你用 `dp[i][j]` 表示骑士到达 `[i][j]` 所需要的最小初始血量，那么 `dp[i][j]` 和 `dp[i - 1][j]` 以及 `dp[i][j - 1]` 的关系式如何确定？**这里关系式不好得出的原因在于骑士的起始血量不能为负数或 0**，比如说下面这个例子：
```
[
    [1,  2, -4],
    [2, -4, 1]
]
```
你可以看到的是，如果状态按照我们之前那样定义的话，`dp[0][1] = 1` 。但是，到了 -4 的格子，**其实是可以用到前面增加的血量的，而不仅仅只是初始血量**。

你可能会说，那就用 `dp[i][j]` 表示当前的血量不就好了，大不了等到了终点再做换算。这样做也是行不通的，因为你很难做条件判断，比如下面的例子：
```
[
    [-2, 3,  -3],
    [-5, -10, 1],
    [10, 30, -5]
]
```
你可以思考一下，在终点处，也就是 `[2][2]` 那里你是选择从上边 `[1][2]` 过来的路线，还是从左边 `[2][1]` 处过来的路线？如果仅仅看当前血量正负多少，你会选择从左边 `[2][1]` 过来的路线，但其实正确答案是上面那条路线。

并不是说按照这种常规的思考方式不可行，也许我们多定义一维的状态也可以做，但是我想说的是，如果我们逆向思考这道问题，思考的复杂性上相对来说会简单不少。**我们从右下角那个位置倒着来遍历**，你可以把状态 `dp[i][j]` 理解为，在 `[i][j]` 处，骑士至少需要多少血量才能救出公主，这个血量是指骑士刚到这个格子的时候的血量，比如上面的第二个例子中，`dp[2][2] = 6`，`dp[2][1] = 1`，`dp[1][2] = 5`，递归方程应该也不难看出：

```
dp[i][j] = min(dp[i + 1][j], dp[i][j + 1]) - dungeon[i][j]
```

当然，除了这个方程，我们还需要做一些边界条件的判断，另外就是状态的值最小是 1，不能是负数或者 0。

如果倒过来思考，从结果反推的话，动态规划的状态定义就会清晰很多，这是我们可以从这道题中借鉴到的东西。

<br>

**参考代码**：
```java
public int calculateMinimumHP(int[][] dungeon) {
    if (dungeon == null || dungeon.length == 0) {
        return 1;
    }

    int m = dungeon.length, n = dungeon[0].length;
    
    // dp[i][j] 表示
    // 到达（刚到） [i][j] 前，最少需要多少的血量才能保证救出公主
    int[][] dp = new int[m][n];

    // 初始化最后一个格子的状态
    dp[m - 1][n - 1] = dungeon[m - 1][n - 1] > 0 ? 1 : 1 - dungeon[m - 1][n - 1];

    for (int i = m - 1; i >= 0; --i) {
        for (int j = n - 1; j >= 0; --j) {
            // 跳过最后一个格子
            if (i == m - 1 && j == n - 1) {
                continue;
            }
            // 选择向下的路线
            int fromTop  = i == m - 1 ? Integer.MAX_VALUE : dp[i + 1][j] - dungeon[i][j];
            // 选择向右的路线
            int fromLeft = j == n - 1 ? Integer.MAX_VALUE : dp[i][j + 1] - dungeon[i][j];
            
            // 取进入当前格子时的最小血量
            dp[i][j] = Math.min(fromTop, fromLeft);

            // 进入房间的血量不能是负数或 0
            // 因此，状态也不能是负数
            // 这里可以理解为，即使房间可以给你加血，进入房间时你至少也要有 1 的血量
            if (dp[i][j] <= 0) {
                dp[i][j] = 1;
            }
        }
    }

    return dp[0][0];
}
```

<br>

## Review

[How to Read an Academic Article](https://organizationsandmarkets.com/2010/08/31/how-to-read-an-academic-article/)

这篇文章主要讲述如何阅读一篇学术文章，即使工作了，有些时候还是避免不了阅读技术论文，因为很多比较新的理念和思想只能在学术论文中找到，那如何快速地理解一篇学术文章呢？这篇短文给出了一些建议

* 阅读学术文章的步骤：
    * 阅读摘要（abstract）
    * 阅读介绍（introduction）
    * 阅读结论（conclusion）
    * 大概浏览文章，主要看每个小部分的标题，还有就是插图，表格等等，试着去感受文章的风格，然后问自己下面的问题：
        * 这是一篇什么类型的论文，比如方法论型，介绍概念型，理论推导型，还是经验型？
        * 文章的主要目是什么？是一个调查，还是阐述一个新的理论，亦或是介绍一个基于其他现存理论的应用，或者是评论别人的观点？
    * 从头到尾读一遍文章，省略一些细节，比如推导式，图像，数据
    * 认真通读全文，特别是那些重要的章节或者段落
    * 一旦你理解了作者的意图和观点，试着去对此进行评论，比如你可以进行以下的一些思考
        * 作者的论证是否合理？文章的一些支持性的引用是否站的住脚？论证过程是否前后一致？
        * 将当前的这篇文章和你之前读过的同一领域，相近的文章做对比，当前的文章和你之前读过的文章的观点是否有冲突？是否一致？
        * 通过 Google Scholar 去找到那些引用这篇文章的文章，看看这篇文章是在哪些地方被引用的，引用这篇文章的目的主要是什么？
        * 查阅相关的参考，看看这篇文章背后的一些广泛的背景。

<br>

[Advice on Reading Academic Papers](https://www.cc.gatech.edu/~akmassey/posts/2012-02-15-advice-on-reading-academic-papers.html)

这篇文章也是讲述如何阅读一篇论文的，但是更多的是一些建议:

* 首先阅读结论和介绍，因为这样可以快速地理解论文想要解决的问题，以及作者所采用的一些方法
* 大致浏览文章，理解文章的目的、结构、以及作者的观点倾向。这么做是因为很多细节只有放在具体的情景中才能更好地被理解
* 不要死抠着一个点不放，应该把整篇文章当作是一个整体，一个段落没有理解或许可以在下面的段落中找到答案
* 阅读完后，一定要明白文章的主要观点，优缺点。不要在不理解文章的情况做猜测和评论，这样的话会将文章的一些支持性的概念和你自己的观点弄混淆。

总的来说，这两篇文章对于怎么样读学术论文的建议都差不多。**主要就是一开始要快速地理解文章的大致架构和主要目的**，切记不要一开始就陷入到细节里，因为很多细节并不会影响你对整体的把握，而且很多细节只有再你理解了整体的框架之后才会变得清晰。比如说公式的推导，如果你一步步细致地去看公式的每个推导步骤，会花很多时间不说，并且对于你理解文章主要目的帮助不大。另外就是读完一篇文章之后，一定要进行批判性思考，作者的观点仅仅代表作者的观点，并不表示这个观点放之四海皆准，**要敢于发现问题和提出问题**，通过这些疑问，你可以对比去看其他类似的文章或是去了解学习相关的背景知识，去发现更为广阔的世界，这样才能让自己对某一领域的知识理解得更深，也才能形成自己对事物的思考和见解。


<br>

## Tip

这周阅读了 Golang 的官方文档，学习了一些基本的概念和指令：

**package**：
* 同一 package 下的函数、变量以及类型是可以被相互访问的
* Go 中的第一行代码是用来指明文件所属的 package。可执行的文件必须要用 main 作为 package 的名字

**module**：
* 项目 root 目录下的 `go.mod` 指明了 module 的路径，以及 import path 的前缀
* 每个 module 的路径不仅仅作为 package 的 import 路径，还指明了在哪下载对应的 外部 module

当创建相关的 go command 时，可以使用下面的指令：
```
>$ go install {modulePath}
```
这个指令会把生成的二进制文件放在 `$HOME/go/bin/` 下，当然这个路径是 GOPATH 和 GOBIN 两个环境变量所指明的

安装好 command 后，你也可以用下面的指令把 command 加到 PATH 中，让 command 运行起来更方便：
```
>$ export PATH=$PATH:$(dirname  $(go list -f ‘{{.Target}}’ .))
```

每当你运行 `go install`, `go build`, 或是 `go run` 的时候，golang 会自动地下载对应的 module 并在 `go.mod` 中记录每个 module 对应的 version

测试：
* 测试文件以 `_test.go` 作为文件的结尾
* 测试函数命名以 Test 作为开头，测试函数的签名 `func (t *testing.T)`
* golang 自带的测试框架会运行以 Test 开头的所有函数，如果函数调用了失败相关的函数，比如 `t.Error` 或是 `t.Fail`，那么这个测试结果就会被认为是失败的
* 通过 `go test` 来运行测试

<br>

## Share

这段时间在学习 Andrew Ng 的机器学习公开课，这里做个记录吧

[机器学习之误差函数与梯度下降](./机器学习之误差函数与梯度下降.md)