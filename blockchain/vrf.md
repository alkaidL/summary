## VRF

可验证随机函数 Verifiable Random Function

### 区块链中VRF的应用

#### 是什么

可验证随机函数可以看作是一个**随机预言机**，意思是说我可以通过任意的一个输入，获得一个随机数输出： 

1. 对于不同的 Input，Output 的值是随机的，并且均匀分布在值域范围内。
2. 对于相同的 Input，它得到的 Output 一定是相同的。

但是可验证随机函数比随机预言机多了一个非交互的零知识证明，可以用来验证该随机数输出的正确性，表明这个随机数的确是某个人生成的。

四个函数：

1. 生成密钥，生成一个公钥私钥对
2. 生成随机数输出
3. 计算零知识证明
4. 验证随机数输出

![åºåé¾ä¸­VRFçåºç¨ååçè§£æ](https://pic.36krcnd.com/201808/01023749/wp48edtg75y0yvj1!heading)

#### 能做到什么，落地在哪里

VRF可用于区块链中抽签来降低参与共识节点或者某种角色的数量：不被预测的随机找到一个节点进行区块打包，并让这个区块能被全网承认

现在大部分的VRF抽签方案都将基于权益来进行票数分配，然后进行抽签算法的设计，因为可以防止女巫攻击

应用：

- VBFT。VBFT是融合了PoS、可验证随机函数（VRF）和BFT的一种新的共识算法。在VBFT算法中，Ontology节点首先通过抵押Stake申请参与网络共识，然后通过可验证随机数（verifiable random number），**随机从所有共识节点中选择若干节点，提出备选区块，验证备选区块，对验证结果进行背书投票后完成区块的共识**。简而言之，VBFT算法通过VRF选择节点子集实现了共识算法的扩展，同时通过随机性和PoS保障了算法的抗攻击能力，通过类BFT算法实现了快速的状态终局性。
- Ouroboros Praos：**增加了打包节点的匿名性，实际上是对系统有了一个安全性上的升级**。当可以提前知道某个区块的打包节点，那么攻击者就可以提前攻击这个打包节点，来达到它攻击的目的，而引入VRF之后，每一轮的slot的slot leader只有这个节点自己知道，他发布之后，其他节点可以验证他是否是这个角色，这样就可以避免上面所说的攻击。
- Algorand：先选打包者，选完打包者选委员会，委员会用BA*进行选区块
- Dfinity：交保证金提高门槛，并降低参与节点的数量，然后选打包者，选完打包者选公证人，对区块权重进行排序，选出区块。
- VBFT：本体（Ontology）的共识算法VBFT中，首先选打包者，打包之后，选投票者对这些包进行投票，之后选确认者，对这些票数进行统计，选出区块。

#### 优点

1. 首先它的抽签过程不需要与其他通信，直接在本机就能够得到这个抽签结果，而且输入是大家公认的，针对同一个输入的输出是固定的，因此无法通过多次尝试来改变抽签结果
2. 某个节点收到其他节点的抽签信息之后，可以用附带的证明，来证明这个随机数的正确性，保证它的确是由私钥的拥有者计算出来的。因此这个抽签结果是无法被伪造的。
3. VRF主要用来得出一个伪随机数，抽签的部分主要是由一个二项分布函数负责，而通过构建二项分布的参数，我们可以很方便的控制需要被得出的中签权益的个数，适配不同的需要抽签的场景。

![åºåé¾ä¸­VRFçåºç¨ååçè§£æ](https://pic.36krcnd.com/201808/01062556/772vx52xodlm3caq!heading)

#### 边界（局限）在哪里

在Ouroboros中：

1. Follow the Satoshi能够随机的给每个slot都分配上slot leader（但就算全部分配上，slot leader也可能掉线造成没有slot leader），但是VRF这种基于概率的抽签方式，在这点上就没有办法做到一定。可能出现某一轮slot没有抽出slot leader或者抽出了多个slot leader的情况。因此Ouroboros Praos中，新增了额外的方案来处理这种情况。
2. 就算能保证每个slot都有slot leader，也没办法保证slot leader是否能在这个slot里打出包来。而验证分叉的方案，又是链一定会考虑的问题，假设某个slot leader被攻击，而在自己的slot里大量重复打包，也会造成分叉问题。

#### 改进点

VRF除了抽签，还能做到什么？

VRF官网三大应用：Key Transparency，DNSSEC，Consensus

其中Key Transparency好像引入到智能合约中了，Consensus应用的最多

挖掘VRF的工业应用是否能应用到区块链中

### 文献draft-irtf-cfrg-vrf

### 概念摘要

可验证随机函数（VRF）是密钥加密散列的公钥版本。只有私有VRF密钥(private VRF key)的持有者才能计算散列，但是具有相应公钥的任何人都可以验证散列的正确性。VRF的关键应用是为存储在基于散列的数据结构中的数据提供针对离线枚举（例如字典攻击）的隐私性。在此应用中，Prover保有VRF私钥并使用VRF哈希在输入数据上构建基于哈希的数据结构。由于VRF的性质，只有Prover才能响应有关数据是否存储在该数据结构中的查询。 知道公共VRF密钥(public VRF key)的任何人都可以验证Prover是否正确响应了查询。 然而，不能对存储在数据结构中的数据做出离线推断（即，在不查询证明者的情况下推断）。

### 术语

  SK：VRF的私钥。
  PK：VRF的公钥。
  alpha：将由VRF进行哈希处理的输入。
  beta：VRF哈希输出。
  pi：VRF证明（proof）。
  Prover：Prover拥有私人VRF密钥SK和公共VRF密钥PK。
  Verifier：Verifier拥有公钥PK。

### VRF Algorithms

VRF附带密钥生成算法，该算法生成公共VRF密钥PK和私有VRF密钥SK。

prover通过SK对输入进行哈希处理后得到beta
`beta = VRF_hash(SK, alpha)`

VRF_hash算法是确定性的，因为它总是在给定一对输入（SK，alpha）的情况下产生相同的输出β。 证明者还使用私钥SK来构造证明pi，证明beta是正确的哈希输出
`pi = VRF_prove(SK, alpha)`

本文档中定义的VRF允许任何人直接从证明值pi确定性地获得VRF哈希输出beta`beta = VRF_proof2hash(pi)`

也就有：`VRF_hash(SK, alpha) = VRF_proof2hash(VRF_prove(SK, alpha))`

因此，本文档将指定`VRF_prove`和`VRF_proof2hash`而不是`VRF_hash`。
pi允许Verifier持有公钥PK以验证beta是否是基于PK下的alpha的正确输出beta。 因此，VRF还附带了算法
`VRF_verify(PK, alpha, pi)`，这个算法在`beta = VRF_proof2hash（pi）`等于PK下的alpha的正确输出beta时输出VALID，否则输出INVALID

### VRF Security Properties

#### Full Uniqueness or Trusted Uniqueness

即广义唯一性或可信唯一性

唯一性意味着，对于任何固定的PK和任何输入alpha，都有一个独特的VRF输出beta，并且beta是可以被验证的。 即使对于知道VRF私钥SK的**adversarial Prover**（我理解为知道SK的攻击者），也必须保持唯一性。

更确切地说，“完全唯一性”表明a computationally-bounded adversary （算力有限的攻击者）无法**选择**（我理解为伪造）VRF公钥PK、VRF输入alpha、两个不同的VRF哈希输出beta1和beta2，以及两个证明pi1和pi2，以便`VRF_verify（PK，alpha，pi1）` 和`VRF_verify（PK，alpha，pi2）`都输出VALID。

对于许多应用而言，“可信唯一性”这种安全属性就足够了。 可信唯一性与完全唯一性相同，但只有在VRF密钥PK和SK以可信赖的方式生成时才能保证可信唯一性。 换句话说，如果密钥以无效方式生成或**随机性差**（可以直接被穷举？），则唯一性可能不成立。

#### Full Collison Resistance or Trusted Collision Resistance

即广义抗碰撞或可信抗碰撞

##### 碰撞性

###### 目标抗碰撞性

给定一个消息m0，很难找到另一个消息m1使得Hash(m0) = Hash(m1)

###### 广义抗碰撞性

很难找到两个消息m0 != m1，使得Hash(m0) = Hash(m1)

###### 安全性

一般认为如果广义抗碰撞性不满足，那么此哈希函数就不再安全。在实际中，一般认为如果**在某种程度上**目标抗碰撞性不满足，那么此哈希函数就不再安全

##### 抗碰撞

与任何加密哈希函数一样，VRF需要具有抗碰撞性。 即使对于知道VRF私钥SK的**adversarial Prover**，也必须保持抗碰撞。

更准确地说，“广义抗碰撞”指出，攻击者找到两个具有相同输出beta的不同输入alpha1和alpha2在计算上是不可行的，即使该攻击者知道SK。

对于绝大多数应用，“可信抗碰撞”这种安全属性就足够了。  Trusted collision resistance is the same as collision resistance, but it holds only if PK and SK were generated in a trustworthy manner（只有在PK和SK以可信赖的方式生成时才能保证抗碰撞是可信的）。

#### Full Pseudorandomness or Selective Pseudorandomness

伪随机性确保当**对抗性Verifier**（攻击者）在没有相应的VRF证明pi的情况下看到VRF哈希输出beta时，则beta看起来就是随机值。

更准确地说，假设PK，SK是以可信赖的方式生成的，伪随机性确保任何攻击者选择的“目标”alpha上的散列输出beta（没有其相应的VRF证明pi）对于未持有SK的任何算力有限的攻击者而言看起来就是一串随机数，即使攻击者还选择了其他alpha'并观察其相应的哈希输出beta'以及pi'也无法改变这一性质。

通过“Full Pseudorandomness”，攻击者可以随时选择“目标”VRF输入alpha，即使在许多选择的alpha'上观察到beta'和pi'之后也是如此。

“Selective Pseudorandomness”是一种更弱的安全属性，不过在许多应用中都足够了。基于此种安全属性，攻击者必须脱离出PK才能选择alpha，并且在它选择的alpha'上观察beta'和pi'之前。

重要的是要记住，beta对于Prover或任何知道**SK**的其他人来说都不是随机的。这些人通过将beta与`VRF_hash（SK，alpha）`的结果进行比较，可以容易地将beta与随机值区分开。

此外，beta对于知道对应于alpha的**pi**的任何一方而言看起来都不是随机的，即使该方不知道SK。通过检查`VRF_verify（PK，alpha，pi）`是否返回（VALID，beta），这样的人也可以容易地将beta与随机值区分开。

此外，如果VRF密钥生成没有以值得信赖的方式进行，则VRF输出beta可能看起来不随机。 （例如生成的VRF密钥具有较差的随机性）

#### A random-oracle-like unpredictability property

主要说的是不可预测性及其条件

如果VRF密钥是异常生成的，则伪随机性不成立。例如，如果对手输出确定性生成（或硬编码且公知）的VRF密钥，则输出很容易由任何人导出。然而，在某些VRF应用中需要不同类型的不可预测性（例如[GHMVZ17]和[KRDO17]）。该属性类似于（普通的，未加密的）加密散列函数所实现的不可预测性：如果输入具有足够的熵（即，不能被预测），则正确的输出几乎是均匀的。虽然加密文献中既没有出现正式定义也没有出现这种属性的证据，但如果公钥是以可信赖的方式生成的，则本说明书中提出的VRF方案被认为满足该属性。此外，只要公钥满足第5.6节中的密钥验证程序，即使公钥不以可靠的方式生成，ECVRF也会满足此属性。

### RSA Full Domain Hash VRF (RSA-FDH-VRF)

满足的特性：

- Trusted Uniqueness
- Trusted Collision Resistance
- Full Pseudorandomness

安全性符合standard RSA assumption in the random oracle model

使用所选散列算法参数化的RSA全域散列算法[RFC8017]，将计算出的pi作为alpha上的确定性RSA签名。 RSA签名验证用于验证pi的正确性。 通过用所选择的散列算法对证明pi进行散列来简单地获得输出beta。
