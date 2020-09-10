## 简单的元素Swipe

### Android的UIAutomator2

默认的UIAutomator2只返回在屏幕上可以看到的元素。如果可见视图和不可见视图分隔了某个容器，那么此容器中一部分内容将无法（被UIAutomator2）识别到。

![simple-element-swipe.png](images/simple-element-swipe.png)

```java
/**
 * Performs swipe inside an element
 *
 * @param el  the element to swipe
 * @param dir the direction of swipe
 * @version java-client: 7.3.0
 **/
public void swipeElementAndroid(MobileElement el, Direction dir) {
    System.out.println("swipeElementAndroid(): dir: '" + dir + "'"); // always log your actions

    // Animation default time:
    //  - Android: 300 ms
    //  - iOS: 200 ms
    // final value depends on your app and could be greater
    final int ANIMATION_TIME = 200; // ms

    final int PRESS_TIME = 200; // ms

    int edgeBorder;
    PointOption pointOptionStart, pointOptionEnd;

    // init screen variables
    Rectangle rect = el.getRect();
    // sometimes it is needed to configure edgeBorders
    // you can also improve borders to have vertical/horizontal
    // or left/right/up/down border variables
    edgeBorder = 0;

    switch (dir) {
        case DOWN: // from up to down
            pointOptionStart = PointOption.point(rect.x + rect.width / 2,
                    rect.y + edgeBorder);
            pointOptionEnd = PointOption.point(rect.x + rect.width / 2,
                    rect.y + rect.height - edgeBorder);
            break;
        case UP: // from down to up
            pointOptionStart = PointOption.point(rect.x + rect.width / 2,
                    rect.y + rect.height - edgeBorder);
            pointOptionEnd = PointOption.point(rect.x + rect.width / 2,
                    rect.y + edgeBorder);
            break;
        case LEFT: // from right to left
            pointOptionStart = PointOption.point(rect.x + rect.width - edgeBorder,
                    rect.y + rect.height / 2);
            pointOptionEnd = PointOption.point(rect.x + edgeBorder,
                    rect.y + rect.height / 2);
            break;
        case RIGHT: // from left to right
            pointOptionStart = PointOption.point(rect.x + edgeBorder,
                    rect.y + rect.height / 2);
            pointOptionEnd = PointOption.point(rect.x + rect.width - edgeBorder,
                    rect.y + rect.height / 2);
            break;
        default:
            throw new IllegalArgumentException("swipeElementAndroid(): dir: '" + dir + "' NOT supported");
    }

    // execute swipe using TouchAction
    try {
        new TouchAction(driver)
                .press(pointOptionStart)
                // a bit more reliable when we add small wait
                .waitAction(WaitOptions.waitOptions(Duration.ofMillis(PRESS_TIME)))
                .moveTo(pointOptionEnd)
                .release().perform();
    } catch (Exception e) {
        System.err.println("swipeElementAndroid(): TouchAction FAILED\n" + e.getMessage());
        return;
    }

    // always allow swipe action to complete
    try {
        Thread.sleep(ANIMATION_TIME);
    } catch (InterruptedException e) {
        // ignore
    }
}
```

### iOS的XCUITest

XCUITest可以显示屏幕外的元素。强烈建议使用屏幕滑动(screen swipe)或'mobile:scroll' / 'mobile:swipe' 方法。如果你需要或更倾向于使用元素相关的方法，需要考虑以下内容：
1. 检查确保起始点和结束点都在屏幕区域之内。
2. 滚动视图通常是背景元素。页面/页脚或其他元素覆盖在滚动视图之上，限制了滚动性。尝试配置正确的边界以保证滚动性。如果不同屏幕上的边界有差异，应该通过swipe方法的参数进行配置化处理。
   
```java
/**
 * Performs swipe inside an element
 *
 * @param el  the element to swipe
 * @param dir the direction of swipe
 * @version java-client: 7.3.0
 **/
public void swipeElementIOS(MobileElement el, Direction dir) {
    System.out.println("swipeElementIOS(): dir: '" + dir + "'"); // always log your actions

    // Animation default time:
    //  - Android: 300 ms
    //  - iOS: 200 ms
    // final value depends on your app and could be greater
    final int ANIMATION_TIME = 200; // ms

    final int PRESS_TIME = 500; // ms

    // init screen variables
    Dimension dims = driver.manage().window().getSize();
    Rectangle rect = el.getRect();

    // check element overlaps screen
    if (rect.x >= dims.width || rect.x + rect.width <= 0
            || rect.y >= dims.height || rect.y + rect.height <= 0) {
        throw new IllegalArgumentException("swipeElementIOS(): Element outside screen");
    }

    // init borders per your app screen
    // or make them configurable with function variables
    int leftBorder, rightBorder, upBorder, downBorder;
    leftBorder = 0;
    rightBorder = 0;
    upBorder = 0;
    downBorder = 0;

    // find rect that overlap screen
    if (rect.x < 0) {
        rect.width = rect.width + rect.x;
        rect.x = 0;
    }
    if (rect.y < 0) {
        rect.height = rect.height + rect.y;
        rect.y = 0;
    }
    if (rect.width > dims.width)
        rect.width = dims.width;
    if (rect.height > dims.height)
        rect.height = dims.height;
    
    PointOption pointOptionStart, pointOptionEnd;
    switch (dir) {
        case DOWN: // from up to down
            pointOptionStart = PointOption.point(rect.x + rect.width / 2,
                    rect.y + upBorder);
            pointOptionEnd = PointOption.point(rect.x + rect.width / 2,
                    rect.y + rect.height - downBorder);
            break;
        case UP: // from down to up
            pointOptionStart = PointOption.point(rect.x + rect.width / 2,
                    rect.y + rect.height - downBorder);
            pointOptionEnd = PointOption.point(rect.x + rect.width / 2,
                    rect.y + upBorder);
            break;
        case LEFT: // from right to left
            pointOptionStart = PointOption.point(rect.x + rect.width - rightBorder,
                    rect.y + rect.height / 2);
            pointOptionEnd = PointOption.point(rect.x + leftBorder,
                    rect.y + rect.height / 2);
            break;
        case RIGHT: // from left to right
            pointOptionStart = PointOption.point(rect.x + leftBorder,
                    rect.y + rect.height / 2);
            pointOptionEnd = PointOption.point(rect.x + rect.width - rightBorder,
                    rect.y + rect.height / 2);
            break;
        default:
            throw new IllegalArgumentException("swipeElementIOS(): dir: '" + dir + "' NOT supported");
    }

    // execute swipe using TouchAction
    try {
        new TouchAction(driver)
                .press(pointOptionStart)
                // a bit more reliable when we add small wait
                .waitAction(WaitOptions.waitOptions(Duration.ofMillis(PRESS_TIME)))
                .moveTo(pointOptionEnd)
                .release().perform();
    } catch (Exception e) {
        System.err.println("swipeElementIOS(): TouchAction FAILED\n" + e.getMessage());
        return;
    }

    // always allow swipe action to complete
    try {
        Thread.sleep(ANIMATION_TIME);
    } catch (InterruptedException e) {
        // ignore
    }
}
```

