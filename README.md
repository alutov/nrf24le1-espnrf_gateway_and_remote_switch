# ESP8266+NRF24LE1 MQTT шлюз и NRF24LE1 беспроводной выключатель для встраивания в выключатели livolo, Maifom, Vhome и т.п.
![PROJECT_PHOTO](https://raw.githubusercontent.com/alutov/nrf24le1-espnrf_gateway_and_remote_switch_for_livolo_etc/master/other/livolo1.jpg)
![PROJECT_PHOTO](https://raw.githubusercontent.com/alutov/nrf24le1-espnrf_gateway_and_remote_switch_for_livolo_etc/master/other/livolo2.jpg)
![PROJECT_PHOTO](https://raw.githubusercontent.com/alutov/nrf24le1-espnrf_gateway_and_remote_switch_for_livolo_etc/master/other/espnrf3.jpg)
![PROJECT_PHOTO]( https://raw.githubusercontent.com/alutov/nrf24le1-espnrf_gateway_and_remote_switch_for_livolo_etc/master/other/espnrf4.jpg)

RUS | [ENG](https://translate.google.com/translate?hl=ru&sl=ru&tl=en&u=https%3A%2F%2Fgithub.com%2Falutov%2Fnrf24le1-espnrf_gateway_and_remote_switch_for_livolo_etc%2Fblob%2Fmaster%2FREADME.md)

> **После последнего релиза добавлена поддержка Home Assistant Mqtt Discovery. В расширенной esp8266 прошивке шлюза добавлена поддержка 4 локальных выключателей, замка или ворот. Выбирается в настройках. Возможна прошивка и готовых устройств на основе esp8266, но в них добавление NRF24LE1 обычно приводит к нестабильной работе последней из-за ограниченной мощности их блока питания. Но только для локального управления в этих устройствах (без использования функции шлюза и доработок) расширенная прошивка у меня работает стабильно.**

## 1. Возможности
&emsp;Проект позволяет доработать выключатели livolo с RF 433MHz приемником для работы в режиме с обратной связью и управлением при помощи MQTT сервера. Для этого в выключателе меняется одна емкость, штатный RF приемник удаляется, а предварительно запрограммированная прошивкой выключателя NRF24LE1 при помощи 5 проводов подключается к выключателю. Два из них - это питание и общий, еще два на выходы светодиодов для считывания состояния  выключателей, и еще один на вход вместо штатного приемника для управления по протоколу livolo. Возможна доработка подобным образом и других выключателей, например, Maifom, Vhome и т.п. Для этого предусмотрена поддержка протокола rcswitch. Возможно также встраивание NRF24LE1 и в другие устройства с управлением импульсом, то есть кнопкой, при использовании дополнительной схемы согласования уровней. NRF24LE1 раз в 8 секунд контролирует напряжение питания и температуру, если подключен датчик DS18B20. Для уменьшения потребления NRF24LE1 приемопередатчик (1мВт -94dBm @250kbps) включается два раза в секунду до 33 ms каждый для приема команд и передаче состояния,  а общее время работы процессора в активном режиме примерно 100 ms каждую секунду. Остальное время процессор находится в режиме сна, кроме случаев, когда нужно передать код на выключатель. Код передается до срабатывания выключателя до 8 раз в каждом полусекундном окне, но процессор работает при этом на минимальной частоте 125kHz. Такой режим работы дает среднее потребление  около 1ma при максимальной мощности передатчика и максимальной чувствительности приемника. Протокол максимально простой, работает на одной частоте. Но эту частоту можно установить выше Wifi, BLE и Zigbee. Для управления выключателями нужен шлюз, который представляет из себя ESP8266 и NRF24LE1(espnrf), соединенные между собой по rs232. Соединения на стороне шлюза ESP tx <-> NRF(32pin) p0.4(rx), ESP rx <-> NRF(32pin) p0.3(tx), ESP gpio02 <-> NRF reset. Шлюз опрашивает 16 NRF выключателей, по 4 числовых параметра в каждом. Имя каждого параметра до 32 символов должно быть определено при конфигурации  в espnrf web интерфейсе.  Первые 2 параметра отражают состояния выключателей (1/0, on/off, true/false), третий параметр используется для вывода температуры с датчика DS18B20 (актуально для встраивания NRF24LE1 в кондиционер), четвертый параметр - напряжение питания NRF24LE1. Использование ассемблера без всяких sdk позволило в выключателе понизить тактовую в частоту в 16 раз(1MHz), и соответственно снизить потребление энергии, а в шлюзе свободно вместить в память не только код программы, но и зарезервировать место для обновления кода и для 64 имен параметров. Подробнее в readme_rus.pdf. 
## 2. Сборка проекта
&emsp;Для сборки бинарных файлов esp-01(esp8266) использован  [ESP8266_RTOS_SDK (IDF Style) версии 3.2](https://codeload.github.com/espressif/ESP8266_RTOS_SDK/zip/v3.2)(последняя версия с OTA не влазит в 1Мбайт памяти esp-01)  c [описанием](https://docs.espressif.com/projects/esp8266-rtos-sdk/en/latest/) и [toolchain 5.2.0](https://dl.espressif.com/dl/xtensa-lx106-elf-win32-1.22.0-92-g8facf4c-5.2.0.tar.gz). Более свежую библиотеку esp-mqtt взял [тут](https://github.com/looi/ESP8266_RTOS_SDK). Для сборки бинарных и HEX файлов nrf24le1 использован  [Telemark Assembler 8051 версии 3.2](http://old-dos.ru/index.php?page=files&mode=files&do=show&id=1385) с [описанием](http://www.cpcalive.com/docs/TASMMAN.HTM).
<br>
