---
title: 將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (C#) | Microsoft Docs
description: 身為裝置開發人員，如何使用C#將 Raspberry Pi 連線到您的 Azure IoT Central 應用程式。
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d27f792b39a1809cde0f27186f343af7d7aef60a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454183"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (C#)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文說明如何以裝置開發人員身分，將 Raspberry Pi 連線到使用 C# 程式設計語言的 Microsoft Azure IoT Central 應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成本文中的步驟，您需要下列元件︰

* 從**舊版應用**程式範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 執行 Raspbian 作業系統的 Raspberry Pi 裝置。 Raspberry Pi 必須能夠連接到網際網路。 如需詳細資訊，請參閱[設定您的 Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)。

## <a name="add-a-device-template"></a>新增裝置範本

在您的 Azure IoT Central 應用程式中，新增具有下列特性的新**Raspberry Pi**裝置範本：

* 遙測，包括裝置將會收集的下列度量：
  * 溼度
  * 溫度
  * 壓力
  * 磁力計 (X, Y, Z)
  * 加速計 (X, Y, Z)
  * 迴轉儀 (X, Y, Z)
* 設定
  * 電壓
  * 目前
  * 風扇速度
  * IR 切換。
* 屬性
  * 模具編號裝置屬性
  * 位置雲端屬性

1. 從 [裝置範本] 中選取 [ **+ 新增**] ![裝置範本](media/howto-connect-raspberry-pi-csharp/adddevicetemplate.png)
   

2. 選取 **Raspberry pi**  並建立 Raspberry pi 裝置範本 ![新增裝置範本](media/howto-connect-raspberry-pi-csharp/newdevicetemplate.png)
   

如需裝置範本設定的完整詳細資訊，請參閱[Raspberry Pi 裝置範本詳細資料](#raspberry-pi-device-template-details)。

## <a name="add-a-real-device"></a>新增真實裝置

在您的 Azure IoT Central 應用程式中，從**Raspberry Pi**裝置範本新增實際裝置。 記下裝置連線詳細資料（**範圍識別碼**、**裝置識別碼**和**主要金鑰**）。 如需詳細資訊，請參閱[將真實裝置新增至 Azure IoT Central 應用程式](tutorial-add-device.md)。

### <a name="create-your-net-application"></a>建立 .NET 應用程式

您可以在桌面機器上建立和測試裝置應用程式。

若要完成下列步驟，您可以使用 Visual Studio Code。 如需詳細資訊，請參閱[使用 C#](https://code.visualstudio.com/docs/languages/csharp)。

> [!NOTE]
> 如果您想要的話，也可以使用不同的程式碼編輯器來完成下列步驟。

1. 若要將 .NET 專案初始化並新增所需的 NuGet 套件，請執行下列命令：

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. 在 Visual Studio Code 中開啟 `pisample` 資料夾。 然後開啟 **pisample.csproj** 專案檔。 新增下列程式碼片段所示的 `<RuntimeIdentifiers>` 標籤：

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > 您的套件版本號碼可能會高於顯示的編號。

1. 儲存 **pisample.csproj**。 如果 Visual Studio Code 提示您執行還原命令，請選擇 [還原]。

1. 開啟**Program.cs** ，並將內容取代為下列程式碼。 使用您先前所記下的值來更新 `{your Scope ID}`、`{your Device ID}`和 `{your Device Primary Key}`：

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
        {
          Random rand = new Random();

          while (true)
          {
            double currentTemperature = baseTemperature + rand.NextDouble() * 20;
            double currentPressure = basePressure + rand.NextDouble() * 100;
            double currentHumidity = baseHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
              humidity = currentHumidity,
              pressure = currentPressure,
              temp = currentTemperature
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));

            token.ThrowIfCancellationRequested();
            await Client.SendEventAsync(message);

            Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
          }
        }


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

## <a name="run-your-net-application"></a>執行 .NET 應用程式

若要建立並執行範例應用程式：

1. 在命令列環境中執行下列命令：

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. 將 `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` 資料夾複製到 Raspberry Pi 裝置。 您可以使用 **scp** 命令來複製檔案，例如：

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    如需詳細資訊，請參閱 [Raspberry Pi 遠端存取](https://www.raspberrypi.org/documentation/remote-access/)。

1. 登入 Raspberry Pi 裝置，並在殼層中執行下列命令：

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. 在 Raspberry Pi 上執行下列命令：

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![程式開始](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. 在 Azure IoT Central 應用程式中，您會看到在 Raspberry Pi 上執行的程式碼如何與應用程式互動：

   * 在真實裝置的 [量測] 頁面上，您可以看到遙測。
   * 在 [屬性] 頁面上，您可以看到所報告 [模具編號] 屬性的值。
   * 在 [設定] 頁面上，您可以變更 Raspberry Pi 的各種設定，例如電壓和風扇。

     下列螢幕擷取畫面顯示 Raspberry Pi 正在接收設定變更：

     ![Raspberry Pi 收到設定變更](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 裝置範本詳細資料

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Raspberry Pi**  裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

| 欄位名稱     | 單位數  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>設定

數值設定

| 顯示名稱 | 欄位名稱 | 單位數 | 小數位數 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電壓      | setVoltage | 伏特 | 0              | 0       | 240     | 0       |
| 目前      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切換設定

| 顯示名稱 | 欄位名稱 | 開啟文字 | 關閉文字 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 開啟      | OFF      | 關     |

### <a name="properties"></a>屬性

| 類型            | 顯示名稱 | 欄位名稱 | Data type                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| 裝置屬性 | 模具編號   | dieNumber  | number                                 |
| 位置        | 位置     | location   | {lat： float，long： float，alt？： float} |

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 Raspberry Pi 連線到您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](howto-set-up-template.md)。
