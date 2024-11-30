# Tất tần tật về Data pipeline


<!--more-->

Trong bài lần này, ta sẽ đi qua tất tần tật mọi thứ về data pipeline, khi nào nên dùng loại nào và làm sao để xây dựng một kiến trúc data pipeline hiện đại, hiệu quả. Gét gô

{{< figure src="https://i.imgur.com/WeJV4L9.png" >}}
## Data Pipeline là gì ?
Data pipeline hay “đường ống dữ liệu” đề cập đến một chuỗi các quá trình chuyển data từ một hoặc nhiều nguồn nào đó đến đích, có thể là data warehouse, database hay app

{{< admonition type=info >}}
Một điều quan trọng là hầu hết data pipeline không phải là một quy trình tuyến tính từ source A đến source B. Nhưng nó là một chuỗi các quá trình xử lý phức tạp và phụ thuộc lẫn nhau. Chẳng hạn như data có thể được lấy từ nhiều nguồn, được biến đổi và join lại với nhau ở những thời điểm khác nhau trước khi load vài đích đến cuối cùng.
{{< /admonition >}}

Một kiến trúc data pipeline tốt là kiến trúc có thể giải quyết được 5 V của Big Data: 

1. Volume (Khối lượng)
2. Velocity (Tốc độ)
3. Veracity (Tính xác thực)
4. Variety (Tính đa dạng)
5. Value (Giá trị)

Ngoài ra một hệ thống tốt cũng phải tối ưu về chi phí hay dễ bảo trì.

## Một số thiết kế Data pipeline xử lý dữ liệu

### ETL/ELT Data pipeline
ETL và ELT là hai trong những kiến trúc data pipeline nổi tiếng và thường được gặp nhiều nhất trong những hệ thống xử lý phức tạp và xử lý big data.
#### E-T-L là gì 

{{< tabs >}}

{{% tab title="Extract" %}}
Extracting là giai đoạn khai thác, trích xuất dữ liệu và cũng là giai đoạn đầu tiên trong hệ thống ETL/ELT. Dữ liệu có thể hoàn toàn thô và đa dạng nguồn (như flat files, APIs, hay các hệ thống IoT, sàn thương mại điện tử,…)

{{< admonition title="Các kỹ thuật Extract" open=false >}}
- **Update Notification**: Data source sẽ thông báo ta khi có những records thay đổi (như webhook). Ta có thể chạy quy trình extraction cho sự thay đổi đó. Hầu hết các database hay web app cung cấp cơ chế cập nhật hỗ trợ cho phương pháp tích hợp này.
 - **Incremental extraction**: Vài nguồn data không cung cấp thông báo update nhưng có thể xác định data theo một khoảng thời gian. Ta có thể kiểm tra sự thay đổi đó định kỳ như hàng tuần, hàng tháng,… Và ta chỉ cần extract phần data đã thay đổi
- **Full extraction**: Trường hợp nếu data source không thể thông báo hay xác định đâu là data mới. Ta chỉ còn cách load tất cả, phương pháp này đòi hỏi ta phải giữ copy của lần extract trước đó để biết đâu là data mới. Chỉ nên dùng với những bảng nhỏ
{{< /admonition >}}

{{% /tab %}}

{{% tab title="Transform" %}}

Transforming là giai đoạn thực hiện xử lý, làm sạch, biến đổi dữ liệu. 

{{< admonition title="Các kỹ thuật Transformation" open=false >}}
- **Data cleaning**: Chuyển đổi type, xử lý string, xử lý null, duplicate, normalizing, data structuring
- **Transforming**:  filtering, joining, feature engineering, Anonymizing and Encrypting, sorting, aggregating,
{{< /admonition >}}

{{% /tab %}}

{{% tab title="Load" %}}
Loading là quá trình tải (load) tất cả các dữ liệu vào hệ thống đích. Hệ thống đích có thể là tệp đơn giản (csv, json,…) hay một cơ sở dữ liệu phức tạp như data warehouse, data lake,…

