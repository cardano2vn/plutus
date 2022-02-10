.. _what_is_a_ledger:

Sổ cái là gì?
=================

:ref:`Nền tảng<what_is_the_plutus_platform>` được thiết kế để hoạt động với các sổ cái phân tán (từ đó trở đi chỉ đơn giản là "sổ cái"). Sổ cái thường được triển khai với một blockchain, chẳng hạn như Cardano. Tuy nhiên, phần lớn khi chúng ta nói về sổ cái, chúng ta không quan tâm đến việc triển khai cơ bản, và vì vậy chúng ta sẽ chỉ nói về bản thân sổ cái.

.. note::
   Điều này không phải lúc nào cũng đúng: các ứng dụng cần quan tâm đến chi tiết về cách hoạt động của blockchain cơ bản, vì điều đó ảnh hưởng đến hành vi như thời gian giải quyết và chính sách hoàn trả. Khung ứng dụng Plutus cố gắng bảo vệ các nhà phát triển khỏi sự phức tạp này càng nhiều càng tốt, nhưng không phải lúc nào cũng có thể.

Ở dạng đơn giản nhất, sổ cái là một hệ thống theo dõi ai sở hữu cái gì..

Ví dụ:

+------------+----------+
|Chủ nhân    |  số dư   |
+============+==========+
| Alice      | 43 USD   |
+------------+----------+
| Bob        | 12 USD   |
+------------+----------+
Sổ cái thường được chuyển đổi bằng cách thực hiện một *giao dịch* chuyển một số tài sản từ bên này sang bên khác. Để có *hiệu lực*, một giao dịch sẽ phải vượt qua một số kiểm tra, chẳng hạn như chứng minh rằng việc chuyển tiền được chủ sở hữu của khoản tiền. Sau khi áp dụng một giao dịch (giả sử Alice gửi cho Bob 5USD), chúng tôi có một trạng thái mới của sổ cái.

+------------+----------+
|Chủ nhân    |  số dư   |
+============+==========+
| Alice      | 38 USD   |
+------------+----------+
| Bob        | 17 USD   |
+------------+----------+

Sổ cái dựa trên tài khoản và dựa trên UTXO
------------------------------------

Có hai mô hình để *thể hiện* sổ cái như thế nào.
Sổ cái đầu tiên dựa trên tài khoản, mô hình hóa hệ thống chính xác như trong ví dụ của chúng tôi ở trên. Họ giữ một danh sách các tài khoản và cho mỗi tài khoản, một số dư. Một giao dịch chỉ đơn giản là làm giảm số dư của người gửi và tăng số dư của người nhận.

Sổ cái dựa trên tài khoản (chẳng hạn như Ethereum) rất đơn giản để thực hiện, nhưng chúng gặp khó khăn do trạng thái của tài khoản là toàn cầu : tất cả các giao dịch thực hiện bất kỳ điều gì với tài khoản đều phải chạm vào một con số này. Điều này có thể dẫn đến các vấn đề về thông lượng, cũng như các vấn đề về đặt hàng (nếu Alice gửi 5USD cho Bob và Bob gửi 5USD cho Carol, điều này có thể thành công hoặc thất bại tùy thuộc vào thứ tự các giao dịch được xử lý).

Mô hình khác là sổ cái dựa trên UTXO. Sổ cái dựa trên UTXO (chẳng hạn như Bitcoin) đại diện cho trạng thái của sổ cái dưới dạng tập hợp các “đầu ra giao dịch chưa sử dụng” (UTXO). UTXO giống như một phong bì với một số tiền trong đó: nó được “gửi” tới một bên cụ thể và nó chứa một số quỹ. Một giao dịch sử dụng một số UTXO và tạo ra một số UTXO khác .

Vì vậy, một giao dịch gửi 5USD từ Alice đến Bob sẽ thực hiện bằng cách chi tiêu một số UTXO hiện có thuộc về Alice và tạo một UTXO mới với 5USD thuộc về Bob.

