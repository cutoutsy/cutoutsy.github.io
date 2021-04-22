---
layout: post
title: 谷歌Kickstart 2017 F轮编程题解答（Java版）
date: 2017-09-28 15:30:10
categories: 算法	
tags:
	- 谷歌
	- Kickstart
	- F轮
	- JAVA
---

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
});
</script>

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

### 写在前面
听说Google的Kickstart应该是在半年前，Google Kickstart即是原APAC Test，G家的校园招聘线上笔试，不过一直没有认真参加过，这个比赛时间一般是周日的下午1点到4点（北京时间），不过这次是后两轮是在周日下午4点开始，然后持续12个小时，自己选择其中的任意连续3个小时提交都有效。总的来说这次的题目比较简单，除了C题的large外。


本次共有4道题目，我只AC了前面2道。里面的解法是根据我的思考结合官方给出的分析给出的一些思路。

### 题目及分析
原题目传送门：[Round F - Dashboard](https://code.google.com/codejam/contest/7254486/dashboard)

本文我会对题目进行简单的中文描述。
#### Problem A. Kicksort
这儿有一种排序叫Kicksort，是来源于快速排序算法。快速排序算法选取一个基准，然后根据基准分为两组，然后在每组里递归的这样做。但是这种算法选取的基准可能会导致按照基准比较后只会产生一组而不是两组，这违反了这种算法的目的。我们称这种基准为最差基准。
<!-- more -->
为了避免这种情况，我们创建Kicksort算法，使用待排序序列的中间的值作为基准，算法如下：
```Java
Kicksort(A): // A is a 0-indexed array with E elements
    If E ≤ 1, return A.
    Otherwise:
      Create empty new lists B and C.
      Choose A[floor((E-1)/2)] as the pivot P.
      For i = 0 to E-1, except for i = floor((E-1)/2):
        If A[i] ≤ P, append it to B.
        Otherwise, append it to C.
    Return the list Kicksort(B) + P + Kicksort(C).
```
通过使用Kicksort算法，我们发现Kicksort仍然存在对于每一次选取的基准，都是最差基准。比如数组：[1，4，3，2]。Kicksort算法首先选取4作为基准，然后原数组变为[1，3，2]。然后选取3作为基准，原数组变为[1，2]。选取1作为基准，原数组变为[2]。至此结束（Kicksort结束是数组长度为0或者1时），可以看出每次选取的基准都是最差基准。

**输入**
第一行是测试用例数量：T。
每个测试用例包括两行，N：元素个数。
接下来的一行是待排序的序列，元素大小从1到N，包括1到N。

**输出**
Case #x: y
x代表是第几个测试案例
y代表如果Kicksort算法一直选取最差基准则为“YES”，其他情况为“NO”。

**小数据集**
1 ≤ T ≤ 32
2 ≤ N ≤ 4

**大数组集**
1 ≤ T ≤ 100
2 ≤ N ≤ 10000

**例子：**
输入：
4
4
1 4 3 2
4
2 1 3 4
2
2 1
3
1 2 3

输出：
Case #1: YES
Case #2: NO
Case #3: YES
Case #4: NO

##### 题目分析
这个题目和快速排序相关，思路还是比较清晰的，我的思路是模拟快速排序，不过是选取中间的值作为基准，而不是我们平常在快速排序中的选取最后的值作为基准，如果在排序的过程中，如果存在按基准值分开为2组，则返回NO，如果一直按基准值分开后都是一组，则返回YES。

**具体代码：**
```Java
public class Problem1 {
    static boolean flag = true;
    public static List isWorstPivots(List<Integer> list) {
        if (list.size() <= 1) return list;
        List<Integer> left = new ArrayList<>();
        List<Integer> right = new ArrayList<>();
        int pivot = list.get((list.size()-1)/2);
        for (int i = 0; i < list.size(); i++) {
            if (i != (list.size()-1)/2) {
                if (list.get(i) <= pivot) {
                    left.add(list.get(i));
                } else {
                    right.add(list.get(i));
                }
            }
        }
        if (left.size() != 0 && right.size() != 0){
            flag = false;
        }
        List<Integer> ans = new ArrayList<>();
        ans.addAll(onlyWorstPivots(left));
        ans.add(pivot);
        ans.addAll(onlyWorstPivots(right));
        return ans;
    }
}    
```
这个代码使用flag作为每一次的标志，如果为TRUE，则返回“YES“，反之则返回“NO“”。这种算法的时间复杂度为$O(n^2)$。

下面介绍一种时间复杂度为O(n)的算法，这是官方题解介绍的。
首先我们考虑返回YES的不同长度序列，我们可以发现它们使用的索引都是一样的。举个例子，
对于长度为6的序列，首先会选取索引为(N-1)/2对于的值，即索引为2的值作为索引，然后剩余的部分组成一个长度为5的序列，这时选取索引为2的值，这个对应于原序列的索引3，如果我们继续这样下去，选取的值的索引在原序列中的所有依次为2，3，1，4，0，5。即我们开始于(N-1)/2，然后向右移动1，然后向左移动2，然后向右移动3......这个不依赖于序列里的值。

对于长度为奇数的情况，也是类似的，最开始从(N-1)/2开始，然后向左移动1，然后向右移动2，然后向左移动3......

这样我们就按照这索引规律，在每次索引的时候判断该索引对应的值是不是最大值或则最小值。如果一直都是最大值或者最小值，则最后返回“YES”，反之则返回“NO”。

对于本题来说，由于序列的元素是1~N，所以最小值是1，最大值是N，在代码里维护好这两个值就可以了。

**具体代码：**
```Java
public static boolean isWorstPivots(int[] nums) {
    int min = 1;
    int max = nums.length;
    int start = (nums.length - 1) / 2;
    int[] move = new int[2];
    for (int i = 0; i <= (nums.length - 1) / 2; i++) {
        if ((nums.length & 1) == 1) {
            move[0] = -1;
            move[1] = 1;
        } else {
            move[0] = 1;
            move[1] = -1;
        }
        for (int j = 0; j < move.length; j++) {
            if ((start + move[j] * i) > -1 || (start + move[j] * i) < nums.length ) {
                if (nums[start + move[j] * i] == min) {
                    min++;
                } else if (nums[start + move[j] * i] == max) {
                    max--;
                } else {
                    return false;
                }
                if (i == 0) break; //当i = 0时，即为初始位置，循环一次
            }
        }
    }
    return true;
}
```
这种思路的代码就比较简洁清晰了。我在做这个题的时候采取的是第一种思路（比较容易想到）。

#### Problem B. Dance Battle
这个题目是关于在“舞蹈战争”中获取最大分数。首先你的团队有E点能量，0点的荣誉值。你会面对大小为N个对手团队的阵容，第i个团队他们的跳舞技能点为S<sub>i</sub>。

在每一轮与对手对战中，你可以采取下面这些策略：

 - 跳舞：你会损失能量值，大小为对手的S<sub>i</sub>，获得1点荣誉值，对手不会退回阵容中。你的能量值损失后不能小于等于0。
 - 推迟：你的能量值和荣誉值不改变，对手会退回阵容中。
 - 休战：你的能力值和荣誉值不改变，对手不退回阵容中。
 - 招募：你招募对手到你的团队里，对手不退回阵容中。你增加对手的S<sub>i</sub>。你会损失1点荣誉值。你的荣誉值不能少于0。
 
这场战争结束直到对手阵容里没有对手团队了。求你能获得的最大荣誉值。

**输入**
第一行：T，表示测试用例数。
接下来E N，E表示你的初始能量数，N表示对手的数量。
接着是N个对手对应的跳舞技能点S<sub>i</sub>。

**输出**
每行输出：Case #x: y。
x代表第几个测试用例。
y代表获得最大的荣誉值。

**限制：**
1 ≤ T ≤ 100。
1 ≤ E ≤ 106。
1 ≤ Si ≤ 106， for all i。

**小数据集：**
1 ≤ N ≤ 5

**大数据集：**
1 ≤ N ≤ 1000

**栗子：**
输入：
2
100 1
100
10 3
20 3 15

输出：
Case #1: 0
Case #2: 1

##### 题目分析
这个题目其实是比较好想的，首先通过延时策略我们可以对对手按照S<sub>i</sub>进行由小到大排序。然后使用两个指针，初始一个指向最左边，一个指向最右边，首先从左边开始，如果当前的能量值大于S<sub>i</sub>，则进行跳舞策略，直到不满足为止。此时从右边开始（这个时候要确保至少还存在2个对手，若只剩一个对手时，直接执行休战策略即可），进行招募策略，招募后再移动左边的指针......整个过程保证能量值E大于0和荣誉值不小于0即可。

**具体代码：**
```Java
public static int findMaxHonor(int energy, int[] skills) {
    int honor = 0;
    Arrays.sort(skills);
    int size = skills.length - 1;
    for (int i = 0; i < skills.length; i++) {
        if (skills[i] != 0) {
            if (energy > skills[i] && energy - skills[i] > 0) {
                energy -= skills[i];
                honor++;
                skills[i] = 0;
            } else if (energy <= skills[i] && honor > 0 && size > i) {
                energy += skills[size];
                honor--;
                skills[size] = 0;
                size--;
                i--;
            }
        }
    }
    return honor;
}
```
#### Problem C. Catch Thme All
这个题目是以Pokemon GO抓取宠物小精灵为背景，这儿有一款游戏叫Codejamon GO，去街道上抓取宠物小精灵。

你的城市有N个位置从1~N，你开始在位置1。这儿有M条双向的道路（1~M）。第i条道路连接两个不同的位置(U<sub>i</sub>, V<sub>i</sub>)，并且需要花D<sub>i</sub>分钟从一边到另一边。保证从位置1可以到其他任何位置。

在时间为0时，一个宠物小精灵会等概率出现在除了你当前位置（时间：0，位置：1）的其他任何位置。即每个位置出现宠物的概率为1/(N-1)。宠物一出现，你就会立刻抓住它，即抓取过程没有时间花费。每次只有一个宠物小精灵存在，只有抓住了存在的这个才会出现下一个。

给你城市布局，计算抓取P个宠物小精灵的期望时间，假设你在两个位置走的是最快的路线。

**输入**
第一行：T，表示测试用例数目。
每个测试用例第一行为: N M P，N代表位置数目，M代表道路数目，P代表要抓取的宠物小精灵数目
接下来的是M条道路信息：U<sub>i</sub> V<sub>i</sub> D<sub>i</sub>，表示从位置U<sub>i</sub>到V<sub>i</sub>存在道路，且花费时间为D<sub>i</sub>。

**输出**
每行输出Case #x: y。
x代表第几个测试用例
y代表花费的期望时间，如果在正确答案的10-4的绝对或相对误差范围内，您的答案将被视为正确。

**限制：**
1 ≤ T ≤ 100.
N - 1 ≤ M ≤ (N * (N - 1)) / 2.
1 ≤ D<sub>i</sub> ≤ 10, for all i.
1 ≤ U<sub>i</sub> < V<sub>i</sub> ≤ N, for all i.
For all i and j with i ≠ j, U<sub>i</sub> ≠ U<sub>i</sub> and/or V<sub>i</sub> ≠ V<sub>i</sub>。

**小数据集**
2 ≤ N ≤ 50.
1 ≤ P ≤ 200

**大数据集**
2 ≤ N ≤ 100.
1 ≤ P ≤ 109

**栗子**
输入：
4
5 4 1
1 2 1
2 3 2
1 4 2
4 5 1
2 1 200
1 2 5
5 4 2
1 2 1
2 3 2
1 4 2
4 5 1
3 3 1
1 2 3
1 3 1
2 3 1

输出：
Case #1: 2.250000
Case #2: 1000.000000
Case #3: 5.437500
Case #4: 1.500000

**题目分析**
首先我们可以想到图的最短路算法，比如使用Dijkstra算法或Floyd-Warshall算法来计算两点的最短路径，在这个题目里即是时间最短的路径。求出所有点之间的花费时间最少的路径的时间，后面会用到。然后我们可以根据动态规划来计算期望时间，令dp[K, L]代表从位置L出发抓取K个精灵的期望时间，然后我们可以得到状态转移方程：
dp[K, L] = Σ<sub>i!=L</sub>(dp[K-1, i] + dis[L, i]) / (N-1).

而dp[K, L] = 0 (K = 0)

**具体代码：**
图的顶点类：
```
public class Vertex implements Comparable<Vertex>{
    private int path;    // 最短路径长度
    private boolean isMarked;    // 节点是否已经出列（是否已经处理完毕）
    
    public int getPath() {
        return path;
    }

    public void setPath(int path) {
        this.path = path;
    }

    public Vertex() {
        this.path = Integer.MAX_VALUE;
        this.isMarked = false;
    }

    public Vertex(int path) {
        this.path = path;
        this.isMarked = false;
    }

    @Override
    public int compareTo(Vertex o) {
        return o.path > path ? -1 : 1;
    }

    public void setMarked(boolean mark) {
        this.isMarked = mark;
    }

    public boolean isMarked() {
        return this.isMarked;
    }
}
```
计算期望时间：
```Java
public class Problem3 {
    // 顶点
    private List<Vertex> vertexs;
    // 边
    private int[][] edges;
    // 没有访问的顶点
    private PriorityQueue<Vertex> unVisited;

    public Problem3(List<Vertex> vertexs, int[][] edges) {
        this.vertexs = vertexs;
        this.edges = edges;
        initUnVisited();
    }

    // 初始化未访问顶点集合
    private void initUnVisited() {
        unVisited = new PriorityQueue<Vertex>();
        for (Vertex v : vertexs) {
            unVisited.add(v);
        }
    }

    // 搜索各顶点最短路径
    public void search() {
        while (!unVisited.isEmpty()) {
            Vertex vertex = unVisited.peek();
            vertex.setMarked(true);
            List<Vertex> neighbors = getNeighbors(vertex);
            updatesDistabce(vertex, neighbors);
            unVisited.poll();
            List<Vertex> temp = new ArrayList<>();
            while (!unVisited.isEmpty()) {
                temp.add(unVisited.poll());
            }
            for (Vertex v : temp) {
                unVisited.add(v);
            }
        }
    }

    // 更新所有邻居的最短路径
    private void updatesDistabce(Vertex vertex, List<Vertex> neighbors) {
        for (Vertex neighbor: neighbors) {
            int distance = getDistance(vertex, neighbor) + vertex.getPath();
            if (distance < neighbor.getPath()) {
                neighbor.setPath(distance);
            }
        }
    }

    private int getDistance(Vertex source, Vertex destination) {
        int sourceIndex = vertexs.indexOf(source);
        int destIndex = vertexs.indexOf(destination);
        return edges[sourceIndex][destIndex];
    }

    private List<Vertex> getNeighbors(Vertex vertex) {
        List<Vertex> neighbors = new ArrayList<Vertex>();
        int position = vertexs.indexOf(vertex);
        Vertex neighbor = null;
        int distance;
        for (int i = 0; i < vertexs.size(); i++) {
            if (i == position) {
                continue;
            }
            distance = edges[position][i];
            if (distance < Integer.MAX_VALUE) {
                neighbor = vertexs.get(i);
                if (!neighbor.isMarked()) {
                    neighbors.add(neighbor);
                }
            }
        }
        return neighbors;
    }

    public static double calculateExpectedTimeSmallDataSet(int locations, int codejamGo, List<String> graph) {
        int[][] edges = new int[locations][locations];
        for (int i = 0; i < locations; i++) {
            for (int j = 0; j < locations; j++) {
                edges[i][j] = Integer.MAX_VALUE;
            }
        }
        for (int i = 0; i < graph.size(); i++) {
            String edge = graph.get(i);
            int source = Integer.valueOf(edge.split(" ")[0]);
            int destination = Integer.valueOf(edge.split(" ")[1]);
            int value = Integer.valueOf(edge.split(" ")[2]);
            edges[source - 1][destination - 1] = value;
            edges[destination - 1][source - 1] = value;
        }
        List<Vertex> vertexs = new ArrayList<>();

        for (int begin = 0; begin < locations; begin++) {
            for (int i = 0; i < locations; i++) {
                vertexs.add((i == begin) ? new Vertex(0) : new Vertex());
            }

            Problem3 problem3 = new Problem3(vertexs, edges);
            problem3.search();
            for (int i = 0; i < vertexs.size(); i++) {
                edges[begin][i] = vertexs.get(i).getPath();
            }
            vertexs.clear();
        }

        double[][] dp = new double[codejamGo + 1][locations];
        Arrays.fill(dp[0], 0);

        for (int i = 1; i < codejamGo + 1; i++) {
            for (int j = 0; j < locations; j++) {
                double sum = 0;
                for (int k = 0; k < locations; k++) {
                    if (k != j) {
                        sum += dp[i - 1][k] + edges[j][k];
                    }
                }
                dp[i][j] = sum / (locations - 1);
            }
        }

        return dp[codejamGo][0];
    }
}
```

这种方式对于小数据集是完全能够解决的，但对于大数据集就不适用了，这个时候我们需要对状态转移方程进行简化。我们可以发现，对于每个dp[K, L]，都是dp[K-1, i]的线性表达式，定义 Σj!=i(dis[i, j])。则我们可以写成如下的形式：
![状态转移方程化简](http://7oxhal.com1.z0.glb.clouddn.com/blog_google_kickstart.png)

F<sub>K</sub>代表dp[K, i]向量，A代表转移矩阵，我们可以得到
F<sub>K</sub> = A * F<sub>K-1</sub> = $A^K$ * F<sub>0</sub>
在计算$A^K$时，我们可以使用快速幂算法。

**具体代码：**
快速幂算法：
```Java
public class MatricQuickPower {

    public static double[][] multiply(double [][]a, double[][]b){
        double[][] arr = new double[a.length][b[0].length];
        for(int i = 0; i < a.length; i++){
           for(int j = 0; j < b[0].length; j++){
               for(int k = 0; k < a[0].length; k++){
                   arr[i][j] += a[i][k] * b[k][j];
               }  
           }  
        }  
        return arr;  
    }

    public static double[][] multiplyPower(double[][] a, int n){
        double[][] res = new double[a.length][a[0].length];
        for(int i = 0; i < res.length; i++){
            for(int j = 0; j < res[0].length; j++) {
                if (i == j)
                    res[i][j] = 1;
                else
                    res[i][j] = 0;
            }
        }  
        while(n != 0){
            if((n & 1) == 1)
                res = multiply(res, a);
            n >>= 1;
            a = multiply(a, a);
        }  
        return res;  
    }
}  
```
计算期望时间：
```Java
public static double calculateExpectedTimeLargeDataSet(int locations, int codejamGo, List<String> graph) {
    int[][] edges = new int[locations][locations];
    for (int i = 0; i < locations; i++) {
        for (int j = 0; j < locations; j++) {
            edges[i][j] = Integer.MAX_VALUE;
        }
    }
    for (int i = 0; i < graph.size(); i++) {
        String edge = graph.get(i);
        int source = Integer.valueOf(edge.split(" ")[0]);
        int destination = Integer.valueOf(edge.split(" ")[1]);
        int value = Integer.valueOf(edge.split(" ")[2]);
        edges[source - 1][destination - 1] = value;
        edges[destination - 1][source - 1] = value;
    }
    List<Vertex> vertexs = new ArrayList<>();
    int[] distanceSum = new int[locations];
    for (int begin = 0; begin < locations; begin++) {
        for (int i = 0; i < locations; i++) {
            vertexs.add((i == begin) ? new Vertex(0) : new Vertex());
        }

        Problem3 problem3 = new Problem3(vertexs, edges);
        problem3.search();
        int totalDistance = 0;
        for (int i = 0; i < vertexs.size(); i++) {
            totalDistance += vertexs.get(i).getPath();
        }
        distanceSum[begin] = totalDistance;
        vertexs.clear();
    }

        double[][] zeroCodeJam = new double[locations + 1][1];
        for (int i = 0; i < zeroCodeJam.length; i++) {
            for (int j = 0; j < zeroCodeJam[i].length; j++) {
                zeroCodeJam[i][j] = 0;
            }
        }
        zeroCodeJam[locations][0] = 1;
        double[][] matris = new double[locations + 1][locations + 1];
        for (int i = 0; i < matris.length; i++) {
            if (i != locations) {
                for (int j = 0; j < matris[i].length; j++) {
                    if (i == j) {
                        matris[i][j] = 0;
                    } else if (j == locations) {
                        matris[i][j] = distanceSum[i] * 1.0/(locations - 1);
                    } else {
                        matris[i][j] = 1.0/(locations - 1);
                    }
                }
            }
            if (i == locations) {
                for (int j = 0; j < matris[i].length; j++) {
                    matris[i][j] = j == locations ? 1 : 0;
                }
            }
        }

        matris = MatricQuickPower.multiplyPower(matris, codejamGo);
        double ans = 0;
        for (int i = 0; i < zeroCodeJam.length; i++) {
            ans += matris[0][i] * zeroCodeJam[i][0];
        }
        return ans;
}
```
这个题目还有其他的一些解法，更多解法移步[ProblemC Analysis](https://code.google.com/codejam/contest/7254486/dashboard#s=a&a=2)

#### Problem D. Eat Cake
这个题目是关于吃蛋糕的，蛋糕的高度都是一样的，上下两面都是正方形，数量不限制。现在
Wheatley想吃面积为N的蛋糕，但是为了健康，想吃的蛋糕块数最少，求最少蛋糕块数。

**输入**
第一行：T，测试用例数
接着每行代表一个测试用例：N，表示吃的蛋糕的面积

**输出**
Case #x: y
x代表第几个测试用例
y代表最小蛋糕块数

**小数据集**
1 ≤ T ≤ 50
1 ≤ N ≤ 50

**大数据集**
1 ≤ T ≤ 100
1 ≤ N ≤ 10000

**栗子**
输入：
3
3
4
5
输出：
Case #1: 3
Case #2: 1
Case #3: 2

**题目分析**
首先我们发现小数据集的数据是很小的，我们可以用暴力的方法。

**具体代码**

```Java
public static int findCakeNumsByArea(int area) {
    if (area == 0) return 0;
    int ans = Integer.MAX_VALUE;
    for (int i = 1; i <= Math.sqrt(area); i++) {
        ans = Math.min(ans, findCakeNumsByArea(area - i * i) + 1);
    }
    return ans;
}
```

对于大数据集，我们可以采取动态规划DP。将每次计算的结果保存起来，这个代码比较简单，大家可以看下下面的伪代码：
```Java
public class Problem4 {

    public boolean[] alreadyComputed;
    public int[] dp;

    public Problem4() {
        this.alreadyComputed = new boolean[10000 + 1];
        this.dp = new int[10000 + 1];
        Arrays.fill(alreadyComputed, false);
    }

    // dp
    public int findCakeNumsByAreaDp(int inputArea) {
        if (inputArea == 0) return 0;
        if (alreadyComputed[inputArea]) return dp[inputArea];
        dp[inputArea] = Integer.MAX_VALUE;
        alreadyComputed[inputArea] = true;
        for (int i = 1; i <= Math.sqrt(inputArea); i++) {
            dp[inputArea] = Math.min(dp[inputArea], findCakeNumsByAreaDp(inputArea - i * i) + 1);
        }
        return dp[inputArea];
    }
}
```
这种方法在使用前，需要提前将1~10000计算下，得到dp这个数组，然后就可以计算给的数据集。

这儿我想介绍一种很简单的方法，就是根据拉格朗日四平方和定理，即任何一个正整数都可以表示成不超过四个整数的平方之和。如果知道这个定理，那这个题目就简单了。代码如下：
```Java
public static int findCakeNumsByArea(int area) {
    for (int i = 1; i <= Math.sqrt(area); i++) {
        if(i * i == area) return 1;
    }
    for (int i = 1; i <= Math.sqrt(area); i++) {
        for (int j = i; j <= Math.sqrt(area); j++) {
            if (i * i + j * j == area) return 2;
        }
    }
    for (int i = 1; i <= Math.sqrt(area); i++) {
        for (int j = i; j <= Math.sqrt(area); j++) {
            for (int k = j; k <= Math.sqrt(area); k++) {
                if (i * i + j * j + k * k == area) return 3;
            }
        }
    }
    return 4;
}
```

### 总结
以上的所有题目的完整代码可在[2017-round-F](https://github.com/cutoutsy/google-kickstart/tree/master/2017-round-F)下载。总的来说，这次题目难度不大，考了一些基础了算法，比如快速排序算法，最短路算法等，还有涉及了较多的动态规划，平时还需要多多练习基础算法，多多刷题。

欢迎大家批评指正与交流。
