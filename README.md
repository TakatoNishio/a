# 一次元拡散方程式シミュレータの結果およびその妥当性評価

## 0. 一次元拡散方程式シミュレータの結果

図1には

$$
\frac{\partial u}{\partial t}=D\frac{\partial^2 u}{\partial x^2}
$$

の形で表される、物理量 $u$ の一次元拡散方程式の初期状態の
スナップショットを示す。

ここで、$t$ は時間であり単位は秒、$D$ は拡散係数であり単位は
$\mathrm{m^2/s}$ である。

また、このとき初期条件からピーク位置は

$$
x=x_0=5\ \mathrm{m}
$$

であり、ピーク高さは

$$
u_{\max}(0)=1
$$

である。ここでは物理量 $u$ は無次元量とする。

|<img src="figure/initial-condition.png" width="600">|
|:--:|
| 図1: 初期条件のスナップショット。縦軸 $u$、横軸 $x$。|

図2には $t\approx5\ \mathrm{s}$ および $t=10\ \mathrm{s}$ での
スナップショットを示す。

|<img src="figure/t-5s.png" width="400"><img src="figure/t-10s.png" width="400">|
|:--:|
| 図2: 異なる時間での数値計算の結果。$t\approx5\ \mathrm{s}$（左）と $t=10\ \mathrm{s}$（右）。縦軸 $u$、横軸 $x$。|

これらから、拡散方程式を解いた結果、時間の経過とともにピーク幅は
広がり、ピーク高さは減少した。

以下では、この二点を解析解と比較することにより、数値計算の妥当性を
検証する。


## 1. 拡散による物理的時間発展の確認

一次元拡散方程式

$$
\frac{\partial u}{\partial t}
=
D\frac{\partial^2 u}{\partial x^2}
$$

に対し、シミュレータでは初期条件として

$$
u(x,0)
=
\exp\left[
-\frac{(x-x_0)^2}{2\sigma_0^2}
\right]
$$

というGaussian分布を与えている。

この初期条件に対する無限領域の解析解は

$$
u(x,t)
=
\frac{\sigma_0}
{\sqrt{\sigma_0^2+2Dt}}
\exp\left[
-\frac{(x-x_0)^2}
{2(\sigma_0^2+2Dt)}
\right]
$$

である。

この解析解は、時刻 $t$ においてもGaussian分布の形を保っており、
その分散は

$$
\sigma^2(t)
=
\sigma_0^2+2Dt
$$

で与えられる。

したがって、$D>0$ では $\sigma^2(t)$ が時間とともに増加し、
Gaussian分布の幅が広がることが分かる。

一方、Gaussianの中心 $x=x_0$ では

$$
x-x_0=0
$$

であるため、指数関数部分は

$$
\exp(0)=1
$$

となる。

したがってピーク高さは

$$
u_{\max}(t)
=
u(x_0,t)
=
\frac{\sigma_0}
{\sqrt{\sigma_0^2+2Dt}}
$$

で与えられる。

一次元拡散方程式シミュレータでは

$$
x_0=5.0\ \mathrm{m},
\qquad
\sigma_0=1.0\ \mathrm{m}
$$

を用いている。また、標準設定として

$$
D=0.20\ \mathrm{m^2/s}
$$

を用いる。

したがって、$t=10\ \mathrm{s}$ における分散は

$$
\begin{aligned}
\sigma^2(10)
&=
\sigma_0^2+2Dt\\
&=
(1.0\ \mathrm{m})^2
+
2(0.20\ \mathrm{m^2/s})(10\ \mathrm{s})\\
&=
5.0\ \mathrm{m^2}
\end{aligned}
$$

となる。

初期状態では

$$
\sigma_0^2=1.0\ \mathrm{m^2}
$$

であったため、$t=10\ \mathrm{s}$ では分散が
$5.0\ \mathrm{m^2}$ まで増加している。

これは拡散によるGaussian分布の幅の広がりを表している。

また、ピーク高さの解析値は

