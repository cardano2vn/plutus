.. highlight:: haskell
.. _plutus_tx_tutorial:

Sử dụng Plutus Tx
===============

Các ứng dụng Plutus được viết dưới dạng một chương trình Haskell duy nhất, mô tả cả mã chạy ngoài chuỗi (ví dụ: trên máy tính của người dùng hoặc trong ví của họ) và trên chuỗi như một phần của xác thực giao dịch.

Các phần của chương trình mô tả mã trên chuỗi vẫn chỉ là Haskell, nhưng chúng được biên dịch thành: Thuật ngữ: `Plutus Core`, thay vì thành ngôn ngữ đích biên dịch thông thường.
Chúng tôi gọi chúng là: thuật ngữ: Chương trình Plutus Tx` (trong đó 'Tx' chỉ ra rằng các thành phần này thường đi vào các giao dịch).

.. Cảnh báo::

  Nói một cách chính xác, trong khi phần lớn Haskell đơn giản sẽ hoạt động, chỉ một tập hợp con của Haskell được hỗ trợ bởi trình biên dịch Plutus Tx.
    Trình biên dịch Plutus Tx sẽ cho bạn biết nếu bạn đang cố sử dụng một thành phần không được hỗ trợ.

Kỹ thuật quan trọng mà chúng tôi sử dụng để triển khai Plutus Tx được gọi là * phân đoạn siêu lập trình *, có nghĩa là chương trình Haskell chính tạo ra chương trình * khác * (trong trường hợp này là chương trình Plutus Core sẽ chạy trên blockchain).
Plutus Tx là cơ chế chúng tôi sử dụng để viết các chương trình đó, nhưng vì Plutus Tx chỉ là một phần của chương trình Haskell chính, chúng tôi có thể chia sẻ các loại và định nghĩa giữa hai loại.

.. _template_haskell_preliminaries:

Sơ bộ mẫu Haskell
------------------------------

Plutus Tx sử dụng hỗ trợ lập trình siêu mẫu của Haskell, Template Haskell, vì hai lý do chính:

- Template Haskell cho phép chúng tôi làm việc tại thời điểm biên dịch, đó là khi chúng tôi biên dịch Plutus Tx.
- Nó cho phép chúng tôi kết nối bộ máy sử dụng trình biên dịch Plutus Tx.

Template Haskell is very versatile, but we only use a few features.

Template Haskell rất linh hoạt, nhưng chúng tôi chỉ sử dụng một vài tính năng.

Mẫu Haskell bắt đầu bằng * dấu ngoặc kép *.
Trích dẫn Haskell Mẫu là một biểu thức Haskell `` e '' bên trong các dấu ngoặc đặc biệt `` [|| e ||] ``.
Nó có kiểu `` Q (TExp a) `` trong đó `` e '' có kiểu `` a ''. `` TExp a`` là một * biểu diễn * một biểu thức của kiểu `` a '', tức là cú pháp của biểu thức Haskell thực đã được trích dẫn.
Câu trích dẫn nằm trong kiểu trích dẫn `` Q '', điều này không thú vị lắm đối với chúng tôi.

.. Lưu ý::

   Ngoài ra còn có một chữ viết tắt `` TExpQ a '' cho `` Q (TExp a) '', tránh một số dấu ngoặc đơn.

Bạn có thể * ghép * một câu trích dẫn vào chương trình của mình bằng toán tử `` $$ ''.
Thao tác này sẽ chèn cú pháp được đại diện bởi câu trích dẫn vào chương trình tại điểm mà mối ghép được viết.

Nói một cách đơn giản, một câu trích dẫn cho phép chúng ta nói về các chương trình Haskell dưới dạng * giá trị *.

Trình biên dịch Plutus Tx biên dịch các biểu thức Haskell * * (không phải giá trị!), Do đó, tự nhiên nó lấy một dấu ngoặc kép (đại diện cho một biểu thức) làm đối số.
Kết quả là một trích dẫn mới, lần này là cho một chương trình Haskell đại diện cho chương trình * đã biên dịch *.
Trong Haskell, kiểu: hsobj: `PlutusTx.TH.compile` là` `TExpQ a → TExpQ (CompiledCode a) ''.
Đây chỉ là những gì chúng tôi đã nói

- `` TExpQ a '' là một đoạn trích dẫn đại diện cho một chương trình kiểu `` a ''.

- `` TExpQ (CompiledCode a) '' là câu trích dẫn đại diện cho một chương trình  đã biên dịch với Plutus Core.

.. Lưu ý::

: hsobj: `PlutusTx.CompiledCode` cũng có tham số kiểu` `a '', tương ứng với kiểu của biểu thức gốc.
       Điều này cho phép chúng tôi "ghi nhớ" loại chương trình Haskell ban đầu mà chúng tôi đã biên dịch.

Vì: hsobj: `PlutusTx.TH.compile` tạo ra một câu trích dẫn, để sử dụng kết quả, chúng tôi cần ghép nó trở lại chương trình của chúng tôi.
Trình biên dịch Plutus Tx chạy khi biên dịch chương trình chính, và chương trình đã biên dịch sẽ được chèn vào chương trình chính.

Đây là tất cả những gì bạn cần biết về Template Haskell!
Chúng tôi thường sử dụng cùng một mẫu đơn giản: tạo một câu trích dẫn, ngay lập tức gọi: hsobj: `PlutusTx.TH.compile`, và sau đó ghép kết quả lại vào.

.. _writing_basic_plutustx_programs:

Viết các chương trình Plutus Tx cơ bản
--------------------------------

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK1
   :end-before: BLOCK2

Chương trình đơn giản này chỉ đánh giá là số nguyên `` 1 ''.

.. note::
   The examples that show the Plutus Core generated from compilation include doctests.
   The syntax of Plutus Core might look unfamiliar, since this syntax is used for the 'assembly language', which means you don't need to inspect the compiler's output.
   But for the purpose of this tutorial, it is useful to understand what is happening.

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK2
   :end-before: BLOCK3

Chúng ta có thể thấy cách lập trình siêu hình hoạt động: chương trình Haskell `` 1 '' đã được biến thành `` Số nguyên mã biên dịch '' tại thời điểm biên dịch, mà chúng tôi đã ghép vào chương trình Haskell của mình.
Chúng tôi có thể kiểm tra chương trình được tạo trong thời gian chạy để xem Plutus Core được tạo (hoặc để đưa nó vào blockchain).

Chúng tôi cũng thấy kiểu sử dụng tiêu chuẩn: một câu trích dẫn TH, được gói trong một lệnh gọi tới: hsobj: `PlutusTx.TH.compile`, được bọc trong một mối nối` `$$ ''.
Đây là cách tất cả các chương trình Plutus Tx của chúng tôi được viết.

Đây là một chương trình phức tạp hơn một chút. Nó bao gồm chức năng nhận dạng trên số nguyên.

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK3
   :end-before: BLOCK4

.. _functions_and_datatypes:

Hàm và kiểu dữ liệu
-----------------------

Bạn có thể sử dụng các hàm bên trong biểu thức của mình.
Trong thực tế, bạn thường sẽ muốn xác định toàn bộ chương trình Plutus Tx của mình như một định nghĩa bên ngoài trích dẫn và sau đó chỉ cần gọi nó bên trong trích dẫn.
.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK4
   :end-before: BLOCK5

Chúng tôi có thể sử dụng các kiểu dữ liệu Haskell bình thường và đối sánh mẫu một cách tự do:

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK5
   :end-before: BLOCK6

Không giống như các hàm, các kiểu dữ liệu không cần bất kỳ loại chú thích đặc biệt nào được sử dụng bên trong một câu trích dẫn, do đó chúng ta có thể sử dụng các loại như `` Có thể '' từ `` Prelude '' của Haskell.
Điều này cũng hoạt động cho các kiểu dữ liệu của riêng bạn!

Dưới đây là một ví dụ nhỏ với loại dữ liệu đại diện cho một ngày kết thúc có khả năng mở

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK6
   :end-before: BLOCK7

Chúng tôi cũng có thể đã định nghĩa hàm `` pastEnd '' như một ràng buộc `` INLINABLE '' riêng biệt và chỉ đề cập đến nó trong phần trích dẫn, nhưng trong trường hợp này, nó đủ nhỏ để chỉ cần viết tại chỗ.

.. _typeclasses:

Typeclasses
-----------

So far we have used functions like ``lessThanEqInteger`` for comparing ``Integer`` s, which is much less convenient than ``<`` from the standard Haskell ``Ord`` typeclass.

Plutus Tx does support typeclasses, but we cannot use many of the standard typeclasses, since we require their class methods to be ``INLINABLE``, and the implementations for types such as ``Integer`` use the Plutus Tx built-ins.

Redefined versions of many standard typeclasses are available in the Plutus Tx Prelude.
As such, you should be able to use most typeclass functions in your Plutus Tx programs.

For example, here is a version of the ``pastEnd`` function using ``<``.
This will be compiled to exactly the same code as the previous definition.

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK7
   :end-before: BLOCK8

.. _the_plutus_tx_prelude:

Khúc dạo đầu của Plutus Tx
---------------------

Mô-đun: hsmod: `PlutusTx.Prelude` là một phần thay thế cho Haskell Prelude thông thường, với một số chức năng và kiểu lớp được định nghĩa trước  giúp trình biên dịch Plutus Tx xử lý dễ dàng hơn (tức là` `CÓ THỂ LƯU TRỮ '').

Sử dụng Plutus Tx Prelude cho mã mà bạn muốn biên dịch bằng trình biên dịch Plutus Tx.
Tất cả các định nghĩa trong Plutus Tx Prelude bao gồm các định nghĩa Haskell đang hoạt động, có nghĩa là bạn cũng có thể sử dụng chúng trong mã Haskell bình thường, mặc dù các phiên bản Haskell Prelude có thể sẽ hoạt động tốt hơn.

Để sử dụng Plutus Tx Prelude, hãy sử dụng pragma ngôn ngữ `` NoImplicitPrelude '' và nhập: hsmod: `PlutusTx.Prelude`.

Plutus Tx bao gồm một số kiểu và hàm tích hợp để làm việc với dữ liệu nguyên thủy (số nguyên và chuỗi bytest), cũng như một số hàm đặc biệt.
Những loại này cũng được xuất ra từ Plutus Tx Prelude.

Tích hợp: hsobj: `PlutusTx.Builtins.error` xứng đáng được đề cập đặc biệt.
: hsobj: `PlutusTx.Builtins.error` khiến giao dịch bị hủy khi nó được đánh giá, đây là một cách để kích hoạt quá trình xác thực không thành công.

.. _lifting_values:

Nâng giá trị
--------------

Cho đến nay, chúng ta đã biết cách xác định các đoạn mã * tĩnh * (khi bạn * biên dịch * chương trình Haskell chính của mình), nhưng bạn có thể muốn tạo mã * động * (nghĩa là khi bạn * chạy * chương trình Haskell chính của mình ).
Ví dụ: bạn có thể đang viết nội dung của giao dịch để bắt đầu hợp đồng thông minh huy động vốn cộng đồng, hợp đồng này sẽ cần được tham số hóa bằng dữ liệu xác định quy mô mục tiêu, thời gian bắt đầu và kết thúc của chiến dịch, v.v.

Chúng ta có thể làm điều này giống như cách chúng ta tham số hóa mã trong lập trình hàm: viết mã tĩnh dưới dạng một * hàm * và cung cấp đối số sau đó để cấu hình nó.

Trong trường hợp của chúng tôi, có một chút phức tạp: chúng tôi muốn tạo đối số và áp dụng hàm cho nó tại * runtime *.
Plutus Tx giải quyết vấn đề này thông qua * nâng *.
Lifting cho phép sử dụng các loại giống nhau, cả bên trong chương trình Plutus Tx của bạn * và * trong mã bên ngoài sử dụng nó.

.. Lưu ý::

 Trong ngữ cảnh này, * runtime * có nghĩa là thời gian chạy của chương trình Haskell chính, ** không phải ** khi Plutus Core chạy trên chuỗi.
 Chúng tôi muốn định cấu hình mã của mình khi chương trình Haskell chính chạy, vì đó là thời điểm chúng tôi sẽ nhận được thông tin đầu vào của người dùng.

Trong ví dụ này, chúng tôi thêm một chức năng bổ sung..

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK8
   :end-before: BLOCK9

Bây giờ, giả sử chúng ta muốn áp dụng điều này cho `` 4 '' trong thời gian chạy, cung cấp cho chúng ta một chương trình tính toán thành `` 5 ''.
Chúng ta cần * nâng * đối số (`` 4 '') từ Haskell sang Plutus Core, và sau đó chúng ta cần áp dụng hàm cho nó.

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK9
   :end-before: BLOCK10

Chúng tôi đã loại bỏ đối số bằng cách sử dụng hàm: hsobj: `PlutusTx.liftCode`.
Để sử dụng điều này, một kiểu phải có một thể hiện của lớp: hsobj: `PlutusTx.Lift`.
Đối với các kiểu dữ liệu của riêng bạn, bạn nên tạo các kiểu này bằng hàm: hsobj: `PlutusTx.makeLift` TH từ: hsmod:` PlutusTx.Lift`.

.. note::

   : hsobj: `PlutusTx.liftCode` tương đối không an toàn vì nó bỏ qua bất kỳ lỗi nào có thể xảy ra khi nâng thứ gì đó có thể không được hỗ trợ.
    Có một: hsobj: `PlutusTx.safeLiftCode` nếu bạn muốn xử lý rõ ràng những sự cố này

Chương trình kết hợp áp dụng lambda đã biên dịch ban đầu cho giá trị nâng lên (lưu ý rằng lambda bây giờ hơi phức tạp, vì chúng tôi đã biên dịch phần bổ sung thành một tích hợp sẵn).

Đây là một ví dụ với loại dữ liệu tùy chỉnh của chúng tôi.
Đầu ra là phiên bản được mã hóa của `` False ''.

.. literalinclude:: BasicPlutusTx.hs
   :start-after: BLOCK10
   :end-before: BLOCK11
