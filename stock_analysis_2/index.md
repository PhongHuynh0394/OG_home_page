# Stock Analysis P2


<!--more-->

{{< admonition warning >}}
Đây là kiến thức tích góp từ nhiều nguồn và tìm hiểu của nhóm OG, tất nhiên không thể tránh khỏi sai sót. 
Hy vọng bài viết lần này thú vị và giúp bạn đọc thư giãn, tham khảo.
{{< /admonition >}}

{{< friend name="PhongHuynh0394" avatar="/img/github-logo.png" bio="Stock-Analysis" url="https://github.com/PhongHuynh0394/Stock-Analysis" >}}

Hé lô hé lô là OG đâyy, ở phần 1, ta đã giảm chiều dữ liệu bằng PCA rồi, tiếp đến phần này, chúng ta sẽ dùng K-means để phân cụm rồi tìm ra điểm
chung của dữ liệu nhé, cuối cùng là phân tích các "sự kiện" đã diễn ra trong từng cụm. Lét gô !

Một lần nữa xin cảm ơn thầy Nguyễn Hoàng Đức và thầy Ngô Minh Mẫn đã hỗ trợ để đồ án được hoàn thiện. Đồng thời cảm ơn các thành viên nhóm 7
đã cùng làm việc hết mình.

## K-Means Clustering
Nhắc lại chút xíu, ở [Phần 1](/stock_analysis/) chúng ta đã dùng PCA làm giảm chiều dữ liệu nhưng xét về mặt ý nghĩa, dữ liệu sau PCA chưa
thể phân tích được mà ta sẽ chỉ dùng nó để áp vào một kỹ thuật tiếp theo. Kỹ thuật này sẽ "gộp nhóm" dữ liệu và trả lời câu hỏi "**làm thể nào để phân dữ liệu thành các cụm (cluster) khác nhau, sao cho dữ liệu trong cùng một cụm có tính chất giống nhau?**"

Bản chất của việc phân nhóm này dựa trên những biến động, sự kiện nào đó trên thị trường mà chúng ta chưa biết nhưng nó chi phối trực tiếp hay gián tiếp, nhiều hay ít đến với các cổ phiếu có liên quan.
Chính vì lý do đó, chúng ta tiến hành sử dụng thuật toán K-Means Clustering để phân cụm dữ liệu phục vụ mục tiêu đề ra.

Trong bài này chúng ta sẽ thực hiện thuật K-means `step by step`, nào bắt đầu thoaii :smile:

### K-Means step by step

**Step 1: Lựa chọn số clusters $K$**
```python
# initialize labels
N, d = X_pca.shape
pre_labels = np.zeros((N, 1))

# Hyper-parameters
K_CLUSTERS = 3 # << N
```
**Step 2: Chọn K điểm ngẫu nhiên từ dữ liệu làm trọng tâm**
```python
import random
# random K-samples to be centroids
k_indices = random.sample(range(0, N), K_CLUSTERS)
centroids = X_pca[k_indices]    # shape: (K, d)
```
**Step 3: Gán tất cả các điểm cho tâm cụm gần nhất**

Hàm `assign_cluster()` giúp ta phân mỗi điểm dữ liệu vào cluster có center gần nó nhất với K điểm bất kỳ được chọn làm các center ban đầu
```python
def assign_cluster(distances: np.ndarray) -> np.ndarray:
    # distances: (N, K)
    return np.argmin(distances, axis=1) # return min value from distances array
```
Cụ thể:
- Mảng **distances** chứa khoảng cách từ điểm $i$ đến cụm $k$, đây là khoảng cách Euclid với công thức distance$(x,y)=||x-y||$
- Hàm **np.argmin()** trả về giá trị của biến số để hàm số đó đạt giá trị nhỏ nhất. Nói cách khác, nó giúp chúng ta tìm ra được vị trí mà điểm dữ liệu phải thuộc về dựa trên quy tắc gần cụm nào nhất thì chọn cụm đó. Ví dụ:
```python
# Example for np.argmin()
       # k =   0  1  2
D = np.array([[1, 0, 3],   # min = 0 and it locate in 1 -> x_1 y_1 = 1
              [-1, 2, 1]]) # min =-1 and it locate in 0 -> x_2 y_2 = 0

np.argmin(D, axis=1) # --> array([1,0])
```
**Step 4: Tính toán lại các trọng tâm của các cụm mới được hình thành**
Hàm **update_centroids()** dùng cập nhật lại tâm cụm và trả về 1 bộ các tâm cụm mới bằng cách lấy trung bình cộng của tất các các điểm dữ liệu đã được gán vào cluster đó.
```python
def update_centroids(X, labels):
    new_centroids = np.array([X[labels == k].mean(axis=0) for k in range(K_CLUSTERS)])
    return new_centroids # (3, 13)
```
**Cuối cùng: lặp lại step 3 và step 4**
{{< admonition question >}}
**Khi nào thì bài toán hội tụ vậy OG ? Khi nào thì chúng ta mới dừng lại thuật toán ?**

