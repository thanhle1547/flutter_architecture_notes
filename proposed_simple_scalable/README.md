# Guideline

Giới thiệu và hướng dẫn về cách thức chung trong việc tổ chức các file và thư mục trong dự án mà vẫn đảm bảo có thể mở rộng.

-------

Có rất nhiều cách để bạn tổ chức thư mục trong dự án, vậy đâu là cách phù hợp cho dự án của bạn? Câu trả lời cho câu hỏi đó do bạn tự quyết định. Bạn có thể đọc mô tả chi tiết của đề xuất được trình bày dưới đây để tham khảo.

Đề xuất này được tham khảo từ một số bài viết sau:

* [Flutter scalable folder & files structure](https://medium.com/flutter-community/flutter-scalable-folder-files-structure-8f860faafebd)

* [Flutter starter app: Very Good Core & CLI](https://verygood.ventures/blog/flutter-starter-app-very-good-core-cli)

* [Flutter Project Structure: Feature-first or Layer-first?](https://codewithandrea.com/articles/flutter-project-structure/)

> Cấu trúc thư mục được mô tả trong hướng dẫn dưới đây được tổ chức theo loại và theo tính năng
(folder-by-type-by-feature).

## 🌵 Cấu trúc thư mục

> **Quy tắc:**
>
> * Tất cả các file và thư mục được đặt tên theo kiểu `snake_case`
>
> * ___Nên___ nhóm các file lại với nhau trong trường hợp:
>
>   - Số lượng file trong một thư mục trên 5 hoặc 7 file.
>
>   - Số lượng file không quá nhiều, nhưng tên file dài.

```dart
  lib
  ├── common
  │   ├── components
  │   ├── models
  │   └── widgets
  ├── core
  │   ├── constants
  │   └── theme
  ├── data
  │   ├── clients
  │   ├── interceptors
  │   ├── models
  │   └── services
  ├── modules
  │   └── app.dart
  ├── routing
  ├── utils
  │   ├── helpers
  │   └── ui
  └── bootstrap.dart
```

## `common`

Đây là thư mục chứa tất cả các thành phần dùng chung trên toàn app.

Trong đó:

* `components`: là thư mục chứa các thành phần như: `painter` bạn tự custom, mixin, hoặc các widget, ...

  ❔ Có thể bạn sẽ thắc mắc tại sao lại có widget ở trong thư mục này, trong khi đó, thư mục `widgets` đã ở trong thư mục `common` rồi. Lý do sẽ được giải thích bằng các trường hợp cụ thể dưới đây:

    - Các widget này được custom để sử dụng với thư viện bên ngoài. Ví dụ như trong app của bạn sử dụng thư viện `flutter_slidable`, nhưng các `ActionPane` được cung cấp sẵn khác với thiết kế,  bạn phải tự custom lại cho nó giống. Hoặc cũng có thể bạn muốn custom lại để phải truyền ít tham số hơn.

    - Ví dụ app của bạn sử dụng `bloc` làm state management và các widget này sẽ gọi/truy cập đến bloc/cubit cụ thể (ví dụ như authentication_bloc, ...).

* `models`: thư mục này chứa các model (các đối tượng được mô hình hóa) được dùng lại nhiều lần.

* `widgets`: thư mục này chứa các widget được custom riêng từ các widget nền tảng của flutter như: material, cupertino, ... Các widget này có thể là các button có chung style, các thành phần form, ...

> Đừng ngại ngần hay lười việc đưa các widget này vào trong một thư mục con nếu chúng có chung một kiểu (ví dụ như button).

Tùy thuộc vào pattern, architecture mà bạn lựa chọn sử dụng cho việc kết nối với dữ liệu bên ngoài (qua api, socket, ...) mà bạn có thể thêm các thư mục `repositories/repos`/`services` (đối với provider provider architecture) để chứa các repo/service dùng chung trong dự án.

Tùy thuộc vào state management mà bạn lựa chọn sử dụng, bạn có thể thêm các thư mục `blocs/cubits`/`providers`/... để chứa các `bloc/cubit`/`provider` dùng chung trong dự án.

## `core`

Đây là thư mục chứa các thành phần làm nền nền tảng của app.

* `constants`: đây là thư mục khai báo các hằng số, các giá trị cố định, hiếm khi thay đổi trong app.

  - `app_constants.dart`: trong file này chứa các biến lưu các giá trị như: tên app, tên font chữ (mặc định), định dạng thời gian, ...

  - `asset_path.dart`: chứa đường dẫn tới các file ảnh, icon, ...

  - `api_paths.dart`/`api_endpoints.dart`: chứa đường link api hoặc các endpoint.

  Ngoài ra,

  - Nếu app không có đa ngôn ngữ thì có thể thêm file `string_constants` để khai báo các chuỗi như: Họ và tên, Ngày sinh...

  - Nếu app có chức năng chat thì thêm file `chat_events`.

  - ...

* `theme`: đây là thư mục khai báo các màu sắc, style chữ, padding, gap, ..., dưới đây là các ví dụ:

  - `app_border_and_radius.dart`: khai báo các biến kiểu `Border`, `Radius` và `BorderRadius` cho thành phần form, card, container, ...

  - `app_colors.dart`: khai báo các màu sắc, các biến có kiểu dữ liệu là `Color`.

  - `app_decoration.dart`: khai báo các `BoxDecoration` và các thành phần khác liên quan tới nó như: `Gradient`, `BoxShadow`.

  - `app_form_field.dart`: khai báo các style cho các thành phần là form như `InputDecorationTheme` cho theme, `InputDecoration` cho `TextField, InputDecorator`.

  <details>
  
  <summary>
    Vậy `DropdownButton` có thể coi là một thành phần form và thêm `BoxDecoration` vào đây được không? 
  </summary>
  
  Có. Tuy không có tham số `validator`, nhưng trong một số trường hợp, ta vẫn có thể coi nó là một thành phần của form.

  </details>

  - `app_gap.dart`: nếu như bạn hay dùng `SizedBox`, `Gap` (nếu dùng thư viện `gap`) để căn khoảng cách giữa các widget.

  - `app_padding.dart`: khai báo các `EdgeInsets`, `Pad` (nếu dùng thư viện `assorted_layout_widgets`).

  - `app_text_styles.dart`: khai báo các `TextStyle`.

  - `app_theme.dart`: khai báo (các) theme của app.

  - ...

  Ở đây, có thể bạn sẽ thắc mắc vì sao không có file định nghĩa các giá trị/con số (có thể là từ Guide UI), ví dụ như:

  - Spacing: 2, 5, 10, ...

  - Padding: 4.0, 16.0, ...

  - Font size/Text size: 12, 14, 16, 18, 20, ...

  - Font weight: black, extraBold, ...

  - ...

  Lý do là vì việc định nghĩa các giá trị này có phần thừa, theo quan điểm của cá nhân tôi. Khi xây dựng giao diện, bạn vẫn phải đo khoảng các trên thiết kế và việc nhập trực tiếp con số vào sẽ nhanh hơn.
  <!-- Nếu bạn dùng figma, với trường hợp `Font Weight`, các giá trị này thường hay là các con số 300, 400, ..., việc dùng tên như `light, regular, ...` sẽ yêu cầu người cắt giao diện biết con số tương ứng sau những cái tên này. -->

  Tất nhiên, việc nhập trực tiếp con số vào cũng không phải là cách tốt nhất. Nếu thiết kế có Guide UI chuẩn, bạn nên lựa chọn những giá trị phù hợp để định nghĩa. Ví dụ với widget Grid, khoảng cách giữa các item trên điện thoại và máy tính bảng khác nhau, và tất cả các màn hình/một phần giao diện có sử dụng Grid đều có chung 1 con số như vậy. Hay đơn giản hơn, tất cả/phần lớn các  AppBar đều có chung chiều cao. Vậy thì bạn có thể khai báo các biến định nghĩa các con số này. Tất cả sẽ được đặt trong một file là `app_dimens.dart`.

Ngoài 2 thư mục con đã nêu trên (`constants` và `theme`), trong thư mục này (`core`) còn có thể chứa các widget, các kiểu dữ liệu do bạn tự định nghĩa, các thành phần sẽ làm nên nền tảng để bạn xây dựng/sử dụng sau này.

Cụ thể về phần widget, các widget này là các widget do bạn tự viết, sử dụng Element tree, RenderObject tree, không có truy cập đến một bloc/provider/... (state management) cụ thể ở bên ngoài (ví dụ như authentication_bloc, ...).

## `data`

Đây là thư mục chứa các lớp (class) liên quan đến việc thực hiện kết nối dữ liệu.

* `clients`: thư mục này chứa các class (utilities) thực hiện các công việc như gọi api (get, post, put, ... method), on/emit socket, ...

Nếu như app chỉ có gọi api thì sự tồn tại của thư mục này là không cần thiết.

* `interceptors`: nếu bạn dùng thư viện `dio` để kết nối api thì các interceptors do bạn custom sẽ được đặt trong này.

* `models`: chứa các lớp (class) thể hiện dữ liệu được trả về từ clients, các class này có thể là:

  - `error.dart`: model cho trường hợp kết quả (gọi api) có lỗi.

  - `request_method.dart`: các phương thức kết nối api (như, get, post, put, ...) và extension liên quan (nếu có).

  - `request_response.dart`: class này thể hiện dữ liệu nhận được (từ api)

  - `status_code.dart`: chứa các status code.

* `services`: các service hoạt động trung gian giữa các tầng (layers) của ứng dụng: hỗ trợ trao đổi với native, kiểm tra kết nối mạng, lưu dữ liệu vào shared preferences, ... Dưới đây là một vài ví dụ:

  - `auth_service.dart`: Thực hiện lưu/truy xuất dữ liệu không quan trọng, liên quan đến người dùng vào shared preferences. Thực hiện làm mới token, ...

  - `connectivity_service.dart`: Đảm nhiệm kiểm tra kết nối mạng.

  - `local_data_storage_service.dart`: Thực hiện lưu/truy xuất dữ liệu ứng dụng, hoặc dữ liệu cần được cache vào shared preferences.

  - `secure_storage_service.dart`: Thực hiện lưu/truy xuất dữ liệu quan trọng liên quan đến user credentials, các API token, ... vào Android Keystore hoặc Apple keychain, ... (tùy thuộc vào nền tảng).

## `modules`

Đây là thư mục chứa các chức năng trong app.

Mỗi một module trong thư mục này được chia ra theo sự liên quan giữa các màn hình. Ví dụ:

* Các màn hình đăng nhập, đăng ký, ... sẽ ở trong module authentication/auth.

* Các màn danh sách sản phẩm, chi tiết sản phẩm , ... sẽ ở trong module products.

* ...

Trong mỗi một module con này có thể có các widget được custom riêng và chỉ dùng trong module này, vậy nên có thể tạo một thư mục `widgets` để cho các file ấy vào.

Áp dụng tương tự đối với các model dữ liệu (thư mục `models`), các repository/repository-provider (đối với repository pattern)/service (đối với provider provider architecture) (thư mục `repositories/repos`/`services`), ...

```
  ├── modules
  │   ├── auth
  │       ├── models
  │       ├── repos
  │       ├── screens
  │       └── widgets
```

Tùy thuộc vào state management mà bạn lựa chọn sử dụng, bạn có thể thêm các thư mục `blocs/cubits`/`providers`/... để chứa các `bloc/cubit`/`provider` tương ứng và phạm vi của nó cũng chỉ nằm trong module này.

Nếu số lượng các màn hình trong cùng một module rất lớn thì hãy tiếp tục phân nhóm tiếp thành các thư mục con bên trong. Ví dụ với các màn sách sản phẩm lúc nãy:

* Ngoài màn danh sách sản phẩm (tất cả sản phẩm), còn có màn danh sách sản phẩm phân theo từng loại cụ thể, theo đơn hàng, theo nhà sản xuất, ...

* Ngoài màn sửa thông tin sản phẩm, còn có các màn sửa loại sản phẩm, sửa giá, ...

Có 2 cách tiếp cận cho vấn đề này:

<table>
  <thead>
    <tr>
      <th>Cách 1</th>
      <th>Cách 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <pre>
  ├── modules
  │   ├── products
  │       ├── editing
  │           ├── models
  │           ├── repos
  │           ├── screens
  │               ├── edit_product_screen.dart
  │               ├── edit_product_type_screen.dart
  │           └── widgets
  │       ├── product_list
  │           ├── models
  │           ├── repos
  │           ├── screens
  │               ├── product_by_type_screen.dart
  │               ├── product_by_producer_screen.dart
  │           └── widgets
        </pre>
      </td>
      <td valign="top">
        <pre>
  ├── modules
  │   ├── products
  │       ├── by_producer
  │       ├── by_type
  │           ├── models
  │           ├── repos
  │           ├── screens
  │               ├── product_edit_screen.dart
  │               ├── product_list_screen.dart
  │           └── widgets
        </pre>
      </td>
    </tr>
  </tbody>
</table>

> **Quy tắc** đặt tên file (ngoại trừ thư mục widgets):
>
> Thêm hậu tố tương ứng vào sau tên mỗi file trong nhóm.
>
>  Ví dụ: File `edit_product.dart` là màn hình sửa sản phẩm. Vậy nên file này được đổi tên thành `edit_product_screen.dart` hoặc `edit_product.screen.dart`. (Nên đặt tên theo 1 trong 2 cách trên để giữ sự nhất quán)


❔ Có thể bạn sẽ thắc mắc: Nếu dự án có ít thành viên (1 hay 2 người) thì cách tổ chức thư mục này còn có thể áp dụng được. Còn trong dự án có nhiều thành viên hơn (5 người), thì liệu có khả năng nào xảy ra xung đột không? 🤔

💡 Trong trường hợp bạn không quan tâm đến việc chia sẻ code - để có thể dùng lại và giảm thiếu lỗi. Bạn có thể thay cách tổ chức thư mục theo module thành tổ chức theo giao diện (folder-per-screen). Mỗi một module sẽ là một màn hình (ví dụ product_page, products_list, shopping_cart, ...).

💡 Bạn có thể thay cách tổ chức thư mục theo module thành tổ chức theo tính năng (folder-by-feature). Lúc đó thư mục `modules` sẽ thành thư mục `features`.

* `app.dart`: Là 1 widget, trong đó hàm build sẽ trả về MaterialApp/CupertinoApp.

## `routing/routes/router`

Đây là thư mục chứa tất cả các file liên quan đến việc điều hướng giữa các màn hình.

Nếu trong thư mục này chỉ chứa các file cấu hình điều hướng trong app, thì có thể đặt tên là `routes`. Nếu bạn viết thêm các tiện ích để điều hướng thì có thể đặt tên là `router`.

## `utils`

Đây là thư mục chứa các tiện ích (utilities).

* `helpers`: Các tiện tích dùng cho việc thao tác/xử lý dữ liệu, ...

  - `file_utils.dart`/`file_extension.dart`: chứa các hàm thực hiện thao tác với file, thông tin của file như: lấy kích thước, lấy phần mở rộng, ...

  - `form_input_formatter.dart`: chứa các `TextInputFormatter`.

  - `string_extension.dart`

  - `system_utils.dart`: chứa các hàm thực hiện thao tác với hệ thống thông qua thư viện.

  - `time_utils.dart`: chứa các hàm thực hiện thao tác với thời gian.

  - `validators.dart`: chứa các hàm thực hiện validate (dùng cho form).

  - ...

* `ui`: Các tiện tích dùng cho giao diện.

  - `app_dialogs.dart`: chứa các hàm thực hiện gọi `showDialog`.

  - `media_query_extension.dart`: chứa các getter (để lấy kích thước màn hình, textScaleFactor, ...).

  - `widget_utils.dart`: chứa các biến được khai báo và khởi tại trước để dùng lại nhiều lần (như `CircularProgressIndicator`), hoặc các `helper function`.

    > **Lưu ý:**
    >
    > Nếu các `helper function` này cần phải truyền `BuildContext` thì bên trong các hàm ấy không nên dùng `BuildContext` đó để gọi đến/truy cập các phương thức của `InheritedWidget` (như Theme.of(context), ...).

   - ...

## `bootstrap.dart`

Trong file này chứa một hàm tên là `bootstrap`, hàm đó thực hiện các thiết lập cấu hình cần thiết và gọi `runApp()`.

## flavors

Nếu bạn cần thiết lập các biến thể khác nhau cho từng môi trường (development, staging, production)thì hãy xem trang web dưới đây:

🔗 [Creating flavors for Flutter](https://docs.flutter.dev/deployment/flavors)
