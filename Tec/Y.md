
### 无名

> 原文:https://segmentfault.com/a/1190000012286537
> 改写为Scheme


> 按语：我从悬崖上跳了下去，在除了坠落没别的事可干的过程中为「不懂编程的人」写了这一系列文章的第八篇，整理于此。。

警告，这一篇很长。若不能坚持看到最后，最好还是别看了。更何况，不看它，也没啥损失。

下面这个函数，可以对自然数求和：

```scheme
(define sum
 (lambda(n)
  (if (= n 0)
      0
    (+ n (sum (- n 1)))))
```

传说数学界的大宗师高斯同学上小学的时候，曾因飞快地算出从 1 到 100 的自然数之和而震惊四座。

在计算机里不需要精巧，只需要笨拙，所以上述的 sum 函数就是从大到小，将数字逐一累加。例如：

```scheme
(sum 100)
```

在 sum 这台发动机周而复始的运转中，每次都将自己的参数减去 1，然后与自己下一次的运行结果相加，最终发动机的运转轨迹形成了 100 + 99 + ... + 1 + 0 这样的表达式，当然在Guile里是这样的：

```scheme
(+ 100 (+ 99 (+ ... (+ 1 0) ...)))
```
当 sum 发现自己的参数为 0 时，它就停止转动，此时 Guile就得到了上述的表达式，接下来，Guile就开始对这个很长的表达式求值，不过就是逐一完成数字累加的这种粗笨的工作，结果为 5050，跟高斯算出来的一样，而且可能比高斯算得还要快许多倍。

不过，这篇文章不是再次重复发动机啊发动机啊的，而是开始思考怎么去定义一个不知道名字的发动机。

再看一遍 sum 函数的定义：

```scheme
(define sum
 (lambda(n)
  (if (= n 0)
      0
    (+ n (sum (- n 1)))))
```

要让一个发动机周而复始地运转，必须得知道它的名字。有了名字，sum 函数方能在自己的定义中对自己进行求值。

于是，问题就来了。倘若宇宙的一切运动是由一台发动机驱动起来的，那么这台发动机是如何运转的呢？这台发动机没有名字，因为它比我们这些命名者先出现。这台发动机的存在，暗示着，没有名字也能周而复始。

所谓函数，本质上就是值与值的映射，它有没有名字，无关紧要，关键在于能否在不依赖名字的前提下精确描述映射关系。为此，Guile提供了让函数匿名的语法——Lambda 表达式。对于函数 f(x, y) = x + y，用 Lambda 表达式可表示为：

```scheme
(lambda(x y) (+ x y))
```

倘若 x = 1, y = 2，那么如何通过这个 Lambda 表达式对 f(x, y) 进行求值呢？像下面这样做：

```scheme
((lambda (x y) (+ x y)) 1 2)
```

没有名字，会让世界变得更混乱一些，不过这反而是世界原本的样子。从这个角度去探索世界的本原会更为客观，可能也更为奇妙。

当我们仰望星空，俯瞰大地，感觉冥冥中有一种力量在驱动着或者支配着这个世界的运行，我们没法给这种力量取名，即使取了名，似乎也没有用，因为没法根据所取的名字去描述它的运作原理。智慧如老子，也只能勉强给这种力量取个名字，叫作「大」。大，就是没有边界。没有边界，就是非常遥远。非常遥远，就是自己的后脑勺——你向任何一个方向看去，距离你最远的地方是你的后脑勺。

不能给这种力量取名，就没法描述它的运作原理了吗？倘若世界受一台没有名字的发动机的驱动而运转，那么我们随便从这个世界里找一台发动机，然后利用匿名函数消除这台发动机的名字，结果是不是就能够切实感受到它的存在呢？

试试看：

```scheme
(lambda(n)
  (if (= n 0)
      0
    (+ n (sum (- n 1)))))
```

很好，已经消除掉外层的 sum 了，但是里面的 sum 怎么消除？这需要借助一个小技巧，即它虽然在那里，但是我们可以装作看不见它。就像房间里有一个人，你不喜欢他，但是又没有能力让他消失，所以只好装作没看见他。每个人应该都具备这种技能。那么，怎样对上面这个匿名函数里面的 sum 视而不见呢？把它当作房间里一种没有意义的物件就可以了，像下面这样：

```scheme
(lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
      (+ n (thing (- n 1))))))
```

没有意义，就是意义不确定。意义不确定的东西，就是变量。函数的参数是变量。所以，只需要将 sum 变成一个函数的参数，就相当于对它视而不见处了。

现在，我们将这个函数视为我们所创造的第一个没有名字的函数，但是为了便于描述，姑且将其称为 `X`。对`X`进行求值，需要向它传递一个匿名函数，求值结果是一个匿名函数，就这么奇怪。

`X`能够用于自然数序列的求和吗？试试看：

```scheme
((lambda (thing)
   (lambda (n)
      (if (= n 0)
          0
          (+ n (thing (- n 1)))))) ...)
```

