# fast k-means

## 技术选型

几种无监督学习算法：K-means、Meanshift、DBSCAN 聚类效果对比 见论文： ./Point cloud segmentation.pdf

## 算法解释：

fast k-measn 原理见论文：./A fast k-means algorithm based on multi-granularity.pdf

### 摘要（Abstract）
自从提出来以来，k-means算法已经被广泛使用，但标准k-means算法在处理大规模数据时效率不高。为了解决这个问题，本文提出了一种基于多粒度的快速k-means算法。首先，从粗粒度的角度出发，我们利用聚类分布信息来缩小样本点的搜索范围，从而使得在大k的情况下，所提出的算法具有很大的优势。其次，从细粒度的角度出发，我们利用上下界规则来减少参与距离计算的样本点数量，从而减少许多不必要的距离计算。最后，我们对几个真实数据集上提出的k-means算法进行了评估，实验结果表明，平均情况下，所提出的算法比标准k-means算法快数百倍，且精度损失控制在约3％左右，当数据集大小更大、数据集的维数更高时，算法的加速效果更为明显。
### 介绍（Introduction）
k-means算法由James MacQueen于1967年提出，主张基于样本点之间的距离进行聚类[1]。如今，k-means算法已成为十大经典机器学习算法之一，广泛应用于各个领域[2][3]。Lloyd算法被称为标准k-means算法[4]，其核心思想是根据样本点之间的距离将给定的样本集划分为k个簇，并让同一簇中的点尽量靠近，不同簇之间的距离尽量远，这可以用以下数据表达式描述。假设有n个样本点和k个簇，分别表示为(𝑥1,𝑥2,...,𝑥𝑛)和(𝐶1,𝐶2,...,𝐶𝑘)，我们的目标是最小化平方误差E，即， 𝐸=min⁡(∑𝑘 ∑ (||𝑥 −𝑐||2)(1) 𝑖=1 𝑥𝑖∈𝐶𝑖 𝑖 𝑖 k-means算法是一种基于距离的计算，分为两个阶段：分配阶段，在该阶段中，每个点被分配到最近的簇中；更新阶段，在分配阶段后重新计算每个簇的质心。这两个阶段一直重复，直到算法收敛。在标准的Lloyd k-means算法中，质心是随机选择的，这可能会导致迭代次数和聚类结果不同。Lloyd已被证明是NP困难问题，它的时间复杂度为O(nkt)，其中n是数据大小，k是簇的数目，t是迭代总数。在非常大规模的聚类中，k的值可能是数百或更大。Lloyd的k-means可能效率低下。因此，为Lloyd提出了许多加速算法。
工作：数学 （Math in Method）
### 结论
本文提出了一种基于多粒度思想的快速近似k-means算法。 我们提出的算法之所以如此高效，一方面是利用了簇分布信息来缩小搜索范围，另一方面是通过使用上界和下界来减少需要参与搜索的样本点。因此，我们的算法为聚类大规模和高维数据提供了高效的解决方案。

### Details
由于标准k-means算法需要计算所有样本点到每个簇中心的距离，因此在处理大数据时效率不高。因此，改进k-means算法的效率是一个非常重要的研究领域，最常用的方法是减少点与簇中心之间距离的计算。使用这种方法来提高k-means的效率会导致两个结果，一个是加速近似k-means[6-9]，损失精度以获得更高的效率。例如，Fahim AM等人提出一个准则，只要当前迭代达到了点x到中心的距离小于上一次迭代的x到中心的距离，就可以避免计算x到其余中心的距离[7]。Perez J等人提出了一种启发式方法，即如果点x到中心的距离小于两个最小质心偏移量之和，则可以避免计算x到其余中心的距离[8]。Daowan Pen等人提出通过邻居信息缩小每个样本点的搜索范围，从而减少不必要的距离计算[10]。另一个结果是加速精确k-means[11-14]。Elkan在2003年提出利用三角不等式来实现这一目标，并为每个点x设置上限和下限。只要上限小于下限或上限小于质心之间的最小距离，就无需计算x与其余质心之间的距离[11]。Elkan大大减少了距离计算的次数并提高了效率。但是，太多的下限会导致较大的时间复杂度和高昂的空间成本。作为最经典的精确k-means，Harmerly通过将下限数量减少到一个以改进了Elkan算法，从而显著提高了效率[12]。很多优秀的算法都是基于Harmerly算法开发的。Annulus算法[13]和Exponion算法[14]通过画出每个点参与距离计算的可能区域来进一步提高其偏移较大时的效率。Yinyang k-means[15]比较了Elkan和Hamerly算法的特点，并提出了三个过滤器：全局过滤器、组过滤器和本地过滤器，并结合了这三个过滤器来克服设置k下限[11]和对质心大偏移的敏感缺陷[12]。Ball k-means将所有簇都视为一个球体，样本在所有簇中的搜索范围是基于距离和球心半径之间的关系[5][16]。本文致力于基于多粒度思想提出一种更有效的k-means算法。首先，从粗粒度的角度出发，我们通过查找相邻的簇来缩小点的搜索范围；其次，我们通过为每个点设置下限和上限来减少需要参与距离计算的点的数量。
证明
我们提出了一种基于多粒度思想的高效k-means算法。本节将从粗粒度和细粒度两个角度描述算法的加速标准。为了区分粗粒度和细粒度加速标准，本文将每个簇视为粗粒度，将每个样本点视为细粒度。
3.1 粗粒度加速标准
为了从粗粒度的角度提高k-means算法的效率，可以利用簇之间的分布关系来缩小样本点的搜索范围。我们的算法借鉴了邻居簇[9]的概念，即在上一次迭代过程中与当前簇交换点的簇，如定义1所述。 定义1：给定簇C，第t次迭代中C的邻居簇定义为在第(t-1)次迭代中与C交换过点的簇的集合，即 𝑁𝐶𝑡 ={𝐶|∃𝑥∈𝐶𝑡−1&𝑥∈𝐶𝑡}∪{𝐶|∃𝑥∈𝐶𝑡−1&𝑥∈𝐶𝑡} (2) 𝑐𝑖 𝑖𝑗𝑗 其中𝑁𝐶𝑐𝑡表示第t次迭代中簇C的邻居簇的集合。 对于簇C，如果存在一个点x，在上一次迭代中它属于簇C，在第t次迭代中它属于𝐶𝑖（或者样本点x在上一次迭代中属于𝐶𝑖，在第t次迭代中它属于C），则𝐶𝑖是C的邻居簇。 基于邻居簇的定义，该算法进一步提出，在第t次迭代中，簇C中的点只能分配到其邻居簇中。也就是说，可以直接避免计算簇C中的点与簇C的非邻居簇之间的距离，从而大大缩小了簇C中点的搜索范围。
3.2 细粒度加速标准
新算法对于每个样本点都维护了一个上界和下界来进一步加速。初始化样本点的上界为样本点到最近簇中心的距离，下界为到次近簇中心的距离。定义2和定理1给出了每个样本点的上下界以及它们的更新方法。 定义2：给定簇C及其中心𝑐，在第t次迭代过程中，对于𝐶中的任意𝑥，它的上界总是大于等于𝑥到𝑐的距离，下界总是小于等于𝑥到除了𝑐之外所有其他簇的距离，即 𝑢(𝑥)𝑡 ≥ ||𝑥 − 𝑐𝑡|| 𝑙(𝑥)𝑡 ≤ 𝑚𝑖𝑛||𝑥 − 𝑐𝑖𝑡|| 其中，𝑢(𝑥)和𝑙(𝑥)分别表示𝑥的上界和下界，𝑐𝑖是离𝑥第二近的簇的中心。 根据上下界的定义，可以得出一个结论，只要样本点的下界大于它的上界，那么该样本点最近的簇就是它当前所在的簇，即该样本点可以避免在当前迭代中参与距离计算。 为了确保定义2的有效性，定理1可以用来更新每个样本点的上下界。 定理1：给定簇C及其中心𝑐，在第t次迭代过程中，对于𝐶中的任意𝑥，它的上界可以表示为上一个迭代中的上界加上中心偏移量，下界可以表示为上一个迭代中的下界减去所有簇中心偏移量的最大值，即 𝑢(𝑥)𝑡 = 𝑢(𝑥)𝑡−1 + 𝑝(𝑐) (5) 𝑙(𝑥)𝑡 = 𝑙(𝑥)𝑡−1 − max⁡(𝑝(𝑐 )) (6) 其中，𝑝(𝑐)和max⁡(𝑝(𝑐 ))分别表示簇C中心的偏移量和所有簇的最大中心偏移量。 定理1的证明：根据定义1，对于给定的簇C中的样本点𝑥，在第t-1次迭代中，我们有𝑢(𝑥)𝑡−1 ≥ ||𝑥 − 𝑐𝑡−1||， 因此在第t次迭代中，我们可以得出结论，||𝑥 − 𝑐𝑡|| ≤ ||𝑥 − 𝑐𝑡−1||+𝑝(𝑐) ≤ 𝑢(𝑥)𝑡−1+𝑝(𝑐); 同样地，我们有𝑙(𝑥)𝑡−1 ≤ 𝑚𝑖𝑛||𝑥 − 𝑐𝑡−1||， 因此在第t次迭代中，我们可以得出结论，𝑚𝑖𝑛 ||𝑥 − 𝑐𝑡|| ≤ 𝑚𝑖𝑛 ||𝑥 − 𝑐𝑡−1||−𝑝(𝑐) ≤ 𝑙(𝑥)𝑡−1−𝑝(𝑐) ≤ max⁡(𝑝(𝑐 )). 综上，由定理1更新的样本点的上下界始终满足定义2。
3.3 新算法的整体描述
算法的流程如图1所示，可以分为以下主要步骤。 步骤1：使用标准k-means算法进行两次聚类，将样本点的上界初始化为样本点到最近簇中心的距离，下界初始化为到次近簇中心的距离。 步骤2：计算所有簇中心的偏移量，并通过定理1更新样本点的上下界。 步骤3：计算不符合上界小于下界的簇中点之间的距离，将样本点分配给最近的簇，并将样本点的上界改为到离其最近的簇中心的距离，将下界改为到次近的簇中心的距离，根据定义1找出当前簇的邻居簇。 步骤4：更新所有簇中心。 步骤5：判断新的簇中心是否与旧的完全相等。如果完全相等，则输出聚类结果；否则跳转到步骤2。
为验证算法的效率，本文使用多个k-means算法进行比较，包括两个精确的k-means算法（分别是Lloyd k-means和ball k-means），以及一个由daowan Pen等人在2021年提出的近似k-means算法（以下简称app_k-means）。在本文中，这四个算法被应用到不同的数据集和不同的k值上，并进行了效率比较。表1中简要描述了这些数据集。
对于每个数据集，对每个算法的性能进行评估，并计算每个算法在每个 k 值下的运行时间，直到算法收敛。具体结果见表2，可以看出，所提出的方法在所有数据集上表现最佳，我们提出的算法比大多数数据集上的标准 k-means 算法快数百甚至数千倍，并且比另一种近似 k-means 算法平均快两倍。根据实验结果可以看出，所提出的算法在 ijcnn 数据集上的加速效果比 fourclass 数据集上更为显著，并且该算法在高维数据 isolet 的所有 k 值上都加速了数千倍，表明该算法可以解决标准 k-means 算法在大规模数据集和高维数据上的低效问题，并且这种加速效果在较大的 k 值上更为明显。表3比较了所提出算法和 app k-means 的平方误差和（SSE）。由于 Ball k-means 收敛后的 SSE 与 Lloyd k-means 算法的聚类结果相同，因此未在表3中显示该算法。综合表2和表3可以发现，所提出算法的平均加速比是数百甚至数千倍，而 SSE 的平均增长率在所有数据集上约为3%。

## 使用示例
``` import { KMEANS } from '@/utils/math/k-means';
    import { FastKMEANS } from '@/utils/math/fast-kmeans';
           const dataset = [
        [1, 1], [0, 1], [1, 0],
        [10, 10], [10, 13], [13, 13],
        [54, 54], [55, 55], [89, 89], [57, 55]
      ];
      const k=3;
      
      const kmeans = new KMEANS();
      const clusters = kmeans.run(dataset,k);
      
      const fastKmeans = new FastKMEANS(dataset, k);
      const fastClusters = fastKmeans.run();

      console.log(clusters);
      console.log(fastClusters);
      /*
      RESULT:
      [
        [0,1,2,3,4,5],
        [6,7,9],
        [8]
      ]
      */
``` 

### 使用效果
目标：对点云实现初步语义分割。让车辆、行人与地面区分开，方便标注员标注。
  用fast k-means 聚类点的四维数据（x，y，z，材质值归一化）为点云上色。点云中：点的数量：79.6w个。去除地面点后参与聚类的点的数量：17.4w。k值选取130。普通k-means聚类耗时34.5s，快速k-means聚类耗时0.54s。速度提升64倍。且聚类效果十分接近。
![image](https://github.com/xiyanma/fast-kmeans/assets/37499101/a47d42fa-25bb-4ed0-8aa0-278d0683a218)
![image](https://github.com/xiyanma/fast-kmeans/assets/37499101/08b29059-bbdd-4877-8126-5a938b99a366)


### 参考文献
https://www.spiedigitallibrary.org/conference-proceedings-of-spie/12174/1217410/A-fast-k-means-algorithm-based-on-multi-granularity/10.1117/12.2628453.short
