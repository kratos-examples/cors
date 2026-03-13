# Changes

Code differences compared to source project.

## internal/server/http.go (+22 -0)

```diff
@@ -4,11 +4,28 @@
 	"github.com/go-kratos/kratos/v2/log"
 	"github.com/go-kratos/kratos/v2/middleware/recovery"
 	"github.com/go-kratos/kratos/v2/transport/http"
+	"github.com/gorilla/handlers"
 	pb "github.com/yylego/kratos-examples/demo1kratos/api/student"
 	"github.com/yylego/kratos-examples/demo1kratos/internal/conf"
 	"github.com/yylego/kratos-examples/demo1kratos/internal/service"
 )
 
+/*
+测试 CORS 是否生效的最简单方案：
+
+在 postman 的 GET http://127.0.0.1:18000/helloworld/kratos
+在请求的 Headers 里面添加 Origin: https://www.google.com 这项
+在 postman 里面响应头里面就能看到 Access-Control-Allow-Origin: * 信息。
+
+或者
+
+使用 curl 命令行工具：
+curl -v --location 'http://127.0.0.1:18000/helloworld/kratos' --header 'Origin: https://www.google.com'
+输出里面有 > Origin: https://www.google.com (这是收到的请求头信息)
+输出里面有 < Access-Control-Allow-Origin: * 的信息。
+就基本证明是大功告成啦。
+*/
+
 func NewHTTPServer(c *conf.Server, student *service.StudentService, logger log.Logger) *http.Server {
 	var opts = []http.ServerOption{
 		http.Middleware(
@@ -20,6 +37,11 @@
 	}
 	if c.Http.Address != "" {
 		opts = append(opts, http.Address(c.Http.Address))
+		// cp from https://github.com/go-kratos/examples/blob/61daed1ec4d5a94d689bc8fab9bc960c6af73ead/http/cors/main.go#L19
+		opts = append(opts, http.Filter(handlers.CORS(
+			handlers.AllowedOrigins([]string{"*"}),
+			handlers.AllowedMethods([]string{"GET", "POST"}),
+		)))
 	}
 	if c.Http.Timeout != nil {
 		opts = append(opts, http.Timeout(c.Http.Timeout.AsDuration()))
```

