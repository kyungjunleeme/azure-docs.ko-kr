---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 70df45877a310d74e7c5c82292d18b1c0eb32da8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521419"
---
다음 리소스도 사용할 수 있습니다.

- [Java SDK 참조 설명서](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable)
- [서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [Digital Twins 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>IoT Hub 서비스 클라이언트 예제

이 섹션에서는 IoT Hub 서비스 클라이언트와 DeviceMethod의 **devicetwin** 및 **DeviceMethod** 클래스를 사용 하는 Java **com.microsoft.azure.sdk.iot.service.devicetwin** 예제를 보여 줍니다. 장치 쌍을 사용 하 여 장치 상태와 상호 작용 하는 데 **Devicetwin** 클래스를 사용 합니다. 또한 **Devicetwin** 클래스를 사용 하 여 IoT Hub에서 [장치 등록을 쿼리할](../articles/iot-hub/iot-hub-devguide-query-language.md) 수 있습니다. **DeviceMethod** 클래스를 사용 하 여 장치에서 명령을 호출 합니다. 장치의 [Dtdl](../articles/iot-pnp/concepts-digital-twin.md) 모델은 장치가 구현 하는 속성과 명령을 정의 합니다. 코드 조각에서 변수는 iot `deviceId` hub에 등록 된 iot 플러그 앤 플레이 장치의 장치 ID를 포함 합니다.

### <a name="get-the-device-twin-and-model-id"></a>장치 쌍 및 모델 ID 가져오기

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치의 장치 쌍 및 모델 ID를 가져오려면 다음을 수행 합니다.

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>디바이스 쌍 업데이트

다음 코드 조각에서는 장치에서 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 업데이트 하기 전에 쌍을 가져와야 합니다. 속성은 장치의 기본 구성 요소에 정의 되어 있습니다.

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

다음 코드 조각에서는 구성 요소의 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 업데이트 하기 전에 쌍을 가져와야 합니다. 속성은 **thermostat1** 구성 요소에 정의 되어 있습니다.

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

구성 요소의 속성에 대 한 속성 패치는 다음 예제와 같습니다.

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>Call 명령

다음 코드 조각에서는 `getMaxMinReport` 기본 구성 요소에 정의 된 명령을 호출 하는 방법을 보여 줍니다.

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

다음 코드 조각에서는 `getMaxMinReport` 구성 요소에서 명령을 호출 하는 방법을 보여 줍니다. 명령은 **thermostat1** 구성 요소에 정의 되어 있습니다.

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub 디지털 쌍 예

**Digitaltwin** 네임 스페이스에서 **DigitalTwinAsyncClient** 클래스를 사용 하 여 디지털 쌍을 통해 장치 상태와 상호 작용 합니다. 다음 예에서는 동일한 네임 스페이스의 **Updateoperationutility** 및 **BasicDigitalTwin** 클래스를 사용 합니다. 장치의 [Dtdl](../articles/iot-pnp/concepts-digital-twin.md) 모델은 장치가 구현 하는 속성과 명령을 정의 합니다.

변수는 iot `digitalTwinid` hub에 등록 된 iot 플러그 앤 플레이 장치의 장치 ID를 포함 합니다.

### <a name="get-the-digital-twin-and-model-id"></a>디지털 쌍 및 모델 ID 가져오기

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치의 디지털 쌍 및 모델 ID를 가져오려면 다음을 수행 합니다.

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>디지털 쌍 업데이트

다음 코드 조각에서는 장치에서 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 속성은 장치의 기본 구성 요소에 정의 되어 있습니다.

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

다음 코드 조각에서는 구성 요소의 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 속성은 **thermostat1** 구성 요소에 정의 되어 있습니다.

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>Call 명령

다음 코드 조각에서는 `getMaxMinReport` 기본 구성 요소에 정의 된 명령을 호출 하는 방법을 보여 줍니다.

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

다음 코드 조각에서는 `getMaxMinReport` 구성 요소에서 명령을 호출 하는 방법을 보여 줍니다. 명령은 **thermostat1** 구성 요소에 정의 되어 있습니다.

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>장치 원격 분석 읽기

IoT 플러그 앤 플레이 장치는 DTDL 모델에 정의 된 원격 분석을 IoT Hub 보냅니다. 기본적으로 IoT Hub는 원격 분석을 사용할 수 있는 Event Hubs 끝점으로 라우팅합니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 다른 끝점으로 장치-클라우드 메시지 보내기를](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)참조 하세요.

다음 코드 조각에서는 기본 Event Hubs 끝점에서 원격 분석을 읽는 방법을 보여 줍니다. 이 코드 조각의 코드는 IoT Hub 빠른 시작에서 [장치에서 IoT Hub로 원격 분석을 보내고 백 엔드 응용 프로그램을 사용](../articles/iot-hub/quickstart-send-telemetry-java.md)하 여 읽습니다.

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

이전 코드의 다음 출력은 기본 구성 요소만 있는 구성 요소가 없는 **자동 온도 조절기** IoT 플러그 앤 플레이 장치에서 보낸 온도 원격 분석을 보여 줍니다. `dt-dataschema`System 속성은 모델 ID를 표시 합니다.

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

이전 코드의 다음 출력에서는 다중 구성 요소 **TemperatureController** IoT 플러그 앤 플레이 장치에서 보낸 온도 원격 분석을 보여 줍니다. `dt-subject`System 속성은 원격 분석을 보낸 구성 요소의 이름을 표시 합니다. 이 예에서 두 구성 요소는 `thermostat1` `thermostat2` dtdl 모델에 정의 된 및입니다. `dt-dataschema`System 속성은 모델 ID를 표시 합니다.

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>장치 쌍 변경 알림 읽기

지원 되는 끝점으로 라우팅하기 위해 장치 쌍 변경 알림을 생성 하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 비 원격 분석 이벤트 > 다른 끝점으로 장치-클라우드 메시지 보내기를](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)참조 하세요.

이전 Java 코드 조각에 표시 된 코드는 IoT Hub 구성 요소 없는 자동 온도 조절기 장치에 대 한 장치 쌍 변경 알림을 생성할 때 다음 출력을 생성 합니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

이전 Java 코드 조각에 표시 된 코드는 IoT Hub 구성 요소를 사용 하 여 장치에 대 한 장치 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성 합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 장치가 알림을 생성할 때 출력을 보여 줍니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>디지털 쌍 변경 알림 읽기

지원 되는 끝점으로 라우팅하도록 디지털 쌍 변경 알림을 생성 하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 비 원격 분석 이벤트 > 다른 끝점으로 장치-클라우드 메시지 보내기를](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)참조 하세요.

이전 Java 코드 조각에 표시 된 코드는 IoT Hub 구성 요소가 없는 자동 온도 조절기 장치에 대 한 디지털 쌍 변경 알림을 생성할 때 다음 출력을 생성 합니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

이전 Java 코드 조각에 표시 된 코드는 IoT Hub 구성 요소가 있는 장치에 대 한 디지털 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성 합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 장치가 알림을 생성할 때 출력을 보여 줍니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
