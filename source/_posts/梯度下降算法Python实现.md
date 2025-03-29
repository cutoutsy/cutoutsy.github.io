---
layout: post
title: 梯度下降算法Python实现
categories: 算法
date: 2020-04-06 15:45:58
tags:
    - 梯度下降
    - 算法
    - Python
---

### 写在前面
本文翻译 [Implement Gradient Descent in Python](https://towardsdatascience.com/implement-gradient-descent-in-python-9b93ed7108d1)

### 什么是梯度下降算法
梯度下降算法是一种寻找函数最小值得优化算法。算法从函数的一个随机点开始，每次移动都是函数梯度的负方向，从而到达函数的全局/局部最小点。

![梯度下降](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/gradient_descent_1.gif)

<!-- more -->

### 例子
**问题**：给定函数$y=(x+5)^2$，从x=3点出发，找到函数的局部最小点。
![二次函数](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/gradient_descent_2.png)

**解决方案**：从函数图形上看，我们可以知道答案。当x = -5 时，函数$y=(x+5)^2$到达最小值(x=-5, y=0)。所以x=-5是函数的局部和全局最小值。

现在，我们来看如何使用梯度下降算法来获得同样的答案。

**第一步**：初始化x=3。找到函数的梯度，$\frac{dy}{dx} = 2*(x+5)$.

**第二步**：向梯度的负方向移动。但是每次移动多少呢。这里，我们需要一个学习率。这里我们假设`学习率->0.01`

**第三步**：我们来演示梯度下降的两次迭代
![两步迭代](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/gradient_descent_3.png)

**第四步**：我们可以看到x的值在下降，应该会收敛到局部最小-5。然而，我们需要多少次迭代呢。

*我们在算法里设置一个精度变量用来计算两个连续的"x"值。如果两个连续x的值得差值小于我们设置的精度，就停止算法。*

### Python实现梯度下降
**步骤1**：初始化参数
```Python
cur_x = 3   # 算法开始在 x=3
rate = 0.01 #学习率
precision = 0.000001 #  什么时候停止算法
previous_step_size = 1
max_iters = 10000   # 最大迭代次数
iters = 0   # 迭代计数器
df = lambda x : 2*(x+5)     # 函数梯度
```
**步骤2**：在循环中进行梯度下降
停止循环当两个连续迭代的x值的差小于*0.000001*或者迭代次数超过10,000
```Python
while previous_step_size > precision and iters < max_iters:
    prev_x = cur_x
    cur_x = cur_x - rate * df(prev_x)   # 梯度
    previous_step_size = abs(cur_x - prev_x)
    iters = iters + 1
    print("Iteration",iters,"\nX value is",cur_x)

print("The local minimum occurs at", cur_x)
```
**输出**：从下面的输出，我们可以看到最开始10次迭代的x的值，我们可以通过这个交叉检查我们上面的计算。算法运行了595次迭代直到停止。

![前10次迭代](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/gradient_descent_4.png)


完整的运行过程：

```python
cur_x = 3   # 算法开始在 x=3
rate = 0.01 #学习率
precision = 0.000001 #  什么时候停止算法
previous_step_size = 1
max_iters = 10000   # 最大迭代次数
iters = 0   # 迭代计数器
df = lambda x : 2*(x+5)     # 函数梯度
```


```python
while previous_step_size > precision and iters < max_iters:
    prev_x = cur_x
    cur_x = cur_x - rate * df(prev_x)   # 梯度
    previous_step_size = abs(cur_x - prev_x)
    iters = iters + 1
    print("Iteration",iters,"\nX value is",cur_x)

print("The local minimum occurs at", cur_x)
```
```python
    Iteration 1 
    X value is 2.84
    Iteration 2 
    X value is 2.6832
    Iteration 3 
    X value is 2.529536
    Iteration 4 
    X value is 2.37894528
    Iteration 5 
    X value is 2.2313663744
    Iteration 6 
    X value is 2.0867390469119997
    Iteration 7 
    X value is 1.9450042659737599
    Iteration 8 
    X value is 1.8061041806542846
    Iteration 9 
    X value is 1.669982097041199
    Iteration 10 
    X value is 1.5365824551003748
    Iteration 11 
    X value is 1.4058508059983674
    Iteration 12 
    X value is 1.2777337898784
    Iteration 13 
    X value is 1.152179114080832
    Iteration 14 
    X value is 1.0291355317992152
    Iteration 15 
    X value is 0.9085528211632309
    Iteration 16 
    X value is 0.7903817647399662
    Iteration 17 
    X value is 0.6745741294451669
    Iteration 18 
    X value is 0.5610826468562635
    Iteration 19 
    X value is 0.44986099391913825
    Iteration 20 
    X value is 0.3408637740407555
    Iteration 21 
    X value is 0.23404649855994042
    Iteration 22 
    X value is 0.1293655685887416
    Iteration 23 
    X value is 0.026778257216966764
    Iteration 24 
    X value is -0.07375730792737258
    Iteration 25 
    X value is -0.1722821617688251
    Iteration 26 
    X value is -0.2688365185334486
    Iteration 27 
    X value is -0.36345978816277963
    Iteration 28 
    X value is -0.45619059239952403
    Iteration 29 
    X value is -0.5470667805515336
    Iteration 30 
    X value is -0.6361254449405029
    Iteration 31 
    X value is -0.7234029360416929
    Iteration 32 
    X value is -0.8089348773208591
    Iteration 33 
    X value is -0.8927561797744419
    Iteration 34 
    X value is -0.9749010561789531
    Iteration 35 
    X value is -1.055403035055374
    Iteration 36 
    X value is -1.1342949743542665
    Iteration 37 
    X value is -1.2116090748671813
    Iteration 38 
    X value is -1.2873768933698377
    Iteration 39 
    X value is -1.361629355502441
    Iteration 40 
    X value is -1.4343967683923922
    Iteration 41 
    X value is -1.5057088330245443
    Iteration 42 
    X value is -1.5755946563640535
    Iteration 43 
    X value is -1.6440827632367725
    Iteration 44 
    X value is -1.711201107972037
    Iteration 45 
    X value is -1.7769770858125964
    Iteration 46 
    X value is -1.8414375440963444
    Iteration 47 
    X value is -1.9046087932144176
    Iteration 48 
    X value is -1.9665166173501292
    Iteration 49 
    X value is -2.0271862850031264
    Iteration 50 
    X value is -2.0866425593030637
    Iteration 51 
    X value is -2.1449097081170025
    Iteration 52 
    X value is -2.2020115139546625
    Iteration 53 
    X value is -2.257971283675569
    Iteration 54 
    X value is -2.312811858002058
    Iteration 55 
    X value is -2.3665556208420164
    Iteration 56 
    X value is -2.419224508425176
    Iteration 57 
    X value is -2.4708400182566725
    Iteration 58 
    X value is -2.521423217891539
    Iteration 59 
    X value is -2.570994753533708
    Iteration 60 
    X value is -2.619574858463034
    Iteration 61 
    X value is -2.667183361293773
    Iteration 62 
    X value is -2.713839694067898
    Iteration 63 
    X value is -2.75956290018654
    Iteration 64 
    X value is -2.804371642182809
    Iteration 65 
    X value is -2.8482842093391527
    Iteration 66 
    X value is -2.8913185251523696
    Iteration 67 
    X value is -2.9334921546493224
    Iteration 68 
    X value is -2.974822311556336
    Iteration 69 
    X value is -3.015325865325209
    Iteration 70 
    X value is -3.055019348018705
    Iteration 71 
    X value is -3.093918961058331
    Iteration 72 
    X value is -3.1320405818371646
    Iteration 73 
    X value is -3.1693997702004215
    Iteration 74 
    X value is -3.206011774796413
    Iteration 75 
    X value is -3.2418915393004846
    Iteration 76 
    X value is -3.277053708514475
    Iteration 77 
    X value is -3.3115126343441856
    Iteration 78 
    X value is -3.345282381657302
    Iteration 79 
    X value is -3.378376734024156
    Iteration 80 
    X value is -3.4108091993436727
    Iteration 81 
    X value is -3.4425930153567994
    Iteration 82 
    X value is -3.4737411550496633
    Iteration 83 
    X value is -3.50426633194867
    Iteration 84 
    X value is -3.534181005309697
    Iteration 85 
    X value is -3.563497385203503
    Iteration 86 
    X value is -3.5922274374994325
    Iteration 87 
    X value is -3.620382888749444
    Iteration 88 
    X value is -3.6479752309744553
    Iteration 89 
    X value is -3.675015726354966
    Iteration 90 
    X value is -3.7015154118278666
    Iteration 91 
    X value is -3.7274851035913095
    Iteration 92 
    X value is -3.7529354015194833
    Iteration 93 
    X value is -3.7778766934890937
    Iteration 94 
    X value is -3.8023191596193118
    Iteration 95 
    X value is -3.8262727764269258
    Iteration 96 
    X value is -3.8497473208983872
    Iteration 97 
    X value is -3.8727523744804193
    Iteration 98 
    X value is -3.895297326990811
    Iteration 99 
    X value is -3.917391380450995
    Iteration 100 
    X value is -3.939043552841975
    Iteration 101 
    X value is -3.9602626817851356
    Iteration 102 
    X value is -3.981057428149433
    Iteration 103 
    X value is -4.001436279586445
    Iteration 104 
    X value is -4.021407553994716
    Iteration 105 
    X value is -4.040979402914822
    Iteration 106 
    X value is -4.060159814856525
    Iteration 107 
    X value is -4.078956618559395
    Iteration 108 
    X value is -4.097377486188207
    Iteration 109 
    X value is -4.115429936464443
    Iteration 110 
    X value is -4.133121337735154
    Iteration 111 
    X value is -4.150458910980451
    Iteration 112 
    X value is -4.167449732760842
    Iteration 113 
    X value is -4.1841007381056246
    Iteration 114 
    X value is -4.200418723343512
    Iteration 115 
    X value is -4.216410348876642
    Iteration 116 
    X value is -4.2320821418991095
    Iteration 117 
    X value is -4.247440499061128
    Iteration 118 
    X value is -4.262491689079905
    Iteration 119 
    X value is -4.277241855298307
    Iteration 120 
    X value is -4.291697018192341
    Iteration 121 
    X value is -4.305863077828494
    Iteration 122 
    X value is -4.319745816271924
    Iteration 123 
    X value is -4.333350899946486
    Iteration 124 
    X value is -4.3466838819475555
    Iteration 125 
    X value is -4.359750204308605
    Iteration 126 
    X value is -4.372555200222433
    Iteration 127 
    X value is -4.385104096217984
    Iteration 128 
    X value is -4.3974020142936245
    Iteration 129 
    X value is -4.409453974007752
    Iteration 130 
    X value is -4.421264894527597
    Iteration 131 
    X value is -4.432839596637045
    Iteration 132 
    X value is -4.444182804704305
    Iteration 133 
    X value is -4.4552991486102185
    Iteration 134 
    X value is -4.466193165638014
    Iteration 135 
    X value is -4.4768693023252535
    Iteration 136 
    X value is -4.487331916278748
    Iteration 137 
    X value is -4.497585277953173
    Iteration 138 
    X value is -4.50763357239411
    Iteration 139 
    X value is -4.517480900946228
    Iteration 140 
    X value is -4.527131282927304
    Iteration 141 
    X value is -4.536588657268758
    Iteration 142 
    X value is -4.545856884123382
    Iteration 143 
    X value is -4.5549397464409145
    Iteration 144 
    X value is -4.563840951512097
    Iteration 145 
    X value is -4.572564132481855
    Iteration 146 
    X value is -4.581112849832218
    Iteration 147 
    X value is -4.589490592835574
    Iteration 148 
    X value is -4.597700780978863
    Iteration 149 
    X value is -4.605746765359285
    Iteration 150 
    X value is -4.6136318300521
    Iteration 151 
    X value is -4.621359193451058
    Iteration 152 
    X value is -4.628932009582036
    Iteration 153 
    X value is -4.636353369390395
    Iteration 154 
    X value is -4.643626302002588
    Iteration 155 
    X value is -4.650753775962536
    Iteration 156 
    X value is -4.657738700443285
    Iteration 157 
    X value is -4.664583926434419
    Iteration 158 
    X value is -4.671292247905731
    Iteration 159 
    X value is -4.6778664029476165
    Iteration 160 
    X value is -4.684309074888664
    Iteration 161 
    X value is -4.6906228933908904
    Iteration 162 
    X value is -4.696810435523073
    Iteration 163 
    X value is -4.702874226812612
    Iteration 164 
    X value is -4.708816742276359
    Iteration 165 
    X value is -4.714640407430832
    Iteration 166 
    X value is -4.720347599282215
    Iteration 167 
    X value is -4.725940647296571
    Iteration 168 
    X value is -4.731421834350639
    Iteration 169 
    X value is -4.736793397663627
    Iteration 170 
    X value is -4.742057529710355
    Iteration 171 
    X value is -4.747216379116147
    Iteration 172 
    X value is -4.752272051533824
    Iteration 173 
    X value is -4.757226610503148
    Iteration 174 
    X value is -4.762082078293084
    Iteration 175 
    X value is -4.766840436727223
    Iteration 176 
    X value is -4.771503627992678
    Iteration 177 
    X value is -4.776073555432824
    Iteration 178 
    X value is -4.780552084324168
    Iteration 179 
    X value is -4.784941042637685
    Iteration 180 
    X value is -4.7892422217849315
    Iteration 181 
    X value is -4.793457377349233
    Iteration 182 
    X value is -4.7975882298022485
    Iteration 183 
    X value is -4.801636465206204
    Iteration 184 
    X value is -4.805603735902079
    Iteration 185 
    X value is -4.809491661184038
    Iteration 186 
    X value is -4.813301827960357
    Iteration 187 
    X value is -4.81703579140115
    Iteration 188 
    X value is -4.820695075573127
    Iteration 189 
    X value is -4.824281174061665
    Iteration 190 
    X value is -4.827795550580431
    Iteration 191 
    X value is -4.831239639568823
    Iteration 192 
    X value is -4.834614846777447
    Iteration 193 
    X value is -4.837922549841898
    Iteration 194 
    X value is -4.84116409884506
    Iteration 195 
    X value is -4.844340816868159
    Iteration 196 
    X value is -4.847454000530796
    Iteration 197 
    X value is -4.85050492052018
    Iteration 198 
    X value is -4.853494822109776
    Iteration 199 
    X value is -4.85642492566758
    Iteration 200 
    X value is -4.859296427154229
    Iteration 201 
    X value is -4.862110498611145
    Iteration 202 
    X value is -4.864868288638922
    Iteration 203 
    X value is -4.867570922866143
    Iteration 204 
    X value is -4.87021950440882
    Iteration 205 
    X value is -4.872815114320644
    Iteration 206 
    X value is -4.875358812034231
    Iteration 207 
    X value is -4.877851635793546
    Iteration 208 
    X value is -4.880294603077676
    Iteration 209 
    X value is -4.882688711016122
    Iteration 210 
    X value is -4.8850349367958
    Iteration 211 
    X value is -4.887334238059884
    Iteration 212 
    X value is -4.8895875532986866
    Iteration 213 
    X value is -4.891795802232712
    Iteration 214 
    X value is -4.893959886188058
    Iteration 215 
    X value is -4.896080688464297
    Iteration 216 
    X value is -4.898159074695011
    Iteration 217 
    X value is -4.9001958932011105
    Iteration 218 
    X value is -4.902191975337089
    Iteration 219 
    X value is -4.904148135830347
    Iteration 220 
    X value is -4.90606517311374
    Iteration 221 
    X value is -4.907943869651465
    Iteration 222 
    X value is -4.909784992258436
    Iteration 223 
    X value is -4.911589292413267
    Iteration 224 
    X value is -4.913357506565002
    Iteration 225 
    X value is -4.915090356433702
    Iteration 226 
    X value is -4.9167885493050285
    Iteration 227 
    X value is -4.918452778318928
    Iteration 228 
    X value is -4.920083722752549
    Iteration 229 
    X value is -4.921682048297498
    Iteration 230 
    X value is -4.923248407331548
    Iteration 231 
    X value is -4.9247834391849175
    Iteration 232 
    X value is -4.926287770401219
    Iteration 233 
    X value is -4.927762014993195
    Iteration 234 
    X value is -4.929206774693331
    Iteration 235 
    X value is -4.930622639199464
    Iteration 236 
    X value is -4.932010186415474
    Iteration 237 
    X value is -4.933369982687164
    Iteration 238 
    X value is -4.934702583033421
    Iteration 239 
    X value is -4.936008531372753
    Iteration 240 
    X value is -4.937288360745298
    Iteration 241 
    X value is -4.938542593530392
    Iteration 242 
    X value is -4.939771741659784
    Iteration 243 
    X value is -4.940976306826588
    Iteration 244 
    X value is -4.942156780690056
    Iteration 245 
    X value is -4.943313645076255
    Iteration 246 
    X value is -4.94444737217473
    Iteration 247 
    X value is -4.945558424731236
    Iteration 248 
    X value is -4.946647256236611
    Iteration 249 
    X value is -4.947714311111879
    Iteration 250 
    X value is -4.9487600248896415
    Iteration 251 
    X value is -4.949784824391848
    Iteration 252 
    X value is -4.950789127904011
    Iteration 253 
    X value is -4.951773345345931
    Iteration 254 
    X value is -4.952737878439012
    Iteration 255 
    X value is -4.953683120870232
    Iteration 256 
    X value is -4.954609458452827
    Iteration 257 
    X value is -4.955517269283771
    Iteration 258 
    X value is -4.956406923898095
    Iteration 259 
    X value is -4.957278785420133
    Iteration 260 
    X value is -4.958133209711731
    Iteration 261 
    X value is -4.958970545517496
    Iteration 262 
    X value is -4.959791134607146
    Iteration 263 
    X value is -4.960595311915003
    Iteration 264 
    X value is -4.9613834056767026
    Iteration 265 
    X value is -4.962155737563169
    Iteration 266 
    X value is -4.962912622811905
    Iteration 267 
    X value is -4.963654370355667
    Iteration 268 
    X value is -4.964381282948554
    Iteration 269 
    X value is -4.965093657289583
    Iteration 270 
    X value is -4.965791784143791
    Iteration 271 
    X value is -4.966475948460915
    Iteration 272 
    X value is -4.967146429491697
    Iteration 273 
    X value is -4.967803500901863
    Iteration 274 
    X value is -4.968447430883826
    Iteration 275 
    X value is -4.969078482266149
    Iteration 276 
    X value is -4.969696912620826
    Iteration 277 
    X value is -4.970302974368409
    Iteration 278 
    X value is -4.970896914881041
    Iteration 279 
    X value is -4.97147897658342
    Iteration 280 
    X value is -4.972049397051752
    Iteration 281 
    X value is -4.972608409110717
    Iteration 282 
    X value is -4.973156240928502
    Iteration 283 
    X value is -4.973693116109932
    Iteration 284 
    X value is -4.974219253787734
    Iteration 285 
    X value is -4.974734868711979
    Iteration 286 
    X value is -4.975240171337739
    Iteration 287 
    X value is -4.975735367910985
    Iteration 288 
    X value is -4.976220660552765
    Iteration 289 
    X value is -4.976696247341709
    Iteration 290 
    X value is -4.977162322394875
    Iteration 291 
    X value is -4.977619075946977
    Iteration 292 
    X value is -4.978066694428038
    Iteration 293 
    X value is -4.978505360539477
    Iteration 294 
    X value is -4.978935253328687
    Iteration 295 
    X value is -4.979356548262113
    Iteration 296 
    X value is -4.979769417296871
    Iteration 297 
    X value is -4.980174028950934
    Iteration 298 
    X value is -4.980570548371915
    Iteration 299 
    X value is -4.980959137404477
    Iteration 300 
    X value is -4.981339954656387
    Iteration 301 
    X value is -4.981713155563259
    Iteration 302 
    X value is -4.982078892451994
    Iteration 303 
    X value is -4.9824373146029535
    Iteration 304 
    X value is -4.982788568310895
    Iteration 305 
    X value is -4.983132796944677
    Iteration 306 
    X value is -4.983470141005784
    Iteration 307 
    X value is -4.983800738185668
    Iteration 308 
    X value is -4.984124723421955
    Iteration 309 
    X value is -4.984442228953515
    Iteration 310 
    X value is -4.984753384374445
    Iteration 311 
    X value is -4.985058316686956
    Iteration 312 
    X value is -4.9853571503532175
    Iteration 313 
    X value is -4.985650007346153
    Iteration 314 
    X value is -4.9859370071992295
    Iteration 315 
    X value is -4.986218267055245
    Iteration 316 
    X value is -4.98649390171414
    Iteration 317 
    X value is -4.986764023679857
    Iteration 318 
    X value is -4.98702874320626
    Iteration 319 
    X value is -4.987288168342134
    Iteration 320 
    X value is -4.987542404975292
    Iteration 321 
    X value is -4.987791556875786
    Iteration 322 
    X value is -4.98803572573827
    Iteration 323 
    X value is -4.988275011223505
    Iteration 324 
    X value is -4.988509510999035
    Iteration 325 
    X value is -4.988739320779054
    Iteration 326 
    X value is -4.988964534363473
    Iteration 327 
    X value is -4.989185243676204
    Iteration 328 
    X value is -4.98940153880268
    Iteration 329 
    X value is -4.989613508026626
    Iteration 330 
    X value is -4.989821237866094
    Iteration 331 
    X value is -4.990024813108772
    Iteration 332 
    X value is -4.9902243168465965
    Iteration 333 
    X value is -4.990419830509665
    Iteration 334 
    X value is -4.990611433899471
    Iteration 335 
    X value is -4.990799205221482
    Iteration 336 
    X value is -4.990983221117052
    Iteration 337 
    X value is -4.991163556694711
    Iteration 338 
    X value is -4.991340285560817
    Iteration 339 
    X value is -4.9915134798496
    Iteration 340 
    X value is -4.991683210252608
    Iteration 341 
    X value is -4.991849546047556
    Iteration 342 
    X value is -4.992012555126605
    Iteration 343 
    X value is -4.992172304024073
    Iteration 344 
    X value is -4.992328857943591
    Iteration 345 
    X value is -4.99248228078472
    Iteration 346 
    X value is -4.992632635169025
    Iteration 347 
    X value is -4.9927799824656445
    Iteration 348 
    X value is -4.992924382816332
    Iteration 349 
    X value is -4.993065895160005
    Iteration 350 
    X value is -4.993204577256805
    Iteration 351 
    X value is -4.993340485711669
    Iteration 352 
    X value is -4.993473675997436
    Iteration 353 
    X value is -4.993604202477487
    Iteration 354 
    X value is -4.993732118427937
    Iteration 355 
    X value is -4.993857476059379
    Iteration 356 
    X value is -4.993980326538191
    Iteration 357 
    X value is -4.9941007200074266
    Iteration 358 
    X value is -4.994218705607278
    Iteration 359 
    X value is -4.994334331495133
    Iteration 360 
    X value is -4.994447644865231
    Iteration 361 
    X value is -4.994558691967926
    Iteration 362 
    X value is -4.994667518128567
    Iteration 363 
    X value is -4.994774167765996
    Iteration 364 
    X value is -4.9948786844106765
    Iteration 365 
    X value is -4.994981110722463
    Iteration 366 
    X value is -4.995081488508014
    Iteration 367 
    X value is -4.995179858737854
    Iteration 368 
    X value is -4.995276261563097
    Iteration 369 
    X value is -4.995370736331835
    Iteration 370 
    X value is -4.9954633216051985
    Iteration 371 
    X value is -4.995554055173095
    Iteration 372 
    X value is -4.995642974069633
    Iteration 373 
    X value is -4.99573011458824
    Iteration 374 
    X value is -4.995815512296476
    Iteration 375 
    X value is -4.995899202050547
    Iteration 376 
    X value is -4.995981218009535
    Iteration 377 
    X value is -4.996061593649345
    Iteration 378 
    X value is -4.996140361776358
    Iteration 379 
    X value is -4.996217554540831
    Iteration 380 
    X value is -4.996293203450014
    Iteration 381 
    X value is -4.996367339381013
    Iteration 382 
    X value is -4.996439992593393
    Iteration 383 
    X value is -4.996511192741525
    Iteration 384 
    X value is -4.996580968886694
    Iteration 385 
    X value is -4.99664934950896
    Iteration 386 
    X value is -4.9967163625187805
    Iteration 387 
    X value is -4.996782035268405
    Iteration 388 
    X value is -4.996846394563037
    Iteration 389 
    X value is -4.996909466671776
    Iteration 390 
    X value is -4.996971277338341
    Iteration 391 
    X value is -4.997031851791574
    Iteration 392 
    X value is -4.997091214755742
    Iteration 393 
    X value is -4.997149390460628
    Iteration 394 
    X value is -4.997206402651415
    Iteration 395 
    X value is -4.997262274598387
    Iteration 396 
    X value is -4.997317029106419
    Iteration 397 
    X value is -4.997370688524291
    Iteration 398 
    X value is -4.997423274753805
    Iteration 399 
    X value is -4.997474809258729
    Iteration 400 
    X value is -4.997525313073554
    Iteration 401 
    X value is -4.997574806812083
    Iteration 402 
    X value is -4.997623310675841
    Iteration 403 
    X value is -4.997670844462324
    Iteration 404 
    X value is -4.997717427573078
    Iteration 405 
    X value is -4.997763079021617
    Iteration 406 
    X value is -4.997807817441185
    Iteration 407 
    X value is -4.997851661092361
    Iteration 408 
    X value is -4.997894627870514
    Iteration 409 
    X value is -4.997936735313104
    Iteration 410 
    X value is -4.9979780006068415
    Iteration 411 
    X value is -4.998018440594705
    Iteration 412 
    X value is -4.998058071782811
    Iteration 413 
    X value is -4.998096910347155
    Iteration 414 
    X value is -4.998134972140212
    Iteration 415 
    X value is -4.998172272697408
    Iteration 416 
    X value is -4.9982088272434595
    Iteration 417 
    X value is -4.998244650698591
    Iteration 418 
    X value is -4.998279757684619
    Iteration 419 
    X value is -4.998314162530927
    Iteration 420 
    X value is -4.998347879280309
    Iteration 421 
    X value is -4.998380921694703
    Iteration 422 
    X value is -4.998413303260809
    Iteration 423 
    X value is -4.998445037195593
    Iteration 424 
    X value is -4.998476136451681
    Iteration 425 
    X value is -4.998506613722648
    Iteration 426 
    X value is -4.998536481448195
    Iteration 427 
    X value is -4.998565751819231
    Iteration 428 
    X value is -4.998594436782846
    Iteration 429 
    X value is -4.998622548047189
    Iteration 430 
    X value is -4.998650097086245
    Iteration 431 
    X value is -4.9986770951445205
    Iteration 432 
    X value is -4.99870355324163
    Iteration 433 
    X value is -4.998729482176797
    Iteration 434 
    X value is -4.998754892533261
    Iteration 435 
    X value is -4.998779794682596
    Iteration 436 
    X value is -4.998804198788944
    Iteration 437 
    X value is -4.998828114813166
    Iteration 438 
    X value is -4.998851552516903
    Iteration 439 
    X value is -4.998874521466565
    Iteration 440 
    X value is -4.998897031037234
    Iteration 441 
    X value is -4.998919090416489
    Iteration 442 
    X value is -4.99894070860816
    Iteration 443 
    X value is -4.998961894435997
    Iteration 444 
    X value is -4.998982656547277
    Iteration 445 
    X value is -4.999003003416331
    Iteration 446 
    X value is -4.999022943348004
    Iteration 447 
    X value is -4.999042484481044
    Iteration 448 
    X value is -4.999061634791423
    Iteration 449 
    X value is -4.999080402095594
    Iteration 450 
    X value is -4.999098794053682
    Iteration 451 
    X value is -4.999116818172609
    Iteration 452 
    X value is -4.999134481809157
    Iteration 453 
    X value is -4.999151792172974
    Iteration 454 
    X value is -4.999168756329515
    Iteration 455 
    X value is -4.999185381202924
    Iteration 456 
    X value is -4.999201673578866
    Iteration 457 
    X value is -4.999217640107289
    Iteration 458 
    X value is -4.999233287305143
    Iteration 459 
    X value is -4.9992486215590395
    Iteration 460 
    X value is -4.999263649127859
    Iteration 461 
    X value is -4.999278376145302
    Iteration 462 
    X value is -4.999292808622396
    Iteration 463 
    X value is -4.999306952449948
    Iteration 464 
    X value is -4.999320813400949
    Iteration 465 
    X value is -4.99933439713293
    Iteration 466 
    X value is -4.999347709190272
    Iteration 467 
    X value is -4.9993607550064665
    Iteration 468 
    X value is -4.999373539906337
    Iteration 469 
    X value is -4.99938606910821
    Iteration 470 
    X value is -4.9993983477260455
    Iteration 471 
    X value is -4.999410380771525
    Iteration 472 
    X value is -4.999422173156094
    Iteration 473 
    X value is -4.9994337296929725
    Iteration 474 
    X value is -4.999445055099113
    Iteration 475 
    X value is -4.999456153997131
    Iteration 476 
    X value is -4.999467030917188
    Iteration 477 
    X value is -4.9994776902988445
    Iteration 478 
    X value is -4.999488136492867
    Iteration 479 
    X value is -4.99949837376301
    Iteration 480 
    X value is -4.99950840628775
    Iteration 481 
    X value is -4.999518238161995
    Iteration 482 
    X value is -4.999527873398756
    Iteration 483 
    X value is -4.99953731593078
    Iteration 484 
    X value is -4.999546569612165
    Iteration 485 
    X value is -4.999555638219921
    Iteration 486 
    X value is -4.999564525455523
    Iteration 487 
    X value is -4.999573234946412
    Iteration 488 
    X value is -4.9995817702474845
    Iteration 489 
    X value is -4.999590134842535
    Iteration 490 
    X value is -4.999598332145684
    Iteration 491 
    X value is -4.99960636550277
    Iteration 492 
    X value is -4.999614238192715
    Iteration 493 
    X value is -4.999621953428861
    Iteration 494 
    X value is -4.999629514360284
    Iteration 495 
    X value is -4.999636924073078
    Iteration 496 
    X value is -4.999644185591617
    Iteration 497 
    X value is -4.999651301879784
    Iteration 498 
    X value is -4.999658275842188
    Iteration 499 
    X value is -4.999665110325345
    Iteration 500 
    X value is -4.999671808118838
    Iteration 501 
    X value is -4.9996783719564615
    Iteration 502 
    X value is -4.999684804517332
    Iteration 503 
    X value is -4.999691108426985
    Iteration 504 
    X value is -4.999697286258446
    Iteration 505 
    X value is -4.9997033405332765
    Iteration 506 
    X value is -4.999709273722611
    Iteration 507 
    X value is -4.999715088248159
    Iteration 508 
    X value is -4.999720786483196
    Iteration 509 
    X value is -4.999726370753532
    Iteration 510 
    X value is -4.999731843338461
    Iteration 511 
    X value is -4.999737206471692
    Iteration 512 
    X value is -4.999742462342258
    Iteration 513 
    X value is -4.999747613095413
    Iteration 514 
    X value is -4.999752660833504
    Iteration 515 
    X value is -4.999757607616834
    Iteration 516 
    X value is -4.999762455464498
    Iteration 517 
    X value is -4.999767206355208
    Iteration 518 
    X value is -4.999771862228104
    Iteration 519 
    X value is -4.999776424983542
    Iteration 520 
    X value is -4.9997808964838715
    Iteration 521 
    X value is -4.999785278554194
    Iteration 522 
    X value is -4.9997895729831106
    Iteration 523 
    X value is -4.999793781523448
    Iteration 524 
    X value is -4.999797905892979
    Iteration 525 
    X value is -4.999801947775119
    Iteration 526 
    X value is -4.999805908819617
    Iteration 527 
    X value is -4.999809790643225
    Iteration 528 
    X value is -4.99981359483036
    Iteration 529 
    X value is -4.999817322933753
    Iteration 530 
    X value is -4.999820976475077
    Iteration 531 
    X value is -4.999824556945576
    Iteration 532 
    X value is -4.999828065806665
    Iteration 533 
    X value is -4.9998315044905315
    Iteration 534 
    X value is -4.999834874400721
    Iteration 535 
    X value is -4.999838176912706
    Iteration 536 
    X value is -4.999841413374452
    Iteration 537 
    X value is -4.999844585106963
    Iteration 538 
    X value is -4.999847693404824
    Iteration 539 
    X value is -4.999850739536727
    Iteration 540 
    X value is -4.999853724745993
    Iteration 541 
    X value is -4.999856650251073
    Iteration 542 
    X value is -4.999859517246051
    Iteration 543 
    X value is -4.99986232690113
    Iteration 544 
    X value is -4.999865080363108
    Iteration 545 
    X value is -4.999867778755846
    Iteration 546 
    X value is -4.999870423180729
    Iteration 547 
    X value is -4.999873014717115
    Iteration 548 
    X value is -4.999875554422772
    Iteration 549 
    X value is -4.999878043334316
    Iteration 550 
    X value is -4.99988048246763
    Iteration 551 
    X value is -4.999882872818278
    Iteration 552 
    X value is -4.999885215361912
    Iteration 553 
    X value is -4.999887511054674
    Iteration 554 
    X value is -4.999889760833581
    Iteration 555 
    X value is -4.999891965616909
    Iteration 556 
    X value is -4.999894126304571
    Iteration 557 
    X value is -4.999896243778479
    Iteration 558 
    X value is -4.999898318902909
    Iteration 559 
    X value is -4.999900352524851
    Iteration 560 
    X value is -4.9999023454743545
    Iteration 561 
    X value is -4.999904298564868
    Iteration 562 
    X value is -4.9999062125935705
    Iteration 563 
    X value is -4.999908088341699
    Iteration 564 
    X value is -4.9999099265748645
    Iteration 565 
    X value is -4.999911728043367
    Iteration 566 
    X value is -4.9999134934825
    Iteration 567 
    X value is -4.99991522361285
    Iteration 568 
    X value is -4.999916919140593
    Iteration 569 
    X value is -4.999918580757781
    Iteration 570 
    X value is -4.999920209142625
    Iteration 571 
    X value is -4.999921804959773
    Iteration 572 
    X value is -4.9999233688605775
    Iteration 573 
    X value is -4.999924901483366
    Iteration 574 
    X value is -4.999926403453699
    Iteration 575 
    X value is -4.999927875384625
    Iteration 576 
    X value is -4.999929317876933
    Iteration 577 
    X value is -4.999930731519394
    Iteration 578 
    X value is -4.999932116889006
    Iteration 579 
    X value is -4.999933474551226
    Iteration 580 
    X value is -4.999934805060202
    Iteration 581 
    X value is -4.999936108958998
    Iteration 582 
    X value is -4.999937386779818
    Iteration 583 
    X value is -4.999938639044221
    Iteration 584 
    X value is -4.999939866263337
    Iteration 585 
    X value is -4.99994106893807
    Iteration 586 
    X value is -4.999942247559309
    Iteration 587 
    X value is -4.999943402608123
    Iteration 588 
    X value is -4.9999445345559606
    Iteration 589 
    X value is -4.999945643864842
    Iteration 590 
    X value is -4.999946730987545
    Iteration 591 
    X value is -4.999947796367794
    Iteration 592 
    X value is -4.999948840440438
    Iteration 593 
    X value is -4.999949863631629
    Iteration 594 
    X value is -4.999950866358997
    Iteration 595 
    X value is -4.9999518490318176
    The local minimum occurs at -4.9999518490318176
```



