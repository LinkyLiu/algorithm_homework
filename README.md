# 高级算法期末大作业
2022202210081 刘歌灵

## 1 实验要求
<br>物流公司在流通过程中，需要将打包完毕的箱子装入到一个货车的车厢中，为了提高物流效率，需要将车厢尽量填满，显然，车厢如果能被100%填满是最优的，但通常认为，车厢能够填满85%，可认为装箱是比较优化的。
<br>设车厢为长方形，其长宽高分别为L，W，H；共有n个箱子，箱子也为长方形，第i个箱子的长宽高为li，wi，hi（n个箱子的体积总和是要远远大于车厢的体积），做以下假设和要求：
<br>1. 长方形的车厢共有8个角，并设靠近驾驶室并位于下端的一个角的坐标为（0,0,0），车厢共6个面，其中长的4个面，以及靠近驾驶室的面是封闭的，只有一个面是开着的，用于工人搬运箱子；
<br>2. 需要计算出每个箱子在车厢中的坐标，即每个箱子摆放后，其和车厢坐标为（0,0,0）的角相对应的角在车厢中的坐标，并计算车厢的填充率。

### 基础部分：
<ol>
<li>所有的参数为整数；</li>
<li>静态装箱，即从n个箱子中选取m个箱子，并实现m个箱子在车厢中的摆放（无需考虑装箱的顺序，即不需要考虑箱子从内向外，从下向上这种在车厢中的装箱顺序）；</li>
<li>所有的箱子全部平放，即箱子的最大面朝下摆放；</li>
<li>算法时间不做严格要求，只要1天内得出结果都可</li>
</ol>

### 高级部分：
<ol>
<li>参数考虑小数点后两位；</li>
<li>实现在线算法，也就是箱子是按照随机顺序到达，先到达先摆放；</li>
<li>需要考虑箱子的摆放顺序，即箱子是从内到外，从下向上的摆放顺序；</li> 
<li>因箱子共有3个不同的面，所有每个箱子有3种不同的摆放状态；</li>
<li>算法需要实时得出结果，即算法时间小于等于2秒。</li>
</ol>

## 2 基础部分算法
### 2.1 算法说明
算法整体的思路是贪心+DFS。在基础部分由于可以获得所有箱子的型号数据，所以先对箱子按体积由大到小排序，然后依次按照由内向外，由下到上的顺序放入。更细节的部分在下方结合图片进行说明。
下面是使用[(91,54,45,32), (105,77,72,24), (79,78,48,30)]的货物型号作为示例，使用matplotlib绘制的车厢示意图。
<br>1.首先初始化车厢。大小是587\*233\*220。<br>

<img src='/figs/图片1.png' height=300px >

<br>2.放入第一个物品。红色圆圈圈出来的地方是物品的放置点。对于第一个物品，它的放置点是(0,0,0)。将它放下之后，这个已经被使用的放置点从列表中删去，然后将新产生的3个放置点加入列表中，用蓝色圆圈圈出。三个放置点分别是来自刚刚放下的物品，以使用的放置点为顶点相连的3条边的端点。<br>

<img src='/figs/图片2.png' height=300px >
<img src='/figs/图片3.png' height=300px >

<br>3.由于老师给的数据是，同一型号的货物有多个，按照常识，同型号的货物整齐堆叠会减少空间的浪费。所以按照由内向外，由下向上的顺序摆放货物。第二个货物在选择放置点时，将优先使用x,y值更小的放置点(x,y,z)。所以第二个货物会叠在第一个货物上面，如下图所示。此时也会产生新的放置点，同样加入列表。<br>

<img src='/figs/图片4.png' height=300px >

<br>4.当在z这个维度上已经没有空间继续放入物品时，会优先选择保持x，y增加的放置点。而当在y维度上也没有剩余空间时，才考虑x增大的放置点，这样就能确保由内向外，由下向上的摆放顺序。<br>

<img src='/figs/图片5.png' height=300px >
<img src='/figs/图片6.png' height=300px >

<br>5.按照这种规则一直放入物品<br>

<img src='/figs/图片7.png' height=300px >
<br>从俯视图可以看到是不会产生类似洞穴一样的空间。<br>