Sổ cái dựa trên UTXO phức tạp hơn, nhưng tránh một số vấn đề của sổ cái dựa trên tài khoản, vì bất kỳ giao dịch nào cũng chỉ xử lý các đầu vào mà nó chi tiêu. Cardano là một sổ cái dựa trên UTXO và chúng tôi chủ yếu dựa vào điều này. Ví dụ, Hydra , giải pháp khả năng mở rộng của Cardano, sử dụng thực tế là các phần độc lập của biểu đồ giao dịch có thể được xử lý song song để cải thiện thông lượng.

Tập lệnh và Mô hình UTXO mở rộng
-----------------------------------
Sổ cái dựa trên UTXO thường bắt đầu với một mô hình rất đơn giản về “quyền sở hữu” các UTXO. Một đầu ra sẽ có một khóa công khai (nghiêm ngặt, băm của khóa công khai) được gắn vào nó và để sử dụng đầu ra này, giao dịch chi tiêu phải được ký bằng khóa riêng tương ứng. Chúng tôi gọi đây là đầu ra "pay-to-pubkey".

Cardano sử dụng một mô hình mở rộng được gọi là :term:`Extended UTXO Model` (EUTXO).
Trong mô hình EUTXO, một đầu ra có thể bị khóa bởi (băm của) một * đoạn mã - script* . Chúng tôi gọi đây là đầu ra “trả cho tập lệnh”.
đoạn mã là một *chương trình* quyết định xem giao dịch sử dụng đầu ra có được phép làm như vậy hay không. Một đoạn mã như vậy được gọi là tập lệnh trình xác thực, bởi vì nó xác nhận xem chi tiêu có được phép hay không!
Một đoạn mã xác thực đơn giản sẽ là một chương trình kiểm tra xem giao dịch chi tiêu có được ký bởi một khóa cụ thể hay không - điều này sẽ sao chép chính xác hành vi của các đầu ra pay-to-pubkey đơn giản. Tuy nhiên, với một chút mở rộng cẩn thận, chúng ta có thể sử dụng các đoạn mã để cho phép chúng ta thể hiện một lượng lớn logic hữu ích trên chuỗi.
Cách thức hoạt động của mô hình EUTXO là các tập lệnh trình xác thực được truyền ba đối số:

- *Datum*: đây là một phần dữ liệu được gắn với *đầu ra* mà tập lệnh đang khóa (nghiêm ngặt, một lần nữa, chỉ có hàm băm). Điều này thường được sử dụng để mang trạng thái..
- *Redeemer*: đây là một phần dữ liệu được đính kèm với *đầu vào* đang thực hiện chi tiêu. Điều này thường được sử dụng để cung cấp đầu vào cho tập lệnh từ người chi tiêu.
- *Context*: đây là một phần dữ liệu đại diện cho thông tin về giao dịch thực hiện chi tiêu. Điều này được sử dụng để xác nhận về cách đầu ra đang được gửi (chẳng hạn như "Bob đã ký nó").

Ví dụ, hãy xem chúng ta có thể thực hiện hoán đổi nguyên tử như thế nào.

- Dữ liệu chứa các khóa của hai bên trong hoán đổi và mô tả về những gì họ đang hoán đổi

- Người mua lại không được sử dụng.

- Bối cảnh chứa một đại diện của giao dịch.

Sau đó, logic của tập lệnh trình xác thực là: liệu giao dịch có thực hiện thanh toán từ bên thứ hai cho bên thứ nhất, có chứa giá trị mà họ phải gửi không? Nếu vậy, họ có thể sử dụng đầu ra này và gửi nó đến nơi họ muốn (hoặc chúng tôi có thể nhấn mạnh rằng gửi nó đến khóa của họ, nhưng chúng tôi cũng có thể để họ làm những gì họ thích với nó).
Tham Khảo thêm
---------------

Mô hình UTXO mở rộng được mô tả trong Chakravarty et al :cite:t:`functional-smart-contracts-summit`.
 Chi tiết chính thức hơn có thể được tìm thấy trong :cite:t:`eutxo,utxoma,eutxoma`.

Để được trợ giúp thêm về cách thực sự triển khai logic thú vị bằng cách sử dụng mô hình EUTXO và các tập lệnh, hãy đọc một số :ref:`tutorials<plutus_tutorials>`
