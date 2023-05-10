### Verifiable shuffles

There are two lists of committed values: ```x1,...,xn``` and ```y1,...,yn```. The goal is to prove that the second list is a permutation of the first. This problem is called a verifiable shuffle. 

Efficient zero-knowledge argument for correctness of a shuffle的具体实现方式是基于离散对数问题的，主要包含以下步骤：

输入参数：一个包含原始消息的数组m、一个随机置换后的消息数组m'，以及一个公共参数G（一般是一个离散对数问题的群）。

建立一个交互式证明系统，包含两个角色：证明者（Prover）和验证者（Verifier）。

Prover首先需要生成一个随机数r，并计算出一个承诺值C，即C = g^r * h^s，其中g和h是G中的生成元，s是一个随机数。

接着Prover需要计算出一个证明值，即一个包含多项式系数的向量t，并将其发送给Verifier。

Verifier需要检查Prover发送的t是否满足一些约束条件，比如系数和为0，系数的个数等于m和m'的长度等。

如果验证通过，Verifier会发送一个随机挑战值e给Prover。

Prover需要根据e计算出一个响应值z，并将其发送给Verifier。

Verifier需要检查Prover发送的z是否满足一些约束条件，比如z的长度等于t的长度等。

如果验证通过，Verifier会接受Prover提供的证明，否则将其拒绝。

Prover需要进行反复的挑战和响应过程，直到Verifier完全接受其提供的证明。

最终，Verifier可以使用承诺值C、随机挑战值e以及Prover提供的响应值z，计算出一个检验值V，并检查V是否等于g^t(m) * h^t(m')。

如果V等于g^t(m) * h^t(m')，则Verifier接受Prover提供的证明，否则将其拒绝。

总之，Efficient zero-knowledge argument for correctness of a shuffle的主要思想是通过交互式的证明过程，让Prover能够证明自己知道一组使原始消息数组m和随机置换后的消息数组m'相等的置换，并且不泄露任何关于置换本身的信息，同时让Verifier能够验证这个证明的正确性，以确保随机置换的正确性。