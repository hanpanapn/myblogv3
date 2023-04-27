#### 环境

硬件：树莓派

软件：点灯科技的NodejsSDK

#### 实现功能

以前用esp8266时情况是这样：

IDE中编写程序，然后烧录到esp8266,通电后esp8266里的程序通过连接点灯科技服务器使设备在线，这时手机app Blinker中的设备就在线，并可以通过app中的按钮发送数据到esp8266,从而控制esp8266的针脚

现在用树莓派也类似：

喜欢的IDE（推荐vscode）中编写程序，然后以服务的方式运行，这时树莓派中的node服务会连接点灯科技的服务器，手机app Blinker中的设备上线，通过app中的按钮发送数据到树莓派,从而控制树莓派的针脚

#### 接下来开发程序了

项目目录

```
---src  程序代码
 |--main.js 主程序
---lib  NodeJsSDK
---tsconfig.json ts配置，不可删除
---readme.md 帮助文档
---package.json npm包
---pm2.yml  服务启动配置
```

基本demo

```
import { BlinkerDevice } from '../lib/blinker';
import { ButtonWidget, NumberWidget } from '../lib/widget';

let device = new BlinkerDevice('秘钥');

// 注册组件
//在手机端app中的组件类型和名称要和这里的一致
let button1: ButtonWidget = device.addWidget(new ButtonWidget('btn-123'));
let button2: ButtonWidget = device.addWidget(new ButtonWidget('btn-abc'));
let number1: NumberWidget = device.addWidget(new NumberWidget('num-abc'));

let num = 0;

device.ready().then(() => {

    device.dataRead.subscribe(message => {
        console.log('-----start');
        console.log('otherData:', message);
        console.log('-----end');
    })

    button1.listen().subscribe(message => {
        console.log('-----start');
        console.log('button1:', message.data);
        console.log('-----end');
        num++;
        number1.value(num).update();
    })

    button2.listen().subscribe(message => {
        console.log('-----start');
        console.log('button2:', message.data);
        console.log('-----end');
        // 其他控制代码
    })

})

```

### 开发环境运行

```npm run dev```

### 生产环境运行

```pm2 start pm2.yml```

