<!--

 * @Author: yang66995
 * @Date: 2020-09-01 21:13:37
 * @LastEditTime: 2020-09-02 20:31:09
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \yang66995\qt_dev\docs\Qt_Q.md
-->

# Qt调试问题

## Qt控制时间精度

1、**enum Qt::TimerType**

The timer type indicates how accurate a timer can be.

| Constant            | Value | Description                                                          |
| ------------------- | ----- | -------------------------------------------------------------------- |
| Qt::PreciseTimer    | 0     | Precise timers try to keep millisecond accuracy                      |
| Qt::CoarseTimer     | 1     | Coarse timers try to keep accuracy within 5% of the desired interval |
| Qt::VeryCoarseTimer | 2     | Very coarse timers only keep full second accuracy                    |

On UNIX (including Linux, macOS, and iOS), Qt will keep millisecond accuracy for Qt::PreciseTimer. For Qt::CoarseTimer, the interval will be adjusted up to 5% to align the timer with other timers that are expected to fire at or around the same time. The objective is to make most timers wake up at the same time, thereby reducing CPU wakeups and power consumption.
On Windows, Qt will use Windows's Multimedia timer facility (if available) for Qt::PreciseTimer and normal Windows timers for Qt::CoarseTimer and Qt::VeryCoarseTimer.
On all platforms, the interval for Qt::VeryCoarseTimer is rounded to the nearest full second (e.g. an interval of 23500ms will be rounded to 24000ms, and 20300ms to 20000ms).

```c++
    //程序启动后 自动开始定时发送串口指令帧 周期为PERIOD
    TimerSendInit = new QTimer(this);
    connect(TimerSendInit, SIGNAL(timeout()), this, SLOT(OnTimerSendInit()));
    TimerSendInit->setTimerType(Qt::PreciseTimer); // 尽可能保证定时器毫秒精度
    TimerSendInit->start(PERIOD);

```

发送80ms，未添加`TimerSendInit->setTimerType(Qt::PreciseTimer);`前，示波器实测92ms，添加后测试为80ms。

## QByteArray to quint16

```Qt
QDataStream dataStream(yourByteArray);
quint16 foo;
dataStream >> foo;
```

## 提取QByteArray中的字节

### mid()

**QByteArray** QByteArray::**mid**(**int** *pos*, **int** *len* = -1) const

Returns a byte array containing *len* bytes from this byte array, starting at position *pos*.

If *len* is -1 (the default), or *pos* + *len* >= [size](qbytearray.html#size)(), returns a byte array containing all bytes starting at position *pos* until the end of the byte array.

Example:

```cpp

  QByteArray x("Five pineapples");
  QByteArray y = x.mid(5, 4);     // y == "pine"
  QByteArray z = x.mid(5);        // z == "pineapples"

```

See also [left](qbytearray.html#left)() and [right](qbytearray.html#right)().

### left()

**QByteArray** QByteArray::**left**(**int** *len*) const

Returns a byte array that contains the leftmost *len* bytes of this byte array.

The entire byte array is returned if *len* is greater than size().

Example:

```cpp

  QByteArray x("Pineapple");
  QByteArray y = x.left(4);
  // y == "Pine"
```

### right()

**QByteArray** QByteArray::**right**(**int** *len*) const

Returns a byte array that contains the rightmost *len* bytes of this byte array.

The entire byte array is returned if *len* is greater than size().

Example:

```cpp

  QByteArray x("Pineapple");
  QByteArray y = x.right(5);
  // y == "apple"
```

## Qt中数据类型定义

| Qt数据类型 | 等效定义               | 字节数 |
| ---------- | ---------------------- | ------ |
| qint8      | signed char            | 1      |
| qint16     | signed short           | 2      |
| qint32     | signed int             | 4      |
| qint64     | long long int          | 8      |
| qlonglong  | long long int          | 8      |
| quint8     | unsigned char          | 1      |
| quint16    | unsigned short         | 2      |
| quint32    | unsigned int           | 4      |
| quint64    | unsigned long long int | 8      |
| qulonglong | unsigned long long int | 8      |
| uchar      | unsigned char          | 1      |
| ushort     | unsigned short         | 2      |
| uint       | unsigned int           | 4      |
| ulong      | unsigned long          | 8      |
| qreal      | double                 | 8      |
| qfloat16   |                        | 2      |

qfloat16是Qt中新增的一个类，用于表示16位浮点数，需包含类`<QFloat16>`。