写着写着就发现，在省略号的地方写不下去了，不知道该向这个函数传递什么样的参数。虽然我们很清楚，thing 应该是一个匿名函数，但是我们现在并没有这个函数。因此，不妨试着随便定义一个，将它作为 thing 传给 X，看看会发生什么：

```scheme
((lambda (thing)
   (lambda (n)
        (if (= n 0)
            0
            (+ n (thing (- n 1)))))) (lambda (m) m))
```

新定义的匿名函数就是`(lambda (m) m)`，这个函数什么也没做，就是把自己接受的参数作为求值结果。将它作为参数传递给 X 之后，X 的求值结果就是下面这个匿名函数：

```scheme
(lambda (n)
  (if (= n 0)
      0
    (+ n ((lambda (m) m) (- n 1)))))
```

倘若将`100`传递给这个匿名函数，即：

```scheme
((lambda (n)
   (if (= n 0)
       0
       (+ n ((lambda (m) m) (- n 1))))) 100)
```

结果得到 199，而不是 5050。看来这个匿名函数只能算 100 + 99。不要沮丧，因为我们现在又得到了一个可以作为`thing`传给`X`的匿名函数了。把它传给 X：

```scheme
((lambda (thing)
     (lambda (n)
        (if (= n 0)
            0
            (+ n (thing (- n 1)))))) (lambda (m) m))

```
现在，得到的是能够计算 100 + 99 + 98 的匿名函数。

是不是发现了一点玄机了？我们一开始随便定义了一个匿名函数，把它传给 X，结果得到了一个新的匿名函数，然后再将这个新的匿名函数传递给 X。

再试试用同样的手法， 将能够计算 100 + 99 + 98 的匿名函数传给 X：

```scheme
((lambda (thing)
   (lambda (n)
     (if (= n 0)
         0
         (+ n (thing (- n 1))))))         
((lambda (thing)
   (lambda (n)
     (if (= n 0)
         0
         (+ n (thing (- n 1)))))) (lambda (m) m)))
```

结果就得到了一个能计算`100 + 99 + 98 + 97`的匿名函数。

继续将能计算`100 + 99 + 98 + 97`的匿名函数传给 X,结果就得到了一个能计算`100 + 99 + 98 + 97 + 96`的匿名函数了。

只要你不怕麻烦，可以将上述过程继续下去，最终 X 就能算出`100 + 98 + ... + 1 + 0`。最后一次传给 X 的匿名函数必定是体积极为庞大的怪物。

为了更便于理解，我们用 X 这个名字，将能计算`100 + 99 + 98 + 97 + 96`的匿名函数简写成：

```scheme
(X (X (X (lambda (m) m))))
```

利用 X 生成匿名函数，再将这个匿名函数传递给 X，这个想法很好，只是在实现上有些愚蠢。这个过程类似于为了实现 1 个发动机运转 100 圈的效果，制造了 100 个发动机，让它们的每一个只运转一圈。即使这样做，也不是太难，但是倘若要实现 1 个发动机转无数圈的效果呢？好办，制造无数个发动机，让每一个只运转一圈。倘若你真的这样想，那就对了。制造无数个发动机，每一个只运转一圈，这不就是相当于将 X 作为参数传给自己，然后让它运转一圈吗？这个过程应该像下面这样描述：

```scheme
(X X)
```

当然，在 X 的定义中，需要让作为参数的 X 运转一圈，以便生成像 (lambda (m) m) 这样的匿名函数。因此，将 X 的定义修改为：

```scheme
(lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
      (+ n ((thing thing) (- n 1))))))
```

然后按照`(X X)`这样写：

```scheme
((lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
        (+ n ((thing thing) (- n 1))))))

(lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
        (+ n ((thing thing) (- n 1)))))))
```

上述表达式的求值结果是一个匿名函数。这样，我们就构造了一个比上文用 X 构造的匿名函数再传给 X 的代码简洁了将近 100 倍的可对自然数序列求和的匿名函数。

看，我们没有使用`define`，单纯借助匿名函数就实现了一个递归函数。这说明了什么？这说明了，即使这个世界没有任何名字，它依然能够运转，亦即世界只能感受或描述，而不能定义。

一个周而复始的发动机，它之所以能够如此，不是因为它有了名字，而是因为它的本质在于将自己作为参数传递给自己。这就是函数递归求值的本质。

现在，我们仅实现了一个特定功能的匿名函数的递归求值，还没有真正达成我们的目标，即寻找一个通用的匿名函数递归机制。只有这种发动机能够驱动整个世界。不过，我们似乎已经有了一个正确的方向，现在要做的就是，继续前进。

重新观察 X：

```scheme
(lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
      (+ n ((thing thing) (- n 1))))))
```

位于内层的匿名函数是与特定功能相关的部分，我们可以用前文已经使用了多次的手法，将这部分代码提出来，作为一个单独的匿名函数，称之为 F，然后想办法将它作为参数传给 X 即可。

F 的定义如下：