<img src='/figs/图片8.png' height=300px >
<br>6.当按照同一方向不能再放下物品的时候<br>

<img src='/figs/图片9.png' height=300px >
<br>将物品的长宽调转，继续进行尝试。如果这样也不行，就跳过这个物品，转而尝试尺寸更小的货物。<br>

<img src='/figs/图片10.png' height=300px >

<br>7.这样就是物品最终摆放的样子<br>
<img src='/figs/图片11.png' height=300px >

### 2.2 代码说明
<ul>
<li>box(ax,x, y, z, dx, dy, dz, color='red')</li>
<li>make_pic(Items)</li>
<li>make(O,C,color)</li>
</ul>
<p>以上三个为画示意图的函数，通过调用make_pic()画图。参数为列表，列表项为(放置点坐标，货物尺寸，颜色)。</p>

<ul>
<li>class TRUCK()</li>
</ul>
<p>车厢类，属性有长宽高、体积、空间。空间是用来标示坐标点有没有被占用，用来防止货品相互堆叠。其中TRUCK.put用来将物品放入车厢。</p>

<ul>
<li>class BOX()</li>
</ul>
<p>货品类，属性有长宽高和位置</p>

<ul>
<li>init_boxlist(config)</li>
</ul>
<p>根据设置的物品配置，初始化物品列表。注意这里要将物品按体积由大到小进行排序。</p>

<ul>
<li>main()</li>
</ul>
<p>主函数，完成算法功能。因为题干要求是最大面朝下，所以这里改变物体摆放位置只考虑长宽的调转。注意这里将可放置点的排序很关键，这是能够保证货物按照我们希望的由内到外、由上到下整齐堆叠的关键。</p>

### 2.3 实验结果
#### 1.算法时间：
计算完成以下表格中的24种货物配置所需的总时间不到10分钟，是完全满足要求的。

#### 2.填充率：

<table>
    <tr>
        <th>箱子种类</th> <th>放入物品数</th> <th>车厢填充率</th> 
    </tr>
    <tr>
        <td>E1-1</td> <td>93</td> <td>0.832773228774574</td>
    </tr>
    <tr>
        <td>E1-2</td> <td>60</td> <td>0.7983367021584188</td>
    </tr>
    <tr>
        <td>E1-4</td> <td>131</td> <td>0.8816362586167589</td>
    </tr>
    <tr>
        <td>E1-5</td> <td>127</td> <td>0.794407905450451</td>
    </tr>
    <tr>
        <td>E2-1</td> <td>55</td> <td>0.7645999517441563</td>
    </tr>
    <tr>
        <td>E2-2</td> <td>91</td> <td>0.874696456784765</td>
    </tr>
    <tr>
        <td>E2-3</td> <td>68</td> <td>0.750021735070100</td>
    </tr>
    <tr>
        <td>E2-4</td> <td>84</td> <td>0.770184003653087</td>
    </tr>
    <tr>
        <td>E2-5</td> <td>68</td> <td>0.8231758327290275</td>
    </tr>
    <tr>
        <td>E3-1</td> <td>63</td> <td>0.7567784505088466</td>
    </tr>
    <tr>
        <td>E3-2</td> <td>89</td> <td>0.73923665370317074</td>
    </tr>
    <tr>
        <td>E3-3</td> <td>71</td> <td>0.7876127382133773</td>
    </tr>
    <tr>
        <td>E3-4</td> <td>83</td> <td>0.8391708502799304</td>
    </tr>
    <tr>
        <td>E3-5</td> <td>119</td> <td>0.8169066276011462</td>
    </tr>
    <tr>
        <td>E4-1</td> <td>91</td> <td>0.8289947496844426</td>
    </tr>
    <tr>
        <td>E4-2</td> <td>99</td> <td>0.8006484628253863</td>
    </tr>
    <tr>
        <td>E4-3</td> <td>94</td> <td>0.806750833011517</td>
    </tr>
    <tr>
        <td>E4-4</td> <td>115</td> <td>0.8128755697147388</td>
    </tr>
    <tr>
        <td>E4-5</td> <td>95</td> <td>0.8026235293101076</td>
    </tr>
    <tr>
        <td>E5-1</td> <td>82</td> <td>0.7830353125097624</td>
    </tr>
    <tr>
        <td>E5-2</td> <td>93</td> <td>0.782061887122536</td>
    </tr>
    <tr>
        <td>E5-3</td> <td>105</td> <td>0.8200687147262079</td>
    </tr>
    <tr>
        <td>E5-4</td> <td>87</td> <td>0.7394454300187241</td>
    </tr>
    <tr>
        <td>E5-5</td> <td>98</td> <td>0.8163267930934323</td>
    </tr>
    <tr>
        <td>平均值</td> <td></td> <td>0.800932</td>
    </tr>
