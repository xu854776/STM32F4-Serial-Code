**********
这是STM32F407IGHX的具体代码
**********
简介：该工程利用USB-CDC来创建一个虚拟串口，然后利用结构体以及枚举等结构来规定了数据的具体形式，从而便于各种函数的封装。
**********
注意：该工程比较复杂，有些内容是直接在初始化好的工程上修改得来而并不是通过STM32CubeIDE来初始化的，所以不要轻易再次初始化，
否则很有可能丢失位于USB_DEVICE中App的usbd_cdc_if.c和usbd_cdc_if.h中修改过的发送/接收函数，从而产生不必要的报错。
**********
基本介绍（为免麻烦，我先不写函数的具体参数了，只写函数名称和括号）：
1、main.c文件
里面包含了结构体初始化以及具体发送/接收函数的使用。
注意事项：
1.如果你要创建一个新结构体变量，记得给它分配空间，不然无法正常使用该结构体变量。
2.很多自定义的函数所传入的是结构体指针或者数组指针，不要传入了错误的参数。
3.PH10,PH11,PH12的小灯已经初始化了，可以作为一个辅助调试的信号。

2、usbd_cdc_if.c
里面包含了热心网友重新编写的
CDC_Init_FS() , CDC_DeInit_FS() , CDC_Control_FS() , CDC_Receive_FS() , CDC_Transmit_FS() , CDC_TransmitCplt_FS()五个函数。
我暂时只写下我了解的部分
CDC_Receive_FS()是接收上位机数据的函数，你可以填入一个数组指针以及需要接收的数据长度(但是为了内部函数的正常工作，所以你要引用传入的长度的地址，如&Len)。
CDC_Transmit_FS()是向上位机发送数据的函数，填入一个数组指针以及需要接收的数据长度。
CDC_TransmitCplt_FS()是中断服务函数，可以让STM32中断来接收数据。

3、usbd_cdc_if.h
里面仅包含上述5个函数的定义。

4、Serial.c
里面包含CDC_SendFeed() , CDC_Receive() , Pack_Data() , UnPack_Data_Cha() , UnPack_Data_Pan()五个函数的具体实现
CDC_SendFeed() 是发送反馈数据的函数。
CDC_Receive() 是接收控制数据的函数，内部会通过帧头的不同来调用不同的解包函数，从而分别处理底盘和云台的控制数据。
如果该函数返回一个-1，那么说明没有收到有效数据。
Pack_Data() 是打包反馈数据的函数，它留有一个空位，可以自己添加一个字节的数据。
UnPack_Data_Cha() 是解码底盘控制数据的函数。
UnPack_Data_Pan() 是解码云台控制数据的函数。

5、Serial.h
里面包含了反馈数据，底盘控制数据，云台控制数据的结构体的具体定义，以及某些结构体成员的枚举。
注意结构体的内容，如typedef struct ControlData_Pan _controldata_pan;  的_controldata_pan，这是结构体的别名
而}*_controldata_panInfo；  这个_controldata_panInfo就是结构体的指针。
这种别名以及指针的写法是被C所允许的。

6、usbd_cdc_if.c
里面虽然不是我写的，但是包含了USB-CDC的设备描述符的定义以及发送，有可能会被用到。

**********
基本操作：
发送数据：
先定义对应结构体以及相应合适的缓冲数组，然后再给结构体分配空间，赋值（可以不赋，等待读入数值）。
然后利用打包函数，打包好数据，接着用发送函数CDC_SendFeed()来发送数据。
接收数据
先定义对应结构体以及相应合适的缓冲数组，然后再给结构体分配空间，赋值（可以不赋，等待读入数值）。
然后利用接收函数（接收函数内部写好了解包函数），获得含有数据的结构体。
**********

参考文档：
https://blog.csdn.net/sinat_16643223/article/details/108894564     ros中使用serial包实现串口通信
https://blog.csdn.net/qq_33904382/article/details/112718948  怎样用串口发送结构体-简单协议的封包和解包
https://blog.csdn.net/Naisu_kun/article/details/118192032   STM32 USB使用记录：使用CDC类虚拟串口（VCP）进行通讯
https://www.cnblogs.com/MORAN-123/p/17084387.html  关于STM32CubeIDE无法正常启动GDB服务端的解决办法

**********
备注：
如果你无法获取设备描述符，从而无法连接的话，可能是你的时钟配错了，建议参考一下该工程的时钟配置。

**********
禁止外传，商业以及非本战队意愿的私自使用以及修改。
**********









