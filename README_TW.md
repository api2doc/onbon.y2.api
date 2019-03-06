仰邦 Y2 Java 函式庫 (預覽版)
===
* [API Document](https://api2doc.github.io/onbon.y2.api/)

## 如何使用
1. 初始化 Y2 API 環境。僅需一次。
    ```java
    // without log4j
    Y2Env.initial();

    // with log4j
    Y2Env.initial(pathToLog4j);

    // android
    Y2Env.initial(true);

    // with log4j and JDK 8
    Y2Env.initial(pathToLog4j, true);
    ```

2. 連上特定的屏幕並執行一些命令。
    ```java
    // 1. 建立一個新的屏幕
    Y2Screen screen = new Y2Screen("http://1.2.3.4");

    // 2. 登入
    if (!screen.login(user, pwd)) {
        return;
    }

    // 3. 做些事情
    screen.turnOn();
    screen.changeVolume(60);
    ...

    // 4. 登出
    screen.logout();
    ```

## 範例
### 簡單的操作
```java
// 1
Y2Screen screen = new Y2Screen("http://1.2.3.4");

// 2
if (!screen.login("guest", "guest")) {
    return;
}

// 3
screen.syncTime(new Date());
screen.turnOff();
screen.turnOn();
screen.changeVolume(60);
screen.clearPlayResources();

// 4
screen.logout();
```

### 撥放一個跑馬燈
```java
// 1
Y2Screen screen = new Y2Screen("http://1.2.3.4");

// 2
if (!screen.login("guest", "guest")) {
    return;
}

// 3.1 建立一個跑馬燈分區
MarqueeArea area = new MarqueeArea(0, 0, screen.getWidth(), 40);
area.addContent("Hello everyone.")
    .fgColor(Color.green)
    .getFont()
        .size(16)
area.addContent("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.blue)
    .getFont()
        .size(20);


// 3.2 建立一個可撥放的節目
ProgramPlayFile prog = new ProgramPlayFile(1);
prog.getAreas().add(area);

// 3.3 寫入節目，取得撥放清單編號。
String listId = screen.writePlaylist(playFile);

// 3.4 撥放
screen.play(listId);

// 4
screen.logout();
```

## 範例 - 播放清單與節目
###
播放清單是節目播放的基礎，由一個以上的節目組合而成。
```java
ProgramPlayFile file1 = new ProgramPlayFile(1) // program_1
ProgramPlayFile file1 = new ProgramPlayFile(2) // program_2
String playlist = screen.writePlaylist(file1, file2, ...);
screen.play(playlist);
```

### 節目 Program
節目用來定義屏幕上要顯示的內容，內容透過區域進行管理。一個節目內可以有一個以上的區域，每個區域有各自的顯示位置與大小。
```java
ProgramPlayFile file = new ProgramPlayFile(3) // program_3
file.getAreas().add(marqueeArea);
file.getAreas().add(dateArea);
```
每個節目可以有各自播放方式，如時間、重複方式、等級等。
#### 每天播放
```java
file.getPlayWeek().all();           
```

#### 以計次方式播放，重複 3 次後切換到下一個節目
```java
file.setPlayMode(PlayMode.COUNTER)  
file.setPlayCount(3);
```
#### 以計時方式播放，播放 45 秒後切換到下一個節目
```java
file.setPlayMode(PlayMode.TIMER)  
file.setPlayTime(45);
```

## 範例 - 區域
區域用來控制顯示內容，可顯示的內容包括：
* 跑馬燈
* 一般圖文
* 具備遮罩效果的圖文
* 時間
* 時鐘
* 計數器
* 視頻

區域皆具備邊框顯示功能，可以需要啟用或取消。

### 邊框效果 Border Style
啟用邊框後，內容的有效可視範圍會依據邊框寬度縮小。取消邊框後，內容的有效可視範圍恢復到原設定大小。
```java
// 啟用邊框
AreaBorderStyle style = area.enableBorder(3);
style.animation(52, 8)  // 特效 52, 速度 8
     .blinkGrade(8);    // 閃爍 8

// 取消邊框
area.disableBorder();   
```


### 跑馬燈分區 Marquee Area
跑馬燈分區是一個單行文字的分區，將內容以水平移動的方式顯示在屏幕上。
```java
MarqueeArea area = new MarqueeArea(0, 0, 128, 30);
area.right2Left(true);

// page1
area.addContent("Hello everyone.")
    .fgColor(Color.white)   // 前景色
    .bgColor(Color.black)   // 背景色
    .animationSpeed(16)     // 特效播放速度
    .getFont()
        .size(24)           // 字體大小
        .bold()             // 粗體
        .strikethrough()    // 刪除線
        .underline();       // 底線

// page2
area.addContent("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.black)   // 前景色
    .bgColor(Color.white)   // 背景色
    .animationSpeed(1)      // 特效播放速度
    .getFont()
        .size(20);          // 字體大小

```

### 文字分區 Text Area
文字分區是一個以文字內容文主的分區，根據設置自動換行、分段顯示在屏幕上。
```java
TextArea area = new TextArea(0, 0, 128, 64);
area.stuntType(10);

// section1
area.addTextSection("Hello everyone.")
    .fgColor(Color.white)   // 前景色
    .bgColor(Color.black)   // 背景色
    .stayTime(8)            // 單頁停留時間
    .animationSpeed(16)     // 特效播放速度
    .horizontalAlignment(AlignmentType.CENTER)  // 水平對齊，置中
    .verticalAlignment(AlignmentType.CENTER)    // 垂直對齊，置中
    .rowHeight(30)          // 行高
    .getFont()
        .size(24)
        .bold()             
        .strikethrough()
        .underline();

// section2
area.addTextSection("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.black)   // 前景色
    .bgColor(Color.white)   // 背景色
    .stayTime(9)            // 單頁停留時間
    .animationSpeed(1)      // 特效播放速度
    .horizontalAlignment(AlignmentType.NEAR)    // 水平對齊，靠左
    .verticalAlignment(AlignmentType.FAR)       // 水平對齊，靠下
    .rowHeight(20)          // 行高
    .getFont()
        .size(20);

```

### 日期時間分區 DateTime Area
日期時間分區根據設定將日期、時間、星期組合顯示在屏幕上。若沒有設定寬與高，日期時間分區會根據內容自動調分區大小顯示完整的內容。
```java
DateTimeArea area;

// x, y, width, height (fixed size)
area = new DateTimeArea(100, 40, 200, 60);
// x, y only (autosize)
area = new DateTimeArea(100, 40);

area.bgColor(Color.darkGray)
    .horizontalAlignment(AlignmentType.CENTER);

// 第二行顯示：時間與日期，格式為 AM 8:16 2019-02-15
area.addUnits(DateTimePattern.AMPM_H_MM, DateTimePattern.YYYY_MM_DD1)
    .fgColor(Color.yellow);
    .getFont()
        .bold()
        .underline();

// 第二行顯示：星期
area.addUnits(DateTimePattern.WEEK);

// 第三行顯示: 月份
area.addUnits(DateTimePattern.MONTH);
    .getFont()
        .bold();
```

## 範例 - 公告分區 Bulletin Area
公告分區用於即刻顯示一些重要的文字訊息。
```java
Y2BulletinManager bulletin = screen.bulletin();

BulletinArea area1 = new BulletinArea(1, "公告一", 0, 0, 200, 40);
area1.bgColor(Color.darkGry)
     .fgColor(Color.red)
     .content("News: We are happy to announce to release this API.")
BulletinArea area2 = new BulletinArea(2, "公告二", 0, 80, 200, 40);
area2.bgColor(Color.darkGry)
     .fgColor(Color.green)
     .content("News: Java Doc is available too.")

// write to Y2
bulletin.write(area1);
bulletin.write(area2);
// play
bulletin.play();
// delete
bulletin.delete(2);
// stop
bulletin.stop();

```

## 範例 - 動態分區 Dynamic Area
動態分區可於即刻顯示訊息，無須時間設置，所有訊息於重開機之後自動被刪除。
```java
Y2DynamicManager dyn = screen.dynamic();

DynamicPlayFile file = new DynamicPlayFile();

DynamicArea area = file.create(0, 0, 100, 40);
area.addText("Welcome to ONBON");
area.addText("We are happy to announce to release this API")

dyn.write(file);

```