{{< admonition title="Các kỹ thuật loading" open=false >}}
- **Full load**: Thường dùng khi ta khởi chạy pipeline lần đầu hoặc đối với dữ liệu lớn không thay đổi thường xuyên, phương pháp này load toàn bộ data đến data source, tốn nhiều thời gian và tài nguyên.
- **Incremental load**: Load chỉ sự khác nhau giữa warehouse và source một cách định kỳ. Nó sẽ lưu ngày extract trước, do đó chỉ add các bản ghi có ngày extract sau đó. Có 2 cách để incremental load:
    - Streaming incremental load: Nếu volume data nhỏ, ta có thể streaming sự thay đổi liên tục qua pipeline đến warehouse.
    - Batch incremental load: nếu data volume lớn, ta có thể collect data theo các batches một cách định kỳ
- **Scheduled loading**: Tải dữ liệu theo lịch trình. Phù hợp với các ứng dụng có thời gian xử lý dữ liệu chậm hay dữ liệu không hay đổi thường xuyên
- **On-demand loading**: Tải dữ liệu khi có yêu cầu từ người dùng hay ứng dụng. Cần có cơ chế phản hồi nhanh để đáp ứng yêu cầu load data
- **Parallel/serial**: Là hai kỹ thuật xử lý dữ liệu trên đa luồng. Parallel xử lý data trên nhiều luồng song song, thích hợp cho ứng dụng xử lý big data hay phân tán. Trong khi serial xử lý data trên một luồng, thích hợp cho hệ thống xử lý đơn giản
{{< /admonition >}}
{{% /tab %}}

{{< /tabs >}}

#### ETL pipeline

{{< admonition type=info title="Một chút lịch sử" open=true >}}
ETL (Extract-Transform-Load) xuất hiện từ những năm 1970, khi các cơ sở dữ liệu quan hệ (Relational Database) trở nên phổ biến. Trong thời kỳ này, dữ liệu giao dịch thô được lưu trữ trong các hệ thống OLTP (Online Transaction Processing) để hỗ trợ hoạt động giao dịch. Tuy nhiên, dữ liệu dạng này không phù hợp cho việc phân tích và ra quyết định kinh doanh. Vì thế, ETL được phát triển để chuyển đổi dữ liệu từ hệ thống giao dịch sang dạng quan hệ tối ưu cho phân tích trong các hệ thống OLAP (Online Analytical Processing).
{{< /admonition >}}

ETL (Extract-Transform-Load) là quy trình xử lý data từ nhiều nguồn với **volume nhỏ** trước khi tải vào hệ thống đích là một data warehouse theo chu kỳ hay định kỳ. Đúng như tên gọi, quy trình này thực hiện lần lượt các bước extract -> transform -> load cụ thể:
1. **Extract**: Như đã đề cập ở mục trước, bước này chủ yếu trích xuất dữ liệu thô từ nhiều nguồn, thường là các transaction database hay các flat files (csv, xlsx). Sau đó data được lưu vào một **vùng trung gian (Staging Area/Landing Zone)**. Vùng này có nhiệm vụ lưu trữ __tạm thời__ và data sẽ bị xóa để giải phóng lưu trữ sau khi hoàn tất quá trình transforming
2. **Transform**: Xử lý và biến đổi dữ liệu, quá trình này thực hiện trong staging area để chuẩn bị clean data cho data warehouse. Đối với ETL pipeline, data thường sẽ được xử lý và chuẩn hóa trước khi load vào data warehouse. Hướng tiếp cận này gọi là **Schema-on-write Approach**

