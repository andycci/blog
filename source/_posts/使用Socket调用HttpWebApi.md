---
title: 使用Socket调用HttpWebApi
date: 2022-09-08 14:00:00
tags:Socket
---

背景：最近有一个需求，单片机需要调用服务端Api获取数据，因为一些原因单片机只能使用TCP方式；

Demo如下（这里用C#语言实现）

```c#
using System.Text;
using System.Net.Sockets;

namespace ConsoleApp3
{
	internal class Program
	{
		static void Main(string[] args)
		{
			var host = "dev.iot.api.mytest.com";
			var port = 80;
            
			Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
			socket.Connect(host, port);

			var body = Newtonsoft.Json.JsonConvert.SerializeObject(new Body { MachineCode = "TBX001" });

			StringBuilder postData = new StringBuilder();
			postData.Append("POST /Machines/Register/XXXF40B93D06E24AE5A060355CE6XXXX HTTP/1.1\r\n");
			postData.Append($"Host: {host}\r\n");
			postData.Append($"Content-Length: {body.Length}\r\n");
			postData.Append($"Content-Type: application/json\r\n");
			postData.Append("\r\n");
			postData.Append(body);
			postData.Append("\r\n");

			Console.WriteLine("开始发送消息");
			byte[] message = Encoding.UTF8.GetBytes(postData.ToString());
			socket.Send(message);

			Console.WriteLine("发送消息为:" + Encoding.UTF8.GetString(message));
			byte[] receive = new byte[1024];
			int length = socket.Receive(receive);
			Console.WriteLine("接收消息为：" + Encoding.UTF8.GetString(receive));

			socket.Close();

			Console.ReadKey();
		}


		public class Body
		{
			public string? MachineCode { get; set; }
		}
	}
}
```

注意：拼接发送数据时Header部分和body部分一定要有换行分隔（如果没有body也要有换行分隔），否则服务器无法识别

控制台输入如下：

![1662605798730](C:\Users\Andycci\AppData\Roaming\Typora\typora-user-images\1662605798730.png)