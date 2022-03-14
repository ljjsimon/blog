---
title: 用jmeter压测
date: 2021-05-18 16:25:47
tags: 运维
id: 1621326369
---
# jMeter 操作
1. 使用 jmeter 要先安装 JDK，安装好后打开软件。可以在菜单栏 Options, Choose Language 里选择中文。
2. 现在左边只有一个 Test Plan，把名称改为「ljj测试」。
3. 然后在「ljj测试」右击，添加，线程，线程组，把名称改为「模拟用户请求」，线程数，时间，循环次数都用默认的。
4. 在「模拟用户请求」右击，添加，取样器，「HTTP请求」，然后修改相应的服务器地址，请求类型，参数等
5. 如果要修改 HTTP header 和 cookie，则在「模拟用户请求」右击，添加，配置元件，「HTTP信息头管理器」和「HTTP COOKIE 管理器」
6. 在「模拟用户请求」右击，添加，监听器，「查看结果树」和「聚合报告」。如果监听器加在「HTTP请求」下面，则只显示这个请求的信息
7. 现在可以点击上面绿色的运行，然后在「查看结果树」里查看请求是否正确返回，然后在「聚合报告」里查看吞吐量信息

# 报告解读
- 样本：总测试数量
- 平均值：单个请求的平均响应时间
- 中位数：50%用户的响应时间（qps）
- 90%百分位：90%用户的响应时间
- 95%百分位：95%用户的响应时间
- 99%百分位：99%用户的响应时间
- 最小值：最小的响应时间
- 最大值：最大的响应时间
- 异常%：请求异常所占百分比
- 吞吐量：每秒完成的请求数，吞吐量=请求数/总时间
- 接受：每秒从服务器段接受到的数据量
- 发送：每秒从客户段发送的请求数量

还可以用下面公式计算吞吐量
吞吐量 = ( 1000 / 响应时间 ms) × 并发数

# Spinach
Spinach 是开源的测试平台。
1. 创建项目
2. 创建用例，上传刚才保存的 JMX 文件，点击确定保存
3. 先点击运行，总线程数1，1分钟进行压测
4. 等完成后再点击运行，总线程数2，1分钟进行压测

可以看到线程数增加一倍后 tps 增加了也增加了一倍，平均响应时间增加了一点，但是最高响应时间增加了非常多。

# 调用javascript
新建 test.js 文件，写入
```
function testJS(input1, input2) {
    return input1 * input2
}
```

1. 在线程组右击添加 Debug Sampler
2. 在线程组右击添加 JSR223 PreProcessor, Language 选择 javascript
在 Script 写入
```
load("test.js")

var jm = testJS(${value1}, ${value2})
vars.put("key",jm)
```
这里的 ${value1}, ${value2} 定义在最外层的「ljj测试」，key 定义在「HTTP请求」中的 ${key}

## 内置函数
1. log：用来记录日志文件，写入到jmeber.log文件，使用方法：
    1. log.info(“Test ABC!”)；
2. vars：操作jmeter变量，提供读取/写入访问变量的方法，常用方法：
    1. vars.get(String key)：从jmeter中获得变量值；
    2. vars.put(String key，String value)：数据存到jmeter变量中；
    3. vars.putObject(“OBJ1”,new Object())；

