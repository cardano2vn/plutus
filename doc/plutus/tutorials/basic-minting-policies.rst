.. highlight:: haskell
.. _basic_minting_policies_tutorial:

Viết các chính sách đúc tiền cơ bản
==============================

: term: `Minting policy scripts <minting policy script>` là các chương trình có thể được sử dụng để kiểm soát việc đúc các tài sản mới trên chuỗi.
Các tập lệnh chính sách đúc tiền giống như: term: `validator scripts <validator script>` và chúng được viết tương tự nhau, vì vậy hãy xem: ref: `basic validators tutorial <basic_validators_tutorial>` trước khi đọc phần này.

Lập luận chính sách đúc tiền
------------------------

Các chính sách đúc kết, như trình xác thực, nhận một số thông tin từ nút xác thực:

- The: term: `Reder`, là một số dữ liệu dành riêng cho tập lệnh được chỉ định bởi bên thực hiện đúc.
- Thuật ngữ: thuật ngữ: `ngữ cảnh tập lệnh`, bao gồm đại diện của giao dịch chi tiêu, cũng như hàm băm của chính sách đúc tiền hiện đang được chạy.

Chính sách đúc tiền là một hàm nhận hai đầu vào này dưới dạng * đối số *.
Nút xác thực chịu trách nhiệm chuyển chúng vào và chạy chính sách đúc tiền.
Như với các tập lệnh trình xác thực, các đối số được mã hóa dưới dạng: hsobj: `PlutusCore.Data.Data`.

Sử dụng ngữ cảnh script
------------------------

Các chính sách đúc tiền có quyền truy cập vào: term: `script context` làm đối số thứ hai của chúng.
Đây sẽ luôn là một giá trị kiểu: hsobj: `` Plutus.V1.Ledger.Contexts.ScriptContext '' được mã hóa thành `` Dữ liệu ''.
Các chính sách đúc tiền có xu hướng đặc biệt quan tâm đến trường `` đúc tiền '', vì quan điểm của chính sách đúc tiền là kiểm soát những mã thông báo nào được đúc.

Điều quan trọng đối với chính sách đào tiền là xem xét các mã thông báo trong trường `` đúc '' là một phần của nhóm tài sản của chính nó.
Điều này yêu cầu chính sách phải tham chiếu đến hàm băm của chính nó - rất may là điều này được cung cấp cho chúng tôi trong ngữ cảnh tập lệnh của chính sách đúc tiền.

Viết chính sách đúc tiền
------------------------

Dưới đây là một ví dụ kết hợp điều này lại với nhau để tạo ra một chính sách đơn giản cho phép mọi người đúc mã thông báo miễn là họ làm điều đó một mã thông báo tại một thời điểm.
Để bắt đầu, chúng tôi sẽ viết một phiên bản hoạt động với các kiểu có cấu trúc.

.. literalinclude:: BasicPolicies.hs
   :start-after: BLOCK1
   :end-before: BLOCK2

Tuy nhiên, các tập lệnh thực sự được cung cấp các đối số của chúng là kiểu `` Dữ liệu '' và phải báo hiệu lỗi bằng `` lỗi '', vì vậy chúng ta cần kết thúc phiên bản đã nhập của mình để sử dụng nó trên chuỗi.

.. literalinclude:: BasicPolicies.hs
   :start-after: BLOCK2
   :end-before: BLOCK3

Các ví dụ chính sách khác
---------------------

Có lẽ chính sách hữu ích đơn giản nhất là chính sách yêu cầu một khóa cụ thể để ký kết giao dịch để thực hiện bất kỳ hoạt động đúc tiền nào.
Điều này mang lại cho người nắm giữ quyền kiểm soát toàn bộ nguồn cung cấp, nhưng điều này thường là đủ đối với các loại tài sản có cơ quan quyền lực tập trung.

.. literalinclude:: BasicPolicies.hs
   :start-after: BLOCK3
   :end-before: BLOCK4

.. Lưu ý::
   Chúng tôi không cần kiểm tra xem giao dịch này có thực sự đúc bất kỳ loại tài sản nào của chúng tôi hay không: các quy tắc sổ cái đảm bảo rằng chính sách đào tiền sẽ chỉ được chạy nếu một số tài sản đó đang được đúc.We don't need to check that this transaction actually mints any of our asset type: the ledger rules ensure that the minting policy will only be run if some of that asset is being minted.
