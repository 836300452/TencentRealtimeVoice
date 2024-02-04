# 简介

腾讯实时语音 js

# 浏览器兼容性问题

1、sdk 内置录音采用 MediaDevices 接口，不兼容 ie 浏览器、夸克浏览器和 uc 浏览器，其他浏览器兼容性具体文档可参考 https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices  
浏览器使用录音时，有版本要求 ios14.3+，6.5+（微信版本）；

# 依赖环境

1. 使用相关产品前需要在腾讯云控制台已开通相关语音产品。
2. 在腾讯云控制台[访问管理](https://console.cloud.tencent.com/cam/capi)页面获取 SecretID 和 SecretKey 。
3. 在腾讯云控制台[账号信息](https://console.cloud.tencent.com/developer)页面获取 AppId。
4. 将获取的参数填入 examples/config.js 中

注意：  
 1）SecretID 和 SecretKey 作为敏感信息，不建议直接放在前端代码里运行，可以通过接口服务获取，同时建议采取临时密钥方案，具体可参考 [临时身份凭证](https://cloud.tencent.com/document/product/1312/48195)  
 2）将获取到的 tmpSecretId、tmpSecretkey 和 Token 依次作为参数 secretid、secretkey 和 token 传入

# sdk 调用方式

1. 参考 demo 引入 examples/config.js、examples/asrauthentication.js 和 examples/speechrecognizer.js，需设置用户的 SecretID，SecretKey 和 AppId。
2. 简单版（使用内置录音）

```javascript
const webAudioSpeechRecognizer = new WebAudioSpeechRecognizer();
```

WebAudioSpeechRecognizer 类，提供 start()、stop()和获取识别结果事件，具体调用参考示例以及 examples 3. 进阶版（自定义数据源）

```javascript
const speechRecognizer = new SpeechRecognizer();
```

SpeechRecognizer 类，提供 start()、stop()、write()方法和获取识别结果事件，具体调用参考示例以及 examples
除此之外，sdk 也提供了 WebRecorder 类，可以获取采集到的浏览器数据（目前内置音频只能获取到 16KHz 的音频）。  
WebRecorder 类 提供 start()、stop()方法、OnReceivedData()和 OnError()事件。

# 参数特别说明

1. 传参说明  
   因为内置 WebRecorder 采样 16k 的数据，所以参数 engine_model_type 需要选择 16k 的引擎，为 '16k_zh'，其他参数具体可见官网 api 文档 https://cloud.tencent.com/document/product/1093/48982
2. examples/asrauthentication.js 为鉴权函数，如需自己鉴权，直接覆盖就可

# 详细说明及示例

示例写法采用 es6 写法，若要兼容低版本浏览器，需要按照 ES5 语法书写。

1. WebRecorder 类，采集浏览器音频数据

```javascript
    // 实例化类
const recorder = new WebRecorder();

// 获取采集到的音频数据
recorder.OnReceivedData = (res) => {
   console.log(res);
};
// 采集音频出错
recorder.OnError() = (err) => {
   console.log(err)
}

// 开始录音
recorder.start();

// 结束录音
recorder.stop();
```

2. SpeechRecognizer 类

```javascript
    // 实例化类
const speechRecognizer = new SpeechRecognizer(params);

if (// 可以开始识别了) { // 此处需要判断是否建立连接成功，可在 OnRecognitionStart 回调中加标识判断
        // 发送数据 (此过程应该是一个连续的过程)
        speechRecognizer.write(data);
}
// 开始识别(此时连接已经建立)
speechRecognizer.OnRecognitionStart = (res) => {
   console.log('开始识别', res)
}
// 一句话开始
speechRecognizer.OnSentenceBegin = (res) => {
   console.log('一句话开始', res)
}
// 识别变化时
speechRecognizer.OnRecognitionResultChange = (res) => {
   console.log('识别变化时', res)
}
// 一句话结束
speechRecognizer.OnSentenceEnd = (res) => {
   console.log('一句话结束', res)
}
// 识别结束
speechRecognizer.OnRecognitionComplete = (res) => {
   console.log('识别结束', res)
}
// 识别错误
speechRecognizer.OnError = (res) => {
   console.log('识别失败', res)
}

// 建立websocket连接
speechRecognizer.start();

// 断开连接
if (连接已经建立...) {
   speechRecognizer.stop();
}
```

3. WebAudioSpeechRecognizer 类

```javascript
    // 实例化类
const webAudioSpeechRecognizer = new WebAudioSpeechRecognizer(params);

// 开始识别(此时连接已经建立)
webAudioSpeechRecognizer.OnRecognitionStart = (res) => {
   console.log('开始识别', res)
}
// 一句话开始
webAudioSpeechRecognizer.OnSentenceBegin = (res) => {
   console.log('一句话开始', res)
}
// 识别变化时
webAudioSpeechRecognizer.OnRecognitionResultChange = (res) => {
   console.log('识别变化时', res)
}
// 一句话结束
webAudioSpeechRecognizer.OnSentenceEnd = (res) => {
   console.log('一句话结束', res)
}
// 识别结束
webAudioSpeechRecognizer.OnRecognitionComplete = (res) => {
   console.log('识别结束', res)
}
// 识别错误
webAudioSpeechRecognizer.OnError = (res) => {
   console.log('识别失败', res)
}

// 建立录音同时建立websocket连接
webAudioSpeechRecognizer.start();

// 断开连接
if (连接已经建立...) {
   webAudioSpeechRecognizer.stop();
}
```
# TencentRealtimeVoice
