> ARTS是什么？<br>
**Algorithm**：每周至少做一个leetcode的算法题；<br>
**Review**：阅读并点评至少一篇英文技术文章；<br>
**Tip**：学习至少一个技术技巧；<br>
**Share**：分享一篇有观点和思考的技术文章。

## Algorithm

[LC 60. Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)

**题目解析**：

一道比较偏数学的题目。题意是给定 1 ～ n 的数字（n <= 9），因为数字所在的位置不同，这些数字可以组成的整数也不同。最后让你求按照组成的整数大小排序，排在第 k 的整数是多少。

要求全排列的话，时间和空间复杂度都是 `O(n!)`。先看最直接的解法，通过暴力搜索，找出所有的全排列的情况。然后排序，直接就可以得出答案。这么做的时间复杂度是 `O(n!log(n!))`。很显然的是，这么做时间上面不符合要求，需要进一步优化算法。

首先考虑的一个问题是，**我们需不需要找出所有的组合情况？** 如果要找出所有的情况，那么时间上肯定是没法提高的。这其实就涉及到一个暴力搜索的剪支的问题，就拿 n = 3 为例，按照元素大小，全排列如下：

```
"123"
"132"
"213"
"231"
"312"
"321"
```

这里，如果说 k = 5，那么是不是可以说以 “1” 和 “2” 开始的情况我们就不需要考虑了？我们只需要从 “3” 开始的序列开始找就可以了，这样子可以很大程度上节省搜索的成本。那么问题来了，**如何确定我们需要找的结果在不在以某个数字开头的区间内？** 其实，每个数字开头的序列总个数都是一样的，比如上面的以 “1”，“2”，“3” 开头的序列个数都是 2 个，我们只需要逐个排除就行了。如果 k = 5，因为 5 > 2，说明结果不会在以 “1” 开头的区间中。又因为 5 > 4，说明结果不会在以 “2” 开头的区间中。但是 5 < 6 的，所以我们需要在开头是 “3” 的序列中继续找。确定了开头数字是 3 之后，我们可以把 3 排除，然后继续去用同样的方法确定第二个数字，第三个数字。。。

这样做下来，时间上面是可以大大提高的。至于具体的时间复杂度是多少，这和具体的输入有关。但是空间方面，我们并不需要 `O(n!)` 的空间，n 的空间（函数栈空间）就足够了。

<br>

**参考代码**：
```go
func getPermutation(n int, k int) string {
    nums := []int{}

    for i := 1; i <= n; i++ {
        nums = append(nums, i)
    }

    return helper(nums, k)
}

func helper(nums []int, k int) string {
    totalNums := 1
    // 计算以每个数字开头的序列个数
    for i := 1; i < len(nums); i++ {
        totalNums *= i
    }

    for i := range nums {
    	// 以当前数字开头的话，前面的序列的总个数
        sum := (i + 1) * totalNums

        sub := k - sum
		
        // 说明结果是以当前数字开头
        // 去掉当前选中的数字，递归去确定接下来的数字
        if sub <= 0 {
            arr := []int{}
            arr = append(arr, nums[:i]...)
            arr = append(arr, nums[i+1:]...)
            return strconv.Itoa(nums[i]) + helper(arr, k - i * totalNums)
        }
    }

    return ""
}
```

<br>

## Review