```scheme
(lambda (n)
    (if (= n 0)
        0
      (+ n ((thing thing) (- n 1)))))
```

这个定义是错的，因为 thing 原本是 X 的参数。之前， F 位于 X 内部，它认识 thing，现在它脱离了 X，就不认识它了。该怎么办呢？用老办法，凡是自己不喜欢的或者不认识的，统统提升为参数，装作没看见它们……于是，我们将 F 修改为：


```scheme
(lambda (thing)
  (lambda (n)
    (if (= n 0)
        0
      (+ n ((thing thing) (- n 1))))))
```

等会！这东西似曾相识，它不就是 X 么？没错……X 的引力太大，F 似乎无法挣脱它而孤立地存在。

再仔细研究一下，F 之所以难以摆脱 X，主要是因为 `((thing thing) (- n 1))` 的制约。这是个函数求值表达式，`(thing thing)` 是一个函数，它接受一个参数。我们想办法把它作为 F 的参数如何？

试试看：

```scheme
(lambda (thing*)
  (lambda (n)
    (if (= n 0)
        0
      (+ n (thing* (- n 1))))))
```

为了与 X 的参数 `thing` 有所区分，我特意将 F 的参数命名为 `thing*`，而实际上，继续用 thing 也没事。

倘若我们对 F 像下面这样求值：

```scheme
((lambda (thing*)
   (lambda (n)
     (if (= n 0)
         0
         (+ n (thing* (- n 1))))))
     (lambda (m) ((thing thing) m)))
```

看上去有点乱，这样看就比较清楚了：

```scheme
(F (lambda (m) ((thing thing) m)))
```

就是向 F 传递了一个具有 1 个参数的匿名函数，函数体中的 `thing`是 X 的参数，所以我们可以将上述表达式扔到 X 的定义里：

```scheme
(lambda(thing) (F (lambda (m) ((thing thing) m))))
```

看，现在 X 里面不再有任何特定功能的代码了。我们已经成功地将原先的自然数求和的代码从 X 中分离了出来，这部分代码构成了匿名函数 F。

现在，我们再用 (X X) 的办法对 X 进行求值，即：

```scheme
((lambda (thing)
           (F (lambda (m) ((thing thing) m))))
(lambda (thing)
           (F (lambda (m) ((thing thing) m)))))
```

这样，就可以得到一个递归的匿名函数，而且这个匿名函数里面没有任何特定功能的代码，它是一个通用的匿名递归函数。不过，现在它还不知道 F 是什么。用老办法，凡是你不喜欢的或者你不知道的，若你不想因为它们而坏掉好心情，就将它们统统提升为函数的参数：

```scheme
(lambda (F)
  ((lambda (thing)
             (F (lambda (m) ((thing thing) m))))
           (lambda (thing)
             (F (lambda (m) ((thing thing) m))))))
```

我们将这个函数称为`Y`。

还记得上面所定义的含有自然数求和代码的那个 `F`吗？

```scheme
(lambda (thing*)
  (lambda (n)
    (if (= n 0)
        0
      (+ n (thing* (- n 1))))))
```

以它为参数，对 Y 进行求值，即

```scheme
(Y F)
```

求值结果是什么呢？一个匿名的自然数序列求值函数。倘若不信，那么用它算一下 0 到 100 的和：

```scheme
((Y F) 100)
```

结果为 5050。

上述的匿名函数 Y，由于它所接受的参数是一个匿名函数。按照数学家们的说法，不管函数是不是匿名的，只要是参数为函数的函数，就叫算子。所以，函数 Y 应该叫 Y 算子，当然更多的人愿意称它为 Y 组合子，并将这个算子视为匿名函数世界中的神迹。发现这个算子的那个数学家，将这个算子的数学公式 `Y = λf. (λx. f(x x)) (λx. f(x x)) `纹在了自己的胳膊上。

这个 Y 组合子，也就是我们一开始想要寻找的那个驱动整个世界的无名发动机。不过，现在它看上去还有点不够理想。因为它的参数 F 只是具有 1 个参数的函数，这意味着 Y 组合子构造的匿名递归函数只能接受 1 个参数。不过，这不是什么大问题，多个参数的函数总是能通过单个参数的函数构造出来，不过，这是另外一个话题了。

这篇文章可能你看不懂。没关系，不懂这个，不影响学会编程。类似于不懂汽车发动机原理，一样可以驾驶汽车。毕竟我们是生活在一个到处都充满了名字的世界里。似乎老子早在两千多年前就看清了这一切，他低吟着：无名，万物之始也；有名，万物之母也。故常无欲，以观其妙；常有欲，以观其徼……虽然数学家发现了 Y 组合子，但他们也不会真的在编程中使用这种东西来写递归函数。

倘若你真的很想看懂这篇文章，唯一的办法是，集中精力，动手动脑，逐步推导。理解 Y 组合子，并非毫无意义，将 Y 倒过来，它是个人。这方面的话题，我不想多说，不然会令人反感。


