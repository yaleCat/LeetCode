### 1728.Cat-and-Mouse-II

此题是```913.Cat-and-Mouse```的一道延伸，基本思路是一致的，就是BFS。建议先仔细阅读913的题解和视频。

对于这种策略型的问题，DFS的解法是错误的。为什么呢？这是因为有“和”的可能。我们回想一下如果用通常用递归来做决策问题时，我们的思路大致是：如果我当前轮的每一种决策，都会导致对手的胜利，那么我这一轮必败；如果我当前轮的某一种决策，会导致对手的失败，那么我这一轮必胜。

但是本题中会有“和”的可能，比如说food被墙壁包围，但是猫永远抓不到老鼠（e.g.绕着圈跑）。这种“和”的决策是递归无法判断的，就目前所知，我们无法写出正确的递归决策表达式。

另外，注意一点，本题也不是简单的判断“老鼠和猫哪个离food近”。猫可以离food更远，但是它可能离老鼠更近，或者可以堵住老鼠的出路而必杀老鼠。

所以本题和913需要采用同样的策略，那就是设计决策状态，然后从最终状态通过BFS逆推最初状态！

我们设计状态(m,c,t)表示当前老鼠在m的位置、猫在c的位置、此时谁先动（1表示老鼠，2表示猫）的状态，其值有三种，0（平局，老鼠和猫都到不了food且不能相遇）、1（老鼠必赢）、2（猫必赢）。

我们先考察哪些状态是已知的，有下面几类：
1. 老鼠已经在food，猫在其他地方，无论turn是谁，老鼠已赢
2. 猫已经在food，老鼠在其他地方，无论turn是谁，猫已赢
3. 老鼠和猫在同一个地方，无论turn是谁，猫已赢

我们将这些状态放入一个队列之中。我们尝试从这些已知的(c,m,t)的状态通过BFS外推出其他的状态的结论（赢或者输），直至得到(m0,c0,1)就是我们的答案，其中m0和c0就是老鼠和猫的初始位置。

那么如何从某个已知的(c,m,t)，来推导另一个(c2,m2,t2)呢？这个决策机制比较神奇，需要好好体会：
1. 我们如何推出某个新状态是必赢的结论呢？如果(c,m,t)是老鼠轮、但结论是已知猫必赢，那么任何能够走向(c,m,t)的状态(c2,m2,t2)，必然也是猫必赢，因为t2是猫轮，猫必然会选择(c,m,t)这个状态。我称之为immediate win。类似地，反过来也成立： 如果(c,m,t)是猫轮、但结论是老鼠必赢，那么任何能够走向(c,m,t)的状态(c2,m2,t2)，必然也是老鼠必赢
2. 我们如何推出某个新状态是必输的结论呢？如果(c2,m2,t2)是老鼠轮、而且它下一步的所有猫的决策(c,m,t)都是已知老鼠输，那么(c2,m2,t2)就是老鼠必输，因为它没有任何能导致自己不输的手段。同理，反之，如果(c2,m2,t2)是猫轮、而且它下一步的所有老鼠的决策(c,m,t)都是已知猫输，那么(c2,m2,t2)就是猫必输，

以上两点就是拓展新状态的输赢结论的方法，事实上其他任何情况下我们都无法推断某个状态的输赢。所以当BFS结束（队列为空的时候），其实会有很多(c,m,t)的结论位置，其实这些状态就意味着猫鼠游戏会是平局。

注意本题里超过1000步、或者猫鼠永远不相遇，也都意味着老鼠输。所以返回结果是判断(c0,m0,t)==1.