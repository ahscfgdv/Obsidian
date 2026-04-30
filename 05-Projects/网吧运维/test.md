### 知识库文档预览

### 知识库文档预览

1. 触发阶段 (Frontend UI)
	  在 KnowledgeView.vue 中，当管理员点击“预览”按钮时，会触发 handlePreview(row) 函数：
	-  状态控制：开启 previewVisible 弹窗，并设置 previewLoading = true。
	-  参数传递：将当前行文档的 filename 传给 API 层。
2. API 请求阶段 (Frontend API)
	  调用 api/rag.ts 中的 previewPdf 方法：
	-  核心配置：通过 Axios 发送请求，并显式设置 { responseType: 'blob' }。
	-  作用：告诉浏览器不要将返回的数据尝试解析为 JSON 或字符串，而是保留为原始的二进制大对象 (Blob)。

3. 网络拦截阶段 (Frontend Utils)
	  请求通过 utils/request.ts 的响应拦截器：
	- 类型识别：拦截器检查 config.responseType。
	- 逻辑跳过：识别到是 blob 类型时，跳过常规的业务状态码 (res.code) 校验。因为二进制流中不存在 JSON 结构，直接将原始 Blob 对象返回给业务函数。

4. 后端处理阶段 (Backend API)
	  后端 app/api/v1/rag.py 中的 preview_pdf 接口被调用：
	- 路径定位：根据 filename 在服务器的 knowledge/ 目录下定位物理文件。
	- 流式响应：使用 FastAPI 的 FileResponse 返回。它会自动设置正确的 Content-Type: application/pdf，并将文件内容以数据流的形式发送给前端。

### 知识库文档预览

1. 触发阶段 (Frontend UI)
	  在 KnowledgeView.vue 中，当管理员点击“预览”按钮时，会触发 handlePreview(row) 函数：
	-  状态控制：开启 previewVisible 弹窗，并设置 previewLoading = true。
	-  参数传递：将当前行文档的 filename 传给 API 层。
2. API 请求阶段 (Frontend API)
	  调用 api/rag.ts 中的 previewPdf 方法：
	-  核心配置：通过 Axios 发送请求，并显式设置 { responseType: 'blob' }。
	-  作用：告诉浏览器不要将返回的数据尝试解析为 JSON 或字符串，而是保留为原始的二进制大对象 (Blob)。

3. 网络拦截阶段 (Frontend Utils)
	  请求通过 utils/request.ts 的响应拦截器：
	- 类型识别：拦截器检查 config.responseType。
	- 逻辑跳过：识别到是 blob 类型时，跳过常规的业务状态码 (res.code) 校验。因为二进制流中不存在 JSON 结构，直接将原始 Blob 对象返回给业务函数。

4. 后端处理阶段 (Backend API)
	  后端 app/api/v1/rag.py 中的 preview_pdf 接口被调用：
	- 路径定位：根据 filename 在服务器的 knowledge/ 目录下定位物理文件。
	- 流式响应：使用 FastAPI 的 FileResponse 返回。它会自动设置正确的 Content-Type: application/pdf，并将文件内容以数据流的形式发送给前端。

5. 浏览器渲染阶段 (Frontend Browser)  
	回到 KnowledgeView.vue 的 handlePreview 回调中：
	- URL 生成：使用浏览器原生 API URL.createObjectURL(blob)。这个方法会为内存中的二进制数据生成一个临时的、以 blob: 开头的伪 URL。
	-  组件嵌入：将这个生成的 URL 赋值给 previewUrl。在 HTML 模板中，这个 URL 被绑定到 <iframe> 的 src 属性上。
	
	-组件嵌入：将这个生成的 URL 赋值给 previewUrl。在 HTML 模板中，这个 URL 被绑定到 <iframe> 的 src 属性上。