{{< admonition title="Schema-on-write Approach" open=false >}}
Là cách tiếp cận thường được sử dụng trong các pipeline ETL, khi mà data schema phải được xác định trước khi write vào storage. Data structure và format phải được định nghĩa từ trước và thường được dùng đối với các database truyền thống (relational database như MySQL, PostgreSQL, Oracle, SQL Server,…). Việc này đảm bảo tính toàn vẹn của data và chất lượng data. Tuy nhiên lại không phù hợp cho unstructure data hay semi-structure data khi có schema data đa dạng và thay đổi liên tục
{{< /admonition >}}

3. **Load**: Sau khi dữ liệu được làm sạch và chuẩn hóa ở staging area, bước cuối cùng là load vào một data warehouse có sức mạnh phân tích mạnh mẽ để bắt đầu phân tích.
{{< figure src="https://cdn.prod.website-files.com/6130fa1501794e37c21867cf/63cf3084f1439f2401b8d477_0qBAzVjwAYTmqKX3Yb5RTDEt3tE_pKN3mWHfRgB1ruK9WCYnMBSUcFL_CwEa9eA4SpO3eYHKwMCzlZI7xwRWcQePNVZ10iOJrPa_AYbSLgcIc3p_VBpwh2sFUeIBtqDrh-Fx3XAdSmTI92UHXIHbNZW7yec8ZzAfc_iFcir6gxE1_qikjmFvnhB8ji0w.png" title="ETL pipeline (Fivetran.com)" >}}

{{< admonition type=tip title="Best practice" >}}
ETL rất phù hợp với dữ liệu có cấu trúc (structured data) và khối lượng vừa phải, vì quá trình transform đòi hỏi xác định schema trước và thường không tối ưu khi xử lý khối lượng dữ liệu khổng lồ. Điều này khiến ETL đặc biệt hữu ích cho các hệ thống SQL-only database hoặc các ứng dụng truyền thống yêu cầu chất lượng dữ liệu cao và truy vấn phức tạp (như OLAP) 
{{< /admonition >}}

#### ELT pipeline
{{< admonition type=info title="Lịch sử ELT" >}}
ELT hay Extract-Load-Transform là một phương pháp xử lý dữ liệu được phát triển mạnh mẽ cùng với sự xuất hiện của các công nghệ cloud vào những năm 2000. Điểm khác biệt lớn nhất của ELT so với ETL truyền thống là việc dữ liệu thô được tải thẳng vào hệ thống đích (data warehouse hoặc data lake) trước khi thực hiện các thao tác biến đổi.
{{< /admonition >}}

ELT thay đổi quá trình load lên trước transform, điều này tạo ra sự khác biệt rõ rệt về mặt tốc độ và sự linh hoạt của hệ thống với big data. Quy trình như sau:
1. **Extract**: vẫn giống với ETL, ở ELT ta cũng extract dữ liệu từ nhiều nguồn. Và lần này đa dạng hơn, ngoài các cơ sở dữ liêu quan hệ (SQL), ta cũng có thể trích xuất dữ liệu từ API, tệp tin log, semi-structure data như JSON, hay thậm chí là unstructure data như ảnh và video.
2. **Load**: Dữ liệu sẽ được tải vào một data lake (HDFS, S3, Azure datalake,...) hoặc thẳng vào data warehouse (BigQuery, Snowflake, Redshift,...).
3. **Transform**: Việc biến đổi dữ liệu thường tận dụng các công cụ mạnh mẽ như SQL engine (Snowflake, BigQuery), Python-based frameworks (Databricks, Apache Spark), hoặc các công cụ chuyên biệt như dbt. Mục tiêu là chuyển đổi dữ liệu thô thành dữ liệu sẵn sàng để phân tích hoặc tích hợp với các hệ thống khác.

{{< admonition >}}
Các hệ thống như object storage (HDFS, S3, Azure Data Lake) sử dụng phương pháp schema-on-read, giúp linh hoạt khi xử lý các loại dữ liệu không có cấu trúc hoặc bán cấu trúc. Ngược lại, cloud data warehouses như Snowflake hay BigQuery thường yêu cầu schema-on-write để đảm bảo hiệu năng phân tích, nhưng ngày nay, nhiều hệ thống đã tích hợp khả năng xử lý semi-structured data như JSON hay Avro.
{{< /admonition >}}

