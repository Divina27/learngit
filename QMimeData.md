`MIME`类型

可以包含为文本、图像、音频、视频以及其他应用程序专用的数据

官方的 MIME 信息是由 Internet Engineering Task Force (`IETF`) 在下面的文档中提供的：

RFC-822 Standard for ARPA Internet text messages
RFC-2045 MIME Part 1: Format of Internet Message Bodies
RFC-2046 MIME Part 2: Media Types
RFC-2047 MIME Part 3: Header Extensions for Non-ASCII Text
RFC-2048 MIME Part 4: Registration Procedures
RFC-2049 MIME Part 5: Conformance Criteria and Examples

具体类型可以参考：[MIME 参考手册 [(w3school.com.cn)]

(https://www.w3school.com.cn/media/media_mimeref.asp)

描述：

`QMimeData`用于描述可以存储在剪贴板中并通过拖放机制传输的信息。`QMimeData`对象将它们所持有的数据与相应的MIME类型相关联，以确保信息可以在应用程序之间安全地传输，并在同一应用程序中进行复制。

`QMimeData`对象通常使用new创建，并提供给`QDrag`或`QClipboard`对象。这是为了使Qt能够管理它们使用的内存。

一个`QMimeData`对象可以同时使用几种不同的格式存储相同的数据。 format()函数按首选项顺序返回可用格式的列表。 data()函数返回与MIME类型关联的原始数据，`setData`()为MIME类型设置数据。

```c++
  void MyWidget::dragEnterEvent(QDragEnterEvent *event)
  {
      if (event->mimeData()->hasUrls())
          event->acceptProposedAction();
  }
 
  void MyWidget::dropEvent(QDropEvent *event)
  {
      if (event->mimeData()->hasUrls()) {
          foreach (QUrl url, event->mimeData()->urls()) {
              ...
          }
      }
  }
```

拖拽事件中将自定义数据类型存放在`QMimeData`对象中的方法

1、可以使用`setData`()将自定义数据作为`QByteArray`直接存储在`QMimeData`对象中。 例如：

```c++
 QByteArray csvData = ...;
 QMimeData *mimeData = new QMimeData;
 mimeData->setData("text/csv", csvData);
```

2、将`QMimeData`子类化，然后重新实现`hasFormat`()，formats()和`retrieveData`()。

3、      如果拖放操作发生在单个应用程序中，则我们可以将`QMimeData`子类化，并在其中添加额外的数据，并在接收者的drop事件处理程序中使用`qobject_cast`()：

```c++
  void MyWidget::dropEvent(QDropEvent *event)
  {
      const MyMimeData *myData =
              qobject_cast<const MyMimeData *>(event->mimeData());
      if (myData) {
          //直接访问myData的数据（不通过QMimeData的API）
      }
  }
```