--> Câu trả lời là khi việc phân cụm không còn sự thay đổi nào nữa hoặc giá trị *hàm mất mát* không thay đổi nhiều sau mỗi lần update tâm cụm.
{{< /admonition >}}
Chúng ta sẽ viết một hàm kiểm tra tính hội tụ của bài toán là **has_convert()**. Hàm này kiểm tra cụm trước và sau có giống nhau không.
```python
# Check convergence
def has_convert(pre_labels: np.ndarray, cur_labels: np.ndarray) -> bool: 
    return (pre_labels == cur_labels).all()
```
Hàm **get_total_wcv()** là một hàm mất mát, tính tổng các phương sai bên trong của 1 cluster

Nếu ta coi $m_k$ là center (representation) của mỗi cluster và ước lượng tất cả các điểm được phân vào cluster này bởi $m_k$, thì
một điểm dữ liệu $x_i$ được phân vào cluster $k$ sẽ bị sai số là $(x_i-m_k)$. Chúng ta mong muốn sai số này có trị tuyệt đối bé nhất nên ta sẽ tìm
cách để đại lượng sau đây đạt min: $||x_i - m_k||^2_2$
```python
def get_total_wcv(X, labels, centroids):
    # Total within cluster variance 
    WCVs = [
        np.sum(np.linalg.norm(X[labels == k] - centroids[k], axis=1) ** 2) \
        for k in range(K_CLUSTERS)
    ]
    return np.sum(WCVs)
```
Nhìn chung về điều kiện hội tụ có thể thấy mối liên hệ giữa các điều kiện là gần tương đồng như nhau.
Khi có ít điểm dữ liệu được gán sang cluster khác có thể khiến điểm trung tâm không thay đổi nhiều và từ đó hàm mất mát cũng sẽ ít bị ảnh hưởng.

### The K-means algorithm is written in object-oriented form
Ta sẽ kết hợp tất cả các bước thuật toán vào một đối tượng `KMeansClustering`
```python
import numpy as np
import pandas as pd
import random
import matplotlib.pyplot as plt
from scipy.spatial import distance

class KMeansClustering:
    """
        An instance of K-Means Clustering algorithm
    """
    def __init__(self, n_clusters=29):
        """
            n_clusters: number of clusters
            _centroids: center/ centroid of clusters
            inertia_: sum of squared distances of samples to their closest cluster center
            labels_: labels of input samples
            X: input data
        """
        self.n_clusters = n_clusters
    
    def fit(self, X: np.ndarray) -> None:
      """
          K-means execution
      """
      N, p = X.shape
      self.X = X

      # random K-samples to be centroids
      k_indices = random.sample(range(0, N), self.n_clusters)
      self._centroids = self.X[k_indices]

      # initialize labels
      pre_labels = np.zeros((N, 1))

      # training
      it = 0
      while True:
          # compute distances from X_i to
          distances = self._calc_dists(self.X, self._centroids)
          # assign new labels
          self.labels_ = self._assign_cluster(distances)  # assign new labels
          # check convergence
          if self._has_convert(pre_labels, self.labels_):
              break
          # update centroids
          self._update_centroids(self.X, self.labels_)
          pre_labels = self.labels_
          it += 1

      # compute total Within Cluster Variance (WCV)
      self.inertia_ = self._calc_total_WCV(self.X, self.labels_, self._centroids)

    def _calc_dists(self, X, centroids):
        return distance.cdist(X, centroids, "euclidean")

    def _assign_cluster(self, distances):
        return np.argmin(distances, axis=1)

    def _update_centroids(self, X, labels):
        self._centroids = np.array(
            [X[labels == k].mean(axis=0) for k in range(self.n_clusters)]
        )

    def _calc_total_WCV(self, X, labels, centroids):
        WCVs = [
            np.sum(np.linalg.norm(X[labels == k] - centroids[k], axis=1) ** 2)
            for k in range(self.n_clusters)
        ]
        return np.sum(WCVs)

    def _has_convert(self, pre_labels, cur_labels):
        return (pre_labels == cur_labels).all()

    def _predict(self, X_test):
        dist_test = self._calc_dists(X_test, self._centroids)
        test_labels = self._assign_cluster(dist_test)
        return test_labels

    def predict(self, X_test):
        return self._predict(X_test)
```
Trước khi thật sự áp dụng thuật toán này cho dataset của chúng ta, có một câu hỏi đặt ra là: **Thuật toán này thực hiện với đầu vào
là số $K$ tức là số lượng cluster, thế thì $K$ bằng bao nhiêu là tốt nhất?**
### Finding the optimal 'K' in a K-Means clustering
Có một phương pháp tên là `Elbow` aka cái khuỷa tay :smile:

