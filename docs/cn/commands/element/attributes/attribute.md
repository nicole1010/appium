# 获得元素属性
获得元素属性值

## 样例

```java
// Java
MobileElement element = (MobileElement) driver.findElementByAccessibilityId("SomeAccessibilityID");
String tagName = element.getAttribute("content-desc");

```

```python
# Python
tagName = self.driver.find_element_by_accessibility_id('SomeAccessibilityID').get_attribute('content-desc')

```

```javascript
// Javascript
// webdriver.io example
let attribute = $("~SomeAccessibilityId").getAttribute("content-desc");

// wd example
let element = await driver.elementByAccessibilityId("SomeAccessibilityID");
let tagName = await element.getAttribute("content-desc");
```

```ruby
# Ruby
# ruby_lib example
find_element(:accessibility_id, 'SomeAccessibilityID').attribute("content-desc")

# ruby_lib_core example
@driver.find_element(:accessibility_id, 'SomeAccessibilityID').attribute("content-desc")

```

```php
# PHP
$el = $this->byAccessibilityId('SomeAccessibilityID');
$name = $el->attribute('name');

```

```csharp
// C#
var element = driver.FindElementByAccessibilityId("SomeAccessibilityID");
string tagName = element.GetAttribute("content-desc");

```

## 支持

### Appium Server
|平台|Driver|平台版本|Appium版本|Driver版本|
|--------|----------------|------|--------------|--------------|
| iOS | [XCUITest](/docs/en/drivers/ios-xcuitest.md) | 9.3+ | 1.6.0+ | All |
|  | [UIAutomation](/docs/en/drivers/ios-uiautomation.md) | 8.0 to 9.3 | All | All |
| Android | [Espresso](/docs/en/drivers/android-espresso.md) | ?+ | 1.9.0+ | All |
|  | [UiAutomator2](/docs/en/drivers/android-uiautomator2.md) | ?+ | 1.6.0+ | All |
|  | [UiAutomator](/docs/en/drivers/android-uiautomator.md) | 4.3+ | All | All |
| Mac | [Mac](/docs/en/drivers/mac.md) | ?+ | 1.6.4+ | All |
| Windows | [Windows](/docs/en/drivers/windows.md) | 10+ | 1.6.0+ | All |

### Appium Clients

|语言|支持|文档|
|--------|-------|-------------|
|[Java](https://github.com/appium/java-client/releases/latest)| All | [seleniumhq.github.io](https://seleniumhq.github.io/selenium/docs/api/java/org/openqa/selenium/WebElement.html#getAttribute) |
|[Python](https://github.com/appium/python-client/releases/latest)| All | [selenium-python.readthedocs.io](http://selenium-python.readthedocs.io/api.html#selenium.webdriver.remote.webelement.WebElement.get_attribute) |
|[Javascript (WebdriverIO)](http://webdriver.io/index.html)| All |  |
|[Javascript (WD)](https://github.com/admc/wd/releases/latest)| All | [github.com](https://github.com/admc/wd/blob/master/lib/commands.js#L1350) |
|[Ruby](https://github.com/appium/ruby_lib/releases/latest)| All | [www.rubydoc.info](https://www.rubydoc.info/gems/selenium-webdriver/Selenium%2FWebDriver%2FElement:attribute) |
|[PHP](https://github.com/appium/php-client/releases/latest)| All | [github.com](https://github.com/appium/php-client/) |
|[C#](https://github.com/appium/appium-dotnet-driver/releases/latest)| All | [github.com](https://github.com/appium/appium-dotnet-driver/blob/master/src/Appium.Net/Appium/AppiumWebElement.cs) |


## HTTP API规范

### 端点

`GET /session/:session_id/elements/:element_id/attribute/:name`

### URL参数

|名称|描述|
|----|-----------|
|session_id|发送命令用来会话的id|
|element_id|从中获取元素属性的id|
|name|属性名称|

### JSON 参数

无

### 响应
如果未设置，属性的置为空 (`string`)

## 另请参见
* [W3C Specification](https://www.w3.org/TR/webdriver/#dfn-get-element-attribute)
* [JSONWP Specification](https://github.com/SeleniumHQ/selenium/wiki/JsonWireProtocol#sessionsessionidelementidattributename)