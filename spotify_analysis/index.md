# Spotify_analysis

{{< admonition warning "Đang thi công">}}
Bài viết vẫn đang trong quá trình.... viết :)) Sẽ hoàn tất trong thời gian sớm nhất. Bạn có thể ghé qua link
Github xem trước nhea :heart:
{{< /admonition >}}
<!--more-->
**Source (Nếu thấy hay, ngại gì tặng chúng tớ 1 sao :joy:)**
{{< friend name="PhongHuynh0394" url="https://github.com/PhongHuynh0394/pythonDS" avatar="/img/github-logo.png" bio="Spotify_analysis">}}

<!--{{< admonition info "Shout out cho anh em">}}-->
<!-- Dự án này là sự đóng góp không hề nhỏ của các thành viên team OG: **Trần Ngọc Tuấn, Phạm Duy Sơn, -->
<!-- Mai Chiến Vĩ Thiên**. Cảm ơn sự đóng góp của các bạn đã giúp cho dự án hoàn thiện :joy: -->
<!-- {{< /admonition >}} -->

<!-- Hello ! Hello ! Lại là OG đây. Lần này mình sẽ cùng nhau xây dựng -->
<!-- End-to-End ETL data pipeline và một Recommender System dựa trên dữ liệu phân tích từ Spotify API nhé. -->
<!-- Không dài dòng nữa, bắt đầu thôi nào. -->

<!-- ## Introduction -->
<!-- Lần này, OG sẽ hướng dẫn bạn xây dựng hệ thống data pipeline hoàn chỉnh thực hiện quá trình ETL -->
<!-- để lưu và phân tích data từ Spotify API. Cụ thể như sau: -->

<!-- {{< image src="https://github.com/PhongHuynh0394/pythonDS/blob/Prefect/image/spotify_data_pipeline.png?raw=true" >}} -->

<!-- {{< admonition note "Giải thích về data flow" >}} -->

<!-- Từ trái sang phải, ban đầu ta sẽ cào danh sánh tên các nghệ sĩ từ trang [**này**](https://kworb.net/spotify/artists.html) -->
<!-- và sau đó dùng Spotify API để cào các thông tin khác như track, album, genre từ danh sách nghệ sĩ. -->
<!-- Sau đó ta sẽ chạy đa luồng và theo batch để đẩy data vào `MongoDB Atlas` (lý do vì sao phải dùng MongoDB chổ này mình sẽ giải thích ở dưới). -->
<!-- Kế đó là data pipeline chính, ta sẽ thực hiện quá trình ETL để transform dữ liệu raw từ MongoDB. Dữ liệu -->
<!-- thô sẽ được Extract và đưa vào `HDFS`, sau đó ta sẽ dùng `PySpark` để làm sạch, tách bảng dữ liệu này. Kế đến,  -->
<!-- chính là công việc của `Dremio`, đây là một nền tảng warehouse và ta sẽ phân tích dữ liệu của mình ở đây, ngoài ra -->
<!-- ta cũng sẽ dùng notebook để nghiên cứu và traning Recommender model. Ta cũng sẽ sử dụng `Power BI` để làm report -->
<!-- từ các kết quả phân tích ở Dremio. Cuối cùng mọi thứ sẽ được trực quan và chạy demo ở `Streamlit UI`. -->
<!-- Tất nhiên là tất cả các services này đều được xây dựng bởi `Docker` và Pipeline xây dựng bởi `Prefect`. -->

<!-- {{< /admonition >}} -->
<!-- Ta sẽ lần lượt thực hiện các bước như sau: -->
<!-- 1. **Set up Docker**: Để bắt đầu dự án, ta sẽ dùng `Docker` để xây dựng các images cho các services -->
<!-- 2. **Set up Environment**: Trong dự án này, ta sẽ sử dụng MongoDB Atlas và Spotify API, ta sẽ cần phải chuẩn bị API key và -->
<!-- một vài thông tin để có thể kết nối được với Cloud. -->
<!-- 3. **Run pipeline 1**: Sau khi hoàn thành việc set up, ta sẽ chạy pipeline 1 để cào data và lưu vào MongoDB. Việc -->
<!-- này nhằm mục đích chuẩn bị nguồn raw data để chạy pipeline 2 -->
<!-- 4. **Run pipeline 2**: Khi ở MongoDB đã có một lượng data nhất định, ta sẽ chạy pipeline 2 để bắt đầu transform dữ liệu để phân tích -->
<!-- 5. **Dremio warehouse**: Kế đến, ta cần connect Dremio với HDFS để có thể query và phân tích -->
<!--     5.1 **Power BI**: Bước này là `Optional`, ta có thể sử dụng Power BI để trực quan hóa dữ liệu đã được phân tích ở data warehouse -->
<!-- 6. **Visualization**: Sau khi tất cả hoàn tất, Streamlit sẽ hiện thành quả phân tích dashboard và cả model được train ở notebook.  -->

<!-- Đó là toàn bộ quy trình hệ thống trên. Bây giờ ta sẽ nhào dô set up luôn cho nóng :smile: -->

<!-- ## Set up  -->
<!-- ### Set up environment -->

<!-- Đầu tiên ta cần phải set up environment, hãy copy content của `env_template` vào file `.env` -->
<!-- ```bash -->
<!-- cp env_template .env -->
<!-- ``` -->

<!-- ## Run your Data Pipeline  -->
<!-- ### Pipeline 1 (Ingest MongoDB Atlas flow) -->

<!-- pipeline 1 here -->

<!-- ### Pipeline 2 (ETL flow) -->