Phương pháp `Elbow` là một cách giúp ta lựa chọn được số lượng các cụm phù hợp dựa vào đồ thị trực quan hoá bằng cách nhìn vào sự suy giảm của hàm biến dạng và lựa chọn ra điểm khuỷu tay (elbow point).

Đối với mỗi giá trị của $K$, ta tính toán **WCSS (Tổng bình phương trong cụm)**. WCSS là tổng bình phương khoảng cách giữa mỗi điểm và tâm trong một cụm.
```python
def elbow_method(X, k_clusters = list(range(1,9))):
    total_wcss = []
    for k in k_clusters:
        # Train with k cluster
        kmeans_model = KMeansClustering(n_clusters=k)
        kmeans_model.fit(X)

        # calculate WCSS
        total_wcss.append(kmeans_model.inertia_)

    plt.figure()
    plt.plot(k_clusters, total_wcss, marker='o', color='r')
    plt.ylabel('WCSS')
    plt.xlabel('Number of clusters K')
    plt.grid()
    plt.show()

elbow_method(X_pca)
```
Khi ta vẽ đồ thị WCSS với giá trị K, đồ thị trông giống như một khuỷu tay. Khi số cụm tăng lên, giá trị WCSS sẽ bắt đầu giảm. Giá trị WCSS lớn nhất khi $K=1$

{{< figure src="https://i.imgur.com/LaQvUnb.png" title="elbow_method" >}}

Chúng ta có thể thấy rằng biểu đồ sẽ thay đổi nhanh chóng tại $K=2$ và do đó tạo ra hình dạng khuỷu tay. Từ thời điểm này, đồ thị di chuyển gần như song song với trục $X$

Giá trị $K$ tương ứng với điểm này là giá trị tối ưu của $K$ hoặc cụm tối ưu.

### Evaluation Metrics of K-Means Clustering
`Silhouette Score`: cho chúng ta biết những điểm dữ liệu hay những quan sát nào nằm gọn bên trong cụm (tốt) hay nằm gần ngoài rìa cụm (không tốt) để đánh giá hiệu quả phân cụm.

Giả sử có 2 cluster A, B thì Silhouette score là:
$$ s_i = \frac{(b_i - a_i)}{max(b_i - a_i)} $$

Với $a_i, b_i$ lần lượt là khoảng cách của điể $i$ đến tâm cụm A và B

Giá trị này nằm trong khoảng [-1, 1]:
- Điểm dữ liệu có Silhouette cao, gần bằng 1: nằm đúng trong cluster.
- Điểm dữ liệu có Silhouette gần bằng 0: nằm giữa 2 cluster
- Điểm dữ liệu có Silhouette thấp, có giá trị âm: thì khả năng đã nằm sai cluster.
```python
from sklearn import metrics
kmeans_train = KMeansClustering(2)
kmeans_train.fit(X_pca)
labels = kmeans_train.labels_
print(f'Silhouette Score (n = 2): {metrics.silhouette_score(X_pca,labels)}') #--> Silhouette Score (n = 2): 0.265365408366845
```
```python
kmeans_train = KMeansClustering(3)
kmeans_train.fit(X_pca)
labels = kmeans_train.labels_
print(f'Silhouette Score (n = 3): {metrics.silhouette_score(X_pca,labels)}') # --> Silhouette Score (n = 3): 0.22528216696570613
```
Ở Phương pháp Elbow, chúng ta cũng đã thấy được $K$ tối ưu cho bài toán chính là khi chọn $K=2$. 
 Nhưng ở bước này, ta cũng thấy được rõ ràng hơn giữa 2 sự lựa chọn $K=2$ và $K=3$  bằng cách so sánh chỉ số Silhouette giữa bọn chúng.