{{< admonition type=info title="Schema-on-read Approach" >}}
Schema chỉ được tạo ra khi ta đọc hay phân tích dữ liệu. Hướng tiếp cận này cho phép ta load dữ liệu vào nguồn đích mà không cần quan tâm đến cấu trúc hiện tại của dữ liệu. Khả năng này thường thấy ở các NoSQL database, object storage hay thậm chí là cloud data warehouse với khả năng tự nhận biết schema với semi-structure data (tuy nhiên bản thân những data warehouses này vẫn là Schema-on-write nhưng cho phép ứng dụng gần giống tiếp cận Schema-on-read).
{{< /admonition >}}

**Một số biến thể của ELT**

{{< tabs >}}

{{% tab title="ELTL" %}}

ELTL (Extract-Load-Transform-Load): Data được load vào một low-cost storage (như trước S3 hay Azure Blob) để phục vụ các mục đích khác (machine learning, data discovery). Sau đó được transform và chuẩn hóa để load vào một data warehouse để phân tích. Thường dùng khi data sources đa dạng và dataset lớn

{{% /tab %}}

{{% tab title="ELLT" %}}

ELLT (Extract-Load-Load-Transform): Data được load vào một low-cost storage. Sau đó có thêm bước 'Load thứ hai' để đưa dữ liệu từ đó vào hệ thống phân tích mạnh như Snowflake hoặc BigQuery. Bước này tối ưu chi phí lưu trữ, đồng thời khai thác sức mạnh của các cloud data warehouses để thực hiện transform phức tạp.

{{% /tab %}}

{{% tab title="EL now, T later" %}}

Data được load vào data warehouse hay data lake trước, và được transform mỗi khi nó được gọi. Thường áp dụng cho dữ liệu thô và unstructured data. Mô hình này rất linh hoạt nhưng dễ gây ra vấn đề quản trị dữ liệu nếu không kiểm soát tốt. Việc để dữ liệu thô và chỉ biến đổi khi cần dùng có thể làm tăng thời gian truy vấn hoặc tạo ra các phiên bản dữ liệu khác nhau, gây khó khăn trong việc đồng nhất thông tin

{{% /tab %}}

{{< /tabs >}}

{{< figure title="ELTL pipeline (loomesoftware.com)" src="https://i.imgur.com/IqICT1B.jpeg" >}}