# 完整内容
```
<?xml version="1.0" encoding="UTF-8"?>
<jmeterTestPlan version="1.2" properties="5.0" jmeter="5.4.3">
  <hashTree>
    <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="ljj测试" enabled="true">
      <stringProp name="TestPlan.comments"></stringProp>
      <boolProp name="TestPlan.functional_mode">false</boolProp>
      <boolProp name="TestPlan.tearDown_on_shutdown">true</boolProp>
      <boolProp name="TestPlan.serialize_threadgroups">false</boolProp>
      <elementProp name="TestPlan.user_defined_variables" elementType="Arguments" guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
        <collectionProp name="Arguments.arguments">
          <elementProp name="value1" elementType="Argument">
            <stringProp name="Argument.name">value1</stringProp>
            <stringProp name="Argument.value">2</stringProp>
            <stringProp name="Argument.metadata">=</stringProp>
          </elementProp>
          <elementProp name="value2" elementType="Argument">
            <stringProp name="Argument.name">value2</stringProp>
            <stringProp name="Argument.value">3</stringProp>
            <stringProp name="Argument.metadata">=</stringProp>
          </elementProp>
        </collectionProp>
      </elementProp>
      <stringProp name="TestPlan.user_define_classpath"></stringProp>
    </TestPlan>
    <hashTree>
      <ThreadGroup guiclass="ThreadGroupGui" testclass="ThreadGroup" testname="模拟用户请求" enabled="true">
        <stringProp name="ThreadGroup.on_sample_error">continue</stringProp>
        <elementProp name="ThreadGroup.main_controller" elementType="LoopController" guiclass="LoopControlPanel" testclass="LoopController" testname="Loop Controller" enabled="true">
          <boolProp name="LoopController.continue_forever">false</boolProp>
          <stringProp name="LoopController.loops">1</stringProp>
        </elementProp>
        <stringProp name="ThreadGroup.num_threads">1</stringProp>
        <stringProp name="ThreadGroup.ramp_time">1</stringProp>
        <boolProp name="ThreadGroup.scheduler">false</boolProp>
        <stringProp name="ThreadGroup.duration"></stringProp>
        <stringProp name="ThreadGroup.delay"></stringProp>
        <boolProp name="ThreadGroup.same_user_on_next_iteration">true</boolProp>
      </ThreadGroup>
      <hashTree>
        <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="HTTP Request" enabled="true">
          <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
            <collectionProp name="Arguments.arguments">
              <elementProp name="key" elementType="HTTPArgument">
                <boolProp name="HTTPArgument.always_encode">false</boolProp>
                <stringProp name="Argument.value">${key}</stringProp>
                <stringProp name="Argument.metadata">=</stringProp>
                <boolProp name="HTTPArgument.use_equals">true</boolProp>
                <stringProp name="Argument.name">key</stringProp>
              </elementProp>
            </collectionProp>
          </elementProp>
          <stringProp name="HTTPSampler.domain">www.baidu.com</stringProp>
          <stringProp name="HTTPSampler.port"></stringProp>
          <stringProp name="HTTPSampler.protocol">http</stringProp>
          <stringProp name="HTTPSampler.contentEncoding"></stringProp>
          <stringProp name="HTTPSampler.path"></stringProp>
          <stringProp name="HTTPSampler.method">GET</stringProp>
          <boolProp name="HTTPSampler.follow_redirects">true</boolProp>
          <boolProp name="HTTPSampler.auto_redirects">false</boolProp>
          <boolProp name="HTTPSampler.use_keepalive">true</boolProp>
          <boolProp name="HTTPSampler.DO_MULTIPART_POST">false</boolProp>
          <stringProp name="HTTPSampler.embedded_url_re"></stringProp>
          <stringProp name="HTTPSampler.connect_timeout"></stringProp>
          <stringProp name="HTTPSampler.response_timeout"></stringProp>
        </HTTPSamplerProxy>
        <hashTree/>
        <DebugSampler guiclass="TestBeanGUI" testclass="DebugSampler" testname="调试取样器" enabled="true">
          <boolProp name="displayJMeterProperties">false</boolProp>
          <boolProp name="displayJMeterVariables">true</boolProp>
          <boolProp name="displaySystemProperties">false</boolProp>
        </DebugSampler>
        <hashTree/>
        <ResultCollector guiclass="ViewResultsFullVisualizer" testclass="ResultCollector" testname="察看结果树" enabled="true">
          <boolProp name="ResultCollector.error_logging">false</boolProp>
          <objProp>
            <name>saveConfig</name>
            <value class="SampleSaveConfiguration">
              <time>true</time>
              <latency>true</latency>
              <timestamp>true</timestamp>
              <success>true</success>
              <label>true</label>
              <code>true</code>
              <message>true</message>
              <threadName>true</threadName>
              <dataType>true</dataType>
              <encoding>false</encoding>
              <assertions>true</assertions>
              <subresults>true</subresults>
              <responseData>false</responseData>
              <samplerData>false</samplerData>
              <xml>false</xml>
              <fieldNames>true</fieldNames>
              <responseHeaders>false</responseHeaders>
              <requestHeaders>false</requestHeaders>
              <responseDataOnError>false</responseDataOnError>
              <saveAssertionResultsFailureMessage>true</saveAssertionResultsFailureMessage>
              <assertionsResultsToSave>0</assertionsResultsToSave>
              <bytes>true</bytes>
              <sentBytes>true</sentBytes>
              <url>true</url>
              <threadCounts>true</threadCounts>
              <idleTime>true</idleTime>
              <connectTime>true</connectTime>
            </value>
          </objProp>
          <stringProp name="filename"></stringProp>
        </ResultCollector>
        <hashTree/>
        <ResultCollector guiclass="StatVisualizer" testclass="ResultCollector" testname="聚合报告" enabled="true">
          <boolProp name="ResultCollector.error_logging">false</boolProp>
          <objProp>
            <name>saveConfig</name>
            <value class="SampleSaveConfiguration">
              <time>true</time>
              <latency>true</latency>
              <timestamp>true</timestamp>
              <success>true</success>
              <label>true</label>
              <code>true</code>
              <message>true</message>
              <threadName>true</threadName>
              <dataType>true</dataType>
              <encoding>false</encoding>
              <assertions>true</assertions>
              <subresults>true</subresults>
              <responseData>false</responseData>
              <samplerData>false</samplerData>
              <xml>false</xml>
              <fieldNames>true</fieldNames>
              <responseHeaders>false</responseHeaders>
              <requestHeaders>false</requestHeaders>
              <responseDataOnError>false</responseDataOnError>
              <saveAssertionResultsFailureMessage>true</saveAssertionResultsFailureMessage>
              <assertionsResultsToSave>0</assertionsResultsToSave>
              <bytes>true</bytes>
              <sentBytes>true</sentBytes>
              <url>true</url>
              <threadCounts>true</threadCounts>
              <idleTime>true</idleTime>
              <connectTime>true</connectTime>
            </value>
          </objProp>
          <stringProp name="filename"></stringProp>
        </ResultCollector>
        <hashTree/>
        <JSR223PreProcessor guiclass="TestBeanGUI" testclass="JSR223PreProcessor" testname="JSR223 预处理程序" enabled="true">
          <stringProp name="cacheKey">true</stringProp>
          <stringProp name="filename"></stringProp>
          <stringProp name="parameters"></stringProp>
          <stringProp name="script">load(&quot;test.js&quot;)

var jm = testJS(${value1}, ${value2})
vars.put(&quot;key&quot;,jm)</stringProp>
          <stringProp name="scriptLanguage">javascript</stringProp>
        </JSR223PreProcessor>
        <hashTree/>
      </hashTree>
    </hashTree>
  </hashTree>
</jmeterTestPlan>
```
-------------------------
参考  
https://www.cnblogs.com/fnng/p/5827577.html  
https://www.cnblogs.com/imyalost/p/5916625.html  
https://cloud.tencent.com/developer/news/721609  
https://blog.csdn.net/weixin_38648597/article/details/95066326