#DevCon School 2016: IoT-�������� 

# ������������ 3: Hello, LED. ������� �����������.


���� ��������� � IoT Hub ������ ������ [� ���� ������](https://blogs.windows.com/buildingapps/2015/12/09/windows-iot-core-and-azure-iot-hub-putting-the-i-in-iot/#XaVrtzWBatUCpe0B.97)

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
                Console.WriteLine(s);
            }
        };
        f();
    }
```

����� � �������������� ����������, ���������� � ���������� ������������.

## ���������� ���������

��� ���������� ������� ��� ���������� ���������� � ����������� ���������, � �������������� ����������� �������� �����. 
����� Raspberry Pi �������� ��������� ����������
������-������� ������ ���������� (�.�. Gpio), ������� ��� �����.

![Raspberry Pi Pinout](../images/RP_Pinout.png)

��� ��������� ����������, ��� ����� ������� �� ����� ��������� �������������� �����. ������ ����������, 
��� �������� ����� � �����������, �������
��� ���������� ������������, � ����� ��������, ������� ���������� �������� ��������������� �� �����������.

![LED Schematics](../images/LED1Schematics.PNG)

� ��������� ���� �� �������� ����� ���� ����� ����� ��������� ���:

![LED Layout](../images/LED1Layout.PNG)

## ��������� �����������

��� ���������� ����������� ���������� ������������� ��������������� ����� (���) ����������� � ������� ��� 
������ ���������. ��� ������ � ������ ������������ ����� `GpioController`, ��������� �������:

```
   var Gpio = GpioController.GetDefault();
   var pin = Gpio.OpenPin(17);
   pin.SetDriveMode(GpioPinDriveMode.Output);
   pin.Write(GpioPinValue.Low);
```

## �������

�������� ���������, ������� ������ ����������� � ����������� ��������������.

**����������� �������**: ���� �� ������ ������������ � ���������������� ����������������, ���������� ����������� 
������� ��������� ������� ���������� � ������� ���������� PWM (Pulse Width Modulation). 
���������� �� ������ � PWM �� Raspberry Pi ���� 
[� ���� ������](http://blog.infernored.com/how-to-fade-an-led-with-pwm-in-windows-iot).