#### ETL vs ELT
Ok bây giờ cùng đưa lên bàn cân so sánh để hiểu rõ hơn sự khác biệt của 2 kiến trúc này
| **Danh mục**             | **ETL (Extract-Transform-Load)**                                      | **ELT (Extract-Load-Transform)**                                      |
|--------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| **Định nghĩa**           | Dữ liệu được trích xuất từ hệ thống nguồn, chuyển đổi trên một máy chủ xử lý trung gian, và sau đó tải vào hệ thống đích. | Dữ liệu được trích xuất từ hệ thống nguồn, tải vào hệ thống đích và chuyển đổi ngay trong hệ thống đích. |
| **Trích xuất (Extract)** | Dữ liệu thô được trích xuất bằng các kết nối API.                    | Dữ liệu thô được trích xuất bằng các kết nối API.                    |
| **Chuyển đổi (Transform)** | Dữ liệu thô được chuyển đổi trên máy chủ xử lý trung gian.           | Dữ liệu thô được chuyển đổi ngay trong hệ thống đích.                |
| **Tải dữ liệu (Load)**    | Dữ liệu đã được chuyển đổi sẽ được tải vào hệ thống đích.            | Dữ liệu thô được tải trực tiếp vào hệ thống đích.                    |
| **Tốc độ**               | Quá trình ETL tiêu tốn thời gian vì dữ liệu phải được chuyển đổi trước khi tải vào hệ thống đích. | ELT nhanh hơn, vì dữ liệu được tải trực tiếp vào hệ thống đích và chuyển đổi song song. |
| **Biến đổi mã (Code-Based Transformations)** | Được thực hiện trên máy chủ trung gian, phù hợp với các chuyển đổi phức tạp và tiền xử lý. | Chuyển đổi được thực hiện trong cơ sở dữ liệu đích, kết hợp tải và chuyển đổi đồng thời, tăng tốc và hiệu quả. |
| **Lịch sử phát triển**    | ETL hiện đại đã tồn tại hơn 20 năm; quy trình và thực hành đã được chuẩn hóa và tài liệu hóa đầy đủ. | ELT là dạng tích hợp dữ liệu mới hơn, tài liệu và kinh nghiệm ít hơn. |
| **Bảo mật**              | Chuyển đổi trước khi tải có thể loại bỏ thông tin nhạy cảm như Personal Identifying Information (PII).      | Tải trực tiếp dữ liệu yêu cầu các biện pháp bảo mật bổ sung.         |
| **Bảo trì**              | Máy chủ xử lý trung gian làm tăng gánh nặng bảo trì.                 | Ít hệ thống hơn nên giảm gánh nặng bảo trì.                          |
| **Chi phí**              | Máy chủ riêng biệt có thể tạo ra vấn đề về chi phí.                 | Hệ thống dữ liệu đơn giản hóa giúp giảm chi phí.                     |
| **Khả năng yêu cầu lại dữ liệu** | Dữ liệu được chuyển đổi trước khi vào hệ thống đích, vì vậy dữ liệu thô không thể được yêu cầu lại. | Dữ liệu thô được tải trực tiếp vào hệ thống đích, có thể yêu cầu lại không giới hạn. |
| **Tương thích với Data Lake** | Không, ETL không hỗ trợ tương thích với Data Lake.                | Có, ELT tương thích với Data Lake.                                   |
| **Dữ liệu đầu ra**        | Thường là dữ liệu có cấu trúc.                                       | Dữ liệu có cấu trúc, bán cấu trúc, hoặc không cấu trúc.              |
| **Khối lượng dữ liệu**    | Lý tưởng cho bộ dữ liệu nhỏ, yêu cầu chuyển đổi phức tạp.            | Lý tưởng cho bộ dữ liệu lớn, yêu cầu tốc độ và hiệu quả.             |

Có thể dễ dàng thấy rằng ELT có tốc độ xử lý nhanh hơn (vì không phải gửi data vào vùng trung gian) và linh hoạt hơn rất nhiều so với ETL.
ELT tỏ ra vượt trội về khả năng mở rộng, linh hoạt, hiệu quả và đặc biệt là xử lý với lượng data rất lớn cả structure và unstructure data. Nhưng ngược lại,
ETL lại có thế mạnh về những tính toán xử lý nặng và phức tạp và cũng khá thích hợp với các hệ thống cần phải xử lý data trước khi đưa vào hệ thống đích (chẳng hạn như xử lý các data nhạy cảm trước khi đẩy vào server)

### Streaming pipeline
**Streaming data pipeline** là một chuỗi các quá trình xử lý dữ liệu liên tục, trong đó dữ liệu được xử lý ngay khi nó được sinh ra hoặc gần như thời gian thực (near real-time). Điều này giúp đảm bảo rằng dữ liệu luôn được cập nhật và có thể phân tích, phản hồi một cách nhanh chóng.

Streaming pipeline thường được sử dụng trong các tình huống yêu cầu **phản hồi nhanh** và **phân tích dữ liệu thời gian thực**, chẳng hạn như:
- **Tài chính**: Giám sát thị trường chứng khoán hoặc phát hiện giao dịch gian lận.
- **Y tế**: Theo dõi sức khỏe bệnh nhân qua thiết bị IoT.
- **E-commerce**: Gợi ý sản phẩm theo thời gian thực dựa trên hành vi người dùng.
- **Internet of Things (IoT)**: Thu thập và xử lý dữ liệu cảm biến liên tục từ thiết bị.

