# The Analytics Setup Guidebook


<!--more-->

Đây là chuyên mục đọc cùng OG. Trong chuyên mục này, OG sẽ tóm tắt lại những điều mà OG cho là giá trị nhất từ những quyển sách mà OG thấy hay. OG sẽ cố gắng tóm tắt lại từng phần hay đề mục quan trọng nhất từ quyển sách để bạn dễ hiểu. Hy vọng bạn sẽ thích chuyên mục này :smile:

{{< admonition type=warning >}}
Tất cả nội dung trong bài (và cả các hình ảnh) đều là các tóm tắt và trích dẫn của OG về nội dung cuốn sách. Để đọc nội dung chi tiết hơn, xin hãy đọc bản gốc ở đường link sách bên dưới. Tất nhiên có thể sẽ có thiếu sót, mong các hạ lượng thứ hehe :wink:
{{< /admonition >}}

# Giới thiệu chút xíu

{{% columns %}}

Cuốn sách "The Analytics Setup Guidebook" được viết bởi [Holistics](https://www.holistics.io/) là một cuốn sách tuyệt vời về Data Analytic mà OG đọc gần đây sau khi tham gia một workshop của Holistics. Ta có thể bắt gặp nhiều khái niệm "vỡ lòng" và rất chi tiết về Data Analytic từ các kiến trúc xử lý dữ liệu đến data modeling và business intelligence. Cuốn sách này vô cùng thích hợp với người muốn hiểu thêm về thế giới data. OG sẽ để link sách ở đây nhé. Đọc đi hay lắm :joy: mà còn free nữa.

> Link sách: [**The Analytics Setup Guidebook**](https://www.holistics.io/books/setup-analytics/)

Ok, giới thiệu sơ sơ vậy thôi, giờ thì lets go !

<--->

{{< figure src="https://cdn.holistics.io/guidebook/book-cover-3.png" width=270 >}}

{{% /columns %}}


## Chapter 1: High-level Overview of an Analytics Setup

Chapter này giới thiệu về quy trình mà một hệ thống phân tích hiện đại nên có cũng như là các ưu nhược điểm của từng loại thiết kế.

### A simple setup for people just staring out

Thông thường với mọi hệ thống phân tích dữ liệu sẽ làm 3 điều cơ bản là:

1. Ta sẽ phải load data vào một kho lưu trữ trung tâm, thường là một data warehouse
2. Ta sẽ phải biến đổi data sao cho nó trở nên đẹp đẽ sạch sẽ để sẵn sàng đưa vào data warehouse
3. Ta sẽ phải lấy data đó để đưa ra các quyết định để giải quyết bài toán doanh nghiệp

Về cơ bản, để bắt đầu phân tích, ta đơn giản chỉ là kết nối công cụ BI (Business Intelligent tool) vào production database là được. Và do đó, khi tương tác với dashboard trong BI tool, data sẽ được truy vấn trực tiếp từ database, đồng nghĩa với việc dữ liệu sẽ được hiển thị ngay thời gian thực.

Cách này vô cùng đơn giản, và tất nhiên là không ổn lắm. Đơn giản là ta sẽ dễ gặp vấn đề về hiệu suất khi production database giờ đây ngoài việc phục vụ cho application của chính nó, còn phải mang “trọng trách” thực hiện các truy vấn phân tích từ BI tool và người dùng app có thể gặp vấn đề về hiệu suất trải nghiệm. 

Cách giải quyết thay thế là ta sẽ kết nối với một database bản sao của production database, thế sẽ không ảnh hưởng đến trải nghiệm của người dùng.

Thế nếu database là một NoSQL database (như MongoDB hay Cassandra) thì sao ? Liệu ta có thể tương tác tốt từ BI tool đến các database này được không ? Câu trả lời là khó. Vì hiện chưa có một công cụ phân tích tiêu chuẩn nào có khả năng tương tác với các loại NoSQL database khác nhau mà mỗi loại NoSQL database lại có một công cụ riêng chuyên biệt khác nhau. Hơn nữa hầu hết các NoSQL database không hỗ trợ mạnh mẽ việc phân tích mà thay vào đó là tốc độ đọc ghi. Thế phải làm thế nào ?

→ Câu trả lời là ta sẽ cố gắng sử dụng một lại SQL data warehouse và load data từ NoSQL database vào warehouse này. Đây cũng chính là kiểu setup đúng đắn

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/proper-analytics-setup.png" >}}

### A modern analytics stack

Để bắt đầu xây dựng một hệ thống phân tích hiện đại. Ta cần hiểu rằng tất cả các hệ thống phải làm 3 bước quan trọng:

1. Thu thập, hợp nhất và lưu trữ data ở một data warehouse
2. Xử lý data như là biến đổi (transform), làm sạch, tổng hợp để đẩy data và warehouse
3. Trực quan hóa dữ liệu, hoặc tải dữ liệu vào các dịch vụ khác để sử dụng

{{< figure src="https://cdn.holistics.io/guidebook/overview-of-an-analytics-setup.png" >}}

{{< tabs >}}

{{% tab title="Step 1" %}}

#### **Collecting, Consolidating and Storing Data**

Trước khi phân tích dữ liệu, thường ta sẽ thu thập dữ liệu từ nhiều nguồn đến một trung tâm dữ liệu trước. Trong quá khứ, đây có thể là một “staging area” hay dạo những năm gần đây được gọi là “data lake”. Dù là gì đi nữa thì ý tưởng chung là nơi lưu trữ dữ liệu thô chuẩn bị cho quá trình hợp nhất data (data consolidating process). 

Staging area này cũng có thể là một analytical database hay một data warehouse vì nó cung cấp khả năng phân tích mạnh mẽ và tối ưu. Và quá trình hợp nhất thường gọi là quy trình ETL hay ELT cũng sẽ được thực hiện với dữ liệu thô được load vào trung tâm cơ sở dữ liệu.

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/collect-consolidate-store-data.png" >}}

{{% /tab %}}

{{% tab title="Step 2" %}}

#### **Processing Data (Transform & Model Data)**

Dữ liệu thô chưa sẵn sàng cho việc làm báo cáo hay trực quan. Cần phải có bước xử lý. Các bước này liên quan đến 2 loại:

- Modeling data: Ứng dụng các công thức hay business logic vào data
- Transforming data: làm sạch data

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/processing-data.png" >}}

{{% /tab %}}

{{% tab title="Step 3" %}}

#### **Presenting & Using Data**

Sau quá trình ở step 2, data giờ đã có thể phân tích. Giờ là lúc sử dụng chúng một cách khôn ngoan để giúp doanh nghiệp phát triển hoặc sử dụng data cho các mục đích khác nhau. Có thể là:

- Ad-hoc reporting
- Data reporting
- Data exploration
- Self-service utopia

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/presenting-using-data.png" >}}

{{% /tab %}}

{{< /tabs >}}

### A good analytics stack

Mọi người sẽ có quan điểm riêng về một hệ thống phân tích tốt. Và ở đây là một số ngộ nhận từ quyển sách này:

1. Sử dụng ELT hơn là ETL
2. Sử dụng cloud data warehouse hơn là on-premis data warehouse. Ngoài ra Massively Parallel Processing (MPP) analytics database cũng được chuộng hơn hệ thống giống Hadoop
3. Data modeling là quan trọng và không nên bị bỏ qua
4. SQL base analytics sẽ hơn non-SQL base analytics
5. Quy trình phân tích thì quan trọng hơn là chỉ tập trung vào trực quan hóa dữ liệu

## Chapter 2: Centralizing data

Chapter này tập trung vào việc truy xuất, thu thập dữ liệu từ nhiều nguồn và quy trình ETL/ELT

{{< figure src="https://cdn.holistics.io/guidebook/consolidate-data-in-central-place.svg" >}}

### Extract & Load process

Dữ liệu có thể đến từ rất nhiều nguồn, nền tảng khác nhau. Đó có thể là từ:

- Các ứng dụng: nếu doanh nghiệp sở hữu các ứng dụng hay website, đây có thể là nơi chứa tất cả các transactional data quan trọng với doanh nghiệp. Đây có thể là một SQL (hay NoSQL) database connect trực tiếp đến các ứng dụng đó
- Các hệ thống Customer Relationship Management (CRM), Content Management System (CMS), Enterprise Resource Planning (ERP) hay hệ thống marketing: thông thường để kết nối, ta sẽ không thể kết nối trực tiếp mà thông qua API
- Data được tạo thủ công từ nhân viên hay các system khác như các file Excel, CSV, báo cáo PDF, text document

Và bước đầu tiên trước khi đẩy các data này vào data warehouse chính là quy trình **Extract & Load**

Cơ bản quá trình này chính là việc ta tạo ra một bản copy của các data nguồn bên trong data warehouse. Có thể thực hiện bằng nhiều cách

1. Viết script thủ công: chẳng hạn như load data từ MySQL database đến warehouse. Việc này phức tạp và theo thời gian sẽ tốn chi phí duy trì cho các đoạn script.
2. Dùng Data loading tool: Có nhiều tool hỗ trợ việc kết nối và tải dữ liệu như:
    1. Trả phí: [StichData](https://www.stitchdata.com/) hay [Talend](https://www.talend.com/)
    2. Miễn phí: [Airflow](https://airflow.apache.org/) hay [Prefect](https://www.prefect.io/)

{{< admonition title="Incremental load" open=false >}}

Khi load, thông thường sẽ có một kỹ thuật được sử dụng rộng rãi. Đó là Incremental load. Cơ bản kỹ thuật này đề cập đến việc chỉ load những updated data thay vì tất cả (full load) từ nguồn mỗi lần chạy quy trình EL.

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/incremental-load-data.png" >}}

{{< /admonition >}}

### Understand Data warehouse

Ta đề cập rất nhiều về data warehouse, vậy nó là gì ? Về cơ bản data warehouse là một database chuyên về phân tích dữ liệu. 

{{< admonition title="Khi nào thì ta sẽ cần một data warehouse ?" >}}
TL;DR Câu trả lời là tùy. Việc này tùy và quy mô, số lượng data và cả ngân sách ta có. Ta cần phải trả lời các câu hỏi:

- Ta có cần phân tích dữ liệu từ nhiều nguồn không ?
- Ta có cần tách biệt transaction data với analytical data không ?
- Data thô của ta có phù hợp cho việc truy vấn không ?
- Ta có muốn tăng hiệu quả của các truy vấn phân tích không ?

Tất nhiên ta vẫn có thể không cần một data warehouse như các thiết kế ở mục [A simple setup for people just staring out](#a-simple-setup-for-people-just-staring-out). 

{{< /admonition >}}

Có rất nhiều các data warehouse mà ta có thể tham khảo:

- Amazon Redshift
- Google BigQuery
- Snowflake
- ClickHouse (self-hosted)
- Presto (self-hosted)

Khi đọc đến đây, bạn có thể sẽ thắc mắc rằng:

> Chẳng phải data warehouse cũng chỉ là các cơ sở dữ liệu quan hệ (relational database) chứa analytical data thôi sao ? Thế tôi chỉ dùng MySQL, PostgreSQL, MSSQL hay Oracle như data warehouse được không ?

Câu trả lời ngắn gọn là được, nhưng còn tùy. Để hiểu được điều này ta phải hiểu sự khác nhau giữa "Transactional workloads" và "Analytical workloads"

{{< tabs >}}

{{% tab title="Transactional workloads" %}}

Có thể hiểu là khối lượng các truy vấn giải quyết các vấn đề cơ bản của app. Như khi người dùng load một trang sản phẩm thì truy vấn sẽ truy xuất thông tin sản phẩm từ database. Một số đặc điểm: 

- Các truy vấn loại này thường trả về số lượng ít các record (ví dụ như 10 bài posts)
- Thường là các truy vấn đơn giản và mất rất ít thời gian thực thi
- Rất nhiều các truy vấn xảy ra cùng lúc, đồng thời

Các transactional database phục vụ cho các loại truy vấn trên được thiết kế có tốc độ phản hồi nhanh và các tác vụ song song.

{{% /tab %}}

{{% tab title="Analytical workloads" %}}

Là các truy vấn tập trung cho việc phân tích. Là thứ mà chúng ta hướng tới ở đây. Một số đặc điểm:

- Scan lượng lớn hoặc rất lớn số lượng record trong bảng
- Mỗi truy vấn có thể rất nặng và mất nhiều thời gian (hàng phút hoặc giờ)
- Không nhiều các truy vấn diễn ra đồng thời và cũng giới hạn chỉ những nhân viên nội bộ có thể truy cập

Các analytical databases được thiết kế tối ưu cho việc xử lý trong thời gian dài, xử lý các truy vấn phức tạp.

{{% /tab %}}

{{< /tabs >}}

{{< figure src="https://cdn.holistics.io/guidebook/analytics-setup/transactional-analytical-db.png" >}}

Về cơ bản nếu số lượng data ít và việc phân tích không quá phức tạp, sẽ ổn khi chọn một SQL database bình thường để làm datawarehouse (MySQL, MSSQL,..). Cuốn sách gợi ý nên chọn PostgreSQL vì database này hỗ trợ nhiều các tính năng phân tích hơn cả. Tuy nhiên nếu có thể, hãy chọn một low-cost data warehouse như BigQuery hay Redshift sẽ mạnh mẽ hơn.

### ETL/ELT

> OG đã có một bài viết rất chi tiết về ETL và ELT ở blog [**Tất tần tật về data pipeline**](/alldatapipeline/) bạn có thể đọc thêm

Về cơ bản ETL (Extract - Transform - Load) là quy trình thực hiện xử lý data trước khi đưa data vô data warehouse. Quy trình này tích xuất và **xử lý data bên ngoài** trước khi load cleaned data vào data warehouse. Việc xử lý data thường là ở “staging area”.

Ngược lại ELT (Extract - Load - Transform) thực hiện việc load dữ liệu thô vào warehouse trước sau đó mới thực hiện transform trong warehouse. Việc này không cần thiết dùng một staging area và data warehouse sẽ là nơi chứa dữ liệu duy nhất.

{{< admonition title="Chuyển đổi từ ETL sang ELT" >}}

Trước đây, mô hình ETL (Extract, Transform, Load) phù hợp do kho dữ liệu rất đắt đỏ và chỉ lưu trữ dữ liệu đã được xử lý. Tuy nhiên, ETL gây ra điểm nghẽn khi khối lượng dữ liệu tăng và phụ thuộc nhiều vào kỹ sư dữ liệu, làm chậm quy trình.

Với sự phát triển của kho dữ liệu đám mây, bùng nổ dữ liệu lớn, và phương pháp Agile, ELT (Extract, Load, Transform) ra đời. ELT tải dữ liệu thô trực tiếp vào kho trước khi xử lý, tận dụng sức mạnh của kho dữ liệu hiện đại, tăng tốc độ và trao quyền cho nhà phân tích tự xử lý dữ liệu mà không cần kỹ sư hỗ trợ. ELT đang trở thành tiêu chuẩn mới cho phân tích dữ liệu.

{{< /admonition >}}

## Chapter 3: Data Modeling for Analytics

Khi thao tác với data, ta thường không muốn tác động đến bản thân data một cách trực tiếp mà thay vào đó là tạo một đối tượng trừu tượng bên trên để thao tác với nó. Đây gọi là data model.

{{< figure src="https://cdn.holistics.io/guidebook/data-model-concept.png" >}}

### Kimball’s Dimensional Data Modeling

Ý tưởng cho data modeling được đưa ra từ khá lâu về trước bởi Ralph Kimball và cộng sự của ông trong cuốn “The Data Warehouse Toolkit” năm 1996. Giới thiệu về một mô hình dữ liệu chiều (dimensional data modeling)

{{< admonition title="Các mô hình khác" >}}

Ngoài Kimball cũng có nhiều các cách tiếp cận với data modeling như Inmon data modeling của Bill Inmon vào năm 1990 (trước Kimball 6 năm). Cách của Inmon tập trung vào chuẩn hóa schema (normalize) hơn là denormalize như của Kimball. Ngoài ra còn có data modeling của Data Vault vào khoảng năm 2000 

{{< /admonition >}}

🌟 **Star Schema**

Star schema là cách tổ chức data cho mục đích phân tích. Kiến trúc này bao gồm 2 loại bảng:

1. Bảng Fact : bảng này chính là trung tâm của star schema. Bảng fact là chứa các thông tin độ đo, giao dịch hay “fact” của một doanh nghiệp
2. Các bảng dim (dimension tables) kết nối với bảng fact: Mỗi bảng dim chứa thông tin về một khía cạnh, thuộc tính nào đó của bảng fact

{{< figure src="https://cdn.holistics.io/guidebook/star-schema.png" >}}

Triết lý của Kimball:

> **Do the hard work now, to make it easy to query later.**

Cách thiết kế của Kimball giúp ta đạt được tốc độ phân tích và độ hiệu quả cao hơn. Tuy nhiên hiện nay với sự phát triển công nghệ, mô hình Kimball vẫn hữu ích, nhưng không còn là điều bắt buộc. 


Chẳng hạn như sử dụng snapshot fact table để giải quyết vấn đề bảng dữ kiến lớn có thể không còn bắt buộc đối với các cloud data warehouse hiện đại, kiến trúc cột (columnar data architecture),… Hoặc với việc data thay đổi liên tục, Kimball thay đổi data bằng cách ghì đè (overwrite) lên bảng hiện tại. Đây là kỹ thuật **Slowly Changing Dimensions (SCD)** loại 1. Kimball đưa ra 3 loại SCDs:

- Type 1: Ghì đè cột dữ liệu cần thay đổi
- Type 2: Thêm hàng mới với dữ liệu mới vào bảng
- Type 3: Thêm cột mới dùng cho việc đánh dấu data mới

> **Vậy hiện này chúng ta thực hiện SCD như thế nào ?**

Vào năm 2018, trong sự kiện [Functional Data Engineering - A Set of Best Practices | Lyft](https://www.youtube.com/watch?v=4Spo2QRTz1k&t=989s) tại Data Council, Senior Lyft data engineer Maxime Beauchemin đề xuất giải quyết SCD bằng cách sử dụng khả năng phân vùng bảng (table partitioning) của các data warehouse hiện đại để tạo bảng sao của toàn bộ dữ liệu theo lịch trình bằng các ETL tool thay vì áp dụng các kỹ thuật SCD phức tạp như type 2 hay type 3. Và cách tiếp cận này hiện được áp dụng tại Facebook (Meta), Airbnb và Lyft.

Việc áp dụng modeling tùy thuộc vào hoàn cảnh và nhu cầu khi ta cần. Chẳng hạn như gặp khó khăn khi trích xuất các báo trực tiếp từ bảng dữ liệu thô với các chỉ số kinh doanh phức tạp. Việc sử dụng các công nghệ và điều chỉnh việc modeling khi cần thiết có thể giúp giảm bớt rất nhiều tác vụ thủ công và tối ưu chi phí. Điều quan trọng là chỉ nên __mô hình hóa khi ta cần__ .

## Chapter 4: Using data

Ở chương này ta sẽ tập chung vào lịch sử của công cụ phân tích dữ liệu và toàn cảnh thị trường báo cáo kinh doanh. Ngoài ra cũng sẽ nói thêm về các nhu cầu báo cáo trong doanh nghiệp. Chương này chủ yếu cung cấp cái nhìn tổng quan thay vì là đi sâu vào chi tiết. Ta sẽ nghe qua thử câu chuyện của một nhà phân tích dữ liệu giả tưởng Daniel và cách mà anh ta sử dụng các công cụ BI qua các thời kỳ

### Daniel Story

**Cognos và sự chờ đợi dai dẳng**

Vào những năm 1990, Daniel bắt đầu sự nghiệp của mình tại một ngân hàng, sử dụng Cognos và OLAP cubes. Quy trình làm việc khi đó rất cứng nhắc: Daniel phải dựa vào bộ phận IT để xây dựng hoặc cập nhật các cubes dữ liệu, một quy trình mất hàng tuần. Nếu yêu cầu công việc có sai sót hoặc không rõ ràng, mọi thứ phải bắt đầu lại từ đầu, gây ra sự trì hoãn và thất vọng. 

Dễ dàng thấy các công cụ BI đời đầu mang tính tập trung cao nhưng lại thiếu linh hoạt và các nhà phân tích dữ liệu phải phụ thuộc nặng nề vào bộ phận IT

{{< figure src="https://cdn.holistics.io/guidebook/job-1.png" >}}

**Kỷ Nguyên Tableau: Tự Phục Vụ Nhưng Lạc Lối Với Metric**

Đến năm 2006, Daniel chuyển sang làm việc tại một công ty phát trực tuyến video sử dụng Tableau và Microsoft SQL Server Analysis Services (SSAS). Tableau với giao diện kéo-thả đã trao quyền cho người dùng phi kỹ thuật, giúp họ tự tạo các biểu đồ và báo cáo.

Tuy nhiên, cách tiếp cận này lại dẫn đến `metrics drift`—các phòng ban khác nhau tính toán các chỉ số giống nhau theo những cách khác nhau. Sự thiếu tiêu chuẩn hóa này gây ra báo cáo mâu thuẫn, quyết định sai lầm và những nỗ lực sửa sai căng thẳng. Giải pháp? Tập trung hóa định nghĩa các chỉ số dưới sự quản lý của đội BI.

{{< figure src="https://cdn.holistics.io/guidebook/job-2.png" >}}

**Lớp Mô Hình Dữ Liệu: Looker và Logic Tập Trung**

Đến năm 2016, Daniel gia nhập một startup sử dụng Amazon Redshift và Looker. Looker mang đến một sự thay đổi lớn với việc cho phép đội dữ liệu định nghĩa logic kinh doanh trong một lớp mô hình (LookML). Những mô hình này có thể được tái sử dụng trên toàn tổ chức, ngăn chặn tình trạng metrics drift và thúc đẩy sự hợp tác giữa các nhân viên kỹ thuật và phi kỹ thuật.

Đối với Daniel, đây là một bước tiến lớn. Khác với quy trình phân tán của Tableau, Looker tập trung hóa logic và đơn giản hóa việc báo cáo. Tuy nhiên, với kinh nghiệm của mình, Daniel hiểu rằng không có quy trình nào là hoàn hảo và anh sẵn sàng đối mặt với những thách thức mới mà phương pháp này mang lại.

Lúc này công cụ BI hiện đại nhấn mạnh vào việc tập trung hóa mô hình và tính linh hoạt, giảm mâu thuẫn và giúp tổ chức mở rộng khả năng phân tích dữ liệu hiệu quả.

{{< figure src="https://cdn.holistics.io/guidebook/job-3.png" >}}

{{< admonition title="Frank Bien’s Three Waves" open=false >}}

Giống với câu chuyện của Daniel. Vào năm 2017, CEO của Looker - Frank Bien - đã viết cuốn sách [Catching the Third Wave of Business Intelligence](https://looker.com/blog/catching-the-third-wave-of-business-intelligence) để diễn tả chính xác 3 làn sóng phát triển của công cụ BI (Business Intelligence). Ông nhấn mạnh rằng các làn sóng này không phân định rõ ràng như sóng trên bãi biển mà thực tế phức tạp hơn nhiều.

**Làn sóng thứ nhất (1990s)**

Các công cụ BI như Cognos hoạt động trên nền tảng hệ thống đơn khối (monolithic stacks) bao gồm tất cả các chức năng từ data warehouse, data transformer, data cube solution, trực quan hóa dữ liệu. Do hẹn chế về phần cứng (RAM đắt đỏ), các công cụ này phải sử dụng OLAP cubes để xử lý dữ liệu, gây phụ thuộc vào đội IT và làm chậm việc phân tích

**Làn sóng thứ hai (2000s)**

Đây là khi Tableau xuất hiện và mang lại khả năng "tự phục vụ" cho người dùng non-tech với giao diện kéo thả thuận tiện. Tuy nhiên điều này lại dân đến sự không đồng nhất giữa các chỉ số và định nghĩa giữa các phòng ban (metric drift). Gây xung đột và nhầm lẫn trong dữ liệu

**Làn sóng thứ ba (2010s)**

Công nghệ khi dữ liệu MPP (Massively Parallel Processing) và lưu trữ dạng cột (columnar storage) đã giúp tăng hiệu suất và giảm chi phí đáng kể. Trong đó, Looker đi tiên phong trong việc tận dụng các kho dữ liệu hiện đại, tạo mô hình tập trung hóa logic mà không cần các OLAP cubes cũ.

Dù có các làn sóng mới nhưng các công cụ từ làn sóng cũ vẫn tiếp tục tồn tại và được sử dụng và tạo nên sự đa dạng trong hệ sinh thái BI hiện nay. Việc hiểu rõ lịch sử và các ý tưởng từ những làn sóng sẽ giúp ta có cái nhìn rõ ràng và đánh giá tốt hơn giữa các công cụ BI, tránh bị cuốn vào các chiêu trò tiếp thị. 

{{< /admonition >}}

### Navigating The Business Intelligence Tool Space

Trên thị trường có rất nhiều loại công cụ BI tool và mục này sẽ giúp ta phân biệt giữa chúng. Việc phân loại sẽ giúp ta nhanh chóng định vị loại công cụ phù hợp khi nghiên cứu thị trường.

**SQL vs Non-SQL**

Một vài công cụ yêu cầu có kiến thức về SQL, một vài thì không. Chẳng hạn với Non-SQL có thể kể để Tableau và PowerBI dành cho người dùng không chuyên. Ngược lại một số loại khác có thể yêu cầu một chút kiến thức SQL như Houlistics, Looker, Mode, Cluvio hay Redash. Và hiện nay, SQL đang trở thành tiêu chuẩn nhờ tính phổ biến và dễ quản lý cũng như mở rộng của nó, do đó các công cụ BI có hỗ trợ SQL lại càng cung cấp khả năng phân tích mạnh mẽ và linh hoạt cho người phân tích dữ liệu.

**Embedded Datastore vs External Datastore**

Các công cụ BI ở thời kỳ đầu là các embedded datastores, nghĩa là các công cụ gắn liền chung với cả hệ thống (bao gồm luôn cả data storage và các thành phần khác). Chẳng hạn như Sisense hay Cognos. Tuy nhiên ngày nay có một chút thay đổi, đa số các công cụ BI hiện đại cho phép kết nối với nhiều đa dạng các loại database hay data warehouse hiện đại khác nhau và có tính linh hoạt tốt hơn rất nhiều như Metabase hay Houlistics. Các công cụ hiện đại cũng uu tiên kết nối với các kho dữ liệu đám mây mạnh mẽ cũng như bất kỳ loại SQL database nào có trên thị trường.

{{< admonition title="Thế nào là một data warehouse hiện đại ?" open=false >}}

Ta đề cập rất nhiều đến "Data warehouse hiện đại". BI tool có thể kết nối với MySQL, PostgreSQL hay BigQuery, Snowflake,... thì những warehouse này là warehouse hiện đại đúng không ? Tất nhiên là **không**.

Như đã đề cập nhiều ở phần [Understand Data warehouse](#understand-data-warehouse), một data warehouse hiện đại có 2 đặc tính:
1. Chúng là các database hướng cột (Column-oriented database): Ngược lại với các database hướng hàng (row-oriented database), loại database này dùng để thích nghi với analytical workloads và thích hợp với OLAP cubes
2. Chúng có thiết kế MPP (Massively Parallel Processing): Ngược lại với việc chạy trên một máy hay một cluster nhỏ. Kiến trúc MPP đề cập đến việc các data warehouse có thể tùy ý tùy chỉnh quy mô (scale up-down) của tài nguyên tính toàn dựa trên độ phức tạp của câu truy vấn. Điều này nghĩa là ta sẽ không phải trả phí trước mà tùy vào lượng tính toán mà ta sử dụng.

{{< /admonition >}}

**In-Memory vs In-Database**

Sự khác biệt ở đây là đối với In-memory, một số công cụ BI yêu cầu tải dữ liệu về máy cá nhân để phân tích (như Tableau hay PowerBI). Thì công cụ In-Database như Looker hay Houlistics có thể thực hiện xử lý trực tiếp trên cơ sở dữ liệu vaft ận dụng tối đa sức mạnh tính toán của chúng.

**Modeling vs Non-Modeling**

Cuối cùng là các loại BI hỗ trợ tích hợp lớp mô hình hóa (Modeling) như Houlistics và Looker để giúp quản lý logic tập trung, nâng cao tính thống nhất. Ngoài ra cũng có các công cụ không hỗ trợ việc đó như ReDash hay Mode.

Việc phân loại các công cụ BI giúp ta dễ định hướng và lựa chọn sản phẩm phù hợp với nhu cầu hơn. Ngoài ra các yếu tố như giá cả, open-source và khả năng triển khai (on-premis hay cloud) cũng là những yếu tố quan trọng có thể cân nhắc.

### The Arc of Adoption

Sau khi đã hiểu về quá trình phát triển cũng như là các loại BI tool, đã đến lúc nghĩ đến việc triển khai các công cụ nào vào các dự án của tổ chức. Đa số các tổ chức sẽ trải qua các giai đoạn chính sau trong quá trình tiếp cận và sử dụng dữ liệu

1. **Ad-hoc Queries**

Mọi chuyện bắt đầu với các câu hỏi từ bộ phận kinh doanh khi họ tìm cách hỗ trợ các quyết định bằng dữ liệu. Lúc này ta sẽ nghĩ đến việc sử dụng các công cụ truy vấn nhanh SQL hoặc phân tích thủ công từ các nguồn dữ liệu phân tán.

2. **Static Report/ Dashboards**

Khi nhu cầu dữ liệu tăng lên, đội data sẽ tạo các bảng báo cáo bằng dashboard cố định để tự động hóa quy trình. Quá trình này có thể sử dụng PowerBI hay Tableau để cung cấp dữ liệu lặp lại cho cả nhóm.

3. **Self-Service**

Khi việc sử dụng dashboard tăng lên, nhu cầu dựa trên dữ liệu ngày càng phổ biến, dẫn đến việc các truy vấn càng trở nên phức tạp hơn từ các bộ phận. Một số công ty hướng dẫn nhân viên sử dụng SQL trong khi số khác triển khai các công cụ dễ dùng hơn để hỗ trợ phân tích tự phục vụ (self-service). Tuy nhiên điều này dễ dẫn đến vấn đề mâu thuẫn và hiểu nhầm trong các quyết định.

Có ý tưởng về "mô hình trưởng thành dữ liệu" (the maturity model) là các mô hình thường được các nhà tư vấn dùng để đánh giá mức độ hoàn thiện dữ liệu về hành vi của một tổ chức. Chẳng hạn như theo [Emily Schario](https://about.gitlab.com/blog/2019/11/04/three-levels-data-analysis/) của Gitlab cho rằng tất cả các tổ chức đều qua ba level phân tích dữ liệu: 
- Reporting: thu thập và báo cáo dữ liệu cơ bản
- Insights: Kết hợp với dữ liệu nguồn để rút ra hiểu biết sâu sắc
- Predictions: Sử dụng phân tích thống kê và máy học (machine learning) để dự đoán và định hướng chiến lược.

Và các tổ chức cần phải nâng cấp các công cụ BI để phù hợp với nhu cầu tiêu thụ ngày càng phức tạp của mình. Chẳng hạn như để giải quyết vấn đề self-service, ta cần phải định nghĩa các model tại data warehouse để giúp giảm các mâu thuẩn số liệu và tăng tính thống nhất. Các công cụ như Looker hay Houlistics sử dụng cách tiếp cận này để hỗ trợ self-service mà không làm tổn thương đến chất lượng dữ liệu.

## Conclusion

OK đã rất rất dài rồi. Phù... :sweat_smile: Cuối cùng đã đến đoạn kết. Phần này đơn giản là đút kết cơ bản tất cả những gì ở phía trên lại một cách ngắn gọn lại thôi. Qua cuốn sách này ta đã học được gì.

Đầu tiên là ba bước cơ bản của một hệ thống phân tích. Nó bao gồm:
- Thu Tập và lưu trữ dữ liệu
- Xử lý Dữ liệu
- Trình bày và trực quan dữ liệu

Ta cũng đã hiểu được sự phát triển của ETL và tầm quan trọng của nó cùng với sự vươn lên của các data warehouse hiện đại. Tiếp đó chính là các công cụ BI hiện nay và xu hướng tiêu chuẩn hóa SQL trong ngành. Trong tương lai, ngành phân tích dữ liệu sẽ liên tục đổi mới, các công cụ và ý tưởng sẽ tiếp tục phát triển và xuất hiện không ngừng. Và trong đó, SQL sẽ là xu hướng trung tâm, mọi thứ sẽ xoay quay SQL và tận dụng sức mạnh của các data warehouse hiện đại để tiếp tục phát triển. Tuy các công cụ và phương pháp mới liên tục thay đổi, sự phát triển và chuyển đổi của các doanh nghiệp lớn sang công nghệ đám mây vẫn sẽ mất hàng thập kỷ. Tuy nhiên, ta vẫn cần phải nắm và hiểu biết các cách tiếp cận từ cả ba "làn sóng" BI vì các phương pháp cũ vẫn sẽ tồn tại trong các doanh nghiệp lớn. Cuối cùng, mọi thứ ta học ngày hôm nay, sẽ chính là nền tảng, là kiến thức giúp chính bản thân ta thích nghi với sự chuyển đổi ngày mai và sau này.

Cuốn sách này mang lại nhiều giá trị đặc biệt ở lĩnh vực data Analyst cho người mới và tổng quát hóa hầu hết các khái niệm quan trọng kèm theo nhiều câu chuyện rất thú vị. Nếu có thể, OG hy vọng bạn có thể vào link (ở đầu bài) để đọc để hiểu sâu hơn nữa những gửi gắm của tác giả. Còn giờ thì hẹn gặp bạn đọc vào số tiếp theo :wink:

**-Mew-**

---

# Related

{{< showcase "Tất tần tật về Data pipeline" "Trở thành thợ ống nước data từ A đến Á với ETL/ELT, streaming và hơn thế nữa" "https://i.imgur.com/WeJV4L9.png" "/alldatapipeline/" 2 >}}

{{< showcase "Thằng nhóc thích code và data" "Ngành Data có gì hot mà mình lại dính" "https://img.freepik.com/free-vector/big-data-center-server-room-rack-engineering-process-teamwork-computer-technology-cloud-storage_39422-1032.jpg" "/start_journey/" 2 >}}
