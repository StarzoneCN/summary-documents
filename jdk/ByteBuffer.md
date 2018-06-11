## ByteBuffer

#### ByteBuffer的字节序

- `ByteOrder`类
  - ByteOrder.`BIG_ENDIAN`：大端序
  - ByteOrder.`LITTLE_ENDIAN`：小端序
  - ByteOrder.`nativeOrder()`：系统默认

#### 其他类型的buffer

* `ByteBuffer`另一个特别的地方是可以在它的基础上得到其他类型的`buffer`。eg：
  * *CharBuffer asCharBuffer()*
    * 在`ByteBuffer`上创建一个`CharBuffer` 视图（从`position`位置到`limit`位置），在该视图上读写操作会按照`ByteBuffer`的字节序去操作数；
    * 视图buffer的`readOnly`属性和`direct`属性与`ByteBuffer`的一致；
    * 只有通过这种方式得到其他类型的`direct buffer`；