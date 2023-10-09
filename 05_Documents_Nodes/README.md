# Documents & Nodes (文档与节点)

在 `LlamaIndex` 中，`Document` 和 `Node` 是最核心的数据抽象。

## Document

`Document` 是任何数据源的容器：

- PDF
- API响应
- 数据库
- ...

`Document` 存储：

- 文本数据
- 属性数据
    - 元数据 (metadata)
    - 关系数据 (relationships)

示例：

```python
from llama_index import Document
text_list = ["hello", "world"]
documents = [Document(text=t) for t in text_list]
```

### 自定义Document

自定义Document可以实现元数据和文档ID的设置。

元数据可以在文档构建时指定，也可以在文档对象上修改，还可以在 `SimpleDirectoryReader` 的使用中设置。

1. 构建时指定

    ```python
    from llama_index import Document
    document = Document(
        text='Hello World',
        metadata={
            'filename': 'hello_world.pdf',
            'category': 'science'
        }
    )
    ```

2. 在文档对象上修改

    ```python
    document.metadata = {'filename': 'hello_world_v2.pdf'}
    ```

3. 在 `SimpleDirectoryReader` 的使用中设置

    当使用 `SimpleDirectoryReader` 加载文档时，利用 `file_metadata` 回调进行设置

    ```python
    from llama_index import SimpleDirectoryReader
    filenama_hook = lambda filename: {'file_name': filename}
    documents = SimpleDirectoryReader('./data', file_metadata=filenama_hook).load_data()
    ```

    注，请参阅[simple-directory-reader](https://docs.llamaindex.ai/en/stable/examples/data_connectors/simple_directory_reader.html#simple-directory-reader)了解 `SimpleDirectoryReader` 的接口定义。

文档ID可以在 `Document` 创建后设置

```python
from llama_index import Document
document = Document(text='Hello World')
document.doc_id = "xxxx-yyyy"
```

## Node

`Node` 是 `LlamaIndex` 中的一等公民。`Node` 也包含了 `Document` 中相同类型的数据和属性。

`Node` 通常有两种构建方式：

1. 基于API直接构建
2. 基于 `Document`，利用节点解析器（NodeParser）生成。

注，基于 `Document` 衍生出来的 `Node` 也继承了 `Document` 上的属性。

示例：

```python
# 基于API直接构建节点
from llama_index.schema import TextNode
node = TextNode(text="hello world", id_="1234-5678")
```

```python
# 利用节点解析器生成节点
from llama_index import Document
from llama_index.node_parser import SimpleNodeParser

text_list = ["hello", "world"]
documents = [Document(text=t) for t in text_list]

parser = SimpleNodeParser.from_defaults()
nodes = parser.get_nodes_from_documents(documents)
```

### 自定义Node

通常开发者可以通过自定义Node来：

1. 定义节点间的关系 (relationship)
2. 自定义节点ID

#### 定义节点间的关系

`RelatedNodeInfo` 被用来建立关系。`RelatedNodeInfo` 支持在构造时传递参数设置元数据。

```python
from llama_index.schema import TextNode, NodeRelationship, RelatedNodeInfo

hello_node = TextNode(text="Hello", id_="1111-1111")
world_node = TextNode(text="World", id_="2222-2222")

hello_node.relationships[NodeRelationship.NEXT] = RelatedNodeInfo(node_id=world_node.node_id, metadata={"created_by": "VerySmallWoods"})
world_node.relationships[NodeRelationship.PREVIOUS] = RelatedNodeInfo(node_id=hello_node.node_id)
nodes = [hello_node, world_node]
```

#### 自定义节点ID

```python
from llama_index.schema import TextNode, NodeRelationship, RelatedNodeInfo

hello_node = TextNode(text="Hello", id_="1111-1111")
hello_node.id_ = "3333-3333"
```

## 完整实例

对于以上介绍的Document和Node的使用，请参考完整示例代码[05_Documents_Nodes.ipynb](./05_Documents_Nodes.ipynb) 
