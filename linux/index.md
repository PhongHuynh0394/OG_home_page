# Linux - tiếng sét ái tình


<!--more-->

Xin chào xin chào là OG đâyy ! Ở bài trước OG đã kể cho các bạn nghe về các khái niệm cơ bản trong ngành Data rồi.
Thế thì ở số này mình sẽ nói nhiều hơn về viên gạch đầu tiên khi mình đến với Data Engineer. Đó chính là việc mình
biết đến hệ điều hành Linux, kể từ đó cuộc đời mình đổi thay.

Lets goo!

## Quá khứ bi đát
Trước khi đến với người anh bạn cánh cụt cư tê kia, mình đã từng làm việc với chiếc laptop cùi bắp (intel pentium)
win 8.1. Khi đó OG vừa bước chân vào giảng đường đại học, và cứ nghĩ rằng thế này đã đủ xài, có thể hơi chậm chạp nhưng có lẽ sẽ sống ổn.

Ôi thật ngây thơ :cry: ngay nửa học kì đầu tiên của năm học mới, cái laptop đó đã dốc từng nhịp thở và chạy chương trình bằng cả tính mạng. Đó là trải nghiệm học tập không mấy dễ chịu khi phải đối mặt với con lap ì ạch như vậy trong suốt
một khoảng thời gian dài. Và một combo hủy diệt đi kèm với sự ì ạch đó: hệ điều hành WINDOW, đặc biệt là phiên bản 8 và 8.1 .

OG trước đây là một fan của window, phải thú thực là vậy. Giao diện dễ dùng, thân thiện. Nhưng có 1 vấn đề, nó quá nặng và chậm chạp (ít nhất là đối với laptop của OG). Còn cả về lỗi và các bản update mà chỉ khi dùng window, OG mới cảm nhận được sự đau khổ.

Thế là có 1 ngày nọ, một người bạn đã chỉ mình chuyển sang Linux và cuộc sống lập trình của mình bước sang một trang mới toanh.

## Linux - Tiếng sét ái tình
Trước khi được giới thiệu về Linux, OG hoàn toàn không biết gì về nó trước đó. Từ Terminal, Distro,... Hoàn toàn là tờ giấy trắng tinh. Tuy nhiên OG vẫn chọn hệ điều hành này vì nó NHANH, rất nhanh là đằng khác. Ngay khoảnh khắc đó, OG đã không biết mình đã mê đắm hđh này mất rồi :kissing_heart:

Và sau một thời gian học tập và làm việc với anh bạn :penguin: cư tê thì OG đã không còn muốn quay lại cửa sổ (window) lần nào nữa. Vậy Linux có gì hay hãy đọc tiếp nhé

