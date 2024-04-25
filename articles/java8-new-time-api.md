# java 8中提供的新时间日期类

## 时间的表示

如今世界主要的时间标准是协调世界时UTC。UTC的时间尺度下，秒的长度是始终固定的，而分钟及比分钟更大的单位长度是可变的。因为地球自转不规律（目前是逐渐变慢），一天的会比86400秒稍微长一些，UTC会不规律加入闰秒（leap second）来抵消这种差距，也就是一分钟可能会有61秒。地球上不同地区的人在同一时刻观察到的时间是不同的，理论上我们以被15整除的经线为中心，向东西两侧延伸7.5度，每15°划分一个时区，以时区中央经线上的时间作为区时，比如零时区是以本初子午线为基准，从东经7.5度到西经7.5度。UTC时间就是零时区时间，表示为1970-01-01 00:00:00 UTC，在此基础上每往东一个时区，时间就快一小时，例如北京时间是东八区区时，表示为1970-01-01 00:08:00 UTC+8，这两个不同的当地时间表示同一个瞬间。  
当前世界通用的历法是公历，也叫格里高利历（Gregorian calendar），是儒略历的替代。格里历每一个可以被4整除的年份都是闰年，但可以被100整除的年份，必须也能被400整除才是闰年。

这里需要明确几个概念：

- 时刻(instant)：表示时间长河中的某个瞬间
- 时间(time/localTime)：可以理解为某个瞬间某个地区的某个钟表上的时间，这个瞬间在其他地区的时间并不相同，只有时间也无法反推到这个瞬间
- 时间戳(timestamp)：标识某个瞬间的一串字符或编码信息，如Unix时间戳是从Unix Epoch到这个瞬间的秒数，不考虑闰秒
- 纪元(epoch)：表示一个时刻，一个参考点，如Unix系统中的Epoch是1970-01-01 00:00:00 UTC，这也是Java中的epoch

*[UTC]:Coordinated Universal Time

## java8之前

### Date

表示某个时刻，记录到epoch的毫秒数  
不提供操作毫秒外其他单位的方法  
toString()方法按照系统默认时区解析为Mon Apr 01 17:15:04 CST 2024

```java
public Date() {
    this(System.currentTimeMillis());
}
```

### Calendar

Calendar提供某个时刻到不同时间单位的转换和不同单位维度的操作  
有时区属性

```java
Calendar rightNow = Calendar.getInstance();

Calendar calendar = Calendar.getInstance();
calendar.setTime(new Date());
int year = calendar.get(Calendar.YEAR);
calendar.set(Calendar.MONTH, 6);
calendar.add(Calendar.MONTH, 3);
boolean before = calendar.before(rightNow);
```

### DateFormat

提供将时间格式化（format）为字符串和将字符串解析（parse）为时间的能力  
线程不安全  
有时区属性  

```java
// 提供多种默认格式，默认为DateFormt.MEDIUM
DateFormat dateFormatter = DateFormat.getDateInstance();
// 根据Locale判断语言，时区等
DateFormat dateFormatter1 = DateFormat.getDateInstance(DateFormat.MEDIUM, Locale.CHINA);

DateFormat timeFormatter = DateFormat.getTimeInstance(DateFormat.FULL);
String formattedTime = timeFormat.format(new Date())；
// 下午05时05分02秒 CST

DateFormat dateTimeFormatter = DateFormat.getDateTimeInstance(DateFormat.MEDIUM, DateFormat.MEDIUM);
String formattedDateTime = dateTimeFormat.format(new Date());
// 2024-4-8 17:09:09       
```

SimpleDateFormat  
继承DateFormat的具体类，允许自定义pattern

```java
SimpleDateFormat dateTimeFormatter = new SimpleDateFormat();
dateTimeFormatter.applyPattern("yyyy-MM-dHH:mm:ss");
String formattedDateTime = dateTimeFormatter.format(new Date());
// 2024-04-08 17:24:00
```

## java8

在java.time包下
