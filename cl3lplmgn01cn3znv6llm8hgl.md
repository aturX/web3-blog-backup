## 如何在 Remix 中将合约源码同步至 Github 仓库?


## 背景介绍

在 [Remix IDE Online](https://remix.ethereum.org/) 中编写智能合约与在 Google 文档中编写文本不同。虽然, 两者都是在浏览器中编写的，但是 Google 文档会将你的工作内容保存到 Google 的服务器上，而 Remix（开箱即用）只会将你的代码保存到浏览器的存储中。所以小心行事,保存好你的合约代码，浏览器存储不是永久的！



如果清除浏览器存储或浏览器严重崩溃时，你可能将丢失你的代码。通常浏览器存储空间非常有限，大约为 5MB。虽然你可以将大量 Solidity 文件放到到 5MB 中，但你还是不会希望在浏览器崩溃时丢失你的代码。



> *据说：在下一个 Remix 版本中，官方将使用 IndexedDB，它极大地扩展了浏览器的存储容量。而且，你现在可以在 https://remix-alpha.ethereum.org 上使用它
> 注意：如果你清除存储空间，或者在使用 IndexedDB 时浏览器崩溃，你仍然会丢失文件。*



## 避免丢失你的代码 — 使用 DGit 插件！


DGit 插件允许你执行push 以及你习惯的所有其他 Git 操作。


这篇文章是关于如何使用DGit 插件在GitHub中推送和保护文件的简短教程。


## 设置、克隆和推送


  1、转到 Remix 中的插件管理器并激活 DGit 插件


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653486921173/2TXqU6zbT.png align="left")


   2、单击DGIT图标以显示其 UI。


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653486940039/qG6vMiTc0.png align="left")


以下是有关其中一些部分的更多详细信息：

- 版本控制：暂存、取消暂存和提交文件。也可以通过单击文件名来显示差异。

- clone, push, pull & remotes：克隆、推送和添加远程仓库

- github settings：设置你的 GitHub 令牌、名称和电子邮件以推送/拉取和克隆私有仓库

- commits：显示提交（你也可以从此处签出提交）

- branches:  创建一个分支并检出一个分支

- IPFS 导出/导入/设置：克隆或推送到 IPFS 服务器而不是 GitHub


3、展开 GitHub 部分，输入仓库的 URL，然后单击clone进行克隆。如果你想稍后推送它，请克隆你有权推送到的存储库。



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653486961168/Sg04bq_pn.png align="left")


4、使用克隆的仓库创建一个新工作区。


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653486985185/gASZb8_Lj.png align="left")


5、创建一个新分支：在branch部分，通过在分支名称文本框中命名来创建一个新分支，然后单击git branch按钮。然后点击它旁边的check按钮进行检查。工作区现在绑定到这个新分支



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653487013619/56cR8Uf8U.png align="left")



6、在推送到远程仓库之前：你需要指定个人访问令牌、姓名和电子邮件，以在 GitHub 平台上识别你的帐户。



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653487033915/tCBL7NKx9.png align="left")


7、在你推送之前（还有一件事）：确保你要推送到正确的仓库。在github部分中，检查你要推送到的 repo 是否是在available remotes 子部分中选择的。如果还没有，请将其添加到git remote 部分。



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653487053961/1Sn2iMhs2.png align="left")



8、 现在点击推送。


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653487070640/Dx3lAaHP9.png align="left")


9、现在编辑一些代码：进入files explorer，选择一个文件并在编辑器中添加它。然后回到 DGit 插件。展开源控制部分。从这里你可以暂存文件（也称为“添加”）、回滚更改、查看差异并提交。提交后，返回github部分并再次推送（与第 8 步相同）。这些更改也可以在拉取请求中看到——就像往常一样。



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653487096704/-wCorqRDw.png align="left")



有关 Remix DGit 插件的更多信息，请[查看这篇文章](https://medium.com/remix-ide/github-in-remix-ide-356de378f7da)。



好的，就是这样！我希望你喜欢这篇短文。

###  请关注我们, 不错过更多优质内容:  

- *Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

- *蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)

- *Youtube*:  [链求君](https://www.youtube.com/channel/UCDrmDcLjnmIQk-xtNuJ42Sw)