So sánh: $0.265(K=2) > 0.225(K=3)$

Từ đó, một lần nữa ta thấy được $K$ tối ưu nhất cho bài toán này là $K=2$

### Visualize
Sau tất cả các bước trên, dữ liệu đã được chia thành 2 label chính tương ứng với từng màu sắc được biểu thị dưới hình vẽ sau đây:
```python
# K means
import plotly.express as px

K_CLUSTERS = 2
kmeans_train = KMeansClustering(K_CLUSTERS)
kmeans_train.fit(X_pca)

fig = px.scatter_matrix(
    X_pca,
    labels=pca_scree,
    dimensions=range(4),
    color=kmeans_train.labels_
)
fig.update_traces(diagonal_visible=False)
fig.show()
```
{{< figure src="https://i.imgur.com/affWhfh.png" title="Kmeans Clustering" >}}

Sau khi chia cụm, chúng ta đến với bước cuối cùng thôi

## Data Analysis
Sau khi K-means, ta có được một tập nhãn (labels), ta sẽ gán các nhãn này vào bộ data có giá trị và tiến hành phân tích từng cụm.
```python
# Data
#set label
value_data = new_market.copy()
value_data['label'] = kmeans_train.labels_
value_data['label'].replace({0: 'A', 1: 'B'}, inplace=True)

# get specific columns
gtb = [col for col in value_data.columns.to_list() if 'gttb_' in col]
mua = [col for col in value_data.columns.to_list() if 'total_mua' in col]
ban = [col for col in value_data.columns.to_list() if 'total_ban' in col]

# create new valuabel column
value_data['total_ban_30'] = value_data[ban].sum(axis=1)
value_data['total_mua_30'] = value_data[mua].sum(axis=1)
value_data['total_volume_30'] = value_data['total_ban_30'] + value_data['total_mua_30']
value_data['mua_ban_ratio'] = value_data['total_mua_30'] / value_data['total_ban_30']
```
value_data bao gồm các cột:
- label: nhãn nhận được từ K means (gồm 'A' và 'B')
- gttb_{mã}: Trung bình giá của một {mã} cổ phiếu
- total_mua_{mã}: Tổng khối lượng mua của {mã} cổ phiếu
- total_ban_{mã}: Tổng khối lượng bán của {mã} cổ phiếu
- total_mua_30: Tổng khối lượng mua của tất cả 30 cổ phiếu
- total_ban_30: Tổng khối lượng bán của tất cả 30 cổ phiếu
- total_volume_30: Tổng khối lượng giao dịch của 30 cổ phiếu (total_mua_30 + total_ban_30)
- mua_ban_ratio: tỉ số total_mua_30 / total_ban_30
- Gia KL: Giá phái sinh gần với VN30

Time-series Data theo phút (5154 dòng)

### Analytical Overview
```python
import plotly.graph_objects as go
import plotly.express as px
from plotly.subplots import make_subplots

fig = make_subplots(rows=2, cols=2, vertical_spacing=0.03)

# plot A
fig.add_trace(go.Bar(name='',x=value_data.index[value_data['label'] == 'A'], 
                     y=value_data['total_volume_30'][value_data['label'] == 'A']), row=1, col=1)

fig.add_trace(go.Scatter(x=value_data.index[value_data['label'] == 'A'], 
                         y=value_data['Gia KL'][value_data['label'] == 'A'], 
                         mode='lines'), row=2, col=1)


# plot B
fig.add_trace(go.Bar(name='',x=value_data.index[value_data['label'] == 'B'], 
                     y=value_data['total_volume_30'][value_data['label'] == 'B']), row=1, col=2)

fig.add_trace(go.Scatter(x=value_data.index[value_data['label'] == 'B'], 
                         y=value_data['Gia KL'][value_data['label'] == 'B'], 
                         mode='lines'), row=2, col=2)

# update layout
fig.update_layout(title_text="Stock Data Visualization", showlegend=False, barmode='stack')
fig.update_yaxes(title_text="Total Volume 30", row=1, col=1)
fig.update_yaxes(title_text="Gia KL", row=2, col=1)
fig.update_yaxes(title_text="Total Volume 30", row=1, col=2)
fig.update_yaxes(title_text="Gia KL", row=2, col=2)

fig.show()
```
{{< figure src="https://i.imgur.com/FRTgHhs.png" title="Stock Data Visualization" >}}
Cluster B chứa các dữ liệu trong giai đoạn từ **20/3 đến 6/4/2023**

