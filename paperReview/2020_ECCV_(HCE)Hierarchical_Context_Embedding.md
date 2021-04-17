### 2020_ECCV_(HCE)Hierarchical_Context_Embedding

1. **动机**

   - ROI的feature map虽有有很大的感受野, 但是有效的感受域占比小, 不足以提供足够的特征用于分类.
   - 一些基于region-level context information的方法旨在提高检测器的localization能力, 但是没有可用的方法是用embedded context information来提高检测网络的分类能力的.

2. **Hierarchical Context Embedding framework**

   - 网络结构图:

     ![image-20200929140416578](/home/cpc/.config/Typora/typora-user-images/image-20200929140416578.png)

   - Image-level categorical embedding:

     ![image-20200929140542516](/home/cpc/.config/Typora/typora-user-images/image-20200929140542516.png)

     GAP: Global Average-pooling

     GMP: Global Max-pooling

     任务loss:

     ![image-20200929140804451](/home/cpc/.config/Typora/typora-user-images/image-20200929140804451.png)

     ![image-20200929140908313](/home/cpc/.config/Typora/typora-user-images/image-20200929140908313.png)

   - Hierarchical Contextual ROI Feature Generation:

     <img src="/home/cpc/.config/Typora/typora-user-images/image-20200929141501185.png" alt="image-20200929141501185" style="zoom:50%;" />

     Context-Embedded Instance-Level Feature: ${\bf x}_{instance}$

     Context-Aggregated Global-Level Feature: ${\bf x}_{global}$

     ![image-20200929141544050](/home/cpc/.config/Typora/typora-user-images/image-20200929141544050.png)

   - Early-and-Late Fusion method

     Early Fusion: features fusion

     Late Fusion: outputs fusion

     这里作者使用了该策略：

     - 首先是Feature Fusion: 将${\bf x}_{context}$与RPN中提取的ROI融合:

       ![image-20200929145947115](/home/cpc/.config/Typora/typora-user-images/image-20200929145947115.png)

     - 其次是Confidence Fusion: 将分类结果进行融合:

       ![image-20200929150046609](/home/cpc/.config/Typora/typora-user-images/image-20200929150046609.png)

   - Inference Process

     - 将不同分支的cls和loc结果合并, 应用NMS得到最后的结果
  - 实验证明, inference时使用单个分之, 而训练是使用单个分之, 不会使结果有显著的下降, 且会减少计算量.
   
- Loss Function
  
  ![image-20200929151610354](/home/cpc/.config/Typora/typora-user-images/image-20200929151610354.png)

