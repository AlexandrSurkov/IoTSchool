#DevCon School 2016: IoT-�������� 

# ������������ 4: �������� ����� ������� � ������

� ���� ���������� �� ������� ����� ����� �������, ���������� ����������� �� ������������ 2, � ������ ��� � ������ IoT-��� � ������.

## ������������ � IoT Hub � ������� ������ ��� ������ ����������

� Device Explorer ����� ��������� ������ ����������� � IoT Hub:
```
HostName=devconhub.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=GSzUSKj0XAQ5jHJF8+nz97ysfo65hbM0VIic8oSb4RU=
```

����� ����� �������� ��� ���������� � ������� *Management* � ���������� ��� ������ �����������.

## �������� ���������� �� GitHub � ��������� ���������

����� ��������� ������� �� ������������ ����, �� ��� ����� ������������ ���� � �� �� ������ ����������, 
����������� � GitHub. ��������
���������� Lab4 � ��������� � ���� ��������� � ������������ �� ������ �������:

```
        string Id = "rpi3";
        string DeviceConnectionString = "[YOUR CONNECTION STRING]";
        string Nick = "shwars";
        int Age = 42;
        string Sex = "M";
        int Dizz = 0;
        // 0 - ���������� ���������
        // 1 - 5 ��������� ������ ����� ���
        // 2 - 10 ��������� ������ ����� ���

        private int DeviceType = 0;
        // 0 - Raspberry Pi
        // 1 - PC UWP

```

����� ������������� ����������, ��������� ��� �� ����������, � �������� ����� ������� � ���� �������:

  * � ���������� ��������� ����� ������� (�������� ������� 10 �������)
  * ����� 10 ��������� ������ ���� ��� (�������� ������� 3 ������� �� 3 ������)
  * ����� 20 ��������� ������ ���� ��� (3 ������� �� 3 ������)

����� �������� �������������� ���������� � ������� � ���� ��������������� ���������, ����� � IoT Hub ���� �������� ���������� ������.

## ������ ������� Stream Analytics

��� ����, ����� ������������ ������ �� IoT Hub, ����� ����� ������������ ���������� **Stream Analytics**, �������
����� ������������ ������ �/��� ������������� �� � �����-�� ���������� ���������.

��� ������, �������� Stream Analytics ��� �������� ������ ������ ���������� �� IoT Hub � PowerBI. ��� ����� 
���������� ������� ������ Stream Analytics, ���������������� � ��� ������� � �������� ������ ������ � ������ ������. 

� �������� ������� ������ ���������� IoT Hub, ������� ������� ������ `InHub`, ������ ������ �����������:

  * IoT Hub: devconhub
  * Endpoint: messaging
  * Shared Access Policy name: iothubowner
  * Shared Accesss Policy Key: GSzUSKj0XAQ5jHJF8+nz97ysfo65hbM0VIic8oSb4RU=

� �������� ������� ������ ���� ����� ������� JSON, ��������� Stream Analytics ����� �������� ��������� ������
� ������������ ���������� ���� � �������.
 
� �������� �������� ������ ������ Power BI, ������ �� `OutBI`. 

��� ������� ������ �� ������ ���������� ���������� ����� ������:
```
SELECT
    *
INTO
    [OutBI]
FROM
    [InHub]
WHERE Id='MyDeviceID'
```

���� �� ����� ����� ��������� ������ ����������� �� ����������� (�������� ��������������� ����������� �������), ����
���������� ����� ���������, �� ����� �������������� ������ ��������� �������:

```
SELECT
    *, System.Timestamp as [TimeStamp],
    CASE 
      WHEN NoExperiment=0 THEN 'First'
      ELSE 'Consecutive'
    END as ExperiementKind
INTO
    [OutBI]
FROM
    [InHub]
WHERE Id='MyDeviceID'
```

��� ���������� ������� ������� �� ������ 5 �����, � ��������� ������ �� ���� ����������, ����� ������������ ����� ������:
```
SELECT
    Id, AVG(Reaction) as React, 
    MAX(Time) as EndTime, MIN(Time) as BeginTime
INTO [OutBI]
FROM [InHub] TIMESTAMP BY Time
GROUP BY Id, TumblingWindow(Duration(minute,5))
```

��� ����� ���������� [���������� �������� � ������� �������� ��������](https://azure.microsoft.com/en-us/documentation/articles/stream-analytics-stream-analytics-query-patterns/).

����� ���������������� ������� Stream Analytics ���������� ��������� ������� �� ����������, � �������� �� �����������
������ � PowerBI.

