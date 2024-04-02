# First work to FPGA

> Дата реализации: 29.03.2024
----

* [Теория по FPGA](#Теория-по-FPGA)
* [Среда Quartus II](#Среда-Quartus-II)
* [Обновление драйверов](#Обновляем-драйвера-для-устройств-(FPGA-и-прогромматора)-и-подключение-физическое)
* [Создание проета и программирование ПЛИСА](#Создание-проета-и-программирование-ПЛИСА)

## Теория по FPGA

`ПЛИС` - это программируемая логическая интегральная схема, такая микросхема состоит из множества одинаковых блоков или макроячеек, каждый производитель по своему их называет, так у `Xilinx это Slices` (ломтики) у `Altera` - LogicElements (логические элементы). Эти блоки относительно простые, они могут выполнять роль нескольких логических элементов, быть маленькой таблицей поиска (LUT), содержать готовый сумматор, умножитель, блок цифровой обработки сигналов (DSP), в общем все что задумает производитель. Пользователь может по своему желанию расположить и настроить эти блоки как ему захочется, реализовав таким образом достаточно сложные цифровые схемы. На ПЛИС можно реализовать хоть микроконтроллер, реализовав например ту же Ардуино или процессор вашей собственной архитектуры, например не регистровый а стековый процессор, даже свою собственную ПЛИС можно реализовать на ПЛИС!

В иностранной литературе можно встретить сокращения для ПЛИС: FPGA и CPLD. CPLD (Complex Programmable Logic Device ) - чипы с небольшим числом макроячеек, специализированных блоков и малым энергопотреблением.

## Среда Quartus II

Для работы с данной ПЛИС нам нужно скачать официальную среду разработки. Заходим на официальный сайт <a href="http://dl.altera.com/15.0/?edition=web" target="_blank">intel Quartus II</a> (если недоступен для России понадобится VPN)

И устанавливаем пакеты:

- Quartus Prime Lite Edition (Free)
  - Quartus Prime (includes Nios II EDS)
  - Questa -Intel FPGA and Starter Edition
    - Starter Edition
- Devices
  - Cyclone IV devices support

> Cyclone IV devices support - выбирал для своей платы, соответственно выбираем пакеты для своего ПЛИС, если он отличается

<img src="https://github.com/StasBeep/FPGA/blob/main/image/Quartus_install.png" alt="install Quarts" width="700" height="600">

## Обновляем драйвера для устройств (FPGA и прогромматора) и подключение физическое

Для этого подключаем плату к питанию и программатору

<img src="https://github.com/StasBeep/FPGA/blob/main/image/PointToPointPlateBlaster.jpg" alt="PointToPointPlateBlaster" width="400" height="400">

Обратите внимание, что на программаторе Blaster должно гореть две точки красного цвета - это говорит о том, что плата "питается" и при этом подключена к плате

<img src="https://github.com/StasBeep/FPGA/blob/main/image/BlasterPower.jpg" alt="PointToPointPlateBlaster" width="200" height="200">

Подключение для программирования в порт `JTAG` или плата не будет реагировать

После переходим в `Пуск`(правая кнопка) -> `Диспетчер устройств` и находим `Дугие устройства`

<img src="https://github.com/StasBeep/FPGA/blob/main/image/otherDevice.PNG" alt="otherDevice" width="300" height="200">

Нажимаем <b>правой кнопкой</b> основные системные устройства и обновить драйвера -> найти драйверы на этом компьютере (вручную) и ищем папку, где устанавливали Quartus, в ней папку `drivers` далее:

<img src="https://github.com/StasBeep/FPGA/blob/main/image/drivers.PNG" alt="drivers" width="300" height="300">

<img src="https://github.com/StasBeep/FPGA/blob/main/image/newDrivers.PNG" alt="newdrivers" width="450" height="300">

и обновляем! Плата готова к работе

<span style="color: red;">Каждый раз обновлять не нужно!</span>

## Создание проета и программирование ПЛИСА

> Цель: при нажатии на кнопки S1 | S2 | S3 | S4 должны загораться LED1 | LED2 | LED3 | LED4

Для создания проекта запускаем приложение `Quartus`

File => New Project Wizard => Next => выбираем [название проекта] и [место где будет располагаться] (в моем случае будет называться RaspinProject) => Empty Project => Next =>

<img src="https://github.com/StasBeep/FPGA/blob/main/image/choiseCyclone.jpg" alt="newdrivers" width="700" height="700">

<span style="color: red;">Обязательно выбираем модель платы, которая у нас! В моём случае EP4CE6E22C8 (без N)</span>

=> Next (проверяем) => Finish

Далее главное меню Assignments -> Device, здесь можно выбрать чип (нашу ПЛИС), выбираем если не выбрали, нажимаем кнопку «Device and Pin Options»

<img src="https://github.com/StasBeep/FPGA/blob/main/image/deviceAndOptions.PNG" alt="deviceAndOptions" width="700" height="500">

<span style="color: yellow;">В окне выбираем пункт «Unused pins». Эта настройка определяет, что будет с неподключенными пинами. Это может быть важно, в своем проекте вы вряд ли используете все пины, а не подключенные могут быть на деле соединены с землей или питанием (ну мало ли, кто разводил плату). Если вы подадите единичку на заземленный пин, то он сгорит, поэтому нужно внимательно следить за этим.</span>

<span style="color: red;">!!!!!!!!!!!!!!!!!!!!!</span>

<span style="color: red;">!!! Выбираем `As input tri-stated` !!!!</span>

<span style="color: red;">!!!!!!!!!!!!!!!!!!!!!</span>

Переходм в пункт «Voltage» и выбираем `3.3-V LVTTL` => Ок (Выходим на главный интерфейс программы)

Далее File => New => Block Diagram/Schematic File

Появляется поле с симуляцией логической схемы. Выбираем раздел `Symbol Tool` и ищем там `input` и `output` => вытаскиваем на рабочее поле 4 input и 4 output

<img src="https://github.com/StasBeep/FPGA/blob/main/image/shemaRaspin.PNG" alt="deviceAndOptions" width="900" height="600">

<b>Соединяем проводом</b> каждый input с протовоположным output

Далее каждый input переименовываем на key[1], key[2] ...

A каждый output переименовываем в схеме led[1], led[2] ...

Сохраняем (дискета сохранения) файл со схемой

После сохранения нажимаем на `Start Analysis & Synthesis` ждём анализ

Если ошибок нет, то нажимаем на Pin Planer и заполняем столбик `location`

<img src="https://github.com/StasBeep/FPGA/blob/main/image/SpinRaspin.PNG" alt="SpinRaspin" width="1000" height="600">

Закрываем смело окно и нажимаем `Start Compilation`

Дожидаемся загрузки и проверяем на ошибки (внимательно!)

Если ошибок нет, то переходим в раздел `Programmer`

---

Выбираем `Hardware Settings` там ищем USB, если его нет или написано, что `No Hardware` - значит ПЛИС с программатором не подключён или драйвера не актуальны, нужно обновить

Нужно найти `USB Blaster[USB 0]` и выбрать его, после нажать Add File => Найти файл со схемой (расширение .sof), поставить галочку `Program/Configure` в Mode выбрать `JTAG` и нажать `Start`

В Progress должно появиться `100% (Successfully)`

ГОТОВО! При нажатии на кнопочки ПЛИС должен зажигать индикаторы! Проверяйте!

Программатор можно отсоеденить от ПЛИС! Но если отключить питание, то ПЛИС вернётся в исходное (заводское состояние)

Поздравляю с первым проектом!

<span style="color: green;">Successfully</span>
