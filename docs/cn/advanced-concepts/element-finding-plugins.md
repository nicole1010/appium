## 元素查找插件

从1.9.2版本开始, Appium支持使用插件来查找元素，通过使用 `-custom` 定位策略。 目前此功能为实验性功能。

### 用法

1. 安装符合Appium元素查找标准的第三方元素查找插件 (标准如下)。 (必须是Node模块, 通过NPM安装或者本地引用). 插件可以安装在你系统上的任意位置，主要有三种方式：
    * 独立于Appium的目录 (通过在任意文件夹运行 `npm install <plugin>`)
    * 在Appium依赖树下 (通过在Appium根目录下运行`npm install <plugin>`)
    * 在系统上全局安装 (通过运行 `npm install -g <plugin>`)

    (当然，插件本身可能有安装和部署说明，在插件的文档中会有详细说明。)

2. 在你的测试中添加一个新的capability：`customFindModules` . 这个capability必须是一个object，至少有一个键和一个值。键名为 "插件快捷键", 值为"插件说明". 例如:

    ```
    {
        "customFindModules": {
            "plug": "my-element-finding-plugin"
        }
    }
    ```

    "plug"是快捷方式, 而"my-element-finding-plugin"是说明.
    You will use the shortcut in your own test code, so it can be any string
    which is a valid JSON key. The reference must be a reference to the plugin's
    Node module, and it must be formatted in such a way that Appium can
    [require](https://nodejs.org/api/modules.html#modules_require) it using
    Node's [module resolution](https://medium.freecodecamp.org/requiring-modules-in-node-js-everything-you-need-to-know-e7fbd119be8).

Once you've started a session with this capability, we say that the plugin (or plugins---multiple plugins are of course supported) are _registered_. You can find an element using a registered plugin by doing two things:

1. Using the `-custom` locator strategy
2. Prefixing your selector with `<shortcut>:`

So with the example plugin above, if I wanted to find an element using the selector "foo", it would look like this (in imaginary client code):

```js
driver.findElement('-custom', 'plug:foo');
```

In other words, I'm using the `-custom` locator strategy, and sending in the selector `foo`, making sure Appium knows that it is specifically the `plug` plugin which should handle the find request.

In the case where only one plugin is registered, you can omit the shortcut in the selector (since Appium will not be confused about which plugin you want to use):

```js
driver.findElement('-custom', 'foo');
```

The `-custom` locator strategy is not well supported in all Appium clients at this point; check client documentation for the correct invocation for this strategy.

### Developing a Plugin

Anyone can develop an element finding plugin for Appium. The only rules are as follows:

* The plugin must be a Node module which has a named export called `find`
* This method, when called, must return a (possibly empty) array of element objects

When Appium calls your `find` method, it will pass the following parameters:

* An instance of the `driver` object representing the current session (this would be, for example an instance of `XCUITestDriver`)
* A logging object which you can use to write logs into the Appium log
* The selector (a string) the user of your plugin has sent for the purpose of finding the element
* A boolean value: whether the user is looking for multiple elements (true) or not (false). Note that you must always return an array, regardless of whether the user needs more than one element. This flag is passed in case it is useful for optimizing searches that do not require multiple elements returned.

That's all there is to it! See the list of known plugins below for concrete examples.


### List of Known Plugins

* [Test.ai Classifier](https://github.com/testdotai/appium-classifier-plugin)