#### Batch processing vs Stream processing
Hiện có hai phương pháp xử lý chủ lực là Batch processing và Stream Processing. Tùy vào nhu cầu mà ta sẽ ứng dụng các phương pháp này sao cho phù hợp

{{< tabs >}}
{{% tab title="Batch processing" %}}
Batch Processing hay xử lý theo lô là việc xử lý một tập dữ liệu lớn trong một lô với khoảng thời gian cụ thể. Được dùng khi ta biết trước kích thước và có giới hạn. Tất nhiên cũng tốn nhiều thời gian để xử lý. Khi dữ liệu được collect qua thời gian sẽ được group lại theo lô và xử lý. Thông thường ta sẽ viết ETL pipeline để làm việc này và việc xử lý có độ trễ cao, và tốn thời gian.
{{% /tab %}}

{{% tab title="Stream processing" %}}
Như cái tên của nó, là xử lý dữ liệu trong thời gian thực và đưa ra kết quả gần như ngay lập tức. Ta thường sẽ không biết trước kích thước data và data cũng không giới hạn, liên tục. Không như hệ thống ETL khi ta phải thu thập data, với streaming, nguồn data sẽ “gửi” data vào một Messaging system rồi luồng luân chuyển đến pipeline để transform. Có khá nhiều tool để xử lý streaming như Apache Flink hay Spark Streaming. Dữ liệu sau đó có thể được write vào một database có tốc độ phản hồi nhanh để phục vụ cho phân tích
{{% /tab %}}
{{< /tabs >}}

{{< figure src="https://i.imgur.com/1DhvhaP.png" title="Batch vs Stream" >}}

#### Workflow
{{< figure src="https://www.montecarlodata.com/wp-content/uploads/2024/03/Data-pipeline-architecture-streaming-kafka-1536x459.jpg" title="Streaming pipeline (montecarlodata.com)" >}}

**Data Source**: Dữ liệu được sinh ra từ các nguồn như thiết bị IoT, log server, ứng dụng hoặc hệ thống giao dịch.

**Messaging System:** Đóng vai trò như một trung gian để lưu trữ tạm thời và truyền dữ liệu. Có hai kiểu messaging chính:

- **Point-to-point (P2P)**: Một message chỉ được tiêu thụ bởi một consumer. Dữ liệu sẽ bị xóa sau khi được đọc (RabbitMQ, Kafka).
- **Publish-Subscribe (Pub/Sub)**: Một message có thể được tiêu thụ bởi nhiều consumer. Phù hợp khi nhiều hệ thống cần dữ liệu giống nhau (Apache Kafka, GCP Pub/Sub, Amazon SNS).

**Processing Engine**: Dữ liệu được xử lý thông qua các công cụ như Apache Flink, Spark Streaming, AWS Lambda, Apache Storm

**Storage:** Tùy vào nhu cầu, dữ liệu có thể được lưu vào hệ thống phản hồi nhanh (NoSQL như Cassandra, DynamoDB, MongoDB) hay batch storage cho việc phân tích (HDFS, S3, BigQuery,..)

