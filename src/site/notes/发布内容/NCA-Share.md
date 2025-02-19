---
{"dg-publish":true,"permalink":"/发布内容/NCA-Share/"}
---

# 使用R语言进行NCA分析
## 更新代码包
`update.packages()`
## 激活和加载NCA包
`library(NCA)`
## 加载数据
去掉数据中的变量名、序号等，只保留要分析的数据，格式为csv，数据均已校准。
![Attachments/NCA-Share-Attachment/Attachment-20250219135902333.jpg](/img/user/%E5%8F%91%E5%B8%83%E5%86%85%E5%AE%B9/Attachments/NCA-Share-Attachment/Attachment-20250219135902333.jpg)

### 路径代码为：
`data <- read.csv("X:\\XX\\XX\\XX\\DatasetName.csv",header = FALSE)`
## 分析效应量与显著性
效应量是指产生特定结果需要必要条件的最低水平，取值范围为0~1之间，数值越趋近于1表示效应量越大，小于0.1则说明效应量很小。

NCA包可以调用上限回归（ceiling regression，CR）技术分析连续变量和超过5级的离散变量，使用上限包络（ceiling envelopment，CE）技术分析二分变量和不到5级的离散变量。

具体根据数据特征选择不同的分析技术，也可以同时汇报CR和CE的计算结果，比较结果的稳健性。

基于Dul等（2020）给出的衡量标准，必要条件的效应量（_d_）需大于0.1且达到显著性水平（_P_<0.01）。

分析效应量与显著性的代码如下：
```R
model<-nca_analysis(data,X,Y,ceilings="cr_fdh", test.rep=10000)
nca_output(model, test=TRUE)
```
其中，X是指前因条件，Y指结果条件（X，Y为数字 指代变量序号）；

cr_fdh表示使用上限回归（ceiling regression，CR）技术分析；test.rep=10000表示重抽次数为10000次。

同理，将分析效应量与显著性的代码中的cr改为ce，分析方法换为CE

以此对所有的前因条件进行必要性分析，将上述指标汇总后制表，如表1所示。综合来看，没有一个前因条件同时满足效应量和显著性两个要求。所以得出结论：X1-X9都不是结果条件Y的必要条件。
![Attachments/NCA-Share-Attachment/Attachment-20250219135902573.jpg](/img/user/%E5%8F%91%E5%B8%83%E5%86%85%E5%AE%B9/Attachments/NCA-Share-Attachment/Attachment-20250219135902573.jpg)
### 结果关键参数解析
#### 1. c-accuracy（精确度，必要性适配度）
- 计算的是数据点相对于必要性边界的适配度。
- **c-accuracy = 100%** 表示所有数据点都位于或低于必要性边界（即没有超出上限的点）。
- 但需要结合 `Effect size` 进行分析，因为 c-accuracy 高并不意味着该变量确实是必要条件。
#### 2. Ceiling zone（上限区域）
- NCA 通过划定上限区域（Ceiling Zone）来确定必要性约束，表示变量在该区域内的约束能力。
- 该值越大，说明必要性关系越强，即该变量对因变量的影响越大。
- 如果该值为 0，说明数据没有进入必要性区域，变量可能不是必要条件。
#### 3. Scope（范围）
- 表示变量的取值范围，用于标准化数据，使其适用于必要性分析。
- 取值通常是 1，表示变量已经标准化到 [0,1] 之间。
#### 4. d（Effect size，效应量）
- 这个值衡量变量的 **必要性强度**，即它对结果变量的必要性影响有多大。
- **通常的解释**：
    - **d > 0.1**：有微弱的必要性影响
    - **d > 0.3**：中等必要性影响
    - **d > 0.5**：强必要性影响
    - **d = 0**：变量可能不是必要条件
#### 5. P-value（p 值）
- 用于检验必要性关系是否具有统计显著性。
- **一般标准**：
    - **p < 0.05**：显著（可以认为变量是必要条件）
    - **p > 0.05**：不显著（可能不是必要条件）
- p 值高（接近 1）通常意味着我们无法拒绝零假设，即该变量可能与因变量无关。

## 分析瓶颈水平
瓶颈水平指达到结果最大观测范围的某一水平，前因条件最大观测范围内需要满足的水平值。 瓶颈水平分析代码为：
```R
model <- nca_analysis (data,c(X1:XN),Y)
nca_output(model, summaries=FALSE, bottlenecks=TRUE)
```
其中，X1：XN表示前因条件的范围，是第几列到第N列，Y表示结果条件。
程序会汇报使用CR和CE两种方法进行分析的瓶颈水平结果，如下图所示。
![Attachments/NCA-Share-Attachment/Attachment-20250219135902738.jpg](/img/user/%E5%8F%91%E5%B8%83%E5%86%85%E5%AE%B9/Attachments/NCA-Share-Attachment/Attachment-20250219135902738.jpg)
![Attachments/NCA-Share-Attachment/Attachment-20250219135902812.jpg](/img/user/%E5%8F%91%E5%B8%83%E5%86%85%E5%AE%B9/Attachments/NCA-Share-Attachment/Attachment-20250219135902812.jpg)
最后，将上述结果制表，如表2所示。要达到60%的Y水平，需要0.4%水平的X1、2.3%水平的X3、0.2%水平的X5、1.4%水平的X7，而X2、X4、X6、X8、X9不存在瓶颈水平。
![Attachments/NCA-Share-Attachment/Attachment-20250219135902917.jpg](/img/user/%E5%8F%91%E5%B8%83%E5%86%85%E5%AE%B9/Attachments/NCA-Share-Attachment/Attachment-20250219135902917.jpg)