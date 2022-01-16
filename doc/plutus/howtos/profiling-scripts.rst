.. highlight:: haskell
.. _profiling_scripts:
Cách lập hồ sơ sử dụng ngân sách của các tập lệnh Plutus
=================================================

Tìm ra lý do tại sao tập lệnh của bạn sử dụng nhiều CPU hoặc đơn vị bộ nhớ hơn bạn mong đợi có thể khó khăn.
Bạn có thể tìm hiểu thêm chi tiết về cách các tài nguyên này đang được sử dụng trong tập lệnh của bạn bằng cách * lược tả * nó và xem kết quả trong một bản ghi.

Biên soạn một kịch bản để lập hồ sơ
--------------------------------

Việc biên dịch đòi hỏi phải biên dịch tập lệnh của bạn theo cách khác để nó sẽ phát ra thông tin mà chúng tôi có thể sử dụng để phân tích hiệu suất của nó.

.. Lưu ý:: 
Giống như việc lập hồ sơ bằng các ngôn ngữ khác, công cụ bổ sung này có thể ảnh hưởng đến cách chương trình của bạn được tối ưu hóa, vì vậy hành vi của nó có thể không giống với phiên bản không được lập hồ sơ.As with profiling in other languages, this additional instrumentation can affect how your program is optimized, so its behaviour may not be identical to the non-profiled version.

Để thực hiện việc này, bạn cần cung cấp một tùy chọn cho plugin Plutus Tx trong tệp nguồn nơi tập lệnh của bạn được biên dịch.

.. code-block:: haskell

   {-# OPTIONS_GHC -fplugin-opt PlutusTx.Plugin:profile-all #-}

Điều này hướng dẫn plugin chèn thiết bị đo đạc hồ sơ cho tất cả các chức năng.
Trong tương lai, có thể có tùy chọn để lập hồ sơ một nhóm chức năng được nhắm mục tiêu hơn.

Nhận một tập lệnh thực thi
------------------------------

Việc lập hồ sơ hoạt động bằng cách xem ngân sách được sử dụng như thế nào khi tập lệnh chạy.
Do đó, nó yêu cầu một tập lệnh thực thi, có nghĩa là bạn không chỉ cần tập lệnh trình xác nhận mà còn cần tất cả các đối số mà nó nhận được.
Bạn có thể tải tập lệnh được áp dụng đầy đủ này từ trình giả lập hoặc từ nút Cardano.

Chạy script
------------------

Bạn có thể chạy tập lệnh với tệp thực thi `` uplc ''.

.. note:: Tất cả các tệp thực thi được đề cập ở đây có thể được xây dựng từ kho lưu trữ `` plutus '' bằng cách sử dụng ``cabal build``.

.. code-block:: bash

   uplc evaluate -i myscript.flat --if flat --trace-mode LogsWithBudgets -o logs

Điều này chạy tập lệnh bằng cách sử dụng chế độ theo dõi phát ra thông tin ngân sách và đưa các nhật ký kết quả vào một tệp.
Đây sẽ là một tệp CSV có ba cột: thông báo cho biết chúng ta đang vào hoặc thoát chức năng nào; CPU tích lũy được sử dụng tại thời điểm đó; và bộ nhớ tích lũy được sử dụng tại thời điểm đó.

Phân tích kết quả
---------------------

Sau đó, chúng tôi có thể chuyển đổi các bản ghi thành một máy đo ngọn lửa bằng cách sử dụng công cụ tiêu chuẩn `` flamegraph.pl ''.
Thực thi `` traceToStacks '' biến nhật ký thành định dạng mà `` flamegraph.pl '' hiểu

.. code-block:: bash

   cat logs | traceToStacks | flamegraph.pl > cpu.svg
   cat logs | traceToStacks --column 2 | flamegraph.pl > mem.svg

Vì `` flamegraph.pl '' chỉ có thể xử lý một số liệu tại một thời điểm, nên `` traceToStacks '' có đối số `` --column '' để chọn cột khác nếu bạn muốn nhận được một bảng ghi bộ nhớ.

Sau đó, bạn có thể xem các SVG kết quả trong một trình xem mà bạn chọn, ví dụ: một trình duyệt web.