### Zero ETL
{{< figure src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2024/07/18/dbblog3225001.jpg" >}}

Được AWS giới thiệu vào năm 2022, zero-ETL là hệ thống tích hợp dữ liệu nhằm giảm thiểu nhu cầu di chuyển dữ liệu giữa các nền tảng. Zero-ETL cho phép truy vấn trục tiếp trên các data silos mà không cần phải di chuyển dữ liệu. Thậm chí điều này còn cho phép khả năng phân tích real-time và tiết kiệm chi phí.

Zero-ETL đòi hỏi cả OLTP và OLAP database đều phải thuộc cùng một nền tảng cloud (như Amazon Aurora và Amazon Redshift). Nhưng lại dễ triển khai hơn vì hệ thống được đơn giản hóa.

## Các kiến trúc data pipeline phổ biến
Trong thực tế, việc thiết kế hệ thống dữ liệu có thể được xử lý với nhiều chiến lược phức tạp, cùng điểm qua một số pattern để xây dựng
được một hệ thống data pipeline hiệu quả nào

### Lambda Architecture
Ta đã biết về batch processing và stream processing rồi. Liệu trong cùng một hệ thống có thể áp dụng đồng thời cả 2 kiểu xử lý data này không ? Câu trả lời
là **có**. Và đó cũng là ý tưởng của kiến trúc Lambda.

Lambda là một kiến trúc hybrid bao gồm một batch data pipeline truyền thống kết hợp với một stream data pipeline có tốc độ cao để xử lý dữ liệu real-time. Lambda cơ bản là
gồm 3 layers:
1. **Batch layer (Cold process)**: Chính là các ETL xử lý chung data đầu vào để xây dựng master dataset (là toàn bộ data và đầy đủ hơn data cả ở speed layer). Quy trình của lớp này vẫn là chạy theo lịch xử lý để đưa data vào data warehouse.
Kết quả của batch layer là tạo ra các batch view
2. **Speed layer (Hot process, stream)**: Layer này giải quyết các data hiện tại để tạo ra view real time hoàn chỉnh cho user. Đầu ra của speed layer chính là các real-time view
3. **Serving layer**: Đây là layer sẽ là nơi để query các batch view cũng như real time view cho user. Các view này cũng được đánh index để tối ưu cho việc truy vấn.

{{< figure src="https://media.geeksforgeeks.org/wp-content/uploads/20240117220316/Layer-of-Lambda-Architecture.jpg" title="Lambda Architecture (geeksforgeeks.org)" >}}

Một số ưu điểm của Lambda Architecture:
- Khả năng xử lý real-time: Batch processing truyền thống không được thiết kế để đáp ứng real-time data. Tuy nhiên nhờ vào thiết kế hybrid, lambda có thể
xử lý được cả các data real-time một cách hiệu quả ngay từ các transaction database ngay khi chúng được tạo ra
- Cân bằng giữa real time processing với historical data processing

Nhược điểm:
- Độ phức tạp cao: độ phức tạp trong thiết kế cũng như duy trì 2 hệ thống song song làm cho kiến trúc này trở nên khó khăn trong việc bảo trì 
- Chi phí tính toán cao: vì kết hợp cả 2 luồng cùng lúc, chi phí tính toán của cả hệ thống nhìn chung là có khả năng tăng lên nhiều.

### Kappa Architecture
Kiến trúc Kappa là kiến trúc được đơn giản hóa từ Lambda bằng cách chỉ sử dụng streaming layer. Trong thiết kế này, ta giả định tất cả data
đều được dùng như stream data và được process ngay khi nó đến. Kể cả dữ liệu quá khứ (historical data) cũng được process thông qua cùng một pipeline với realtime data.
Kiến trúc này thường được dùng khi ứng dụng cần real-time processing và không yêu cầu một batch layer phức tạp như IoT data stream hay real time dashboard.

Ưu điểm:
- Tính đơn giản: Kappa có thiết kế đơn giản hơn và dễ xây dựng hơn rất nhiều so với lambda
- Tính ứng dụng cao: Phù hợp hơn đối với trường hợp historical data không thực sự khác biệt so với live data

Nhược điểm:
- Không phù hợp và giới hạn trong việc xử lý batch

### Event-Driven Architecture (EDA)
{{< admonition type=note >}}
Mục này chỉ mang tính giới thiệu :smile:
{{< /admonition >}}

Trong Event-Driven Architecture hay kiến trúc hướng sự kiện, hệ thống tương tác với các
events khi chúng xảy ra. Kiểu thiết kế này tập trung vào các thành phần rời nhau và sử dụng
các messages hay events và xử lý data dựa trên việc kích hoạt hơn là chạy theo lịch 

Thiết kế này thường sử dụng message queue như RabbitMQ hay event buses như Kafka để trigger việc xử lý. Thường được dùng
trong các hệ thống e-commerce cũ khi từng event (thực hiện order, thanh toán) sẽ trigger các hành động khác nhau.

Ưu điểm:
- Khả năng mở rộng cao và linh hoạt khi được thiết kế theo microservices
- Độ trễ thấp và tốc độ phản hồi cao đến các event

Nhược điểm:
- Độ phức tạp cao giữa các service và workflow
- Yêu cầu thiết kế cẩn thận để đảm bảo hệ thống có độ chịu lỗi và sự đồng nhất

### Data Lake Pattern
{{< admonition type=tip >}}
Data lake là một kho lưu trữ được thiết kế để lưu lượng lớn raw data ở định dạng gốc của nó. Trong thực tế, data lake là một "central hub" cho data dạng các nguồn data khác nhau.
{{< /admonition >}}

Thiết kế này đơn giản là ứng dụng data lake vào để lưu raw data gồm cả strucure, semi-structure hay unstructure data. Data Lake thường là hệ thống
phân tán như Hadoop HDFS hay cloud storage như AWS S3, Google Cloud Storage (GCS). Data ở đây sẽ được transform và xử lý sau đó nếu cần.

Ưu điểm:
- Tính linh hoạt với việc lưu được đa dạng data types
- Tối ưu chi phí cho việc lưu trữ dữ liệu thô lâu dài

Nhược điểm:
- Quản lý dữ liệu (Data gorvernance) có thể phức tạp
- Có thể trở thành một "Vũng lầy data" (Data Swamp) nếu không quản lý đúng cách

{{< admonition type=question title="Data Swamp" >}}
Là một data lake xuống cấp và không có tổ chức. Khi data lake không được quản lý, data trong đó trở nên
lộn xộn và khó sử dụng.
{{< /admonition >}}

### Fan-Out/Fan-In Pattern
Fan-Out đề cập đến việc chia data stream thành nhiều tác vụ xử lý stream song song. Ngược lại, Fan-In sẽ gộp các kết quả lại
với nhau. Kiểu thiết kế này thường hữu ích trong việc xử lý data có lưu lượng lớn

Ưu điểm:
- Cải thiện tốc độ xử lý nhờ vào xử lý song song
- Giúp xử lý lượng lớn công việc một cách hiệu quả

Nhược điểm:
- Phức tạp khi xử lý phân tán và gộp kết quả
- Khó khăn trong quản lý lỗi

## Conclusion
Có rất nhiều các thế kế và xử lý data khác nhau, tùy vào từng trường hợp và nhu cầu mà ta có thể sẽ phải thiết kế hệ thống sao cho phù hợp
và tối ưu nhất về mặt chi phí tính toán lưu trữ và hiệu quả về mặt hiệu năng. Hy vọng bài viết sẽ giúp bạn phần nào đó hiểu rõ cách mà các pipeline vận hành và cách thiết kế các kiến trúc
pipeline hiện đại. Giờ thì end thoy :smile: Hẹn gặp lại trong số blog sau.

**-Mew-**

---

# References
[Data pipeline design pattern system design - GeeksforGeeks](https://www.geeksforgeeks.org/data-pipeline-design-patterns-system-design/)

[What is the Difference Between ETL and ELT? - AWS](https://aws.amazon.com/compare/the-difference-between-etl-and-elt/?nc1=h_ls)

[Data Pipeline Architecture Explained: 6 Diagrams and Best Practices - Montecarlo Data](https://www.montecarlodata.com/blog-data-pipeline-architecture-explained/)

