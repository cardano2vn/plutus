.. _what_is_plutus_foundation:

Plutus Foundation là gì?
==========================

Để một ứng dụng chạy :ref:` hạt nhân đáng tin cậy <what_is_the_plutus_platform>` dưới dạng tập lệnh :ref:`ledger<what_is_a_ledger>`, sổ cái cần có cách chỉ định và thực thi các tập lệnh. Scripts chỉ đơn giản là các chương trình, vì vậy điều này có nghĩa là chúng ta cần một  *ngôn ngữ lập trình*.

Plutus Core
-----------

Trong Nền tảng Plutus, ngôn ngữ này là  *Plutus Core*.
Plutus Core là một biến thể của lambda calculus, một ghiên cứu kỹ lưỡng về tính toán.

.. note::
    Plutus Core là “hợp ngữ” của chúng tôi. Hãy tin tưởng ở tôi, bạn không muốn nhìn thấy bất kỳ điều gì! Đối phó với điều đó là công việc của trình biên dịch.

Plutus Core được thiết kế cho sự đơn giản, tính xác định và cho phép kiểm soát chi phí cẩn thận khi thực hiện chương trình. Sử dụng phép tính lambda làm cho nó trở thành mục tiêu biên dịch dễ dàng cho các ngôn ngữ lập trình hàm và cho phép chúng tôi có một bộ đánh giá đơn giản, được xác minh chính thức.

Plutus Tx
---------

Viết Plutus Core bằng tay không phải là công việc của một con người! Nó được thiết kế để được viết bởi một trình biên dịch và Nền tảng cung cấp một trình biên dịch từ một tập hợp con của Haskell đến Plutus Core. Điều này cho phép bạn viết ứng dụng liền mạch trong Haskell, trong khi biên dịch một phần mã sang Plutus Core trên chuỗi và một phần vào ứng dụng ngoài chuỗi.

Hỗ trợ mã “hỗn hợp” theo cách này cho phép các thư viện được viết bằng Plutus Haskell SDK để chia sẻ logic và kiểu dữ liệu trên cả hai phần của ứng dụng, giảm nguy cơ lỗi đáng kể.

Further reading
---------------

Các chi tiết chính thức của Plutus Core nằm trong đặc điểm kỹ thuật của nó  :cite:p:`plutus-core-spec`.
Thiết kế được thảo luận trong IOHK :cite:t:`plutus-report`.

Để biết thêm về Plutus Tx, hãy xem :ref:`tutorial<plutus_tx_tutorial>`.