![Linux](https://media.discordapp.net/attachments/1155749221677400085/1156609682962653255/linux-logo.jpg)

### Hệ điều hành chất chơi người dơi
Linux là một hệ điều hành được phát triển từ năm 1991 bởi Linus Torvalds dựa trên hệ điều hành Unix. Chú cánh cụt này được viết bằng C và đảm nhận nhiệm vụ nhận các request từ chương trình trên máy tính và chuyển chúng đến phần cứng.

Điều đặc biệt nhất có lẽ là nó được phát hành **miễn phí** (open source). Nghĩa là bạn có thể dễ dàng cài Linux một cách quang minh chính đại mà không cần phải crack hay "Đi cửa sau" với nhiều rủi ro lỗi như khi sử dụng hđh khác. Thậm chí bạn có thể chỉnh sửa, đóng góp xây dựng thêm cho hệ điều hành này nếu muốn.
Không những vậy, cộng đồng hỗ trợ của hệ điều hành này cũng rộng lớn và vô cùng "bá đạo" về nhiều khía cạnh :joy: Bạn sẽ có thể gần như ngay lập tức được giải đáp hầu hết các thắc mắc hay trục trặc khi sử dụng linux.
Cộng đồng này đa số là các lập trình viên, nhà phát triển,... Vì chú cánh cụt này hệ điều hành ưu thích và thích hợp để phát triển sản phẩm hoặc xây dựng hệ thống nhờ là ứng dụng
mã nguồn mở và tính bảo mật cao.

Khi chuyển sang dùng Linux, bạn cũng không cần phải quá lo lắng về việc thay đổi môi trường và thích nghi với hệ điều hành này. Vì cơ bản Linux vẫn giống với những hệ điều hành trước đây bạn dùng thôi.
Bạn vẫn có thể thao tác với giao diện hệ điều hành, vẫn sử dụng được các phần mềm như xử lý văn bản, edit video hay ảnh,... Nghĩa là các thao tác sử dụng thông thường, Linux vẫn đáp ứng tốt và còn rất nhanh.

### Shell
Hẳn là có thể đâu đó, bạn thấy một anh chàng ngầu lòi nào đó bấm vài dòng lệnh, Bụp enter. Rồi tự nhiên một đống chữ xuất hiện dồn dập trên cái nền terminal tím. Ồ có thể anh ta đang sử dụng shell đấy. Hoặc
là một lập trình viên, bạn đã quá chán với giao diện Command Prompt và PowerShell ? Hãy chuyển sang Linux và bạn sẽ bước vào thế giới huyền diệu khi sử dụng terminal.

{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157633553908105277/Screenshot_7.png?ex=651951c3&is=65180043&hm=8adadba96871d23eb0bc773a66300d5b7c6271358cb15681fc3832ef804bc692&" caption="Terminal của OG" title="Ubuntu 22.04.3 LTS" >}}

Shell là một chương trình phát triển dành cho các máy tính chạy trên hệ điều hành Unix và Linux. Phần mềm này cũng cấp giao diện người dùng nhập và giao tiếp
với máy tính dưới dạng văn bản. Và trên các máy tính Ubuntu thì Shell cũng có thể gọi là Terminal.

Đối với người dùng Linux, việc thao tác với terminal là điều gần như hiển nhiên. Đặc biệt thích hợp cho các "cót đơ" hay nhà phát triển. Việc chạy lệnh trên Terminal
ngoài việc trông có vẻ "nguy hiểm" hơn, trải nghiệm sử dụng shell cũng được cho là thoải mái hơn trên window rất nhiều. Không những vậy, với một cộng đồng cực lớn, việc làm quen với
shell dường như đã trở nên dễ dàng hơn bao giờ hết.

Việc sử dụng shell như thế nào có lẽ OG sẽ để dành lại vào 1 bài khác nhé hihi

### Đa dạng phiên bản
Vì là sản phẩm mã nguồn mở, Linux có rất nhiều phiên bản khác nhau. Các **Linux Distro** là phần đã góp phần vào sự thú vị nói chung của hệ điều hành này và giúp cho chúng ta có đa dạng
sự chọn lựa cho phiên bản mình yêu thích. Hiện có khoảng 600 bản Distro và hơn nửa trong số đó được liên tục phát triển và cải thiện.

{{< image src="https://media.discordapp.net/attachments/1155749221677400085/1156992218054266900/linux-distro-stickers.png?ex=6516fc78&is=6515aaf8&hm=22ea42fd0863243517b87d37e2cf2798554db5428c0c31dfd3191f3d88b81eaa&=&width=2500&height=500" title="Linux distro" width=1000 >}}

Có đa dạng các distro và phù hợp với đa dạng các nền tảng khác nhau từ desktop, laptop, di động,... Và cũng nhắm tới phục vụ cho đa dạng các đối tượng khác nhau. Hãy cùng xem qua một số distro phổ biến
nhất nào

1. **Ubuntu**: đây là phiên bản đông đảo người dùng sủ dụng nhất. Đây chính là một nhánh của Debian Linux. Ubuntu có giao diện dễ nhìn và dễ tiếp cận, do đó người dùng mới
không khó để làm quen và sử dụng. Hơn nữa là cộng đồng sử dụng lớn nên bạn sẽ dễ dàng tìm được giải đáp cho các thắc mắc mình trong khi sử dụng.
Và OG cũng đãng sử dụng Distro này :smile:
{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157340523330281565/Ubuntu_23.04_Lunar_Lobster_English_1.png?ex=651840db&is=6516ef5b&hm=d3e9676b70fbd59b20bfc94cc0437938b398644d631a1f30a9606d796bcfee8e&" caption="Ubuntu 23.04 Lunar Lobster" title="Ubuntu" >}}

2. **Linux Mint**: Là một trong những distro tốt nhất dành do người dùng Linux mới. Giao diện Desktop của Mint rất là Window, tạo cảm giác vô cùng quen thuộc và dễ thích nghi đối với
người dùng mới chuyển hoặc ưu thích Window. Đây cũng là một trong những điểm đặc sắc của distro này. Ngoài ra Linux Mint dựa trên Ubuntu, do đó cũng có thể chạy ứng dụng dành cho Ubuntu.
{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157342184375320648/linux_mint.png?ex=65184267&is=6516f0e7&hm=49208e455955eec0d3028630bfc85f0c28287732ae6bc77fe06c3b5cec5d384d&" title="Mint" caption="Linux Mint 21 \"Vanessa\"">}}

3. **Fedora**: Trước đây gọi là `Fedora core`, được phát triển dựa trên cộng đồng theo Fedora Project và được bảo trợ bởi Red Hat (Công ty con của IBM). Đây là một trong
những bản phân phối có tốc độ nhanh và ổn định nhất. Được ưu chuộng bởi các nhà phát triển.
{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157621812289613896/Fedora_Linux_35_About_3840px_2160px_Zoom2.5.png?ex=651946d3&is=6517f553&hm=1ab95b91beb0f1c4b92c23d45e9ee9d0fa52e256159079fd7b3f84a0e0fc7bd1&" caption="Fedora Linux 35" title="Fedora" >}}

4. **CentOS**: Một trong những phân phối Linux dành cho môi trường server, CentOS dựa trên mã nguồn mở của Red Hat Enterprise Linux (RHEL) và miễn phí. CentOS có độ ổn định cao và được nhiều công ty ưu chuộng.
{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157624014601203742/CentOS_7.0_GNOME.png?ex=651948e0&is=6517f760&hm=1541cd8b082619b8935835f1d28675002a09016a8c02e0939fe1dfebe054f889&" caption="CentOS 7.0" title="CentOS" width=1000 height=700 >}}

5. **Arch Linux**: Bản phân phối này là một "Rolling Release", đại loại như là nó sẽ luôn được cung cấp bản cập nhật phần mềm sớm nhất có thể và cải thiện tốt nhất. Điều
đặc biệt của distro này là khả năng cá nhân hóa rất cao, bạn thậm chí sẽ không nhận được giao diện đồ họa khi mới bắt đầu. Đổi lại bạn sẽ có thể thiết kế, xây dựng theo sở thích của chính mình.
{{< image src="https://cdn.discordapp.com/attachments/1155749221677400085/1157626560338210886/ykf1z3vebaw71.png?ex=65194b3f&is=6517f9bf&hm=f89fd63e6e7c78d648652402a130f84fd9928233ba502be2e939360293936134&" caption="Arch Linux" title="Arch Linux" >}}

Và còn rất rất nhiều phân phối khác thú vị nữa mà kể ra chắc phải thành sách mất :joy:

### Linux vs Window
Hiện nay có 3 hệ điều hành phổ biến là Window, Linux, MacOS. Đối với Mac thì OG chưa trải nghiệm (vì mình nghèo :cry:). 

Do đó chúng ta sẽ so sánh trải nghiệm giữa 2 hệ điều hành này theo cảm nhận của OG sau một thời gian sử dụng Linux và cả Window nhé

- **Đối tượng sử dụng**: Đối với Window, đã quá phổ biến rồi, do đó đối tượng hướng đến cũng rất rộng rãi già trẻ lớn bé đều có thể sử dụng dễ dàng sử dụng.
Ngược lại, những người sử dụng Linux thường là các lập trình viên và các nhà phát triển. Họ là người hiểu về hệ thống và có thể kiểm soát hệ thống.
- **Bash Shell**: Đầu tiên, đối với việc lập trình cần phải thao tác nhiều với hệ thống thì việc sử dụng cmd hay PowerShell gần như là 1 trải nghiệm
"đồ đá" đối với OG. Window 10 có hỗ trợ cmd và PowerShell nhưng cả 2 loại này đều có giao diện cũ kĩ và cú pháp dài dòng, khó nhớ. Trong khi đó, sử dụng Bash Shell ở
Linux đưa lại trải nghiệm mượt mà hơn với câu lệnh dễ nhớ, màu sắc cũng đa dạng hơn và tất nhiên là cũng dễ dùng hơn rất nhiều.
- **Xử lý, can thiệp mã nguồn**: Trong khi sử dụng Window, việc truy cập mã nguồn gần như là bất khả thi. Thì đối với người dùng Linux lại ngược lại, bạn hoàn
toàn có khả năng truy cập hay chỉnh sửa mã nguồn đến tận nhân. Việc cấu hình hệ thống theo sở thích cá nhân hoàn toàn là điều khả thi và tạo tính linh hoạt khi sử dụng.
- **Xử lý văn bản, hình ảnh**: Về phương diện này, phải nói là không thể qua được Microsoft Office 365 của Window, tất nhiên Linux cũng có LibreOffice, tuy nhiên phần mềm này vẫn
còn khá hạn chế và kém. Ngoài ra việc sử dụng các công cụ chỉnh sửa hình ảnh, video như Adobe Photoshops hay Adobe Premiere trên linux cũng là không thể.
- **Gaming**: Thành thật mà nói, nếu bạn sử dụng linux, thì hãy tập bỏ dần thói quen chơi game đi :joy: vì hầu hết các game hỗ trợ tốt nhất trên hệ điều hành Window
- **Độ bảo mật**: Tất nhiên, không thể phủ nhận Window là một hệ điều hành cực kỳ bảo mật. Nhưng nó luôn là mục tiêu của các cuộc tấn công vì độ phổ biến quá lớn của nó. Trong khi người dùng Linux hầu hết là các lập trình viên và tất nhiên là cả Hacker.

Okay nói quá trời nói thì tóm gọn qua bảng sau đây:
| Tiêu chí | Window | Linux |
| :------: |:------:|:------:|
| Đối tượng | Người dùng phổ thông với nhu cầu cơ bản: lướt web, công việc văn phòng,... | Lập trình viên, nhà phát triển, hacker,...|
| Giao diện Command Line | Thô sơ, khó dùng với Command Prompt, PowerShell | Mạnh mẽ với nhiều loại shells (Bash, Zsh,...) |
| Khả năng tùy chỉnh mã nguồn | Không thể | Hoàn toàn có thể, giúp cá nhân hóa và tạo tính linh hoạt |
| Xử lý văn bản, phim ảnh | Rất tốt với bộ Office 365, Adobe Photoshops,... | Có thể sử dụng LibreOffice chỉnh sửa cơ bản nhưng chức năng ít |
| Game | Hỗ trợ tốt | Khá khó khăn |
| Độ bảo mật | Tốt, nhưng dễ bị nhắm tới | Tốt và có tính bảo mật cao |

Việc học lập trình hay quản lý hệ thông, có lẽ Linux sẽ là lựa chọn hoàn hảo hơn (hoặc nếu có điều kiện, hãy mua Mac :smile: thật đấy !). Còn nếu bạn là người dùng phổ thông,
không có nhu cầu phải biết về lập trình, quản lý hạ tầng,... thì cứ cửa sổ mà dùng thôi hihi

## Muốn thì tìm cách
Nếu bạn là một người dùng Mac, chúc mừng bạn đã quay vào ô an toàn :smile: sướng nhất bạn rồi hi. Còn nếu sử dụng Window mà bất giác có một tình yêu với Linux thì phải làm sao đây ?
Không lẽ phải bỏ Win cài lại Linux sao ?

Câu trả lời là **Không**. Hiện nay có 3 cách chính để bạn có thể sử dụng Linux mà không cần phải từ bỏ chiếc cửa sổ của mình:
1. **Dùng máy ảo**: Việc đầu tiên mà hầu hết mọi người nghĩ tới khi dùng một hệ điều hành khác với hệ điều hành chính của máy chính là dùng một con máy ảo. Bạn có thể sử dụng
VirtualBox hoặc VMware PLayer một cách miễn phí để cài đặt. Nhưng với OG, sử dụng máy ảo chả khác nào một cực hình, vì nó chậm kinh khủng khiếp. Vì loại máy ảo này chạy trên hệ điều hành máy và sử dụng công nghệ
ảo hóa của CPU để tạo ra các máy ảo khác. Cơ bản là phải thông qua một OS trung gian (Window), do đó tốc độ của nó thực sự như rùa.
2. **Dùng dual boot**: Cơ bản cách này là cài đặt cho máy tính bạn chạy được cả 2 hệ điều hành cùng một lúc, cách này được khá nhiều người sử dụng và tất nhiên là nhanh hơn việc sử dụng máy ảo.
3. **Dùng WSL**: hay còn được gọi là Window Subsystem for Linux, về bản chất mà nói thì đây cũng là một loại máy ảo. Tuy nhiên nó là loại máy ảo chạy trên một
nền tảng ảo hóa của CPU là Hyper-V, và không hề thông qua hệ điều hành trung gian nào. Có thể hiểu là công nghệ Hyper-V là công nghệ ảo hóa của CPU cung cấp các tầng ảo hóa, kể cả cho chính Window của bạn.
Việc sử dụng máy ảo loại này thực sự nhanh hơn rất nhiều và cũng không cần phải restart máy để chuyển sang OS khác như cách dual boot mà đơn giản chỉ là tắt wsl trên PowerShell đi là xong. Quá đã phải không nào.

Đối với OG thì mình đang dùng cách 3 để có thể sử dụng Linux trên Window một cách hoàn toàn miễn phí và trải nghiệm vô cùng ổn định. Microsoft đã tạo ra WSL để giúp giải tỏa cơn khát Linux của người dùng Window và 
giúp cho cuộc sống các lập trình viên thêm ngọt ngào dễ sống hơn :joy:

> Hoặc bạn có thể ghé thăm page [**Ubunchuu trường ú**](https://www.facebook.com/ubunchuu.club.hcmus) để tìm hiểu tất tần tật mọi thứ về Linux và Ubuntu nhé. Đây là kênh
do một nhóm các sinh viên đam mê với linux của HCMUS thành lập nhằm tạo cộng đồng linux giúp đỡ lẫn nhau. Và tất nhiên là hướng đến các bạn người mới, newbie với Ubuntu rồi.

## Cúng cùi
Và đó là tất cả cảm nhận của OG về việc sử dụng Linux, cũng như là giới thiệu một chút về chú cánh cụt :penguin: cư tê này. Tất cả chỉ là cảm nhận cá nhân của OG trong quá trình
sử dụng Linux, hy vọng rằng sẽ giúp bạn cảm thấy thú vị.

-Mew-