**Nhìn nhận một cách tổng quát:**
- Đối với cluster B, thị trường luôn trong thế "giằng co" với khoảng 6 đợt giảm mạnh và chừng ấy đợt phục hồi liên tục trong suốt 18 ngày. Mặc dù GiaKL duy trì được đà tăng (Giá KL từ 1036 tăng đến 1080) tạo tích cực cho thị trường song thế giằng co vẫn có chiều hướng kéo dài.

Cluster A chứa các dữ liệu trong giai đoạn từ **1/4 đến 19/4/2023**:

**Nhìn nhận một cách tổng quát:**
- Đối với cluster A các khoảng giảm kéo dài trong nhiều ngày dẫn đến việc phục hồi gặp khó khăn đáng kể. Cụ thể, GiaKL có mức "vùng dậy" đến ngưỡng 1086 điểm tại 6/4 nhưng lại bị đẩy về 1054 vào cuối giai đoạn.

Ta sẽ phân tích sâu hơn từng cluster một

### Cluster B
```python
import plotly.express as px

gtb = [col for col in value_data.columns.to_list() if 'gttb_' in col]

fig = px.line(value_data[gtb][value_data['label'] == 'B'], y=gtb, title='Mean Values by Label B')
fig.update_layout(xaxis_title="Times", yaxis_title="Mean Values",
font=dict(family="Courier New, monospace", size=10, color="RebeccaPurple"))
fig.show()
```
{{< figure src="https://i.imgur.com/mZgwwZL.png" title="Mean Values by Label B" >}}

```python
import plotly.graph_objs as go

# Create first trace
trace1 = go.Bar(name='mua', x=value_data.index[value_data['label'] == 'B'], y=value_data['total_ban_30'][value_data['label'] == 'B'])

# Create second trace
trace2 = go.Bar(name='ban', x=value_data.index[value_data['label'] == 'B'], y=value_data['total_mua_30'][value_data['label'] == 'B'])

# Create third trace
trace3 = go.Scatter(name="GiaKL",x=value_data.index[value_data['label'] == 'B'], y=value_data['Gia KL'][value_data['label'] == 'B'], mode='lines', yaxis='y2')

# Create layout
layout = go.Layout(title='The chart shows the correlation between Total Volumn and GiaKL', xaxis=dict(title='Times'), yaxis=dict(title='Total Volumn', side='left'), yaxis2=dict(title='Gia KL', side='right', overlaying='y'), barmode="stack")

# Add traces to figure
fig = go.Figure(data=[trace1, trace2, trace3], layout=layout)

# Show figure
fig.show()
```
{{< figure src="https://i.imgur.com/zb7qIkn.png" title="B-Correlation between Total Volumn and GiaKL" >}}

#### Biến động thị trường
```python
phantramtangtruong = (1081.55 - 1014.331)/1014.331 
print("Gia tri cua phien cao nhat so voi phien nho nhat: ",phantramtangtruong) # --> 0.06626929473712223
```
Giai đoạn này cho thấy sự gia tăng của giá trị các cổ phiếu, với sức tăng lớn nhất trong ngưỡng tiệm cận 0.07% 
cho thấy sự hấp dẫn của thị trường đang trên đà phục hồi.

Vào cuối tháng 3, nhóm dịch vụ tài chính tiếp tục tăng điểm khá mạnh, nhóm ngân hàng cũng có diễn biến tích cực, nhóm bất động sản với thông tin khả năng tiếp tục hạ lãi suất gúp thanh khoản cải thiện tốt, nhiều mã có tín hiệu thoát khỏi xu hướng giảm giá trung hạn kéo dài...

Với các phiên tăng giảm đan xen, khối lượng giao dịch về tổng thể trung hạn vẫn đang ở mức thấp.

Giai đoạn cuối tháng 3, khối lượng giao dịch đang trong xu hướng giảm, cho thấy các trader đang giảm dần các vị thế nắm giữ và có thể dịch chuyển sang ở thị trường cơ sở khi trên thị trường cơ sở đang có nhiều cơ hội sinh lợi ngắn hạn tốt. 
Thị trường đã hồi phục 6 phiên liên tiếp dù điểm số tăng từng phiên không lớn nhưng đủ giúp thị trường quay trở lại kênh tăng giá ngắn hạn và nằm trên đường MA20.

