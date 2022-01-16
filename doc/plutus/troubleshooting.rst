Xử lý sự cố
===============

Lỗi plugin
-------------

Các lỗi bắt đầu với `` GHC Core to PLC plugin '' là lỗi từ `` plutus-tx-plugin ''.

.. Lưu ý::
  Thường thì những lỗi này phát sinh do GHC làm gì đó với mã trước khi plugin nhìn thấy nó.
    Vì vậy, giải pháp thường là ngăn GHC làm điều này, đó là lý do tại sao chúng tôi thường khuyên bạn nên thử các cờ trình biên dịch GHC khác nhau.

Haddock
~~~~~~~

Plugin thường sẽ bị lỗi khi tạo tài liệu Haddock.
Tuy nhiên, trong trường hợp này, bạn có thể chỉ cần yêu cầu nó trì hoãn bất kỳ lỗi nào đối với thời gian chạy (điều này sẽ không bao giờ xảy ra vì bạn đang xây dựng tài liệu).

Một cách dễ dàng để làm điều này là thêm các dòng sau cho ``package-name`` thành ``cabal.project``::

  package package-name
    haddock-options: "--optghc=-fplugin-opt PlutusTx.Plugin:defer-errors"

Các hàm không phải ``INLINABLE ''
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một lỗi phổ biến là:

``Error: Reference to a name which is not a local, a builtin, or an external INLINABLE function``

Điều này có nghĩa là plugin không có quyền truy cập vào việc triển khai chức năng, mà nó cần để có thể biên dịch chức năng thành Plutus Core.
Một số điều bạn có thể làm để khắc phục:

- Đảm bảo thêm  ``{-# INLINABLE functionname #-}`` vào hàm của bạn.
- Nếu có thểm ``$c``phía trước tên hàm bị lỗi, GHC đã tạo một phiên bản chuyên biệt cho hàm của bạn, điều này ngăn không cho plugin truy cập vào nó .
  Bạn có thể tắt  với ``{-# OPTIONS_GHC -fno-specialise #-}``
- Các tùy chọn trình biên dịch khác có thể giúp:

  - ``{-# OPTIONS_GHC -fno-strictness #-}``
  - ``{-# OPTIONS_GHC -fno-ignore-interface-pragmas #-}``
  - ``{-# OPTIONS_GHC -fno-omit-interface-pragmas #-}``
  - ``{-# OPTIONS_GHC -fobject-code #-}``

  Một số chi tiết khác có trong `the plutus-tx readme <https://github.com/input-output-hk/plutus/tree/master/plutus-tx#building-projects-with-plutus-tx>`_.

Sự cố máy chủ ngôn ngữ Haskell
------------------------------

Để được tư vấn thêm về cách sử dụng Máy chủ ngôn ngữ Haskell (HLS), hãy tham khảo `CONTRIBUTING guide <https://github.com/input-output-hk/plutus/blob/master/CONTRIBUTING.adoc>`_ in the ``plutus`` repository.

Sai version
~~~~~~~~~~~~~

``ghcide compiled against GHC 8.10.3 but currently using 8.10.2.20201118``

Trình chỉnh sửa của bạn không chọn đúng phiên bản của Máy chủ Ngôn ngữ Haskell (HLS).
Plutus cần một phiên bản tùy chỉnh của HLS do Nix cung cấp.
Vì vậy, làm cho điều này hoạt động trong trình chỉnh sửa của bạn, hãy đảm bảo thực hiện hai điều sau:

- Khởi động trình soạn thảo của bạn từ `` nix-shell '' (hoặc sử dụng `` direnv '')
- Hầu hết các trình soạn thảo được định cấu hình để sử dụng `` haskell-language-server-wrapper '', là một trình bao bọc chọn phiên bản HLS phù hợp.
   Thay đổi điều này thành chỉ `` haskell-language-server ''.

Nếu cách này không hiệu quả, hãy chạy `` haskell-language-server '' trong `nix-shell` và sử dụng đường dẫn tuyệt đối này trong cấu hình trình soạn thảo của bạn.

Mã lỗi
-----------

Để giảm kích thước mã, lỗi trên chuỗi chỉ xuất mã. Đây là ý nghĩa của chúng:

..
  This list can be generated with:
  grep -rEoh "\btrace\w*\s+\"[^\"]{1,5}\"\s+(--.*|\{-\".*\"-\})" *

- Ledger errors

  - ``L0: Input constraint``
  - ``L1: Output constraint``
  - ``L2: Missing datum``
  - ``L3: Wrong validation interval``
  - ``L4: Missing signature``
  - ``L5: Spent value not OK``
  - ``L6: Produced value not OK``
  - ``L7: Public key output not spent``
  - ``L8: Script output not spent``
  - ``L9: Value minted not OK``
  - ``La: MustPayToPubKey``
  - ``Lb: MustPayToOtherScript``
  - ``Lc: MustHashDatum``
  - ``Ld: checkScriptContext failed``
  - ``Le: Can't find any continuing outputs``
  - ``Lf: Can't get any continuing outputs``
  - ``Lg: Can't get validator and datum hashes``
  - ``Lh: Can't get currency symbol of the current validator script``
  - ``Li: DecodingError``

- Prelude errors

  - ``PT1: TH Generation of Indexed Data Error``
  - ``PT2: Void is not supported``
  - ``PT3: Ratio number can't have a zero denominator``
  - ``PT4: 'round' got an incorrect input``
  - ``PT5: 'check' input is 'False'``
  - ``PT6: PlutusTx.List.!!: negative index``
  - ``PT7: PlutusTx.List.!!: index too large``
  - ``PT8: PlutusTx.List.head: empty list``
  - ``PT9: PlutusTx.List.tail: empty list``
  - ``PT10: PlutusTx.Enum.().succ: bad argument``
  - ``PT11: PlutusTx.Enum.().pred: bad argument``
  - ``PT12: PlutusTx.Enum.().toEnum: bad argument``
  - ``PT13: PlutusTx.Enum.Bool.succ: bad argument``
  - ``PT14: PlutusTx.Enum.Bool.pred: bad argument``
  - ``PT15: PlutusTx.Enum.Bool.toEnum: bad argument``
  - ``PT16: PlutusTx.Enum.Ordering.succ: bad argument``
  - ``PT17: PlutusTx.Enum.Ordering.pred: bad argument``
  - ``PT18: PlutusTx.Enum.Ordering.toEnum: bad argument``

- State machine errors

  - ``S0: Can't find validation input``
  - ``S1: State transition invalid - checks failed``
  - ``S2: Thread token not found``
  - ``S3: Non-zero value allocated in final state``
  - ``S4: State transition invalid - constraints not satisfied by ScriptContext``
  - ``S5: State transition invalid - constraints not satisfied by ScriptContext``
  - ``S6: State transition invalid - input is not a valid transition at the current state``
  - ``S7: Value minted different from expected``
  - ``S8: Pending transaction does not spend the designated transaction output``

- Currency errors

  - ``C0: Value minted different from expected``
  - ``C1: Pending transaction does not spend the designated transaction output``
