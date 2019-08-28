# <div style="text-align:center;color:#FF9900">Elasticsearch Reference</div>

*本篇将对Elasticsearch[7.3]文档中的重点进行记录和备注*

## 概念
### 倒排索引
`Elasticsearch`(下文缩写为：`ES`或`es`)使用了`倒排索引`(inverted index)，保证了每次全局索引的实时性（一般在1秒以内）；`倒排索引`会列出所有文档中的唯一的`word`（词），并标记`word`关联的文档；
> An index can be thought of as an optimized collection of documents and each document is a collection of fields, which are the key-value pairs that contain your data.

默认，es会对所有的`field`创建索引，并且每种类型（type）的索引保存和搜索方式也各不相同
> Elasticsearch indexes all data in every field and each indexed field has a dedicated, optimized data structure. For example, text fields are stored in inverted indices, and numeric and geo fields are stored in BKD trees. The ability to use the per-field data structures to assemble and return search results is what makes Elasticsearch so fast.