</table>



#### 3.结果分析：
在所给出的所有箱子种类上，填充率平均值为80.09%。达到了80%以上。从表格中也可以看到，当物品整体尺寸比较小，如E1-4、E2-2的配置下，填充率是会显著更高的。因为当物品体积大的时候，难以避免的会导致车厢内一些空间不能被任何物品填充。
实际上这里受到最大面朝下条件的限制，导致还有一些空间不能被利用。如果破除这种限制，填充率可以进一步得到提升。我进行了尝试性的实验，仅再添加一种可选的摆放姿势，就可使填充率从80.09%提升到80.94%。

## 3 高级部分算法
### 3.1算法说明
和基础部分算法的整体思路是一致的。但是有三点不同。一是在对小数的处理上，直接向上取整作为箱子的尺寸。二是随机生成箱子的尺寸，不进行箱子体积的排序，以满足随机到达，先到达先摆放的要求。三是允许其他的摆放姿势，不要求最大面朝下。
<br>这样的效果是，箱子会先从底部开始摆放。

<img src='/figs/图片13.png' height=300px >
<br>最终的车厢摆放效果如下图所示

<img src='/figs/图片14.png' height=300px >
### 3.2实验结果
<br>对于计算时间，绝大多数的箱子位置计算可以在2秒内完成，有极少部分超过2秒的也能在5秒内完成计算。
<br>对于在这个范围(长40.0～100.0，宽20.0～80.0，高20.0～80.0)内生成的箱子，摆放位置的输出如下。(物品的尺寸是向上取整的结果)	
<br>车厢的填充率为0.7322892213145259。因为直接暴力取整是会带来空间浪费的。
物品:(79, 73, 64),位置:[0, 0, 0]
物品:(73, 67, 41),位置:(0, 0, 64)
物品:(96, 55, 45),位置:(0, 0, 105)
物品:(100, 60, 50),位置:(0, 0, 150)
物品:(57, 53, 52),位置:(0, 60, 150)
物品:(99, 63, 52),位置:(0, 67, 64)
物品:(70, 52, 42),位置:(0, 73, 0)
物品:(92, 50, 34),位置:(0, 67, 116)
物品:(70, 63, 39),位置:(0, 113, 150)
物品:(66, 65, 33),位置:(0, 117, 116)
物品:(78, 74, 69),位置:(70, 113, 150)
物品:(72, 56, 50),位置:(0, 125, 0)
物品:(61, 54, 27),位置:(0, 113, 189)
物品:(62, 57, 33),位置:(0, 130, 64)
物品:(64, 63, 23),位置:(0, 167, 189)
物品:(78, 69, 43),位置:(62, 130, 64)
物品:(79, 74, 52),位置:(72, 125, 0)
物品:(85, 49, 45),位置:(57, 60, 150)
物品:(95, 66, 41),位置:(73, 0, 64)
物品:(62, 57, 44),位置:(79, 0, 0)
物品:(71, 65, 55),位置:(79, 57, 0)
物品:(86, 66, 49),位置:(140, 130, 64)
物品:(75, 49, 47),位置:(99, 67, 64)
物品:(69, 44, 32),位置:(0, 176, 150)
物品:(73, 47, 32),位置:(0, 182, 116)
物品:(83, 69, 46),位置:(148, 113, 150)
物品:(75, 73, 55),位置:(151, 125, 0)
物品:(99, 68, 55),位置:(150, 57, 0)
物品:(73, 57, 41),位置:(96, 0, 105)
物品:(76, 72, 71),位置:(226, 125, 0)
物品:(69, 43, 37),位置:(0, 181, 0)
物品:(61, 46, 30),位置:(0, 187, 64)
物品:(63, 51, 23),位置:(0, 181, 37)
物品:(95, 75, 38),位置:(226, 125, 71)
物品:(91, 80, 35),位置:(140, 130, 113)
物品:(89, 37, 30),位置:(70, 187, 150)
物品:(94, 52, 26),位置:(92, 67, 116)
物品:(94, 79, 28),位置:(231, 113, 150)
物品:(91, 53, 37),位置:(100, 0, 150)
物品:(53, 49, 27),位置:(73, 182, 116)
物品:(54, 49, 36),位置:(141, 0, 0)
物品:(77, 60, 57),位置:(191, 0, 150)
物品:(64, 52, 30),位置:(100, 0, 187)
物品:(67, 57, 48),位置:(174, 67, 64)
物品:(72, 66, 45),位置:(169, 0, 105)
物品:(97, 76, 53),位置:(268, 0, 150)
物品:(54, 48, 45),位置:(142, 60, 150)
物品:(83, 47, 28),位置:(141, 0, 36)
物品:(66, 48, 25),位置:(57, 60, 195)
物品:(75, 69, 66),位置:(302, 125, 0)
物品:(75, 61, 46),位置:(249, 57, 0)
物品:(84, 63, 39),位置:(168, 0, 64)
物品:(67, 46, 42),位置:(159, 187, 150)
物品:(98, 79, 40),位置:(231, 113, 178)
物品:(56, 50, 46),位置:(196, 60, 150)
物品:(99, 73, 55),位置:(252, 0, 64)
物品:(80, 65, 48),位置:(321, 125, 71)
物品:(67, 50, 23),位置:(123, 60, 195)
物品:(71, 50, 38),位置:(252, 73, 64)
物品:(63, 57, 53),位置:(324, 57, 0)
物品:(74, 69, 21),位置:(148, 113, 196)
物品:(46, 41, 38),位置:(70, 187, 180)
物品:(94, 65, 60),位置:(351, 0, 64)
物品:(78, 71, 57),位置:(365, 0, 150)
物品:(66, 54, 44),位置:(351, 65, 64)
物品:(80, 53, 37),位置:(231, 130, 113)
物品:(92, 64, 42),位置:(329, 113, 178)
物品:(74, 30, 23),位置:(62, 199, 64)
物品:(77, 49, 37),位置:(231, 183, 113)
物品:(64, 43, 30),位置:(186, 67, 116)
物品:(91, 68, 58),位置:(377, 125, 0)
物品:(61, 45, 38),位置:(252, 73, 102)
物品:(85, 71, 64),位置:(401, 125, 71)
物品:(57, 53, 52),位置:(387, 57, 0)
物品:(69, 57, 53),位置:(417, 65, 64)
物品:(77, 74, 42),位置:(401, 125, 135)
物品:(76, 88, 24),位置:(321, 125, 119)
物品:(87, 56, 55),位置:(443, 0, 150)
物品:(84, 52, 39),位置:(329, 177, 178)
物品:(74, 60, 51),位置:(443, 56, 150)
物品:(71, 57, 51),位置:(444, 57, 0)
物品:(83, 67, 37),位置:(468, 125, 0)
物品:(93, 52, 46),位置:(445, 0, 64)
物品:(87, 53, 34),位置:(195, 0, 0)
物品:(43, 39, 28),位置:(92, 119, 116)
物品:(81, 75, 43),位置:(478, 125, 135)
物品:(52, 48, 46),位置:(282, 0, 0)
物品:(91, 56, 49),位置:(334, 0, 0)
物品:(64, 51, 44),位置:(425, 0, 0)
物品:(93, 60, 48),位置:(486, 65, 64)
物品:(65, 55, 49),位置:(486, 125, 64)
物品:(49, 39, 33),位置:(231, 192, 150)
物品:(94, 36, 30),位置:(226, 197, 0)
物品:(55, 53, 21),位置:(196, 60, 196)
物品:(56, 33, 27),位置:(62, 199, 87)
物品:(66, 64, 49),位置:(515, 57, 0)
物品:(98, 52, 37),位置:(413, 177, 178)
物品:(71, 67, 25),位置:(252, 0, 119)
物品:(68, 58, 37),位置:(445, 0, 110)
物品:(57, 77, 70),位置:(530, 0, 150)
物品:(68, 56, 56),位置:(489, 0, 0)
物品:(44, 57, 57),位置:(538, 0, 64)
物品:(53, 40, 29),位置:(231, 192, 183)
物品:(98, 44, 35),位置:(478, 125, 178)
物品:(57, 86, 23),位置:(325, 113, 150)
物品:(38, 49, 47),位置:(313, 73, 102)
物品:(77, 61, 21),位置:(351, 0, 124)
物品:(71, 56, 28),位置:(417, 65, 117)
物品:(51, 50, 44),位置:(486, 180, 64)
物品:(98, 52, 29),位置:(488, 65, 117)
物品:(73, 54, 41),位置:(511, 177, 178)
物品:(60, 37, 25),位置:(140, 196, 64)
物品:(35, 77, 66),位置:(551, 125, 0)
物品:(74, 37, 22),位置:(0, 73, 42)
物品:(53, 42, 38),位置:(321, 190, 71)
物品:(76, 41, 22),位置:(148, 182, 196)
物品:(70, 47, 21),位置:(468, 125, 37)
物品:(66, 25, 21),位置:(72, 199, 0)
物品:(95, 49, 24),位置:(486, 180, 108)
物品:(52, 42, 33),位置:(351, 65, 108)
物品:(65, 54, 23),位置:(468, 172, 37)
物品:(51, 42, 22),位置:(0, 187, 94)
物品:(35, 52, 51),位置:(551, 125, 66)
物品:(50, 35, 29),位置:(151, 198, 0)
物品:(78, 35, 25),位置:(226, 197, 30)
物品:(85, 39, 32),位置:(377, 193, 0)
物品:(54, 43, 27),位置:(224, 0, 36)
物品:(22, 58, 22),位置:(66, 117, 116)
物品:(53, 48, 29),位置:(530, 77, 150)
物品:(49, 23, 23),位置:(72, 199, 21)
物品:(98, 37, 30),位置:(268, 76, 150)
物品:(23, 72, 42),位置:(559, 125, 135)
物品:(46, 58, 23),位置:(538, 0, 121)
物品:(48, 43, 23),位置:(530, 77, 179)
物品:(67, 33, 32),位置:(151, 198, 29)
物品:(69, 40, 33),位置:(377, 193, 32)
物品:(52, 39, 29),位置:(462, 193, 0)
物品:(79, 36, 23),位置:(140, 196, 89)
物品:(61, 46, 22),位置:(486, 125, 113)
物品:(22, 49, 45),位置:(557, 0, 0)
物品:(86, 35, 31),位置:(268, 76, 180)
物品:(82, 30, 30),位置:(226, 200, 71)
物品:(77, 37, 33),位置:(354, 76, 180)
物品:(76, 37, 30),位置:(366, 76, 150)
物品:(55, 35, 32),位置:(304, 197, 30)
物品:(25, 51, 38),位置:(323, 73, 64)
物品:(59, 35, 28),位置:(401, 196, 71)
物品:(45, 41, 28),位置:(280, 192, 150)
物品:(61, 32, 21),位置:(325, 199, 150)
物品:(26, 58, 33),位置:(164, 0, 187)
物品:(25, 64, 30),位置:(323, 0, 119)
物品:(30, 43, 26),位置:(116, 187, 180)
物品:(51, 33, 24),位置:(320, 197, 0)
物品:(34, 47, 40),位置:(551, 177, 66)
物品:(58, 31, 30),位置:(401, 196, 99)
物品:(45, 26, 21),位置:(284, 192, 183)
物品:(56, 28, 27),位置:(386, 199, 150)
物品:(68, 24, 21),位置:(478, 200, 135)
物品:(26, 42, 21),位置:(374, 190, 71)
