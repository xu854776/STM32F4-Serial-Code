**********
这是Ubuntu端的具体代码
**********
简介：本代码创建了一个名为communicate的包，通过使用场地名为robo-serial的节点，与虚拟串口进行通信。
**********
基本操作：
1、启动服务
先打开roscore，再输入rosrun communicate robo-serial来启动服务。
注意：如果没有启动串口的权限或者线缆没有连接，那么会产生“Unable to open port!Please check your setting!”的报错。
而如果没有启动串口的权限，你就需要使用你的浏览器了，但是注意，串口名称是“/dev/ttyACM0”。
2、修改代码
打开 /test-03/src/communicate/src ，所有的文件均在这个文件夹里。
robo-serial.cpp内包含了主程序以及自定义发送和接收的函数，结构体的初始化以及全局变量的定义均在这个文件
Serial.cpp内包含了打包控制数据以及解包反馈数据的具体实现，不过我认为这个实现还是相当粗糙的，还是需要继续打磨的。
相比于我参考的原型，我省略了一个数组赋值过程，因为我觉得暂时没有什么必要。
Serial.h内包含了结构体以及枚举的具体内容，可以在这里看到某些成员的值
备注：修改完记得重新编译一下，因为这个不是python文件。
3、其实你只需要调用Data_Receive()函数以及Data_Transmit()函数就可以实现收与发。
注意：
我没有做结构体的赋值函数或者之类的东西，结构体的具体赋值还是需要自己手动完成或者自己添加一个赋值的函数。
**********
重要的提醒：
1、控制底盘和控制云台的帧头是不同的，前者是0XFA，后者是0XFB，别写错了，要不然会有一端发错或者收错数据。
2、我感觉我写的帧尾暂时没什么用，因为我默认传输没有错误，所以没有做检验，但是我实际用起来还行，没有传错的数据。
可以等有实际的车之后再进行完善。
3、我给某些结构体预留了一些空位，如果你觉得还有值得发送的数据，你可以把Serial.cpp里面的打包函数中的赋值为0XAA的数组元素改成你想要的值，
我定义0XAA就是暂时无意义的。
**********
请遵守使用规则，不要外传该资料！
**********