```python
total_ban_theo_ngay = value_data.total_ban_30[value_data['label'] == 'B'].to_frame()

ngay_dau = total_ban_theo_ngay['total_ban_30'].iat[0]
ngay_cuoi = total_ban_theo_ngay['total_ban_30'].iat[-1]

phantramtangtruong = (ngay_cuoi - ngay_dau)/ngay_dau
print("Tong so co phieu ban ra cua ngay cuoi giai doan so voi ngay dau co muc tang truong la: ",phantramtangtruong) 
# --> -0.637694475209741

total_ban_theo_ngay = value_data.total_mua_30[value_data['label'] == 'B'].to_frame()

ngay_dau = total_ban_theo_ngay['total_mua_30'].iat[0]
ngay_cuoi = total_ban_theo_ngay['total_mua_30'].iat[-1]

phantramtangtruong = (ngay_cuoi - ngay_dau)/ngay_dau
print("Tong so co phieu mua vao cua ngay cuoi giai doan so voi ngay dau co muc tang truong la: ",phantramtangtruong) 
# --> -0.8043500703402909
```
Có thể thấy, số cổ phiếu được bán ra ở cuối giai đoạn (đầu tháng 4) so với tháng 3 có sự sụt giảm. 
Cổ phiếu được mua vào cũng tăng trưởng âm, các nhà đầu tư buộc phải giữ lại các cổ phiếu còn tồn đọng tạo nên một thế kìm hãm thanh khoản. Do đó, trong tương lai gần sẽ chịu tác động của sự chênh lệch này.

#### Các sự kiện ảnh hưởng
- Ngày 22-23/3/2023, lần thứ 9 liên tiếp FED tăng lãi suất, nâng phạm vi lãi suất lên mức dao động từ 4,75% đến 5%. Mục đích để trấn an thị trường, giữ uy tín và chống lạm phát. Trong bối cảnh thanh khoản trên thị trường tài chính quốc tế thắt chặt hơn, Chính phủ và các doanh nghiệp Việt Nam sẽ khó huy động vốn trên thị trường quốc tế và phải chịu mức chi phí tài chính cao hơn.
- Ngày 31/3/2023, Ngân hàng Nhà nước Việt Nam điều chỉnh giảm các mức lãi suất điều hành, có hiệu lực từ ngày 03/4/2023....
- Từ đầu năm đến nay, VNĐ chỉ giảm giá 0.6% so với USD, trong khi đó các đồng tiền khác giảm giá nhiều hơn so với USD từ 20-30% cho thấy nội tại vững chắc của kinh tế Việt Nam trên đường dài.

#### Kết luận xu hướng
Ở giai đoạn này chiều hướng tăng nhẹ đã diễn ra do đó thị trường có xu thế đi lên. Thị trường phái sinh có tiềm năng hồi phục về mức điểm tiệm cận 1100. Có thể cho rằng thị trường sẽ theo xu hướng tích lũy với biến động hẹp ít rủi ro nhưng những nhịp hồi phục sẽ không quá mạnh và khó kỳ vọng VN30 vượt qua mức 1.150 điểm. Về ngắn hạn thị trường đang cho thấy việc trở lại kênh tăng giá ngắn hạn với xung lực khá tốt, tuy nhiên nhà đầu tư nên hạn chế mua đuổi cổ phiếu ở các vùng giá cao mà nên giải ngân theo hướng tích lũy và duy trì tỷ trọng cổ phiếu ở dưới mức trung bình. Có thể kết luận, nửa cuối tháng 3 có xu hướng **UPTREND**, nhưng về đầu tháng 4 sẽ trở về **DOWNTREND**.

### Cluster A
Hãy cùng xem qua Cluster A nhé
```python
import plotly.express as px

gtb = [col for col in value_data.columns.to_list() if 'gttb_' in col]

fig = px.line(value_data[gtb][value_data['label'] == 'A'], y=gtb, title='Mean Values by Label A')
fig.update_layout(xaxis_title="Times", yaxis_title="Mean Values",
font=dict(family="Courier New, monospace", size=10, color="RebeccaPurple"))
fig.show()
```
{{< figure src="https://i.imgur.com/kfDqxRX.png" title="Mean Values by Label A" >}}

{{< figure src="https://i.imgur.com/1HWRxDL.png" title="A-Correlation between Total Volumn and GiaKL" >}}

