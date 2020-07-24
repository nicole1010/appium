## Chromedriver

Appium 支持对基于 Chrome 内核的 H5 应用（混合应用）或者网页（Chrome 中的网页或者内建的浏览器中的网页）进行自动化。Appium 管理维护着一个 [Chromedriver](https://sites.google.com/a/chromium.org/chromedriver/) 实例，当需要的时候，会使用代理模式将命令传递给这个实例。这和[最新版本的 Chromedriver](https://chromedriver.storage.googleapis.com/LATEST_RELEASE) 是绑定的。我们可以通过 npm 包来安装[appium-chromedriver](https://www.npmjs.com/package/appium-chromedriver)。 (Github: [appium-chromedriver](https://github.com/appium/appium-chromedriver))。

不幸的是，随着 Chromedriver 的每次升级，能支持的 Chrome 的最小版本都会升级，这就导致了老设备上经常无法在绑定的版本上进行自动化。于是在 Appium 的服务端就会有这样的错误日志：
```
An unknown server-side error occurred while processing the command.
Original error: unknown error: Chrome version must be >= 55.0.2883.0
```

为了解决这个问题，我们很有必要去给 Appium 提供一个合适的 Chromedriver 二进制文件去匹配 (https://raw.githubusercontent.com/appium/appium-chromedriver/master/config/mapping.json) 运行在你被测试设备上的 Chrome 引擎的版本。
通过阅读`Chromedriver/Chrome compatibility`这一章节下的内容，你可以了解到更多关于 Chromedriver 可执行文件的信息。

这里有几个方法去让你自定义 Appium 中的 Chromedriver:

#### 当你在安装服务时

提供包含实际版本号的 `--chromedriver_version` 命令行参数
```
npm install appium --chromedriver_version="2.16"
```
或者在环境变量 `CHROMEDRIVER_VERSION` 中去指定 Chromedriver 的版本，
例如，
```
CHROMEDRIVER_VERSION=2.20 npm install appium
```
当然你也可以通过设置 `LATEST` 去获取最新版本。

#### 当你启动服务时
通过设定`--chromedriver-executable` 这个服务器标记，你可以在运行时指定 Chromedriver 的版本。而这个服务器的标记会根据 Chromedriver 可执行文件的绝对路径被手动下载并放入到服务器文件系统相应目录下。
```
appium --chromedriver-executable /path/to/my/chromedriver
```

#### 当你开始一个会话时（手动发现）
通过提供 `chromedriverExecutable` cap包括已被手动下载并放入到服务器文件系统的 Chromedriver 可执行文件的绝对路径后，你可以在会话功能中指定 Chromedriver 的版本。
你可以通过点击http://appium.io/docs/en/writing-running-appium/caps/去查看更多的详细内容。

#### 当你开始一个会话时（自动发现）
Appium 可以自动检测出目标所需要的 Chrome 引擎的版本。当本地文件系统没有相匹配的 Chromedriver 时，Appium 会自动下载。
你可以通过阅读 `Automatic discovery of compatible Chromedriver` 章节下的内容去获取更多详细信息。

### Chromedriver/Chrome 的兼容性
你可以在https://raw.githubusercontent.com/appium/appium-chromedriver/master/config/mapping.json中找到 Chromedriver 版本的列表以及其能支持的最低 Chrome 版本。

自版本 *2.46* 后，Google 更改了对 Chromedriver 版本控制的规则，所以现在主要的 Chromedriver 版本对应的主要的 web 视图/浏览器的版本可以被自动化。根据 [版本选择](https://chromedriver.chromium.org/downloads/version-selection) 此文档

要查找最低版本的 Chromedriver （低于73）能支持的最低版本浏览器，获取 Chrome 的源码，查看发布提交并且检查在此文件下 `src/chrome/test/chromedriver/chrome/version.cc` 的 `kMinimumSupportedChromeVersion` 变量。（为了找到release commites ，你可以使用命令 `git log --pretty=format:'%h | %s%d' | grep -i "Release Chromedriver version"`）

可用的Chromedriver版本和发行说明的完整列表在[此处](https://chromedriver.storage.googleapis.com/index.html)。

### 能自动发现兼容性问题的Chromedriver

早在1.8.0版本的Appium开始，Appium 就能够自主选择一个正确的 Chromedriver 版本进行测试。
```JSON
{
  "2.42": "63.0.3239",
  "2.41": "62.0.3202"
}
```
从 Appium 1.15.0开始，可以从 Google 官方存储中自动将必要的 chromedriver 下载到 chromedriverExecutableDir 中。脚本将会自动下载支持给定的浏览器/网页视图的最新版本的 chromedriver，在下载（对于下载的文件会通过哈希和进行验证）的同时并且添加到 `chromedriverChromeMappingFile` 映射中。你所需要做的事情就是在启动服务时确保 `chromedriver_autodownload` 是开启的 (如 `appium --allow-insecure chromedriver_autodownload`)。
您还可以检查[安全](https://github.com/appium/appium/blob/master/docs/en/writing-running-appium/security.md) 文档以获得关于如何控制潜在不安全的服务器特性的详细信息。


### 安装遇到的网络问题

Appium 安装的时候需要下载 Chromedriver，所以经常会遇到网络问题，尤其在有长城防火墙的中国。

Chromedriver 默认是从 `https://chromedriver.storage.googleapis.com/` 下载。如果要使用镜像的话，需要配置npm的参数 `chromedriver_cdnurl`。

```bash
npm install appium-chromedriver --chromedriver_cdnurl=http://npm.taobao.org/mirrors/chromedriver
```

或者把这个参数加到你的 [`.npmrc`](https://docs.npmjs.com/files/npmrc) 文件中去.

```bash
chromedriver_cdnurl=http://npm.taobao.org/mirrors/chromedriver
```

也可以使用环境变量 `CHROMEDRIVER_CDNURL`.

```bash
CHROMEDRIVER_CDNURL=http://npm.taobao.org/mirrors/chromedriver npm install appium-chromedriver
```

当然最好开着代理或者使用vpn进行下载。

### W3C 支持

Chromedriver直到第75版才遵循 W3C 标准。当你遇到类似[这样](https://github.com/appium/python-client/issues/234) 的代理命令错误时，请升级你的 Chromedriver 版本。
旧的 Android 设备不能使用较新的 Chrome 驱动程序。通过使用 the Mobile JSON Wire Protocol 来避免执行测试时的错误。
尽管主要版本的 *75* W3C 模式可以根据传递的会话功能切换为 JSONWP 模式，但它仍然是 Chromedriver 的默认模式，您可以从[下载文件](https://sites.google.com/a/chromium.org/chromedriver/downloads) 中阅读 Chromedriver 中的关于W3C支持的历史记录。

本文由 [lihuazhang](https://github.com/lihuazhang) 翻译，由 [lihuazhang](https://github.com/lihuazhang) 校验。