$$
u_{\max,\mathrm{exact}}(10)
=
\frac{\sigma_0}
{\sqrt{\sigma_0^2+2Dt}}
=
\frac{1.0\ \mathrm{m}}
{\sqrt{5.0\ \mathrm{m^2}}}
=
\frac{1}{\sqrt5}
\approx
0.45
$$

と求められる。

なお、ここでの手計算は有効数字2桁で評価している。

ここで、分子と分母はいずれも長さの次元を持つため、その比である
$u_{\max}$ は無次元である。

同じ $D=0.20\ \mathrm{m^2/s}$、$t=10\ \mathrm{s}$ の条件で
一次元拡散方程式シミュレータを実行すると、数値計算から得られる
中心最大値は

$$
u_{\max,\mathrm{num}}
=
0.4472042017
$$

であった（図2の $t=10\ \mathrm{s}$ のスナップショットより）。

したがって、$u_{\max,\mathrm{num}}$ を数値計算によるピーク高さ、
$u_{\max,\mathrm{exact}}$ をピーク高さの解析値とすると、
有効数字2桁で評価した解析値との相対差は

$$
E
=
\frac{
\left|
u_{\max,\mathrm{num}}
-
u_{\max,\mathrm{exact}}
\right|
}{
u_{\max,\mathrm{exact}}
}
$$

で定義できる。

実際に代入すると

$$
E
=
\frac{
|0.4472042017-0.45|
}{
0.45
}
\approx
6.2\times10^{-3}
$$

となる。

百分率では

$$
E\approx0.62\%
$$

である。

以上から、Gaussianの幅の増大とピーク高さの低下という
一次元拡散方程式の基本的な時間発展について、数値計算結果は
解析的に予想される挙動とよく整合している。


## 2. 数値解と解析解を実際に重ねた確認

最後に、図3では実際に計算した数値解とGaussian解析解を
同一グラフ上に重ねて比較した。

ここで、数値解は青色実線、解析解は橙色破線で示す。

|<img src="figure/initial-condition-ana.png" width="400"><img src="figure/t-10s-ana.png" width="400">|
|:--:|
| 図3: 解析解と数値解の比較。初期条件（左）と $t=10\ \mathrm{s}$（右）。青色実線：数値解、橙色破線：解析解。|

数値解と比較する解析解は

$$
u_{\mathrm{exact}}(x,t)
=
\frac{\sigma_0}
{\sqrt{\sigma_0^2+2Dt}}
\exp\left[
-\frac{(x-x_0)^2}
{2(\sigma_0^2+2Dt)}
\right]
$$

である。

標準設定

$$
D=0.20\ \mathrm{m^2/s},
\qquad
N=181,
\qquad
t=10\ \mathrm{s}
$$

において数値解と解析解を比較した結果、両曲線はグラフ上で
ほぼ重なって表示された。

さらに、解全体の差を定量的に評価するため、相対 $L_2$ 誤差を

$$
E_{L_2}
=
\frac{
\sqrt{
\displaystyle\sum_i
\left(
u_{\mathrm{num},i}
-
u_{\mathrm{exact},i}
\right)^2
}
}{
\sqrt{
\displaystyle\sum_i
u_{\mathrm{exact},i}^{\,2}
}
}
$$

と定義した。

ここで、
$u_{\mathrm{num},i}$ は格子点 $i$ における数値解、
$u_{\mathrm{exact},i}$ は同じ格子点・同じ時刻における解析解である。

計算終了時の $t=10\ \mathrm{s}$ では

$$
E_{L_2}
=
1.841\times10^{-5}
$$

であった。

したがって、単にグラフの形が似ているだけではなく、
数値的にも解析解との差は非常に小さいことが確認できる。

以上より、実際に一次元拡散方程式を解いた結果と解析解を重ねて比較した際、
両者はよく一致していた。

前節で確認したGaussianの理論的な幅の増大およびピーク高さの低下とも
整合していることから、今回検証した条件では、一次元拡散方程式
シミュレータは拡散方程式の時間発展を適切に計算できていると判断できる。
