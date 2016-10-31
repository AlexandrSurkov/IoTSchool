#DevCon School 2016: IoT-�������� 

# ������������ 3: Light Bot.

� ���� ������������ ������ �� ���������� Azure IoT Hub ��� ������������ ������������� ����� � �����������. �� ����������
��������-���������, ����������� � ������� ���-����.

## ������ IoT-hub

��� �������� ������� ��� ����������� IoT Hub. �������� ��� � ����� �������� �������� (������ "�������� �����").
����� ����� ���������� ������ ����������� � IoT Hub (����� �������� � ������� ����������� ����), ��������������� 
������ ������� `iothubowner`.

![Get Access Key](../images/IoTHub_AccessKeys.PNG)

## ������������ � IoT-���� � ������� ������ ����������� ��� ����������

����������� � ���������� [Device Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md). � ��� �������
������ ����������� � IoT-����:

![Device Explorer 1](../images/DeviceExplorer1.PNG)

����� ����� ��������� �� ������� "Management" � �������� ����� ����������. ����� ������ ������� ������� �� ������ � ����������� � �������� "Copy Connection String" 
(��� ������ ����� �������� ������� ����������� ����������).

![Device Explorer 2](../images/DeviceExplorer2.PNG)

## �������� ��� ��� ������ � IoT-�����

����� ������� ��������� [Getting Started](https://azure.microsoft.com/ru-ru/develop/iot/get-started/) ���� � MSDN. 
��������� ��� ����������, ���� ���������������� � �.�. - � ��������� �������� ����.

� ����� ������ �������� Raspberry Pi 2 -> Windows -> C#. ��� ���������� ���� � ������, ���������� ����� �������� ������ �� NuGet-�����
`Microsoft.Azure.Devices.Client`. � ���������� ���� �� �������� ��������� ������ ����������� �� ��, ������� ���� �������� �� ���������� ����.

## ���������� � IoT Hub ������ � ��������� ����������

���������� ������������ ��� �� ������������ ������ 1 � ��������� ��� ��� ����������� � IoT Hub � ������� `OnNavigatedTo`:
```
iothub = DeviceClient.CreateFromConnectionString(DeviceConnectionString);
await iothub.OpenAsync();
```

����� ��� ��������� ��������� ���������� ����� �������� � IoT Hub ��������� *on* ��� *off*:
```
var s = state ? "on" : "off";
var b = Encoding.UTF8.GetBytes(s);
await iothub.SendEventAsync(new Message(b));
```

## ��������� ������ �� IoT Hub
������� �� ���� ��� ���������/������� ���������� �� ����� ����� �������� �� IoT Hub. ��� ����� ��������� �� 
IoT Hub ����� ������������ ��������� ���:
```
        private async Task Receive()
        {
            while (true)
            {
                var msg = await iothub.ReceiveAsync();
                if (msg != null)
                {
                    var s = Encoding.ASCII.GetString(msg.GetBytes());
                    // ������� ���-�� � ���������� ����������, ��������, ������ ���������
                    LED(s=="on");
                    await iothub.CompleteAsync(msg);
                }
            }
        }
```

��� ������� ����������� ����� ����������� ���������� �������� ������� `Receive` ����� �������� IoT Hub, �� ��� �������������
`await`, ����� �� �� ������� ���������� ������������ ����� (����������� �������������� Visual Studio).

## ������ ���-���� 

������� �������� ���� ������ [��� �����](https://github.com/evangelism/ModernAI/tree/master/SimpleCommandBot) 
��� [� ���� ������](http://blog.soshnikov.com/2016/04/12/hello-bot-%D1%87%D0%B0%D1%82-%D0%B1%D0%BE%D1%82%D1%8B%D1%81%D0%BB%D0%B5%D0%B4%D1%83%D1%8E%D1%89%D0%B5%D0%B5-%D0%BF%D0%BE%D0%BA%D0%BE%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BF%D1%80%D0%B8%D0%BB/).

���������� Bot Application Template, �������� ����� ������ ����. ����� ����� ��������� �������� ������ �����������
� ����� ��������� �� IoT Hub.

��� ������ � IoT Hub �� ������� ������� ������ ����� `Microsoft.Azure.Devices`, ������� ���� ���������� � ������� NuGet.

��� �������� ��������� � IoT Hub ������������ ��������� ���:

```
var hub = ServiceClient.CreateFromConnectionString(HubConnString);
await hub.SendAsync(DEVICE_ID, new Message(Encoding.UTF8.GetBytes("Hello")));
```

����� `HubConnString` - ������ ����������� � IoT Hub (�� � ����������� ����������, � � ���� � �����), `DEVICE_ID` -
������������� ����������, �������� ���� ������� ���������, `"Hello"` - ���������� ���������.

��� �������� ��� �������� � ��� ��������� "on" � "off", �������� ��� ����� ���������� ��������.

���� ��������� � IoT Hub ��������� �������, �� �� ������ ������ [� ���� ������](https://blogs.windows.com/buildingapps/2015/12/09/windows-iot-core-and-azure-iot-hub-putting-the-i-in-iot/#XaVrtzWBatUCpe0B.97)

��� ���������� ���������� ����� `WindowsAzure.ServiceBus`, ��������� �������� � IoT Hub �� ����� ��� � `Event Hub` - 
�� ����� ���� ��� ��� �������� �������� ������ ������.

��� ��� �������� ��� ��� ����� ��������� �� IoT Hub:

```
    EventHubClient cli = EventHubClient.CreateFromConnectionString(HubConnString, "messages/events");

    var runtimeInfo = await cli.GetRuntimeInformationAsync();
    foreach (var p in runtimeInfo.PartitionIds)
    {
        var rec = await cli.GetDefaultConsumerGroup().CreateReceiverAsync(p);
        Func<Task> f = async () =>
        {
            while (true)
            {
                var x = await rec.ReceiveAsync();
                var s = Encoding.UTF8.GetString(x.GetBytes());
                Console.WriteLine(s); // ��� ���-�� �٨
            }
        };
        f();
    }
```

����� ������������ ����� ������ ������ �������� ������������ �������. �� ����� ����, ��� ������� ������� IoT Hub
��������� ���� ����������� ������� ��� ������ ������, ������� ����������� ��� �������� ���������� (��� ����� ����� 
`await` ���������), � ���������� ������ � ������� ����� ������� ������ ���������.

����� ��� ��� ������� ��������� ������������, ��� ����� ����� ��� ������������ � ����� �����. � ����� ����-�������
��� ������ ��������� � ���� (� ������ `Post` � `MessagesController`) �� ����� ���������� `activity` � `connector` �
��������� ����������� ������, � ����� ������������ �� ��� �������� ���������.
