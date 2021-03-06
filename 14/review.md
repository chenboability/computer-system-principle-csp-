# review

## 总结

计算机系统由于各种各样的故障和bug都会导致发生crash，致使用户数据丢失。为了避免系统crash后数据发生丢失，本文提出了CHL，允许开发人员对系统状态和行为进行准确的定义，并可以自动检查系统状态是否符合预定义的specification。并且，基于CHL提出了第一个crash安全的文件系统——FSCQ，保障了crash后系统的一致性。最后，对系统的评估结果表明，系统可以运行大量未修改的Unix应用程序并取得可接受的性能。

Crash Hoare Logic（CHL）：由于文件系统在正常操作过程中出现bug或者其他原因产生crash都会导致数据的不一致，因此作者在Hoare Logic的基础上提出了CHL，用于确保文件系统的状态符合specification，主要包含3方面的改进：crash状态、逻辑地址空间和恢复执行语义。首先，定义二元组来表示每一个状态（包含最后一次写的值和一个先前值的集合），接着，在过程、前置状态、后置状态的基础上增加crash状态，用于确保crash后，状态要么回滚到事务操作前的状态，要么整个事务到达完成状态，从而保证事务的原子性。对于更高层次抽象的操作（如日志系统操作）而言，提供了逻辑地址空间的状态表达形式，使得specification更为简洁。最后，对于恢复，定义了log_recover的恢复执行语义，包含了两种流程（正常完成流程和恢复流程）。

验证specification：利用CHL定义的specification如何进行验证呢？作者提出了一种逻辑蕴含推理方法，用于对人工构建好的specification进行自动验证（当涉及到多层次抽象时仍需人工参与验证），主要包含两个步骤。第一步：程序步，用于将specification转换成一系列proof obligations，对于程序中的每一步都会产生2个proof obligations，即程序的当前状态暗示了当前步的前置状态和当前步的crash状态暗示了程序的crash状态，此外在程序结束时还会产生最终的proof obligation，即程序最终状态暗示了程序的后置状态。第二步：断言蕴含，利用分离逻辑验证第一步中的obligations。

FSCQ文件系统： FSCQ提供了3种基于CHL的磁盘操作，即disk_write、disk_read和disk_sync。提供了一种类似于posix的上层接口，选择all-or-nothing策略来保证事务的原子性，从而确保crash时的一致性和持久性。设计了一个写前日志的系统FscqLog，且限制了事务的并发性（牺牲性能），在系统发生crash之后，可以正确地从7种可能的状态中恢复。定义了一个通用的资源分配器，使用不同的bitmap来对不同级别的资源进行分配（如inodes、磁盘块）。对于磁盘块中的数据结构，建立Rec库将数据结构以bit级的方式组织和表示。

性能评估：FSCQ的性能接近xv6，但是和ext4系列相比，还是产生了不小的性能下降；FSCQ有效地减少了一些bug，如违反文件/目录不变性、日志逻辑错误等；由于FSCQ设计较为简单，导致了功能不够完善和较大的性能负担。

## 观点

（1）优点：

1. 在Hoare Logic的基础上提出了CHL，同时利用逻辑地址空间使得不同层次操作具有更加简洁的表达形式；
2. 对specification具有高度的自动化验证，减少开发人员干预；
3. 提出了第一个crash安全的文件系统——FSCQ，保障了crash后系统的一致性。


（2）缺点：

1. 利用CHL方法需要开发人员定义大量的specification，需要高度专业化知识，且费时费力；
2. 提出的FscqLog日志系统不允许事务的并行，带来了比较严重的性能负担；
3. 提出的FSCQ文件系统还不够完善，且性能与现有文件系统相比差距较大，难以实用化。
