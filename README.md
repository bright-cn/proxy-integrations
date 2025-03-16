# 代理集成

如果你和大多数网络开发者和爬虫专家一样，你可能已经在某些项目中使用过 <b>Selenium</b> 或类似的浏览器自动化工具。它们是非常强大的测试和网页交互工具，但在大多数情况下，为了在执行某些类型的任务时避免被封锁，往往需要配置合适的 <b>代理 IP 集成</b>。

目前有两种代理集成方式：第一种是直接与 [Bright Data 超级代理](https://www.bright.cn/proxy-types/proxy-servers) 集成，第二种是通过 Bright Data 的 [代理管理器](https://github.com/bright-cn/luminati-proxy) 进行集成。

![Bright Data Proxy Manager](https://github.com/bright-cn/proxy-integrations/blob/main/Proxy%20Manager.png)

<h2>Selenium 代理集成</h2>

[Selenium](https://github.com/SeleniumHQ/selenium) WebDriver 是一款在 Python 开发者中非常流行的浏览器自动化工具，可用于创建逼真的浏览场景来进行精准的网站测试和模拟真实用户行为的网页爬虫。

<h3>将 Selenium 与 Bright Data 超级代理集成，请按照以下步骤操作：</h3>

* 首先，进入 Bright Data [控制面板](https://www.bright.cn/cp/zones)，点击 “<b>add zone</b>”。
* 选择所需的网络类型（如 Datacenter、ISP、Residential、Mobile 等），然后再次点击 “<b>add zone</b>”。
* 在 Selenium WebDriver 中，将 ```Proxy IP:Port```（例如 ```zproxy.lum-superproxy.io:22225```）填入 ```setProxy``` 函数中的 HTTP 和 HTTPS。
* 在 ```sendKeys``` 中输入你的 Bright Data 账户 ID 以及代理 Zone 名称（如 ```lum-customer-CUSTOMER-zone-YOURZONE```）和在该 Zone 设置中找到的密码。
* 下面是示例代码展示了如何编写：

```s
const {Builder, By, Key, until} = require('selenium-webdriver');
const proxy = require('selenium-webdriver/proxy');

(async function example(){
  let driver = await new Builder().forBrowser('firefox').setProxy(proxy.manual({
    http: 'zproxy.lum-superproxy.io:22225',
    https: 'zproxy.lum-superproxy.io:22225'
  })).build()

  try {
    await driver.get('http://lumtest.com/myip.json');
    driver.switchTo().alert()
      .sendKeys('lum-customer-USERNAME-zone-YOURZONE'+Key.TAB+'PASSWORD');
    driver.switchTo().alert().accept();
  } finally {
      await driver.quit();
  }
})();
```

<h3>将 Selenium 与 Bright Data 代理管理器集成，请按照以下步骤操作：</h3>

* 创建一个代理 Zone，选择需要的网络、IP 类型以及 IP 数量。
* 在本地主机上 [安装](https://www.bright.cn/products/proxy-manager) Bright Data 代理管理器，或者在 Bright Data 控制面板上云端访问它。
* 点击 <b>“add new proxy”</b>，选择相应 Zone 并进行所需配置，然后点击 <b>“save”</b>。
* 返回到 Selenium WebDriver 中，将本地主机 IP 以及代理管理器端口（如 127.0.0.1:24000）写入 `setProxy`。
* 本地主机 IP 为 127.0.0.1。
* 在代理管理器中默认创建的端口格式为 24XXX，例如 24000。
* 无需在字段中输入用户名和密码——Bright Data 代理管理器已经和超级代理服务器完成了身份验证。
* 以下示例代码展示了如何编写你的 Selenium 代码：

```s
const {Builder, By, Key, until} = require('selenium-webdriver');
const proxy = require('selenium-webdriver/proxy');

(async function example(){
    let driver = await new Builder().forBrowser('firefox').setProxy(proxy.manual({
        http: '127.0.0.1:24000',
        https: '127.0.0.1:24000'
    })).build()

    try {
        await driver.get('http://lumtest.com/myip.json');
        driver.switchTo().alert().accept();
    } finally {
        await driver.quit();
    }
})();
```

开始使用 <b>[Selenium 代理集成](https://www.bright.cn/integration/selenium)</b>。

<h2>Puppeteer 代理集成</h2>

Puppeteer 是一个 Node 库，可用其高级 API 操控 Chrome 和 Chromium 浏览器（支持无头与非无头模式）。尽管它最初并不是专为测试平台而设计，但它已经成为 JavaScript 用户在浏览器自动化领域中与 Selenium 并驾齐驱的重要选择，并且还提供一些额外的 [stealth 插件](https://github.com/berstend/puppeteer-extra)。

<h3>将 Puppeteer 与 Bright Data 超级代理集成，请按照以下步骤操作：</h3>

* 首先，访问你的 Bright Data 控制面板并点击 <b>“add zone”</b>。
* 选择你需要的代理网络类型（如 Datacenter、ISP、Residential、Mobile 等），然后再次点击 <b>“add zone”</b>。
* 在 Puppeteer 中，将 ```Proxy IP:Port```（示例：```zproxy.lum-superproxy.io:22225```）填入 ```proxy-server``` 值中。
* 在 ```page.authenticate``` 中，将 Bright Data 账户 ID 和代理 Zone 名称写入 ```username```（如 ```lum-customer-CUSTOMER-zone-YOURZONE```），将代理 Zone 设置中的密码写入 ```password```。
* 下例演示了你的 Puppeteer 代码应如何编写：

```javascript
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    headless: false,
    args: ['--proxy-server=zproxy.lum-superproxy.io:22225']
  });
  const page = await browser.newPage();
    await page.authenticate({
        username: 'lum-customer-USERNAME-zone-YOURZONE',
        password: 'PASSWORD'
    });
    await page.goto('http://lumtest.com/myip.json');
    await page.screenshot({path: 'example.png'});
    await browser.close();
})();
```

<h3>将 Puppeteer 与 Bright Data 代理管理器集成，请按照以下步骤操作：</h3>

* 访问你的 Bright Data 控制面板并创建一个代理 Zone，选择需要的网络类型、IP 类型以及 IP 数量。
* 在本地主机上安装代理管理器，或者在 Bright Data 控制面板上云端访问它。
* 点击 <b>“add new proxy”</b>，选择对应的 Zone 和所需的设置，然后点击 <b>“save”</b>。
* 在 Puppeteer 中，将本地主机 IP 和代理管理器端口（如 127.0.0.1:24000）写入 `proxy-server`。
* 本地主机 IP 为 127.0.0.1。
* 在 Bright Data 代理管理器中默认创建的端口格式为 24XXX，例如 24000。
* 无需在字段中输入用户名和密码 — Bright Data 代理管理器已与超级代理服务器完成身份验证。
* 下面是示例代码，展示 Puppeteer 应如何编写：

```javascript
const puppeteer = require('puppeteer');

(async () => {
    const browser = await puppeteer.launch({
        headless: false,
        args: ['--proxy-server=127.0.0.1:24000']
    });
    const page = await browser.newPage();
    await page.authenticate();
    await page.goto('http://lumtest.com/myip.json');
    await page.screenshot({path: 'example.png'});
    await browser.close();
})();
```

开始使用 Bright Data <b>[Puppeteer 代理集成](https://www.bright.cn/integration/puppeteer)</b>。

<h2>Playwright 代理集成</h2>

Playwright 是一个 Node.js 库，使用同一个 API 来自动化 Chromium、Firefox 和 WebKit。下面是使用 Bright Data 进行 Playwright 代理集成的快速步骤。

<h3>将 Playwright 与 Bright Data 超级代理集成，请按照以下步骤操作：</h3>

* 首先，访问你的 Bright Data 控制面板并点击 <b>“add a zone”</b>。
* 选择你偏好的代理网络类型（如 Datacenter、ISP、Residential、Mobile 等），然后再次点击 <b>“add zone”</b>。
* 在 Playwright 中，将 ```Proxy IP:Port```（例如 ```http://zproxy.lum-superproxy.io:22225```）填入 ```server``` 的值。
* 在 ```username``` 中输入你的 Bright Data 账户 ID 及代理 Zone 名称（如 ```lum-customer-CUSTOMER-zone-YOURZONE```），在 ```password``` 中填写你在 Zone 设置中找到的密码。
* 以下示例展示了你的 Playwright 代码应如何编写：

```javascript
const playwright = require('playwright');

(async () => {
    for (const browserType of ['chromium', 'firefox', 'webkit']) {
        const browser = await playwright[browserType].launch({
            headless: false,
            proxy: {
                server: 'http://zproxy.lum-superproxy.io:22225',
                username: 'lum-customer-USERNAME-zone-YOURZONE',
                password: 'PASSWORD'
            },
        });
        const context = await browser.newContext();
        const page = await context.newPage();
        await page.goto('http://lumtest.com/myip.json');
        await page.screenshot({ path: 'example.png' });
        await browser.close();
    }
})();
```

<h3>将 Playwright 与 Bright Data 代理管理器集成，请按照以下步骤操作：</h3>

* 进入 Bright Data 控制面板并创建一个 Zone，选择所需的代理网络类型、IP 类型，以及 IP 数量。
* 在本地主机上安装代理管理器，或者通过 Bright Data 控制面板在云端使用它。
* 点击 <b>“add new proxy”</b>，选择所需的 Zone 和相关设置，然后点击 <b>“save”</b>。
* 在 Playwright 中，将本地主机 IP 和 Bright Data 代理管理器端口（如 127.0.0.1:24000）填入 `server`。
* 本地主机 IP 为 127.0.0.1。
* 在 Bright Data 代理管理器中默认创建的端口格式为 24XXX，例如 24000。
* 无需在字段中输入用户名和密码 — Bright Data 代理管理器已与超级代理服务器完成身份验证。
* 以下是示例代码展示 Playwright 应如何编写：

```javascript
const playwright = require('playwright');

(async () => {
    for (const browserType of ['chromium', 'firefox', 'webkit']) {
        const browser = await playwright[browserType].launch({
            headless: false,
            proxy: {
                server: '127.0.0.1:24000',
                username: '',
                password: ''
            },
        });
        const context = await browser.newContext();
        const page = await context.newPage();
        await page.goto('http://lumtest.com/myip.json');
        await page.screenshot({ path: 'example.png' });
        await browser.close();
    }
})();
```

开始使用 Bright Data <b>[Playwright 代理集成](https://www.bright.cn/integration/playwright)</b>。

<h2>其他实用的 Bright Data 代理集成</h2>

* PhantomBuster - 可以在 [YouTube](https://youtu.be/Tw68CHXs_jE) 上观看相关代理集成教程视频
* Apify
* SessionBox
* VMLogin
* AdsPower

请访问 Bright Data [代理集成中心](https://www.bright.cn/integration) 了解更多最新信息。