#### Biến động thị trường
```python
tb_SAB = value_data['gttb_SAB'].mean()
print("Gia tri trung binh cua co phieu SAB",tb_SAB) # --> 179.1409610698177
```
**Đối với giá cổ phiếu trung bình:**

Có thể thấy giá trung bình của cổ phiếu SAB (Tổng Công ty cổ phần Bia - Rượu - Nước giải khát Sài Gòn SABECO) có giao động khá ổn định xung quanh giá trị 165K. Hơn nữa giá trung bình của SABECO trong tháng 4/2023 là cao hơn hẳn so với các cổ phiếu khác trong VN30, chứng tỏ thị phần của công ty đó trong ngành có thể đang tăng lên, và có thể được đánh giá cao bởi các nhà đầu tư vì nó có khả năng tăng trưởng mạnh trong tương lai.

Tuy nhiên từ đầu tháng 4 về sau, đồng hành cùng với SAB là khoảng 13 mã cổ phiếu khác như VJC, GAS, VCB... đang có xu hướng giảm giá nhẹ.

Một số mã cổ phiếu như VCB có sự giao động khá mạnh mẽ khi liên tục xuống ngưỡng 44K/1 cổ phiếu trong các ngày 20, 21/03/2023. Và từ đầu tháng 4 là sự tuột dốc khá nhanh khi từ khoảng 93K xuống còn tầm 52K.

Ngoài ra có thể thấy rằng cổ phiếu NVL của Tập đoàn Đầu tư Địa ốc NOVA khá "ảm đạm" khi ngưỡng thấp nhất là vào ngày 03/04/2023 với 2.7K và cao nhất là xấp xỉ 11K. Có lẽ cổ phiếu NVL đang ngày càng "rớt giá", chính vì vậy mà các nhà đầu tư nên bán tháo, thị trường chung cũng nhận định nên BÁN mạnh.

**Đối với khối lượng mua bán cổ phiếu:**

Từ biểu đồ trên, nhìn sơ lược qua thì ta có thể thấy được khối lượng giao dịch có xu hương tăng dần từ ngày 1/4, chạm mốc cao nhất vào ngày 6/4. Trong khoảng thời gian này, khối lượng giao dịch mua nhiều, điều này chứng tỏ thị trường đang rất quan tâm và kỳ vọng vào sự tăng giá của các cổ phiếu. Giá phái sinh và khối lượng giao dịch có một sự tương quan với nhau, việc khối lượng giao dịch mua tăng lên, giá phái sinh trong khoảng thời gian này cũng đã được đẩy lên.

Sau sự gia tăng giá đến một mức cao nhất là vào ngày 6/4, thì sau đó giá phái sinh lại khá ảm đạm. Sau ngày 17/4 thì bắt đầu có dấu hiệu giảm mạnh và thấp nhất vào ngày 19/4. Trong khoảng thời gian này, khối lượng giao dịch cũng giảm dần, khối lượng giao dịch bán tăng nhiều hơn so với kì trước. Mức giảm này nhận định là khá mạnh, trong khi khối lượng giao dịch mua lại không nhiều. Thị trường không có nhiều điểm nhấn trong bối cảnh phục hồi thanh khoán thấp.

```python
total_ban_theo_ngay = value_data.total_ban_30[value_data['label'] == 'A'].to_frame()

ngay_dau = total_ban_theo_ngay['total_ban_30'].iat[0]
ngay_cuoi = total_ban_theo_ngay['total_ban_30'].iat[-1]

phantramtangtruong = (ngay_cuoi - ngay_dau)/ngay_dau
print("Tong so co phieu ban ra cua ngay cuoi giai doan so voi ngay dau co muc tang truong la: ",phantramtangtruong)
# --> -0.7704310701624562
```
**Về biến động giá phái sinh**:

 VN30Index phiên mở đầu tháng 4 giao dịch tăng điểm tích cực và duy trì đà tăng đến cuối phiên. Số liệu cuối cùng ghi nhận được vào ngày 03/04/2023 là 1.081,28 điểm (+1.38% so với 1.070,14 vào 31/03/2023) để hướng đến 1.085-1.095 điểm.

