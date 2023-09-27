# 个性化配置

`LlamaIndex` 对 `RAG` 过程提供了全面的配置支持，允许开发者对整个过程进行个性化设置。常见的配置场景包括：

- 自定义文档分块
- 自定义向量存储
- 自定义检索
- 指定 `LLM`
- 指定响应模式
- 指定流式响应

注，个性化配置主要通过 `LlamaIndex` 提供的 `ServiceContext` 类实现。

## 配置场景示例

接下来通过简明示例代码段展示 `LlamaIndex` 对各种配置场景的支持。

### 自定义文档分块

```python
from llama_index import ServiceContext
service_context = ServiceContext.from_defaults(chunk_size=500)
```

### 自定义向量存储

```python
import chromadb
from llama_index.vector_stores import ChromaVectorStore
from llama_index import StorageContext

chroma_client = chromadb.PersistentClient()
chroma_collection = chroma_client.create_collection("quickstart")
vector_store = ChromaVectorStore(chroma_collection=chroma_collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)
```

### 自定义检索

自定义检索中，我们可以通过参数指定查询引擎(Query Engine)在检索时请求的相似文档数。

```python
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine(similarity_top_k=5)
```

### 指定 `LLM`

```python
service_context = ServiceContext.from_defaults(llm=OpenAI())
```

### 指定响应模式

```python
query_engine = index.as_query_engine(response_mode='tree_summarize')
```
### 指定流式响应

```python
query_engine = index.as_query_engine(streaming=True)
```

## 完整实例

请参考[03_Customization.ipynb](./03_Customization.ipynb) ，这是一个基于第1课的示例实现上述的所有个性化配置：
1. 文档分块大小：500
2. Chromadb作为向量存储
3. 自定义检索文档数为5
4. 指定大模型为OpenAI的模型
5. 响应模式为 `tree_summarize`
6. 问答实现流式响应

注，响应模式会在后续课程中详细介绍。
