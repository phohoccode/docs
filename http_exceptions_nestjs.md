# HTTP Exceptions in NestJS

NestJS provides a set of built-in HTTP exceptions that correspond to standard HTTP status codes. These exceptions can be used to handle errors gracefully and provide meaningful responses to clients.

| Exception | HTTP Status | Description | Example |
|-----------|-------------|-------------|---------|
| **BadRequestException** | 400 | Yêu cầu không hợp lệ. Thường dùng khi dữ liệu đầu vào không đúng định dạng hoặc thiếu trường bắt buộc. | `throw new BadRequestException('Invalid input');` |
| **UnauthorizedException** | 401 | Không có quyền truy cập. Dùng khi người dùng chưa xác thực hoặc token không hợp lệ. | `throw new UnauthorizedException('Invalid token');` |
| **ForbiddenException** | 403 | Bị từ chối quyền truy cập. Người dùng đã xác thực nhưng không có quyền thực hiện hành động. | `throw new ForbiddenException('Access denied');` |
| **NotFoundException** | 404 | Không tìm thấy tài nguyên. | `throw new NotFoundException('User not found');` |
| **NotAcceptableException** | 406 | Yêu cầu không chấp nhận được. Thường dùng khi `Accept` header không phù hợp. | `throw new NotAcceptableException('Requested format not supported');` |
| **RequestTimeoutException** | 408 | Yêu cầu vượt quá thời gian cho phép. | `throw new RequestTimeoutException('Request timed out');` |
| **ConflictException** | 409 | Xung đột dữ liệu. Thường dùng khi dữ liệu trùng lặp hoặc không thể thực hiện vì xung đột trạng thái. | `throw new ConflictException('Email already exists');` |
| **GoneException** | 410 | Tài nguyên đã bị xóa và không còn tồn tại. | `throw new GoneException('Resource deleted');` |
| **HttpVersionNotSupportedException** | 505 | Phiên bản HTTP không được hỗ trợ. | `throw new HttpVersionNotSupportedException('HTTP version not supported');` |
| **PayloadTooLargeException** | 413 | Dữ liệu gửi lên quá lớn. | `throw new PayloadTooLargeException('File is too large');` |
| **UnsupportedMediaTypeException** | 415 | Kiểu dữ liệu không được hỗ trợ. | `throw new UnsupportedMediaTypeException('Content type not supported');` |
| **UnprocessableEntityException** | 422 | Dữ liệu hợp lệ nhưng không thể xử lý (ví dụ: lỗi xác thực phức tạp). | `throw new UnprocessableEntityException('Validation failed');` |
| **InternalServerErrorException** | 500 | Lỗi server nội bộ. | `throw new InternalServerErrorException('Something went wrong');` |
| **NotImplementedException** | 501 | Chức năng chưa được triển khai. | `throw new NotImplementedException('Feature not implemented');` |
| **ImATeapotException** | 418 | Lỗi "Tôi là một cái ấm trà" (chỉ dùng để vui). | `throw new ImATeapotException('I\'m a teapot');` |
| **MethodNotAllowedException** | 405 | Phương thức HTTP không được phép với URL này. | `throw new MethodNotAllowedException('POST not allowed');` |
| **BadGatewayException** | 502 | Lỗi từ gateway hoặc proxy khi nhận phản hồi không hợp lệ từ server khác. | `throw new BadGatewayException('Bad gateway');` |
| **ServiceUnavailableException** | 503 | Dịch vụ tạm thời không khả dụng. | `throw new ServiceUnavailableException('Service unavailable');` |
| **GatewayTimeoutException** | 504 | Gateway hoặc proxy hết thời gian chờ. | `throw new GatewayTimeoutException('Gateway timed out');` |
| **PreconditionFailedException** | 412 | Điều kiện tiền đề thất bại. Thường dùng với header `If-Match` hoặc `If-Unmodified-Since`. | `throw new PreconditionFailedException('Precondition failed');` |