Tuy nhiên, vào tuần thứ 2 từ 14/04/2023 VN30Index biến động tiêu cực hơn. Phiên đầu tuần, mở cửa ở vùng giá 1.073 điểm và chịu áp lực điều chỉnh ở vùng giá 1.070-1.075 điểm để kết thúc tuần giao dịch với phiên giảm điểm mạnh. Tuần điều chỉnh đã diễn ra như dự đoán khi trong tuần qua Vn30Index giảm 16.82 điểm (-1,57%) với khối lượng giao dịch tiếp tục ở mức cao, điểm số điều chỉnh tuy không lớn nhưng trong bối cảnh thị trường đang trong khu vực giao động hẹp thì đợt điều chỉnh này cũng tạo ra cảm giác bất an cho nhà đầu tư.

Đến ngày 19/04/2023. Nỗ lực phục hồi từ 2 phiên đầu tuần đã không được tiếp tục duy trì trong phiên này khi Thời điểm cuối cùng là xấp xỉ 1.055 điểm. Sụt giảm (-0,77%) và đối diện với nguy cơ đánh mất kênh tăng giá ngắn hạn

#### Sự kiện ảnh hưởng
- Ngày 31/3, thông tin sẽ có thêm một đợt giảm lãi suất từ phía các NHTM, đề xuất triển khai giãn hoãn nợ cho doanh nghiệp. Đây là động lực tích cực hỗ trợ cho thanh khoản của thị trường cải thiện thể hiện ở nhóm dịch vụ tài chính tiếp tục tăng điểm khá mạnh như BSI (+27,20%), FTS (18,01%), AGR (+14,51%),...

- Ngày 14/4, Bộ Tài Chính trình Chính phủ báo cáo Quốc hội xem xét tiếp tục chính sách giảm 2% thuế GTGT năm 2023. Cho thấy các trader đang giảm dần các vị thế nắm giữ và chưa có kỳ vọng lạc quan đối với chỉ số VN30.

- Ngày 17/4, thanh khoản trên hai sàn niêm yết đạt gần 8.900 tỉ đồng, giảm 30% so với phiên trước, mức giảm khá mạnh đặc biệt là trong thời điểm thị trường sắp kết thúc đáo hạn phái sinh.

- Ngày 18/4, thị trường đón nhận thông tin khá tích cực khi Chính phủ đồng ý trình Quốc hội giảm 2% thuế VAT cho tất cả hàng hóa, dịch vụ đang chịu thuế 10%.

#### Kết luận xu hướng
**DOWNTREND**

Nhìn chung, Công ty Cổ phần Chứng khoán SSI đánh giá thị trường trong tháng 4 không quá lạc quan nhưng cũng không quá tiêu cực. Thách thức với thị trường trong ngắn hạn lại tiếp tục hình thành cơ hội tốt cho các nhà đầu tư dài hạn.

Khi thị trường chứng khoán có xu hướng downtrend, nhiều nhà đầu tư có thể rơi vào trạng thái sợ hãi, lo lắng về mức độ rủi ro và có thể bán ra cổ phiếu của mình, dẫn đến sự suy giảm của giá trị cổ phiếu. Tuy nhiên, đối với nhà đầu tư thông thạo, thị trường downtrend cũng cung cấp cơ hội để mua vào các cổ phiếu ở mức giá thấp hơn, đồng thời nắm bắt các cơ hội đầu tư mới có thể xuất hiện trong thời gian tới.

## Kết thúc
Qua đồ án “Stock Analysis” chúng ta đã thực hành dùng các kỹ thuật như Cleaning data, Scaling, PCA, K-means Clustering… cùng các kỹ năng phân tích dữ liệu để làm rõ insight cùng với tình hình của thị trường chứng khoán phái sinh VN30 trong 31 ngày. Từ đây có thể nhận định xu hướng của chứng khoán trong thời gian tới, đó là xu hướng tích lũy. Một số hợp đồng có thể tham khảo như VN30F2306 và VN30F2Q được dẫn dắt bởi các cổ phiếu thuộc nhóm ngành xây dựng (DIG), ngân hàng (VCB), chứng khoán (SSI)...

Cảm ơn bạn đã đọc đến giờ phút này, OG cảm động quá :smile: Hy vọng bài viết này sẽ giúp cho bạn cảm thấy thú vị.

Chúc bạn một ngày tốt lành :smile:

**-Mew-**

---

# Related

{{< showcase "Football ETL Analysis" "A Data Engineer project building pipeline to analyze football data" "img/projects-football.jpg" "/football_etl/">}}

{{< showcase "Stock Analysis" "Stock Analysis using PCA and K-means" "img/projects-stock.jpg" "/stock_analysis/">}}


