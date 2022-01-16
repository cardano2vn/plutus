.. highlight:: haskell
.. _basic_validators_tutorial:

Viết các tập lệnh xác thực cơ bản
-------------------

: term: `Validator scripts <validator script>` là các chương trình có thể được sử dụng để khóa các đầu ra giao dịch trên chuỗi.
Các tập lệnh của trình xác thực là: term: chương trình `Plutus Core`, nhưng chúng ta có thể sử dụng: term:` Plutus Tx` để viết chúng một cách dễ dàng trong Haskell.
Hãy xem: ref: `Plutus Tx tutorial <plutus_tx_tutorial>" trước khi đọc cái này.

Đối số của trình xác thực
-------------------

Trình xác thực nhận được một số thông tin từ nút xác thực:

- The: term: `Reder`, là một số dữ liệu theo tập lệnh cụ thể được chỉ định bởi bên chi tiêu đầu ra.
- Thuật ngữ: term: `datum`, là một số dữ liệu dành riêng cho tập lệnh được chỉ định bởi bên tạo đầu ra.
- The: term: `script context`, bao gồm đại diện của giao dịch chi tiêu, cũng như chỉ mục của đầu vào mà trình xác thực hiện đang được chạy.

Trình xác thực là một hàm nhận ba đầu vào này dưới dạng * đối số *.
Nút xác thực chịu trách nhiệm chuyển chúng vào và chạy trình xác thực.

Loại `` Dữ liệu ''
-----------------

Nhưng các đối số của trình xác thực được thông qua như thế nào?
Các tập lệnh khác nhau sẽ mong đợi các loại giá trị khác nhau trong kho dữ liệu và bộ định nghĩa lại của chúng.

Câu trả lời là chúng ta truyền các đối số dưới dạng kiểu dữ liệu có cấu trúc * chung chung *: hsobj: `PlutusCore.Data.Data`.
`` Dữ liệu '' được thiết kế để giúp dễ dàng mã hóa dữ liệu có cấu trúc vào đó và về cơ bản là một tập hợp con của CBOR.

Tập lệnh trình xác thực nhận ba đối số kiểu `` Dữ liệu ''.
Vì `` Dữ liệu '' được biểu thị dưới dạng kiểu nội trang trong Plutus Core, chúng tôi sử dụng kiểu Haskell đặc biệt `` BuiltinData '' thay vì kiểu `` Dữ liệu '' bên dưới

Tuy nhiên, bạn thường sẽ không muốn sử dụng trực tiếp `` BuiltinData '' trong chương trình của mình, thay vào đó bạn sẽ muốn sử dụng các kiểu dữ liệu của riêng mình.
Chúng ta có thể dễ dàng chuyển đổi sang và từ `` BuiltinData '' với: hsobj: `PlutusTx.IsData.Class.ToData`,: hsobj:` PlutusTx.IsData.Class.FromData` và: hsobj: `PlutusTx.IsData.Class Kính đánh máy .UnsafeFromData`.
Bạn thường không cần phải viết các phiên bản của riêng mình cho các lớp này.
Thay vào đó, bạn có thể sử dụng hàm Haskell mẫu `` makeIsData '' để tạo một mẫu.

.. Lưu ý::
   Lớp :hsobj:`PlutusTx.IsData.Class.UnsafeFromData` cung cấp ``unsafeFromBuiltinData``, giống như  ``fromBuiltinData``, nhưng nhanh hơn và lỗi với  ``error`` thay vì trả về ``Maybe``.
   Chúng ta sẽ dùng  ``unsafeFromBuiltinData`` trong ví dụ này, nhưng thỉnh thoảng nó sẽ được dùng ở ví dụ khác

.. literalinclude:: BasicValidators.hs
   :start-after: BLOCK1
   :end-before: BLOCK2

Báo hiệu thất bại
-----------------

Điều quan trọng nhất mà trình xác thực có thể làm là * fail *.
Điều này chỉ ra rằng nỗ lực sử dụng đầu ra là không hợp lệ và việc xác thực giao dịch sẽ không thành công.
Trình xác thực thành công nếu nó không thất bại một cách rõ ràng.
Giá trị thực tế do trình xác thực trả về không liên quan.

Làm thế nào để trình xác thực không thành công?
Nó làm như vậy bằng cách sử dụng nội trang: hsobj: `PlutusTx.Builtins.error`.
Một số nội trang khác cũng có thể gây ra lỗi nếu chúng được sử dụng không đúng cách (ví dụ: `` 1/0 '').

Các chức năng của trình xác thực
-------------------

Chúng tôi viết các tập lệnh trình xác thực dưới dạng các hàm Haskell, chúng tôi biên dịch với Plutus Tx thành Plutus Core.
Loại của một hàm xác thực là `` BuiltinData -> BuiltinData -> BuiltinData -> () ``, nghĩa là, một hàm nhận ba đối số kiểu `` BuiltinData '' và trả về giá trị kiểu `` () `` ("đơn vị" hoặc "bộ trống" - vì kiểu trả về không quan trọng nên chúng tôi chỉ chọn một cái gì đó tầm thường).

Dưới đây là hai ví dụ về trình xác nhận đơn giản luôn thành công và luôn thất bại, tương ứng:

.. literalinclude:: BasicValidators.hs
   :start-after: BLOCK2
   :end-before: BLOCK3

Nếu chúng ta muốn viết trình xác thực sử dụng các kiểu không phải là `` BuiltinData '', chúng ta sẽ cần sử dụng các hàm từ: hsobj: `PlutusTx.IsData.Class.FromData` để giải mã chúng.
Quan trọng là, `` secureFromBuiltinData '' có thể bị lỗi: trong ví dụ của chúng tôi nếu `` BuiltinData '' trong đối số thứ hai là * không phải * là `` Ngày tháng '' được mã hóa chính xác thì nó sẽ không xác thực toàn bộ với `` lỗi '', mà thường là những gì chúng ta muốn nếu chúng ta có những lập luận không tốt.

.. TODO: write a HOWTO about error reporting

.. important::
   Unfortunately there's no way to provide failure diagnostics when a validator fails on chain -- it just fails.
   However, since transaction validation is entirely deterministic, you'll always be informed of this before you submit the transaction to the chain, so you can debug it locally using ``traceError``.

Dưới đây là một ví dụ sử dụng các loại ngày của chúng tôi để kiểm tra xem ngày được cung cấp có nhỏ hơn giới hạn được lưu trữ trong dữ liệu hay không.

.. literalinclude:: BasicValidators.hs
   :start-after: BLOCK3
   :end-before: BLOCK4

Sử dụng ngữ cảnh script
------------------------

Trình xác thực có quyền truy cập vào: term: `script context` làm đối số thứ ba của họ.
Đây sẽ luôn là một giá trị kiểu: hsobj: `Plutus.V1.Ledger.Contexts.ScriptContext` được mã hóa là` `Dữ liệu ''.

Bối cảnh tập lệnh cung cấp cho trình xác thực rất nhiều quyền lực, vì nó cho phép họ kiểm tra các đầu vào và đầu ra khác của giao dịch hiện tại.
Ví dụ: đây là trình xác thực sẽ chỉ chấp nhận giao dịch nếu một khoản thanh toán cụ thể được thực hiện như một phần của nó..

.. literalinclude:: BasicValidators.hs
   :start-after: BLOCK4
   :end-before: BLOCK5

Điều này sử dụng một số chức năng hữu ích để làm việc với các ngữ cảnh tập lệnh.
