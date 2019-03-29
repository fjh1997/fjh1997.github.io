---
layout:     post                    # 使用的布局（不需要改）
title:      show you how to compile the c language plugin for wireshark 3.1 step by step  # 标题 
subtitle:    (windows platform 2019-3-20) #副标题
date:       2019-03-29              # 时间
author:     fjh1997                 # 作者
header-img: img/home-intro.jpg     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - wireshark
    - c plugin
---

# show you how to compile the c language plugin for wireshark 3.1 step by step (windows platform 2019-3-20)
>reference：
<br>https://www.wireshark.org/docs/wsdg_html_chunked/ChSetupWin32.html （official documentation）
<br>http://eucifyy.com/wireshark-dissector-mwe.html  （python test example）


## before the beginning
Wireshark is a famous network sniffing software. In the well-known CCTV 315 party this year, a professor  used wireshark to show us some apps illegally obtain user privacy. In wireshark, users can write their own plugins to make custom protocol dissector. There are two ways to made it, one is of c plugin, in the windows platform, embodied as a dynamic link library .dll file, placed in the wireshark plugin directory.The advantage of c plugin is fast, but the disadvantages are also obvious.For example, It should be compiled with a large amount of engineering, poor compatibility, plugins compiled under this version, can not be used under another version of wireshark. There is also a way of lua script, this is very convenient, compatibility is also good, the production is also simple, tutorials are also a lot, but I don't know why wireshark official does not recommend this. In short, In this post,I will show you how to compile the c plugin according to the official Wireshark tutorial.
## Step one, compile the wireshark source code once
This step is very tedious, but it is detailed in the official documentation.https://www.wireshark.org/docs/wsdg_html_chunked/ChSetupWin32.html。
Compiled following  the official documentation. Then you have successfully installed the environment and compiled a large number of target files. These target files will be used later.
## Step two, compile the c plugin
### 1. Create a source file
According to thishttps://www.wireshark.org/docs/wsdg_html_chunked/ChDissectAdd.html
Create the "wireshark\plugins\epan\foo directory" and create a file named packet-foo.c inside (this foo is your plugin name)

```c
#include "config.h"

#include <epan/packet.h>

#define FOO_PORT 1234

static int proto_foo = -1;

static int dissect_foo(tvbuff_t *tvb, packet_info *pinfo, proto_tree *tree _U_, void *data _U_)
{
    col_set_str(pinfo->cinfo, COL_PROTOCOL, "FOO");
    /* Clear out stuff in the info column */
    col_clear(pinfo->cinfo,COL_INFO);

    return tvb_captured_length(tvb);
}


void proto_register_foo(void)
{
    proto_foo = proto_register_protocol (
        "FOO Protocol", /* name       */
        "FOO",      /* short name */
        "foo"       /* abbrev     */
        );
}
void proto_reg_handoff_foo(void)
{
    static dissector_handle_t foo_handle;

    foo_handle = create_dissector_handle(dissect_foo, proto_foo);
    dissector_add_uint("udp.port", FOO_PORT, foo_handle);
}


```
### 2. Copy another plugin file as templates
Then copy the plugin.rc.in file in the wireshark\plugins directory into the foo directory, and the CMakeLists.txt in the wireshark\plugins\epan\gryphon directory should also copied into the foo directory.
### 3.Edit plugin information
Use Notepad to open CMakeLists.txt and replace the "gryphon" text inside with "foo".
### 4.Edit the custom plugin configuration
Go to the wireshark directory, rename the "CMakeListsCustom.txt.example" inside to "CMakeListsCustom.txt", open CMakeListsCustom.txt, and remove some "#" inside, so that a "set(CUSTOM_PLUGIN_SRC_DIR plugins/epan/foo)"  statement is complete, here plugins/epan/foo is the directory of your plugin.
```cmake
set(CUSTOM_PLUGIN_SRC_DIR
	plugins/epan/foo
)

# Do not fail CMake stage if any of the optional plugins are missing from source tree
set(_OPTIONAL_CUSTOM_PLUGIN_SRC_DIR
	plugins/epan/bar
)

foreach(  _plugin_dir ${_OPTIONAL_CUSTOM_PLUGIN_SRC_DIR} )
	if( EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${_plugin_dir}/CMakeLists.txt )
		list( APPEND CUSTOM_PLUGIN_SRC_DIR ${_plugin_dir} )
	else()
		message( WARNING "Custom plugins: No ${_plugin_dir}/CMakeLists.txt file found - ignoring" )
	endif()
endforeach()
```
## Step four, recompile the wireshark source code
This recompilation is much faster than the initial compilation. The reason is that the initial compilation generates enough target files.So, this compilation is just to compile your plugin.
After compiling, you will see foo.dll in your build directory run\RelWithDebInfo\plugins\3.1\epan, so that the compilation is successful.
![add picture description here]({{site.url}}/img/dir.jpg)
## Step five, start wireshark and test
Start wireshark.exe under run\RelWithDebInfo, use python to write a program based on foo protocol and test it. The script is as follows：
```python
# server.py
from socket import *

serverSocket = socket(AF_INET, SOCK_DGRAM) # UDP
serverSocket.bind(('', 1234))

while True:
	message, address = serverSocket.recvfrom(1024) # buffer size
	serverSocket.sendto('thanks', address)
```
```python
# client.py
import time
from socket import *

clientSocket = socket(AF_INET, SOCK_DGRAM)
clientSocket.settimeout(1)
message = 'test'
addr = ('127.0.0.1', 1234)

start = time.time()
clientSocket.sendto(message, addr)
try:
	data, server = clientSocket.recvfrom(1234)
	end = time.time()
	elapsed = end - start
	print '%s %d' % (data, elapsed)
except timeout:
	print 'REQUEST TIMED OUT'
```
Note that the above script must be run with python2. If you have already installed python3, you can install another python2 and add the following content to the bin directory: python2.bat：
```batch 
@echo off
%~dp0/python.exe %*
```
Add the bin directory to the environment variable path to execute python2 with the "python2" command.It should also be noted that wireshark compiled according to the default configuration does not support listening to the native loopback address of 127.0.0.1. You need to install an Npcap loopback adapter (https://nmap.org/download.html) to enable wireshark to listen to this address.
Open two terminals separately and execute the following commands.
```cmd
>python2 server.py
>python2 client.py
```
If the above command is successful, your client window will receive a "thanks" result, you can set udp.port==1234 through the filter to get the following sniffing results (for testing, I did not use the native loopback address, and I execute the server.py on another machine)：
![add picture description here]({{site.url}}/img/capture.jpg)
In this way, a wireshark c language plugin is initially compiled, and for a more detailed protocol dissector programming guide, you can check the official documentation.：https://www.wireshark.org/docs/wsdg_html_chunked/ChDissectAdd.html
