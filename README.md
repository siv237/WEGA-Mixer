### Install software 

#### Arduino IDE
[Install Arduino IDE](https://www.arduino.cc/en/software)

Запускаем Arduino IDE и устанавливаем все необходимые библиотеки

Tools -> Manage libraries -> search libraries
 - LiquidCrystal_I2C.h
 - Adafruit_MCP23017.h
 - HX711.h

Вы можете пропустить этот шаг если вы собираетесь использовать vscode IDE как основной

Выбираем правильную плату ESP

Tools -> boards manager -> ESP8266 -> NodeMCU 1.0(ESP-12E)

#### Visual Studio Code configuration
Если вы планируете использовать vscode как основную IDE для работы с файлами ардуины вам потребуется установить [arduino plugin](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) и сконфигурировать его для работы с вашей платой.

[Здесь видео пример как это сделать](https://www.youtube.com/watch?v=FnEvJXpxxNM)


#### Проверка правильности выбранных настроек
Будем считать, что вы уже сконфигурировали вашу IDE для работы с платой. Теперь попробуем загрузить простой пример, маргание светодиодом.

File -> examples -> basics -> blink
Нажать upload/загрузить

Как только процесс загрузки закончится вы должны увидеть как светодиод маргает на вашей ESP

#### Проверка ESP и простой веб сервера
- Открыть файл `web-ota-example` 
- Изменить значения в файле на ваши
```bash
const char* ssid = "YOUR_WIFI_NETWORK_NAME";
const char* password = "YOUR_WIFI_PASSWORD";
```

- Теперь можно заливать код на вашу плату
- Нажать upload/загрузить

Как только процесс загрузки закончится веб сервер должен быть запущен, теперь необходимо найти IP, который получила плата.
Это можно сделать на странице конфигурации вашего роутера.
Рекомендуется прописать резервацию для этого IP, в этом случае каждый раз ваша плата будет получать один и тот же адрес.

#### Подключение по схеме компонентов для миксера
Здесь можно видеть текущую схему подключения

<a href="images/connection_diagramm.png"><img src="images/connection_diagramm.png" width="250"></a>

Здесь находится [диаграмма](scheme)

#### Mixer
Последняя версия [миксера](mixer)

- Открываем файл прошивки из папки `mixer/mixer.ino`
- Обновляем `ssid` и `password` для вашего WiFi.

```bash
const char* ssid = "YOUR_WIFI_NETWORK_NAME";
const char* password = "YOUR_WIFI_PASSWORD";
```

- Обновляем имена насосов, если это требуется. Если вы собирали точто по схеме, то скорее всего ничего обновлять не нужно.
- Пример имен насосов в коде:

```bash
a0=0 .. a7=7
b0=8 .. b7=15
```

Это означает если вы подключаете B0/A0 то имена будут 8/0, еще один пример B3/A3 == 11/3
Почему так?
На плате написаны номера портов A0, A1...B7, но библиотеке без различны эти имена и она назначает портам очередность с нуля до 15.
Так как этих плат может быть несколько 2, 3 и до 32 и все порты имеют сквозную нумерацию.

```bash
// Pump #1
int a0 = 0;
int b0 = 8;
...
etc.
```

- Сохраняем код
- Нажать upload/загрузить

Как только код будет загружен на плату и произойдет перезагрузка, на экране должна появится надпись `Ready`

<a href="images/screen_ready.png"><img src="images/screen_ready.png" width="250"></a>

Ну все, код залит, экран работает.

### Калибровка весов/стола

1. нужно взять точно известный вес в районе 500 грамм
2. отметить две точки на которых будут стоять бутылки А и B
3. Перезагрузить систему с пустым столом.
4. Поставить на точку А известный вес и подбирать значение `scale_calibration`

Если вес ниже эталлонного то значение `scale_calibration` уменьшаем

Например:

- scale_calibration = 2045
- эталлоный вес 47.90
- ставим его на весы(стол) и если значение ниже эталонного
- вес на столе 47.50
- scale_calibration уменьшаем
- scale_calibration = 2040
- прошиваем
- повторяем процедуру
- ставим эталлоный вес на весы(стол) и если значение ниже эталонного
- вес на столе 47.64
- scale_calibration уменьшаем
- scale_calibration = 2040
