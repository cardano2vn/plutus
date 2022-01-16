.. highlight:: haskell
.. _exporting_a_script:

Cách xuất script, datums và redemers
===========================================

.. Lưu ý::
    Hướng dẫn này sử dụng các tập lệnh từ: ref: `basic validators tutorial <basic_validators_tutorial>`.

Vì các tập lệnh phải khớp chính xác với các hàm băm trên chuỗi của chúng, nên điều quan trọng là các tập lệnh mà ứng dụng sử dụng không được vô tình thay đổi.
Ví dụ: thay đổi mã nguồn hoặc cập nhật các phần phụ thuộc hoặc công cụ có thể dẫn đến những thay đổi nhỏ trong tập lệnh - nhưng vì các hàm băm phải khớp chính xác nên ngay cả những thay đổi nhỏ cũng có thể gây ra vấn đề.

Vì lý do này, một khi bạn mong đợi rằng bạn sẽ không sửa đổi phần on-chain của ứng dụng của mình nhiều hơn, bạn có thể * đóng băng * nó bằng cách lưu Plutus Core cuối cùng vào một tệp.

Ngoài ra, trong khi hầu hết các Ứng dụng Plutus sử dụng các tập lệnh bằng cách trực tiếp gửi chúng như một phần của các giao dịch từ chính ứng dụng, thì việc xuất một tập lệnh được tuần tự hóa có thể hữu ích.
Ví dụ: bạn có thể muốn gửi nó như một phần của giao dịch được tạo theo cách thủ công với CLI của nút Cardano hoặc gửi nó cho một bên khác để họ sử dụng.

May mắn thay, nó là khá đơn giản để làm điều này.
Hầu hết các loại đều có phiên bản typeclass cho `` Serialise '' cho phép dịch trực tiếp sang CBOR.
Điều này áp dụng cho các loại `` Validator '', `` Redeemer '' và `` Datum ''.
Nếu bạn muốn tạo các giá trị mà bạn có thể chuyển đến Cardano CLI, bạn sẽ cần chuyển đổi chúng thành các loại thích hợp từ `` cardano-api '' và sử dụng `` serialiseToTextEnvelope ''.

.. literalinclude:: ../tutorials/BasicValidators.hs
   :start-after: BLOCK5
   :end-before: BLOCK6

`` CompiledCode '' có một phương thức tuần tự hóa khác, `` Flat '', nhưng nguyên tắc thì giống nhau.

Dạng tuần tự của `` CompiledCode ''  cũng có thể được kết xuất bằng cách sử dụng tùy chọn plugin:

.. code-block:: haskell

   {-# OPTIONS_GHC -fplugin-opt PlutusTx.Plugin:dump-uplc #-}

Điều này sẽ kết xuất đầu ra vào một tệp tạm thời có tên dựa trên tên mô-đun.
Tên tệp sẽ được in ra bảng điều khiển khi biên dịch tệp nguồn.
Sau đó, bạn có thể di chuyển nó đến một vị trí lâu dài hơn.

Nó có thể được đọc một cách thuận tiện với `` loadFromFile '' như một giải pháp thay thế cho `` biên dịch ''.

.. literalinclude:: ../tutorials/BasicValidators.hs
   :start-after: BLOCK6
   :end-before: BLOCK7
