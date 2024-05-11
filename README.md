# FreeSwitch Mod_Asr 模块

**很多人都对FreeSWITCH和ASR对接比较感谢兴趣，这里提供阿里云Asr mod模块直接对接阿里Asr识别模型，大家可以基于此模块实现检测早期媒体音,实现手机号空号识别+各种前期手机号异常状态功能**

mod_asr.so是模块主程序，该模块需要授权，授权价格3K，模块一次性永久授权，包售后。如有其他定制化模块需求，请加微信或进入https://www.callwg.com/ 了解更多详情。

![](https://www.callwg.com/templets/callgw/images/ma.png)

## `mod_asr模块介绍`

此模块由合肥标通科技有限公司开发，基于FreeSwitch1.10.9版本开发，使用C++11原生编写。本模块基于阿里云对接实现实时Asr识别需求，经大量生产高并发测试非常稳定，特别适合需要检测早期媒体音判断是否是空号等场景,如果您是做外呼系统的相关科技公司可以直接采购本模块,节省大量的踩坑成本。本公司有成熟的callwg语音呼叫系统，欢迎各位老板前来采购。


## `mod_asr模块使用教程`
1.首先下载mod_asr.so文件到FreeSwitch运行目录,正常目录是/usr/local/freeswitch/mod/。
2.复制aliasr.xml文件到/usr/local/freeswitch/conf/autoload_configs/目录下，并修改相关参数。

```
<configuration name="aliasr.conf" description="mod_asr configuration">
    <settings>
	    <!-- 阿里云Asr相关key参数 -->
        <param name="appkey" value=""/>
        <param name="akid" value=""/>
        <param name="aksecret" value=""/>
		<!-- 推送asr识别结果到接口 -->
        <param name="pushUrl" value=""/>
    </settings>
</configuration>
```

3.修改/usr/local/freeswitch/conf/autoload_configs/modules.conf.xml 增加一行<load module="mod_asr"/>,重启fs系统生效模块。

4.模块使用命令如下:

```
originate {origination_caller_id_name=955555,origination_caller_id_number=955555,absolute_codec_string=^^:PCMU:PCMA,leg_timeout=30,ignore_early_media=false}user/4000 'start_asr,playback:/opt/record/2023-03-29/start.wav' inline
```

命令解释：使用955555号码呼叫分机4000并同时开启start_asr模块,等4000分机接通后播放start.wav音乐。

## `mod_asr模块事件结果`

1.上面的命令执行成功以后，会不断地生成Asr识别的结果，分别通过Post json到接口或Esl事件方式通知。

Esl事件如下：

​		update_asr中间识别结果事件

```
Event-Subclass: update_asr
Event-Name: CUSTOM
Unique-ID: 58a08a69-7858-407a-be69-679150d34193
FreeSWITCH-Hostname: MiWiFi-R3D-srv
FreeSWITCH-Switchname: MiWiFi-R3D-srv
FreeSWITCH-IPv4: 192.168.31.164
FreeSWITCH-IPv6: ::1
Event-Date-Local: 2017-12-10 11:30:32
Event-Date-GMT: Sun, 10 Dec 2017 03:30:32 GMT
Event-Date-Timestamp: 1512876632835590
Event-Calling-File: mod_asr.cpp
Event-Calling-Function: OnResultDataRecved
Event-Calling-Line-Number: 55
Event-Sequence: 914
ASR-Response: {"is_final":false,"mode":"2pass-online","text":"的都","wav_name":"asr"}
Channel: sofia/external/linphone@192.168.31.210
```

​		stop_asr识别结束事件 建议使用这个事件作为最终识别结果

​		

```
Event-Subclass: stop_asr
Event-Name: CUSTOM
Unique-ID: 58a08a69-7858-407a-be69-679150d34193
FreeSWITCH-Hostname: MiWiFi-R3D-srv
FreeSWITCH-Switchname: MiWiFi-R3D-srv
FreeSWITCH-IPv4: 192.168.31.164
FreeSWITCH-IPv6: ::1
Event-Date-Local: 2017-12-10 11:30:32
Event-Date-GMT: Sun, 10 Dec 2017 03:30:32 GMT
Event-Date-Timestamp: 1512876632835590
Event-Calling-File: mod_asr.cpp
Event-Calling-Function: OnResultDataRecved
Event-Calling-Line-Number: 55
Event-Sequence: 914
ASR-Response: {"is_final":false,"mode":"2pass-offline","stamp_sents":[{"end":3875,"punc":"","start":2570,"text_seg":"都 怎 么 可 能 是","ts_list":[[2570,2770],[2770,3050],[3050,3290],[3290,3510],[3510,3670],[3670,3875]]}],"text":"都怎么可能是","timestamp":"[[2570,2770],[2770,3050],[3050,3290],[3290,3510],[3510,3670],[3670,3875]]","wav_name":"asr"}
Channel: sofia/external/linphone@192.168.31.210
```

​	 post json格式：

​	

```
{"call_info":{"call_id": "c8379b9a-2c21-41c7-9a65-3636a29e2fbe","caller": "955555","callee": "4000"},"asr_result": "安"}
```

​	