[3 Hours of Creative ‘Flow’ Every Day Is All You Need to Change Your Life](https://medium.com/the-ascent/3-hours-of-creative-flow-every-day-is-all-you-need-to-change-your-life-4afa8df5eec)

文章主要是介绍 “心流” 这么一个状态。在读之前，我是对 “心流” 有一点的认识，最近也正在看《心流》这本书。可以说，心流是一种精神状态，在心流下，人自身处于忘我的状态，全神贯注于当前的事情。只要进入心流状态，你会感觉时间过的飞快，3，4 个小时就好像半个小时一样，在这种状态下，做事情的效率也是最高的。但是这里的问题在于，**如何进入心流状态？**，想要进入心流状态可没有那么简单。文章中提到几个建议：

* 放松的环境和心情

  进入心流首先需要有一个放松的环境。你可以想象一下，如果在压力大的环境下，你需要时时刻刻保持警惕，或者说时刻处于一个准备被打扰的状态。那么这是很难沉浸并专注到一件事情中去的。如果你正处于一种压力山大的状态，那么可以考虑运动一下，洗个澡，等放松完后再回来。不知道你有没有觉察到，很多时候在你洗澡的时候你会想到一些平时想不到的点，这就是因为在洗澡的时候你是处于一种完全放松的状态。

* 多多阅读

  有些时候，阅读确实是一种进入心流的好方法。特别是读一些有故事情节的小说或者是一些推理故事。你会把自己完全陷入到书本的场景中去。读书不仅可以增长知识，还是一个帮助你走出当前的现实，进入到一个让你为之兴奋和好奇的场景中去的一种手段。

* 让自己兴奋起来

  不知道你有没有因为一个电影的片段，或者是一首歌曲而感觉兴奋，就是那种让自己为之兴奋，又为之震撼的感觉。你可以多多收集这些东西。当你感觉自己陷入压力和被动之中的时候，可以拿出来看看，听听。当你处于这种高昂的状态下的时候，更容易进入心流状态。

* 不要被打断

  心流状态下最忌讳的就是被打断。被打断意味着你需要从心流状态中走出来。这就好像你从睡梦中惊醒，就很难再回到睡梦中去了。当环境比较嘈杂时，你可以选择带一个防噪耳机，或者是戴耳塞，总之是让自己不被这些所影响。
  
上面这些是文章给的建议，这里我补充一下。其实进入心流状态并没有那么简单，它对你所做的事情也有很高的要求，首先你所做的事情不能远远超出你的能力范围，否则你找不到 “做成一件事情的感觉”，也就很难进入心流状态。相反的，你做的事情也不能过于简单，就像开车一样，开多了，熟练了其实就不会放过多的注意力在上面，也很难进入心流状态。最好的是，你所做的事情巧合符合你的能力，就是那种你需要集中注意力才能做好的事情。

所以，如果你觉得当前你做的事情太难，那就把这个难的事情拆分成你会做的相对来说简单的事情。如果你做的事情太简单，那么在时间分配上，可以把这些事情分配到自己可能会被打扰的时间段去完成。总之，需要不断去尝试就对了，让自己不浮躁，能专注到事情上去才是关键。

<br>

## Tip

这周继续学习 vim 的一些技巧和插件。

### vim 中的寄存器：

vim 中的寄存器可以搭配 c, d, y 等命令来进行编辑。vim 中有很多不同的寄存器：

* **无名寄存器**：我们默认使用的寄存器，可用 "" 指定，""p 等效于 p
* **数字寄存器**：1-9，存的是最后一次，倒数第二次。。。倒数第九次的操作内容。其中删除的内容进入 1-9 寄存器的条件是删除的内容至少有一行，或者使用了移动命令，否则进入的是**小删寄存器（-）**。0 号寄存器放的永远是最近一次复制的内容，这里区别于无名寄存器的是，无名寄存器存放的是最近的一次操作，比如 d, c, p, y，而 0 号寄存器仅仅是存放最近一次复制 (y) 的内容。
* **有名寄存器**：a-z，用户指定的寄存器，下一次依然存在。例如，ayy 是把当前行复制到寄存器 a 中
* **剪贴板寄存器**：+，操作系统的剪贴板，比如，"+p 可以代替 <C-V\> 或者是 Mac 下的 <D-V\>

所有的这些寄存器在正常模式下可以用 d, y, p 等命令来访问。在插入模式下可以用<C-R\>加寄存器名来访问。
  
应用场景：

* **交换相邻的两行** -> ddp
* **交换两处文本** -> 选中一处文本 (ve)；删除 (d)；选中另一处文本；粘贴 (p)；去到之前的位置粘贴 (p)。
 
 这里主要是利用了粘贴和删除都会把内容存在无名寄存器这一特性

* **少量修改某一变量名称** -> 把光标移到变量上；\*；编辑；复制(y)；n；viw"0p；最后两部不断地重复

<br>

### vim 中的宏

宏是 vim 中执行重复操作的利器。其主要是将一系列操作录入（q）寄存器中，然后需要的时候播放（@）出来。比如说，我们要进行变量名称的变更，但是文件中有很多地方引用了该变量，如果一个个去改比较费时费力，这里可以考虑用宏，当然你也可以使用全局的替换命令。这里先看看用宏该怎么做：

* 在变量上，用 \* 指令开启全局搜索
* qa，开始录制宏到寄存器 a 中
* n，开始搜索，这里先开始搜索可以保证找不到要修改的变量就不执行变更操作
* 修改变量
* q，结束宏的录制
* @a，开始播放宏，当然你也可以在这个命令前面加上数字表示需要重复播放的次数

<br>

### vim 中的自动完成
<C-P\>、<C-N\> 可以自动完成变量的填写，可以用其进行需要完成的变量的选择和切换

<C-X\><C-F\> 启动文件的自动完成，但是注意这里的目录是当前启动 vim 所在的目录，而不是文件所在的目录

<C-W\>f 和 gf 可以跳转到文件中去，其中前者是新建一个窗口来打开文件，后者是直接打开文件。

<br>

### 一些好用的插件

**tpope/vim-surround**：

新增了一些比较方便的操作，比较常用的有：

ysiw" -> 单词外面加引号 <br>
cs"'  -> 单词的双引号变单引号 <br>
ds"   -> 移除外围的双引号，另外 dst 可以移除文本外的第一个 HTML 标签 <br>
S<em\> -> 可视模式下给选中的文本加上 <em\> 的标签 <br>

**tpope/vim-repeat**:

使得 vim 的重复操作命令（.）对上面给出的自定义的命令生效

**mbbill/undotree**:

装了这个插件之后，就可以把之前对文本进行的操作通过文本的形式显示出来。可以很方便地跳转到之前的编辑状态（有点类似 git）。

需要注意的是，不管你有没有更改文件，如果想要保存编辑记录，都需要 :w 存盘一次，这样下次打开文件才能看到之前的编辑历史。

另外如果用其它的编辑器修改了文件，如果想保留编辑历史，需要用 vim 存盘后同时打开该文件。不然，之前编辑记录也会消失。

**tpope/vim-eunuch**

也是一个针对上面插件（mbbill/undotree）的一个辅助插件。可以保证文件被重命名或者被移动后，编辑历史依然存在。

:Rename ...   重命名 <br>
:Move ...     移动文件

**fzf**

一个非常有用的文件的模糊搜索工具，该工具不仅可以在 vim 中使用，还可以在 shell 中使用。在 Mac 下可以使用 Homebrew 进行安装：

```
brew install fzf
```

安装后，直接输入 fzf 就可以使用，它会显示当前目录下的所有文件，然后你可以模糊输入你想要查找的文件名，效果图如下：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/413bc10d085342fdbc208dc41ad7c11f~tplv-k3u1fbpfcp-zoom-1.image)

