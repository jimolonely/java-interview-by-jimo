## 字节流和字符流有什么区别？
1. 字节流以字节为单位，字符流以字符，也就是`char`为单位；
2. 字节流的接口为`InputStream`和`OutputStream`，字符流为`Reader`和`Writer`。
3. 对于文本数据，可以直接用字符流读取，会自动解码出文本内容。

## 如何将字节流转为字符流？
使用`InputStreamReader`，这是个代理类，输入字节流。当然也有`OutputStreamReader`。

## 常见的字符流类有哪些？
* `FileReader/Writer`：文件读写

* `CharArrayReader/Writer`：类似字节流的`ByteArrayInputStream`
* `StringReader/Writer`：里面就是一个`String`，调用其`charAt`读取字符，`StringWriter`内部是`StringBuffer`来写入。感觉用处不大。

## PrintStream和PrintWriter是什么？
用于打印字符，增加了各种数据类型的`print`方法。`System.out.println`就是`PrintStream`实现的。他的好处是不会抛出IOException。

