# aujc

## 20250327

提供自己训练模型，优化点选验证码通过率，aujc_trainer项目：[https://github.com/icepage/aujc_trainer](https://github.com/icepage/aujc_trainer)

训练完后有onnx和charsets.json文件，docker启动可以通过挂载方式使用

```python
docker run -i \
    -v $PWD/config.py:/app/config.py \
    -v $PWD/myocr_v1.onnx:/app/myocr_v1.onnx \
    -v $PWD/charsets.json:/app/charsets.json \
    icepage/aujc:latest python main.py
```

## 介绍
- 用来自动化更新青龙面板的失效JD_COOKIE, 主要有三步
    - 自动检测并获取青龙面板的失效JD_COOKIE;
    - 拿到失效JD_COOKIE内容后, 根据配置的账号信息, 自动化登录JD页面, 拿到key;
    - 根据拿到的key, 自动化更新青龙面板的失效JD_COOKIE。
- 支持的验证码类型有：
  - 滑块验证码;
  - 形状颜色验证码(基本不会出现了);
  - 点选验证码;
  - 短信验证码,支持手动输入和webhook(首次登录大概率出现, 其它时间出现频率低。webhook配置流程繁琐, 不爱折腾的建议使用手动输入或关闭。)
  - 手机语音识别验证码
- 支持的账号类型有：
  - 账号密码登录
  - QQ登录
- python >= 3.9 (playwright依赖的typing，在3.7和3.8会报错typing.NoReturn的BUG)
- 支持windows,linux(无GUI)
- 支持docker部署
- 支持代理
- linux无GUI使用文档请转向 [linux无GUI使用文档](https://github.com/icepage/AutoUpdateJdCookie/blob/main/README.linux.md)
- WINDOWS整体效果如下图

![GIF](./img/main.gif)


## 使用文档
## 1、docker部署(推荐)

### 下载镜像
```shell
docker pull icepage/aujc:latest
```

### 生成config.py
```python
# 新建一个config.py
touch config.py
# 执行生成make_config.py, 记得最后要按y覆盖config.py
docker run -i --rm \
  -v $PWD/config.py:/app/config.py \
  icepage/aujc python make_config.py
```

说明：
- 执行make_config.py, 会生成config.py
- config.py的说明请转向 [配置文件说明](https://github.com/icepage/AutoUpdateJdCookie/blob/main/配置文件说明.md)
- Linux的**无头模式(headless)一定要设为True!!!!**
- 如果不会python的，参考config_example.py, 自己配置一个config.py, 我们基于这个config.py运行程序;

### 手动执行
- 2种场景下需要手动
  - 1、需要短信验证时需要手动, 本应用在新设备首次更新时必现. 
  - 2、定时时间外需要执行脚本. 
- 配置中的sms_func设为manual_input时, 才能在终端填入短信验证码。
- 当需要手动输入验证码时, docker运行需加-i参数。否则在触发短信验证码时会报错Operation not permitted
```bash
docker run -i -v $PWD/config.py:/app/config.py icepage/aujc:latest python main.py
```

![PNG](./img/linux.png)

### 长期运行
- 程序读config.py中的cron_expression, 定期进行更新任务
- 当sms_func设置为manual_input, 长期运行时会自动将manual_input转成no，避免滥发短信验证码, 因为没地方可填验证码. 
```bash
docker run -v $PWD/config.py:/app/config.py icepage/aujc:latest
```

## 2、本地部署
### 安装依赖
```commandline
pip install -r requirements.txt
```

### 安装chromium插件
```commandline
playwright install chromium
```

### 生成config.py
```python
python make_config.py
```

说明：
- 执行make_config.py, 会生成config.py
- config.py的说明请转向 [配置文件说明](https://github.com/icepage/AutoUpdateJdCookie/blob/main/配置文件说明.md)
- 如果不会python的，参考config_example.py, 自己配置一个config.py, 我们基于这个config.py运行程序;

### 运行脚本
#### 1、单次手动执行
```commandline
python main.py
```

#### 2、常驻进程
进程会读取config.py里的cron_expression,定期进行更新任务
```commandline
python schedule_main.py
```

## 特别感谢
- 感谢 [所有赞助本项目的热心网友 --> 打赏名单](https://github.com/icepage/AutoUpdateJdCookie/wiki/%E6%89%93%E8%B5%8F%E5%90%8D%E5%8D%95)
- 感谢 **https://github.com/sml2h3/ddddocr** 项目，牛逼项目
- 感谢 **https://github.com/zzhjj/svjdck** 项目，牛逼项目

## 创作不易，如果项目有帮助到你，大佬点个星或打个赏吧
![JPG](./img/w.jpg)