回车之后会在 shell 中输出你选中的文件路径。

如果想要在 vim 中使用的话，可以安装下面两个 vim 插件：

```
junegunn/fzf
junegunn/fzf.vim
```

安装好后，直接命令模式下 :Files 加回车就可以进行文件搜索，效果图如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27a7d3a98b3f45c4800aaa835a089c82~tplv-k3u1fbpfcp-zoom-1.image)

这里，左边是根据你的输入匹配上的文件列表，右边是当前选中的文件的内容。选中文件后，按回车会直接跳转到选中的文件中去。

除了这些，当然你还可以安装一些辅助插件来让右边的文件显示上多一些色彩，但是感觉没有太大的必要，我这里就没有安装。

<br>

## Share

最近在读 《知识的错觉》，这里简单说一下感想吧，毕竟书还没有读完。但是里面讲述的一个认知我觉得还是蛮有价值的。

在日常的生活中，我们可以完成很多的事情，比如开车，做饭，打开电脑编程。当你做的事情越多，你会有一个感觉就是自己貌似掌握了很多全能的技能，比如说会开车后，你会觉得自己应该什么样的车都可以开。但是事实是，**很多事情我们的认知其实都是停留在事物的表明，一个事物远比你想象的复杂**。你会开车那也只能说明你会打方向盘和挂档，而且仅限于某一类的车型。你会在家做饭也只能说明你可以利用家庭的小设备进行一些简单烹调，在另一个场景下，设备换了，环境换了，可能你又需要花时间熟悉。另外一点是，我们更加习惯快速的直觉思维，比如遇到需要开车的时候，你的直觉思维会马上告诉你 “你会开车”。在这种状态下，你都不会去想自己会的是哪种类型的车，当前的场景适不适用，就觉得自己完全可以胜任。这也就会导致自己的期待和自己的能力不匹配的情况。

所以，有些时候，明确自己的认知边界其实挺重要的，这样可以避免掉很多没有必要的尝试。也可以防止事与愿违而打击自己的自信。当然这也需要自己能够尽量多地去养成谨慎思考的习惯，而不是仅仅依赖于自己的直觉思维。总之，相比于这个世界上的所有的知识，我们知道的很少很少。但是我们可以利用这些知道的东西去完成很多不可思议的事情，至少是在某一方面。这需要你静下心来，不急不躁地去认识自己，认识你所要干的事情，理性并带有思考地去行事。