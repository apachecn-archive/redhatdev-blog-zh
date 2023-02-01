# 声明边界注释和位置视图

> 原文：<https://developers.redhat.com/blog/2017/07/11/statement-frontier-notes-and-location-views>

当然，当在符号调试器中单步执行优化的程序时，您也感到沮丧，因为源代码中的[布朗运动](https://en.wikipedia.org/wiki/Brownian_motion)，并且永远不确定当您到达某个源代码行时(如果您能到达的话)，前面的行是否已经生效。我们的挫折感将会大大减轻，这要感谢即将贡献给 GNU 工具链的两项新技术。

语句边界注释是源位置的稳定标记，例如，在编译中很早引入的语句的开始，并且相对于诸如在 VTA(赋值时的变量跟踪)项目中引入的“变量绑定”副作用调试标记这样的预先存在的源位置标记而保持在适当的位置。

这种独立的标记充当调试信息生成的固定点，同时让编译器可以自由地优化和重新安排代码，已被证明是改进变量位置和值信息的坚实基础。它们现在被带到下一个级别，识别理想的检查点来观察程序状态，正如人们对源代码的非优化执行所期望的那样。

通过启用语句边界注释，GCC 将使用它们来发出行号表的`is_stmt`列，这样单步执行将从一个语句前进到下一个语句，正如所预期的那样，即使从它们周围的多行生成的指令最终被打乱。单个指令仍将引用回它们的原始源代码行，以便仍有可能理解例如机器指令步进。

此外，`is_stmt`将不再与从给定源代码行生成的流中的第一条机器指令相关联，而是与在该行之前预期已经发生的变量绑定事件逻辑一致的程序位置相关联。您在一行的推荐断点处停下来，您可以观察所有绑定到期望值的变量，只要它们是可用的或可计算的。

现在，那不是很好吗，或者至少，嗯，很高兴拥有？唉，有点小复杂。例如，在观察某个源程序位置的状态的理想检查点，可能有一条机器指令与一条不相关的线相关联。代码移动甚至可能为同一条机器指令中的多条语句留下理想的检查点！

我们可以发出编码所有这些信息的行号表和变量位置列表，在同一个可执行地址有多个检查点，变量绑定在同一个地址开始和结束。但是这不会帮助调试器理解它:变量绑定的这种空的活动范围也是没有意义的，因为它们不能以任何方式与指示程序预期进度的检查点相关联。

输入位置视图。我们已经设计了一种从行号表中导出视图计数器的方法，使得在相同的代码地址可以有多个视图，并且还以向后兼容的方式扩展了变量位置列表，使得绑定范围可以将各个视图命名为起点和终点。这使调试信息使用者能够在建议的断点处停止程序。

这使调试信息使用者能够在建议的语句断点处停止程序，并观察在源程序点应该可以观察到的状态。即使与该语句相关的所有指令都被优化或移走，然后将该语句转到下一个语句，并观察前者的副作用。即使两者的推荐断点都在相同的地址:只有位置视图前进。

我们已经列出了交互式调试器的优势，但是这些优势通常有可能在非优化的程序上工作，并且这些新特性只对优化的程序有利。对优化程序进行操作的调试信息消费者，例如在生产中检查优化程序内部状态的监视器，也将受益匪浅:由于推荐的检查点相对于变量绑定是逻辑排序的，它们将更有可能获得所需的信息，至少对于从行号信息的`is_stmt`标记中导出的检查点是如此。

我们设想这样的监视器可以受益于额外的标记和视图扩充位置，例如用于内联函数的入口的检查点，使得监视器可以检查所有参数都被绑定的状态，而不是在来自内联函数的任何随机指令处，该指令可能已经被调度得更早。

出口点的标记使监视器能够确定一个函数将要返回的值，同时仍然在 return 语句的范围内，这在期望列表中也是很重要的。所有这些都需要进一步扩展调试信息和工具链，但它们很可能建立在语句边界注释和位置视图的基础上。

这个项目有很长的路要走:它第一次[发布](https://people.redhat.com/aoliva/papers/sfn/gcc2010.pdf)，[在 2010 年的 GCC 峰会上展示](https://people.redhat.com/aoliva/papers/sfn/slides.pdf)，但它直到 2017 年才得到一个工作实现。在 GCC、GIT 分支奥利瓦/SFN 实施；通过 binutils+gdb GIT branch users/ao liva/SFN，我们可以获得比单独使用 GCC 更紧凑的行号信息。位置视图编号作为[提议的扩展](https://people.redhat.com/aoliva/papers/sfn/dwarf6-sfn-lvu.txt)提交给 DWARF 调试信息格式标准。

Systemtap 和 GDB 已经可以使用`is_stmt`标记，所以他们可以从声明前沿注释中获益，而无需任何进一步的努力；至于位置视图，我们期望调试信息消费者将在不太遥远的将来获得对它们的支持。

* * *

**无论你是 Linux 新手还是有经验的人，下载这个** [**备忘单**](https://developers.redhat.com/promotions/linux-cheatsheet/) **都可以在遇到你最近没做过的任务时辅助你。**

*Last updated: July 10, 2017*