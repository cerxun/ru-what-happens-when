# What happens when on Russian  
# Что происходит когда ... (русская версия)  

This repository is an attempt to answer the age-old interview question "What happens when you type google.com into your browser's address box and press enter?"  
Этот репозиторий попытка ответить на извечный вопрос на интервью "Что происходит когда вы набираете google.com в строке вашего браузера и нажимаете Ввод?"  
Except instead of the usual story, we're going to try to answer this question in as much detail as possible. No skipping out on anything.  
Только вместо обычной истории, мы постараемся ответить на этот вопрос как можно подробнее. Ничего не упуская.  

This is a collaborative process, so dig in and try to help out! There are tons of details missing, just waiting for you to add them! So send us a pull request, please!  
Это коллективный процесс, так что вникайте и старайтесь помочь! Не хватает множества деталей, мы просто ждем, когда вы их добавите! Поэтому, пожалуйста, отправьте нам Pull Request!  

This is all licensed under the terms of the Creative Commons Zero license.  
Распространяется на условиях Creative Commons Zero license.  

**Содержание**

1. The "g" key is pressed - Нажата клавиша "g".  
2. The "enter" key bottoms out - Клавиша "enter" опускается до самого низа.  
3. Interrupt fires [NOT for USB keyboards] - Срабатывает прерывание [Не для USB-клавиатур].  
4.1 (On Windows) A WM_KEYDOWN message is sent to the app - Сообщение "WM_KEYDOWN" отправляется приложению.  
4.2 (On OS X) A KeyDown NSEvent is sent to the app - NSEvent KeyDown отправляется приложению.  
4.3 (On GNU/Linux) the Xorg server listens for keycodes - Сервер Xorg слушает коды клавиш.  
5. Parse URL - Парсинг URL.  
6. Is it a URL or a search term? - Это URL или поисковый запрос?  
7. Convert non-ASCII Unicode characters in the hostname - Преобразование не-ASCII символов Unicode в имени хоста.  
8. Check HSTS list - Проверка списка HSTS.  
9. DNS lookup - Поиск DNS.  
10. ARP process - Процесс ARP.  
11. Opening of a socket - Открытие сокета.  
12. TLS handshake - Установление связи TLS.  
13. If a packet is dropped - Если пакет отброшен?!  
14. HTTP protocol - Протокол HTTP.  
15. HTTP Server Request Handle - Обработка запроса HTTP сервера.  
16. Behind the scenes of the Browser - За кулисами браузера.  
17. Browser - Браузер.  
18. HTML parsing - Парсинг HTML.  
19. CSS interpretation - Интерпретация CSS.  
20. Page Rendering - Рендеринг страницы.  
21. GPU Rendering - Рендеринг процессора.  
22. Window Server - Сервер Windows. 
23. Post-rendering and user-induced execution - Последующий рендеринг и пользовательское выполнение.  

The following sections explain the physical keyboard actions and the OS interrupts.  
В следующих разделах описываются физические действия с клавиатуры и прерывания работы операционной системы.  

## **1. The "g" key is pressed**  
## **1. Нажата клавиша "g"**  

When you press the key "g" the browser receives the event and the auto-complete functions kick in. Depending on your browser's algorithm and if you are in private/incognito mode or not various suggestions will be presented to you in the dropdown below the URL bar. Most of these algorithms sort and prioritize results based on search history, bookmarks, cookies, and popular searches from the internet as a whole. As you are typing "google.com" many blocks of code run and the suggestions will be refined with each keypress. It may even suggest "google.com" before you finish typing it.
При нажатии клавиши "g" браузер получает сообщение о событии и запускает функции автозаполнения. В зависимости от алгоритма работы вашего браузера и от того, находитесь ли вы в режиме приватности / инкогнито или нет, вам будут представлены различные предложения в выпадающем списке под строкой URL. Большинство из этих алгоритмов сортируют и определяют приоритетность результатов на основе истории поиска, закладок, файлов cookie и популярных поисковых запросов в Интернете в целом. При вводе "google.com" запускается множество блоков кода, и предложения будут уточняться с каждым нажатием клавиши. Он может даже предложить "google.com" до того, как вы закончите вводить его.  

## **2. The "enter" key bottoms out**  
## **2. Клавиша "enter" опускается до самого низа**  

To pick a zero point, let's choose the Enter key on the keyboard hitting the bottom of its range. At this point, an electrical circuit specific to the enter key is closed (either directly or capacitively). This allows a small amount of current to flow into the logic circuitry of the keyboard, which scans the state of each key switch, debounces the electrical noise of the rapid intermittent closure of the switch, and converts it to a keycode integer, in this case 13. The keyboard controller then encodes the keycode for transport to the computer. This is now almost universally over a Universal Serial Bus (USB) or Bluetooth connection, but historically has been over PS/2 or ADB connections.
Чтобы выбрать нулевую точку, давайте нажмем клавишу Enter на клавиатуре в нижней части диапазона клавиш. В этот момент электрическая цепь, соответствующая клавише *Enter*, замыкается (либо напрямую, либо емкостно). Это позволяет небольшому количеству тока поступать в логическую схему клавиатуры, которая сканирует состояние каждого переключателя клавиш, устраняет электрические помехи, возникающие при быстром прерывистом замыкании переключателя, и преобразует их в целое число с кодом клавиши, в данном случае 13. Затем контроллер клавиатуры кодирует код клавиши для передачи на компьютер. В настоящее время это почти повсеместно осуществляется через универсальную последовательную шину (USB) или Bluetooth-соединение, но исторически это происходило через PS/2 или ADB-соединения.

### **In the case of the USB keyboard:**  
### **В случае USB-клавиатуры:**  

The USB circuitry of the keyboard is powered by the 5V supply provided over pin 1 from the computer's USB host controller.
The keycode generated is stored by internal keyboard circuitry memory in a register called "endpoint".
The host USB controller polls that "endpoint" every ~10ms (minimum value declared by the keyboard), so it gets the keycode value stored on it.
This value goes to the USB SIE (Serial Interface Engine) to be converted in one or more USB packets that follow the low-level USB protocol.
Those packets are sent by a differential electrical signal over D+ and D- pins (the middle 2) at a maximum speed of 1.5 Mb/s, as an HID (Human Interface Device) device is always declared to be a "low-speed device" (USB 2.0 compliance).
This serial signal is then decoded at the computer's host USB controller, and interpreted by the computer's Human Interface Device (HID) universal keyboard device driver. The value of the key is then passed into the operating system's hardware abstraction layer.  
USB-схема клавиатуры питается от источника питания напряжением 5 В, подключенного к контакту 1 USB-контроллера компьютера. Сгенерированный код ключа сохраняется во внутренней памяти клавиатуры в регистре, называемом "конечная точка". Главный USB-контроллер опрашивает эту "конечную точку" каждые ~10 мс (минимальное значение, объявленное клавиатурой), поэтому он получает сохраненное на нем значение кода ключа. Это значение передается в USB SIE (модуль последовательного интерфейса) для преобразования в один или несколько USB-пакетов, которые соответствуют низкоуровневому USB-протоколу. Эти пакеты передаются с помощью дифференциального электрического сигнала по контактам D+ и D-link (средние 2) с максимальной скоростью 1,5 Мб/с, поскольку устройство HID (устройство для взаимодействия с человеком) всегда считается "низкоскоростным устройством" (соответствует стандарту USB 2.0). Этот последовательный сигнал затем декодируется на главном USB-контроллере компьютера и интерпретируется драйвером универсального клавиатурного устройства HID компьютера. Значение ключа затем передается на уровень аппаратной абстракции операционной системы.  

### **In the case of Virtual Keyboard (as in touch screen devices):**  
### **В случае виртуальной клавиатуры (как в устройствах с сенсорным экраном):**  

When the user puts their finger on a modern capacitive touch screen, a tiny amount of current gets transferred to the finger. This completes the circuit through the electrostatic field of the conductive layer and creates a voltage drop at that point on the screen. The screen controller then raises an interrupt reporting the coordinate of the keypress.
Then the mobile OS notifies the currently focused application of a press event in one of its GUI elements (which now is the virtual keyboard application buttons).
The virtual keyboard can now raise a software interrupt for sending a 'key pressed' message back to the OS.
This interrupt notifies the currently focused application of a 'key pressed' event.  
Когда пользователь прикасается пальцем к современному емкостному сенсорному экрану, к пальцу передается небольшое количество тока. Это замыкает цепь через электростатическое поле проводящего слоя и создает падение напряжения в этой точке экрана. Затем экранный контроллер выдает прерывание, сообщающее о координате нажатия клавиши.  
Затем мобильная операционная система уведомляет приложение, на котором в данный момент сосредоточено внимание, о событии нажатия в одном из элементов графического интерфейса (который теперь является кнопками приложения виртуальной клавиатуры).  
Виртуальная клавиатура теперь может вызывать программное прерывание для отправки сообщения "нажата клавиша" обратно в операционную систему.
Это прерывание уведомляет текущее приложение о событии "нажата клавиша".  

## **3. Interrupt fires [NOT for USB keyboards]**  
## **3. Срабатывает прерывание [Не для USB-клавиатур]**  

The keyboard sends signals on its interrupt request line (IRQ), which is mapped to an interrupt vector (integer) by the interrupt controller. The CPU uses the Interrupt Descriptor Table (IDT) to map the interrupt vectors to functions (interrupt handlers) which are supplied by the kernel. When an interrupt arrives, the CPU indexes the IDT with the interrupt vector and runs the appropriate handler. Thus, the kernel is entered.  
Клавиатура отправляет сигналы в строке запроса на прерывание (IRQ), которая преобразуется контроллером прерываний в вектор прерываний (целое число). Центральный процессор использует таблицу дескрипторов прерываний (IDT) для сопоставления векторов прерываний с функциями (обработчиками прерываний), которые предоставляются ядром. Когда поступает прерывание, центральный процессор индексирует IDT с вектором прерывания и запускает соответствующий обработчик. Таким образом, вступает ядро.  

## **4.1 (On Windows) A WM_KEYDOWN message is sent to the app**  
## **(В Windows) В приложение отправляется сообщение WM_KEYDOWN** 

The HID transport passes the key down event to the KBDHID.sys driver which converts the HID usage into a scancode. In this case, the scan code is VK_RETURN (0x0D). The KBDHID.sys driver interfaces with the KBDCLASS.sys (keyboard class driver). This driver is responsible for handling all keyboard and keypad input in a secure manner. It then calls into Win32K.sys (after potentially passing the message through 3rd party keyboard filters that are installed). This all happens in kernel mode.  
Транспорт HID передает событие нажатия клавиши драйверу KBDHID.sys, который преобразует использование HID в скан-код. В данном случае скан-кодом является VK_RETURN (0x0D). Драйвер KBDHID.sys взаимодействует с KBDCLASS.sys (драйвер класса клавиатуры). Этот драйвер отвечает за безопасную обработку всех данных, вводимых с клавиатуры. Затем он обращается к Win32K.sys (возможно, после прохождения сообщения через установленные фильтры клавиатуры сторонних производителей). Все это происходит в режиме ядра.  

Win32K.sys figures out what window is the active window through the GetForegroundWindow() API. This API provides the window handle of the browser's address box. The main Windows "message pump" then calls SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam). lParam is a bitmask that indicates further information about the keypress: repeat count (0 in this case), the actual scan code (can be OEM dependent, but generally wouldn't be for VK_RETURN), whether extended keys (e.g. alt, shift, ctrl) were also pressed (they weren't), and some other state.  
Win32K.sys определяет, какое окно является активным, с помощью API GetForegroundWindow(). Этот API предоставляет дескриптор окна для адресной строки браузера. Затем основной "поток сообщений" Windows вызывает SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam). lParam - это битовая маска, которая указывает дополнительную информацию о нажатии клавиши: количество повторений (в данном случае 0), фактический код сканирования (может зависеть от производителя, но, как правило, не относится к VK_RETURN), были ли также нажаты дополнительные клавиши (например, alt, shift, ctrl) (они не были нажаты), и какое-то другое состояние.  

The Windows SendMessage API is a straightforward function that adds the message to a queue for the particular window handle (hWnd). Later, the main message processing function (called a WindowProc) assigned to the hWnd is called in order to process each message in the queue.  
Windows SendMessage API - это простая функция, которая добавляет сообщение в очередь для конкретного дескриптора окна (hWnd). Позже вызывается основная функция обработки сообщений (называемая WindowProc), назначенная hWnd, для обработки каждого сообщения в очереди.  

The window (hWnd) that is active is actually an edit control and the WindowProc in this case has a message handler for WM_KEYDOWN messages. This code looks within the 3rd parameter that was passed to SendMessage (wParam) and, because it is VK_RETURN knows the user has hit the ENTER key.
Активное окно (hWnd) на самом деле является элементом управления редактированием, и WindowProc в этом случае имеет обработчик сообщений для сообщений WM_KEYDOWN. Этот код просматривается в пределах 3-го параметра, который был передан в SendMessage (wParam), и, поскольку именно VK_RETURN знает, что пользователь нажал клавишу ENTER.  

### 4.1. (On OS X) A KeyDown NSEvent is sent to the app  
### 4.1. (В OS X) В приложение отправляется сообщение о нажатии клавиши NSEvent  
The interrupt signal triggers an interrupt event in the I/O Kit kext keyboard driver. The driver translates the signal into a key code which is passed to the OS X WindowServer process. Resultantly, the WindowServer dispatches an event to any appropriate (e.g. active or listening) applications through their Mach port where it is placed into an event queue. Events can then be read from this queue by threads with sufficient privileges calling the mach_ipc_dispatch function. This most commonly occurs through, and is handled by, an NSApplication main event loop, via an NSEvent of NSEventType KeyDown.
Сигнал прерывания запускает событие прерывания в драйвере клавиатуры kext Kit ввода-вывода. Драйвер преобразует сигнал в код клавиши, который передается серверному процессу OS X WindowServer. В результате оконный сервер отправляет событие любым подходящим приложениям (например, активным или прослушивающим) через их Mach-порт, где оно помещается в очередь событий. Затем события могут быть считаны из этой очереди потоками с достаточными привилегиями, вызывающими функцию mach_ipc_dispatch. Чаще всего это происходит через основной цикл обработки событий NSApplication и обрабатывается им с помощью NSEvent из NSEventType KeyDown.  

### 4.2. (On GNU/Linux) the Xorg server listens for keycodes  
### 4.2. (В GNU/Linux) сервер Xorg прослушивает коды клавиш  
When a graphical X server is used, X will use the generic event driver evdev to acquire the keypress. A re-mapping of keycodes to scancodes is made with X server specific keymaps and rules. When the scancode mapping of the key pressed is complete, the X server sends the character to the window manager (DWM, metacity, i3, etc), so the window manager in turn sends the character to the focused window. The graphical API of the window that receives the character prints the appropriate font symbol in the appropriate focused field.
Когда используется графический сервер X, X будет использовать универсальный драйвер событий evdev для получения нажатия клавиши. Повторное сопоставление кодов клавиш со сканкодами выполняется с использованием специальных сопоставлений клавиш и правил для X-сервера. Когда отображение сканкода нажатой клавиши завершено, X-сервер отправляет символ оконному менеджеру (DWM, metacity, i3 и т.д.), а оконный менеджер, в свою очередь, отправляет символ в сфокусированное окно. Графический API окна, принимающего символ, выводит соответствующий символ шрифта в соответствующем выделенном поле.  

## 5. Parse URL  
## 5. Разобрать URL-адрес  
The browser now has the following information contained in the URL (Uniform Resource Locator):
Теперь браузер имеет следующую информацию, содержащуюся в URL-адресе (единый указатель ресурсов):

Protocol "http"  
Use 'Hyper Text Transfer Protocol'  
Resource "/"  
Retrieve main (index) page  
Протокол "http"
Используйте "Протокол передачи гипертекста"
Ресурс "/"
Извлеките главную (индексную) страницу  

## 6. Is it a URL or a search term?  
## 6. Это URL-адрес или поисковый запрос?  
When no protocol or valid domain name is given the browser proceeds to feed the text given in the address box to the browser's default web search engine. In many cases the URL has a special piece of text appended to it to tell the search engine that it came from a particular browser's URL bar.
Если не указан протокол или действительное доменное имя, браузер отправляет текст, указанный в адресной строке, в поисковую систему браузера по умолчанию. Во многих случаях к URL-адресу добавляется специальный фрагмент текста, сообщающий поисковой системе, что он взят из строки URL-адреса конкретного браузера.  

## 7. Convert non-ASCII Unicode characters in the hostname  
## 7. Преобразуйте символы Юникода, отличные от ASCII, в имя хоста  

The browser checks the hostname for characters that are not in a-z, A-Z, 0-9, -, or ..
Since the hostname is google.com there won't be any, but if there were the browser would apply Punycode encoding to the hostname portion of the URL.  
Браузер проверяет имя хоста на наличие символов, отличных от a-z, A-Z, 0-9, -, или ..
Поскольку имя хоста google.com, его там не будет, но если бы оно было, браузер применил бы кодировку Punycode к части URL, содержащей имя хоста.  

## 8. Check HSTS list  
## 8. Проверка списка HSTS (HTTP Strict Transport Security)  

The browser checks its "preloaded HSTS (HTTP Strict Transport Security)" list. This is a list of websites that have requested to be contacted via HTTPS only.
If the website is in the list, the browser sends its request via HTTPS instead of HTTP. Otherwise, the initial request is sent via HTTP. (Note that a website can still use the HSTS policy without being in the HSTS list. The first HTTP request to the website by a user will receive a response requesting that the user only send HTTPS requests. However, this single HTTP request could potentially leave the user vulnerable to a downgrade attack, which is why the HSTS list is included in modern web browsers.)  
Браузер проверяет свой список "предварительно загруженных HSTS (HTTP Strict Transport Security)". Это список веб-сайтов, которые запросили доступ только по протоколу HTTPS.
Если веб-сайт есть в списке, браузер отправляет запрос по протоколу HTTPS, а не по протоколу HTTP. В противном случае первоначальный запрос отправляется по протоколу HTTP. (Обратите внимание, что веб-сайт все равно может использовать политику HSTS, не находясь в списке HSTS. При первом HTTP-запросе пользователя к веб-сайту будет получен ответ с просьбой отправлять только HTTPS-запросы. Однако этот единственный HTTP-запрос потенциально может сделать пользователя уязвимым для атаки с понижением версии, поэтому в современных веб-браузерах включен список HSTS.)  

## 9. DNS lookup  
## 9. Поиск по DNS  

Browser checks if the domain is in its cache. (to see the DNS Cache in Chrome, go to chrome://net-internals/#dns).
If not found, the browser calls gethostbyname library function (varies by OS) to do the lookup.
gethostbyname checks if the hostname can be resolved by reference in the local hosts file (whose location varies by OS) before trying to resolve the hostname through DNS.
If gethostbyname does not have it cached nor can find it in the hosts file then it makes a request to the DNS server configured in the network stack. This is typically the local router or the ISP's caching DNS server.
Браузер проверяет, есть ли домен в его кэше. (чтобы просмотреть кэш DNS в Chrome, перейдите по ссылке chrome://net-internals/#dns).
Если он не найден, браузер вызывает библиотечную функцию gethostbyname (зависит от операционной системы) для выполнения поиска.
gethostbyname проверяет, можно ли разрешить имя хоста по ссылке в локальном файле hosts (расположение которого зависит от операционной системы), прежде чем пытаться разрешить имя хоста через DNS.
Если gethostbyname не сохранен в кэше и не может быть найден в файле hosts, он отправляет запрос на DNS-сервер, настроенный в сетевом стеке. Обычно это локальный маршрутизатор или кэширующий DNS-сервер интернет-провайдера.  

If the DNS server is on the same subnet the network library follows the ARP process below for the DNS server.  
If the DNS server is on a different subnet, the network library follows the ARP process below for the default gateway IP.  
Если DNS-сервер находится в той же подсети, сетевая библиотека выполняет описанный ниже процесс ARP для DNS-сервера.  
Если DNS-сервер находится в другой подсети, сетевая библиотека выполняет описанный ниже процесс ARP для IP-адреса шлюза по умолчанию.  

## 10. ARP process  
## 10. Процесс ARP (протокол разрешения адресов)  

In order to send an ARP (Address Resolution Protocol) broadcast the network stack library needs the target IP address to lookup. It also needs to know the MAC address of the interface it will use to send out the ARP broadcast.
Для отправки широковещательной передачи по протоколу ARP (протокол разрешения адресов) библиотеке сетевого стека необходим целевой IP-адрес для поиска. Ей также необходимо знать MAC-адрес интерфейса, который она будет использовать для отправки широковещательной передачи по протоколу ARP.  

The ARP cache is first checked for an ARP entry for our target IP. If it is in the cache, the library function returns the result: Target IP = MAC.  
Сначала кэш ARP проверяется на наличие записи ARP для нашего целевого IP. Если она есть в кэше, библиотечная функция возвращает результат: Целевой IP = MAC.  
If the entry is not in the ARP cache:  
Если записи нет в кэше ARP:  

The route table is looked up, to see if the Target IP address is on any of the subnets on the local route table. If it is, the library uses the interface associated with that subnet. If it is not, the library uses the interface that has the subnet of our default gateway. The MAC address of the selected network interface is looked up.
The network library sends a Layer 2 (data link layer of the OSI model) ARP request:
Выполняется просмотр таблицы маршрутов, чтобы узнать, находится ли целевой IP-адрес в какой-либо из подсетей в локальной таблице маршрутов. Если это так, библиотека использует интерфейс, связанный с этой подсетью. Если это не так, библиотека использует интерфейс, который имеет подсеть нашего шлюза по умолчанию. Выполняется поиск MAC-адреса выбранного сетевого интерфейса. Сетевая библиотека отправляет ARP-запрос уровня 2 (канальный уровень модели OSI):  

**ARP Request:**    
Sender MAC: interface:mac:address:here  
Sender IP: interface.ip.goes.here  
Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)  
Target IP: target.ip.goes.here  
Depending on what type of hardware is between the computer and the router:  
В зависимости от того, какой тип оборудования находится между компьютером и маршрутизатором:  

**Directly connected:**   
**Прямое подключение:**  

If the computer is directly connected to the router the router response with an ARP Reply (see below) Hub:  
Если компьютер напрямую подключен к маршрутизатору, маршрутизатор ответит ARP-ответом (см. ниже) Хаб:  

If the computer is connected to a hub, the hub will broadcast the ARP request out of all other ports. If the router is connected on the same "wire", it will respond with an ARP Reply (see below).  
Если компьютер подключен к концентратору, концентратор будет транслировать запрос ARP со всех других портов. Если маршрутизатор подключен к тому же "проводу", он отправит ответ ARP (см. ниже).  
Switch:  
Переключатель:  

If the computer is connected to a switch, the switch will check its local CAM/MAC table to see which port has the MAC address we are looking for. If the switch has no entry for the MAC address it will rebroadcast the ARP request to all other ports.  
If the switch has an entry in the MAC/CAM table it will send the ARP request to the port that has the MAC address we are looking for.  
If the router is on the same "wire", it will respond with an ARP Reply (see below)  
Если компьютер подключен к коммутатору, коммутатор проверит свою локальную таблицу CAM/MAC, чтобы узнать, какой порт содержит искомый MAC-адрес. Если у коммутатора нет данных для MAC-адреса, он повторно передаст запрос ARP на все остальные порты.  
Если у коммутатора есть запись в таблице MAC/CAM, он отправит запрос ARP на порт, который имеет искомый MAC-адрес.  
Если маршрутизатор подключен к тому же "проводу", он отправит ответ ARP (см. ниже).  

**ARP Reply:**  
**ARP-ответ:**  

Sender MAC: target:mac:address:here  
Sender IP: target.ip.goes.here  
Target MAC: interface:mac:address:here  
Target IP: interface.ip.goes.here  
Now that the network library has the IP address of either our DNS server or the default gateway it can resume its DNS process:  
MAC отправителя: адрес назначения:mac-адрес:здесь  
IP отправителя: target.ip.goes.здесь  
Целевой MAC: интерфейс:mac:адрес:здесь  
Целевой IP: interface.ip.goes.здесь  
Теперь, когда у сетевой библиотеки есть IP-адрес либо нашего DNS-сервера, либо шлюза по умолчанию, она может возобновить процесс tsDNS:  

The DNS client establishes a socket to UDP port 53 on the DNS server, using a source port above 1023.  
If the response size is too large, TCP will be used instead.  
If the local/ISP DNS server does not have it, then a recursive search is requested and that flows up the list of DNS servers until the SOA is reached, and if found an answer is returned.  
DNS-клиент устанавливает сокет на UDP-порт 53 на DNS-сервере, используя исходный порт выше 1023.  
Если размер ответа слишком велик, вместо него будет использоваться протокол TCP.  
Если у локального DNS-сервера/интернет-провайдера его нет, то запрашивается рекурсивный поиск, который перемещается вверх по списку DNS-серверов до тех пор, пока не будет достигнут SOA, и, если он найден, возвращается ответ.  

## 11. Opening of a socket  
## 11. Открытие сокета  

Once the browser receives the IP address of the destination server, it takes that and the given port number from the URL (the HTTP protocol defaults to port 80, and HTTPS to port 443), and makes a call to the system library function named socket and requests a TCP socket stream - AF_INET/AF_INET6 and SOCK_STREAM.  
Как только браузер получает IP-адрес конечного сервера, он берет его и указанный номер порта из URL-адреса (по умолчанию для протокола HTTP используется порт 80, а для HTTPS - порт 443), вызывает функцию системной библиотеки с именем socket и запрашивает поток сокетов TCP - AF_INET/AF_INET6 и SOCK_STREAM.  

This request is first passed to the Transport Layer where a TCP segment is crafted. The destination port is added to the header, and a source port is chosen from within the kernel's dynamic port range (ip_local_port_range in Linux).  
This segment is sent to the Network Layer, which wraps an additional IP header. The IP address of the destination server as well as that of the current machine is inserted to form a packet.  
Этот запрос сначала передается на транспортный уровень, где создается сегмент TCP. Порт назначения добавляется в заголовок, а порт источника выбирается из динамического диапазона портов ядра (ip_local_port_range в Linux).  
Этот сегмент отправляется на сетевой уровень, который передает дополнительный IP-заголовок. Для формирования пакета вводятся IP-адреса сервера назначения, а также текущего компьютера.  

The packet next arrives at the Link Layer. A frame header is added that includes the MAC address of the machine's NIC as well as the MAC address of the gateway (local router). As before, if the kernel does not know the MAC address of the gateway, it must broadcast an ARP query to find it.  
At this point the packet is ready to be transmitted through either:  
Затем пакет поступает на канальный уровень. Добавляется заголовок фрейма, который включает MAC-адрес сетевой карты компьютера, а также MAC-адрес шлюза (локального маршрутизатора). Как и прежде, если ядро не знает MAC-адрес шлюза, оно должно отправить запрос ARP, чтобы найти его.  
На этом этапе пакет готов к передаче через любой из:  

Ethernet  
Локальная сеть  
WiFi  
Wi-Fi  
Cellular data network  
Сотовая сеть передачи данных  

For most home or small business Internet connections the packet will pass from your computer, possibly through a local network, and then through a modem (MOdulator/DEModulator) which converts digital 1's and 0's into an analog signal suitable for transmission over telephone, cable, or wireless telephony connections. On the other end of the connection is another modem which converts the analog signal back into digital data to be processed by the next network node where the from and to addresses would be analyzed further.  
Для большинства подключений к Интернету для дома или малого бизнеса пакет передается с вашего компьютера, возможно, через локальную сеть, а затем через модем (модулятор/демодулятор), который преобразует цифровые 1 и 0 в аналоговый сигнал, пригодный для передачи по телефонным, кабельным или беспроводным телефонным соединениям. На другом конце соединения находится другой модем, который преобразует аналоговый сигнал обратно в цифровые данные для обработки следующим узлом сети, где адреса "от" и "к" будут проанализированы дополнительно.  

Most larger businesses and some newer residential connections will have fiber or direct Ethernet connections in which case the data remains digital and is passed directly to the next network node for processing.  
Большинство крупных предприятий и некоторые новые жилые комплексы имеют оптоволоконные или прямые Ethernet-соединения, и в этом случае данные остаются цифровыми и передаются непосредственно на следующий сетевой узел для обработки.  

Eventually, the packet will reach the router managing the local subnet. From there, it will continue to travel to the autonomous system's (AS) border routers, other ASes, and finally to the destination server. Each router along the way extracts the destination address from the IP header and routes it to the appropriate next hop. The time to live (TTL) field in the IP header is decremented by one for each router that passes. The packet will be dropped if the TTL field reaches zero or if the current router has no space in its queue (perhaps due to network congestion).  
В конце концов, пакет достигнет маршрутизатора, управляющего локальной подсетью. Оттуда он продолжит путь к пограничным маршрутизаторам автономной системы (AS), в других случаях и, наконец, к целевому серверу. Каждый маршрутизатор на своем пути извлекает адрес назначения из IP-заголовка и перенаправляет его на соответствующий следующий переход. Поле time to live (TTL) в IP-заголовке уменьшается на единицу для каждого проходящего маршрутизатора. Пакет будет отброшен, если поле TTL достигнет нуля или если у текущего маршрутизатора не будет свободного места в очереди (возможно, из-за перегрузки сети).  

This send and receive happens multiple times following the TCP connection flow:  
Эта отправка и получение происходят несколько раз в соответствии с потоком TCP-соединений:  

Client chooses an initial sequence number (ISN) and sends the packet to the server with the SYN bit set to indicate it is setting the ISN  
Клиент выбирает начальный порядковый номер (ISN) и отправляет пакет на сервер с установленным битом SYN, указывающим на то, что он устанавливает ISN
Server receives SYN and if it's in an agreeable mood:
Server chooses its own initial sequence number
Server sets SYN to indicate it is choosing its ISN
Server copies the (client ISN +1) to its ACK field and adds the ACK flag to indicate it is acknowledging receipt of the first packet  
Сервер получает SYN и, если он в хорошем настроении:
Сервер сам выбирает свой начальный порядковый номер
Сервер устанавливает SYN, чтобы указать, что он выбирает свой ISN
Сервер копирует (client ISN +1) в свое поле подтверждения и добавляет флаг подтверждения, чтобы указать, что он подтверждает получение первого пакета  

Client acknowledges the connection by sending a packet:
Increases its own sequence number
Increases the receiver acknowledgment number
Sets ACK field  
Клиент подтверждает соединение, отправляя пакет:
Увеличивает свой собственный порядковый номер
Увеличивает номер подтверждения получателя
Устанавливает поле подтверждения  

Data is transferred as follows:  
Передача данных осуществляется следующим образом:  

As one side sends N data bytes, it increases its SEQ by that number  
Когда одна сторона отправляет N байт данных, она увеличивает свой SEQ на это число  

When the other side acknowledges receipt of that packet (or a string of packets), it sends an ACK packet with the ACK value equal to the last received sequence from the other  
Когда другая сторона подтверждает получение этого пакета (или цепочки пакетов), она отправляет подтверждающий пакет со значением подтверждения, равным последней полученной последовательности от другой стороны  

To close the connection:  
Чтобы закрыть соединение:  

The closer sends a FIN packet  
Closer отправляет пакет FIN  

The other sides ACKs the FIN packet and sends its own FIN
The closer acknowledges the other side's FIN with an ACK   
Другая сторона подтверждает получение пакета FIN и отправляет свой собственный FIN
Closer подтверждает подтверждение FIN другой стороны  

## 12. TLS handshake  
## 12. Рукопожатие по протоколу TLS  

The client computer sends a ClientHello message to the server with its Transport Layer Security (TLS) version, list of cipher algorithms and compression methods available.
The server replies with a ServerHello message to the client with the TLS version, selected cipher, selected compression methods and the server's public certificate signed by a CA (Certificate Authority). The certificate contains a public key that will be used by the client to encrypt the rest of the handshake until a symmetric key can be agreed upon.
The client verifies the server digital certificate against its list of trusted CAs. If trust can be established based on the CA, the client generates a string of pseudo-random bytes and encrypts this with the server's public key. These random bytes can be used to determine the symmetric key.  
The server decrypts the random bytes using its private key and uses these bytes to generate its own copy of the symmetric master key.  
The client sends a Finished message to the server, encrypting a hash of the transmission up to this point with the symmetric key.  
The server generates its own hash, and then decrypts the client-sent hash to verify that it matches. If it does, it sends its own Finished message to the client, also encrypted with the symmetric key.  
From now on the TLS session transmits the application (HTTP) data encrypted with the agreed symmetric key.  
Клиентский компьютер отправляет серверу сообщение ClientHello с указанием версии протокола Transport Layer Security (TLS), списка доступных алгоритмов шифрования и методов сжатия.
Сервер отправляет клиенту сообщение ServerHello с версией TLS, выбранным шифром, выбранными методами сжатия и открытым сертификатом сервера, подписанным Центром сертификации (CA). Сертификат содержит открытый ключ, который будет использоваться клиентом для шифрования остальной части квитирования до тех пор, пока не будет согласован симметричный ключ.
Клиент проверяет цифровой сертификат сервера на соответствие своему списку доверенных центров сертификации. Если доверие может быть установлено на основе центра сертификации, клиент генерирует строку псевдослучайных байтов и шифрует ее с помощью открытого ключа сервера. Эти случайные байты могут быть использованы для определения симметричного ключа.
Сервер расшифровывает случайные байты с помощью своего закрытого ключа и использует эти байты для создания собственной копии симметричного мастер-ключа.
Клиент отправляет готовое сообщение на сервер, зашифровывая хэш-код, который был передан до этого момента, с помощью симметричного ключа.  
Сервер генерирует свой собственный хэш, а затем расшифровывает отправленный клиентом хэш, чтобы убедиться в его совпадении. Если это так, он отправляет клиенту свое собственное готовое сообщение, также зашифрованное симметричным ключом.  

## 13. If a packet is dropped  
## Если пакет пропущен  

Sometimes, due to network congestion or flaky hardware connections, TLS packets will be dropped before they get to their final destination. The sender then has to decide how to react. The algorithm for this is called TCP congestion control. This varies depending on the sender; the most common algorithms are cubic on newer operating systems and New Reno on almost all others.  
С этого момента сеанс TLS передает данные приложения (HTTP), зашифрованные с помощью согласованного симметричного ключа.
Иногда из-за перегрузки сети или сбоев в подключении оборудования пакеты TLS отбрасываются до того, как они дойдут до конечного пункта назначения. Затем отправитель должен решить, как реагировать. Алгоритм для этого называется TCP congestion control. Это зависит от отправителя; наиболее распространенными алгоритмами являются cubic в новых операционных системах и New Reno почти во всех остальных.  

Client chooses a congestion window based on the maximum segment size (MSS) of the connection.  
For each packet acknowledged, the window doubles in size until it reaches the 'slow-start threshold'. In some implementations, this threshold is adaptive.
After reaching the slow-start threshold, the window increases additively for each packet acknowledged. If a packet is dropped, the window reduces exponentially until another packet is acknowledged.  
Клиент выбирает время перегрузки на основе максимального размера сегмента (MSS) соединения.
Для каждого подтвержденного пакета размер окна увеличивается вдвое, пока не достигнет "порога медленного запуска". В некоторых реализациях этот порог является адаптивным.
После достижения порогового значения медленного запуска окно увеличивается для каждого подтвержденного пакета. Если пакет отбрасывается, окно уменьшается экспоненциально до тех пор, пока не будет подтвержден другой пакет.  

## 14. HTTP protocol.
Протокол HTTP  

If the web browser used was written by Google, instead of sending an HTTP request to retrieve the page, it will send a request to try and negotiate with the server an "upgrade" from HTTP to the SPDY protocol.  
Если используемый веб-браузер был создан компанией Google, то вместо отправки HTTP-запроса для получения страницы он отправит запрос на попытку согласовать с сервером "обновление" с HTTP до протокола SPDY.  

If the client is using the HTTP protocol and does not support SPDY, it sends a request to the server of the form:  
Если клиент использует протокол HTTP и не поддерживает SPDY, он отправляет запрос на сервер вида:  

**GET / HTTP/1.1**  
**Host: google.com**  
**Connection: close**  
[other headers]  
**ПОЛУЧИТЬ / HTTP/1.1**  
**Хост: google.com**  
**Соединение: закрыть**  
**[другие заголовки]**  
where [other headers] refers to a series of colon-separated key-value pairs formatted as per the HTTP specification and separated by single newlines. (This assumes the web browser being used doesn't have any bugs violating the HTTP spec. This also assumes that the web browser is using HTTP/1.1, otherwise it may not include the Host header in the request and the version specified in the GET request will either be HTTP/1.0 or HTTP/0.9.)  
где [другие заголовки] относятся к серии пар ключ-значение, разделенных двоеточием, отформатированных в соответствии со спецификацией HTTP и разделенных отдельными символами новой строки. (Предполагается, что в используемом веб-браузере нет ошибок, нарушающих спецификацию HTTP. Это также предполагает, что веб-браузер использует HTTP/1.1, в противном случае он может не включать заголовок Host в запрос, и версия, указанная в запросе GET, будет либо HTTP/1.0, либо HTTP/0.9.)  

HTTP/1.1 defines the "close" connection option for the sender to signal that the connection will be closed after completion of the response. For example, Connection: close
HTTP/1.1 applications that do not support persistent connections MUST include the "close" connection option in every message.  
HTTP/1.1 определяет параметр "закрыть" соединение, чтобы отправитель мог сигнализировать о том, что соединение будет закрыто после завершения ответа. Например, Connection: закрыть
Приложения HTTP/1.1, которые не поддерживают постоянные соединения, должны включать параметр "закрыть" соединение в каждое сообщение.  

After sending the request and headers, the web browser sends a single blank newline to the server indicating that the content of the request is done.  
После отправки запроса и заголовков веб-браузер отправляет на сервер одну пустую строку перевода текста, указывающую на то, что содержание запроса выполнено.  

The server responds with a response code denoting the status of the request and responds with a response of the form:  
Сервер выдает код ответа, обозначающий статус запроса, и выдает ответ следующего вида:  

**200 OK**  
[response headers]  
**200 ОК**  
[заголовки ответа]  

Followed by a single newline, and then sends a payload of the HTML content of www.google.com. The server may then either close the connection, or if headers sent by the client requested it, keep the connection open to be reused for further requests.  
If the HTTP headers sent by the web browser included sufficient information for the webserver to determine if the version of the file cached by the web browser has been unmodified since the last retrieval (ie. if the web browser included an ETag header), it may instead respond with a request of the form:  
За которыми следует одна новая строка, а затем отправляется полезная нагрузка в виде HTML-содержимого www.google.com. Затем сервер может либо закрыть соединение, либо, если заголовки, отправленные клиентом, запрашивают это, сохранить соединение открытым для повторного использования для дальнейших запросов.  
Если HTTP-заголовки, отправленные веб-браузером, содержат достаточную информацию для веб-сервера, чтобы определить, была ли версия файла, кэшированного веб-браузером, неизменена с момента последнего извлечения (т.е. если веб-браузер включил заголовок ETag), он может вместо этого ответить запросом формы:  

**304 Not Modified**  
304 Не изменено  
[response headers] and no payload, and the web browser instead retrieve the HTML from its cache.  
After parsing the HTML, the web browser (and server) repeats this process for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page, except instead of GET / HTTP/1.1 the request will be GET /$(URL relative to www.google.com) HTTP/1.1.  
If the HTML referenced a resource on a different domain than www.google.com, the web browser goes back to the steps involved in resolving the other domain, and follows all steps up to this point for that domain. The Host header in the request will be set to the appropriate server name instead of google.com.  
[заголовки ответов] и нет полезной нагрузки, а веб-браузер вместо этого извлекает HTML из своего кэша.  
После синтаксического анализа HTML веб-браузер (и сервер) повторяет этот процесс для каждого ресурса (изображения, CSS, favicon.ico и т.д.), на который ссылается HTML-страница, за исключением того, что вместо GET / HTTP/1.1 запрос будет GET /$(URL относительно www.google.com) HTTP/1.1.  
Если HTML-код ссылается на ресурс, расположенный в домене, отличном от www.google.com, веб-браузер возвращается к шагам, связанным с разрешением доступа к другому домену, и выполняет все действия до этого момента для этого домена. В заголовке Host в запросе будет указано соответствующее имя сервера вместо google.com.  

## 15. HTTP Server Request Handle  
## 15. Дескриптор запроса HTTP-сервера  

The HTTPD (HTTP Daemon) server is the one handling the requests/responses on the server-side. The most common HTTPD servers are Apache or nginx for Linux and IIS for Windows.  
Сервер HTTPD (HTTP-демон) обрабатывает запросы и ответы на стороне сервера. Наиболее распространенными HTTPD-серверами являются Apache или nginx для Linux и IIS для Windows.  
The HTTPD (HTTP Daemon) receives the request.  
Запрос получает HTTPD (HTTP-демон).  

The server breaks down the request to the following parameters:  
Сервер разбивает запрос на следующие параметры:  

HTTP Request Method (either GET, HEAD, POST, PUT, PATCH, DELETE, CONNECT, OPTIONS, or TRACE). In the case of a URL entered directly into the address bar, this will be GET.
Domain, in this case - google.com.  
Метод HTTP-запроса (GET, HEAD, POST, PUT, PATCH, DELETE, CONNECT, OPTIONS или TRACE). В случае URL-адреса, введенного непосредственно в адресную строку, это будет GET.
Домен, в данном случае - google.com.  

Requested path/page, in this case - / (as no specific path/page was requested, / is the default path).  
Запрашиваемый путь/страница, в данном случае - / (поскольку конкретный путь/страница не запрашивались, по умолчанию используется путь /).  

The server verifies that there is a Virtual Host configured on the server that corresponds with google.com.  
The server verifies that google.com can accept GET requests.  
The server verifies that the client is allowed to use this method (by IP, authentication, etc.).  
Сервер проверяет, настроен ли на сервере виртуальный хост, соответствующий google.com.  
Сервер проверяет, может ли google.com принимать запросы GET.  
Сервер проверяет, разрешено ли клиенту использовать этот метод (по IP, аутентификации и т.д.).  

If the server has a rewrite module installed (like mod_rewrite for Apache or URL Rewrite for IIS), it tries to match the request against one of the configured rules. If a matching rule is found, the server uses that rule to rewrite the request.  
The server goes to pull the content that corresponds with the request, in our case it will fall back to the index file, as "/" is the main file (some cases can override this, but this is the most common method).  
The server parses the file according to the handler. If Google is running on PHP, the server uses PHP to interpret the index file, and streams the output to the client.
Behind the scenes of the Browser  
Если на сервере установлен модуль перезаписи (например, mod_rewrite для Apache или URL Rewrite для IIS), он пытается сопоставить запрос с одним из настроенных правил. Если найдено подходящее правило, сервер использует это правило для перезаписи запроса.
Сервер извлекает содержимое, соответствующее запросу, в нашем случае оно возвращается к индексному файлу, поскольку "/" является основным файлом (в некоторых случаях это можно переопределить, но это наиболее распространенный метод).
Сервер анализирует файл в соответствии с обработчиком. Если Google работает на PHP, сервер использует PHP для интерпретации индексного файла и передает выходные данные клиенту.
За кулисами браузера  

Once the server supplies the resources (HTML, CSS, JS, images, etc.) to the browser it undergoes the below process:  
Как только сервер предоставляет ресурсы (HTML, CSS, JS, изображения и т.д.) браузеру, он выполняет описанный ниже процесс:  

## 16. Parsing - HTML, CSS, JS  
## 16. Синтаксический анализ - HTML, CSS, JS  

Rendering - Construct DOM Tree → Render Tree → Layout of Render Tree → Painting the render tree Browser  
The browser's functionality is to present the web resource you choose, by requesting it from the server and displaying it in the browser window. The resource is usually an HTML document, but may also be a PDF, image, or some other type of content. The location of the resource is specified by the user using a URI (Uniform Resource Identifier).  
Рендеринг - Построение дерева DOM → Дерево рендеринга → Макет дерева рендеринга → Отображение дерева рендеринга в браузере  
Функциональность браузера заключается в представлении выбранного вами веб-ресурса путем запроса его с сервера и отображения в окне браузера. Ресурс обычно представляет собой HTML-документ, но также может быть в формате PDF, с изображением или каким-либо другим типом содержимого. Местоположение ресурса определяется пользователем с помощью URI (Uniform Resource Identifier).  

The way the browser interprets and displays HTML files is specified in the HTML and CSS specifications. These specifications are maintained by the W3C (World Wide Web Consortium) organization, which is the standards organization for the web.  
Способ, которым браузер интерпретирует и отображает HTML-файлы, указан в спецификациях HTML и CSS. Эти спецификации поддерживаются организацией W3C (World Wide Web Consortium), которая является организацией по стандартизации в Интернете.  

Browser user interfaces have a lot in common with each other. Among the common user interface elements are:  
Пользовательские интерфейсы браузеров имеют много общего друг с другом. К числу общих элементов пользовательского интерфейса относятся:  

An address bar for inserting a URI  
Адресная строка для ввода URI  

Back and forward buttons  
Bookmarking options  
Кнопки "Назад" и "Вперед"  
Параметры закладок  

Refresh and stop buttons for refreshing or stopping the loading of current documents  
Home button that takes you to your home page  
Browser High-Level Structure  
Кнопки "Обновить" и "Остановить" для обновления или остановки загрузки текущих документов  
Кнопка "Домой", которая приведет вас на вашу домашнюю страницу  
Высокоуровневая структура браузера  

The components of the browsers are:  
Компонентами браузеров являются:  

User interface: The user interface includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser display except the window where you see the requested page.  
Пользовательский интерфейс: Пользовательский интерфейс включает в себя адресную строку, кнопки возврата/перемотки вперед, меню закладок и т.д. Все части экрана браузера, кроме окна, в котором вы видите запрашиваемую страницу.  

Browser engine: The browser engine marshals actions between the UI and the rendering engine.  
Движок браузера: Движок браузера управляет действиями между пользовательским интерфейсом и движком рендеринга.  

Rendering engine: The rendering engine is responsible for displaying requested content. For example if the requested content is HTML, the rendering engine parses HTML and CSS, and displays the parsed content on the screen.  
Движок рендеринга: движок рендеринга отвечает за отображение запрошенного контента. Например, если запрошенный контент является HTML, движок рендеринга анализирует HTML и CSS и отображает обработанный контент на экране.  

Networking: The networking handles network calls such as HTTP requests, using different implementations for different platforms behind a platform-independent interface.
UI backend: The UI backend is used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform-specific. Underneath it uses operating system user interface methods.  
Сетевое взаимодействие: Сеть обрабатывает сетевые вызовы, такие как HTTP-запросы, используя различные реализации для разных платформ, используя независимый от платформы интерфейс.  
Внутренний интерфейс пользовательского интерфейса: Внутренний интерфейс пользовательского интерфейса используется для создания базовых виджетов, таких как поля со списком и окна. Этот внутренний интерфейс предоставляет общий интерфейс, который не зависит от платформы. В своей основе он использует методы пользовательского интерфейса операционной системы.  

JavaScript engine: The JavaScript engine is used to parse and execute JavaScript code.  
Data storage: The data storage is a persistence layer. The browser may need to save all sorts of data locally, such as cookies. Browsers also support storage mechanisms such as localStorage, IndexedDB, WebSQL and FileSystem.  
Движок JavaScript: Движок JavaScript используется для анализа и выполнения кода JavaScript.  
Хранение данных: Хранилище данных представляет собой постоянный уровень. Браузеру может потребоваться локальное сохранение всех видов данных, таких как файлы cookie. Браузеры также поддерживают такие механизмы хранения, как localStorage, IndexedDB, WebSQL и файловая система.  

## 17. HTML parsing  
## 17. Синтаксический анализ HTML  

The rendering engine starts getting the contents of the requested document from the networking layer. This will usually be done in 8kB chunks.  
The primary job of the HTML parser is to parse the HTML markup into a parse tree.  
The output tree (the "parse tree") is a tree of DOM element and attribute nodes. DOM is short for Document Object Model. It is the object presentation of the HTML document and the interface of HTML elements to the outside world like JavaScript. The root of the tree is the "Document" object. Prior to any manipulation via scripting, the DOM has an almost one-to-one relation to the markup.  
Механизм рендеринга начинает получать содержимое запрошенного документа с сетевого уровня. Обычно это выполняется фрагментами по 8 Кб.  
Основная задача HTML-анализатора - преобразовать HTML-разметку в дерево синтаксического анализа.  
Дерево вывода ("дерево синтаксического анализа") - это дерево элементов DOM и узлов атрибутов. DOM - это сокращение от Document Object Model. Это объектное представление HTML-документа и интерфейс HTML-элементов с внешним миром, такой как JavaScript. Корнем дерева является объект "Document". Перед выполнением любых манипуляций с помощью сценариев DOM имеет почти однозначное отношение к разметке.  

The parsing algorithm HTML cannot be parsed using the regular top-down or bottom-up parsers.  
Алгоритм синтаксического анализа HTML не может быть проанализирован с помощью обычных синтаксических анализаторов "сверху вниз" или "снизу вверх".  

The reasons are:  
Причины в том, что:  

The forgiving nature of the language.  
Язык прост в использовании.  

The fact that browsers have traditional error tolerance to support well known cases of invalid HTML.  
Тот факт, что браузеры традиционно допускают ошибки для поддержки хорошо известных случаев некорректного HTML.  

The parsing process is reentrant. For other languages, the source doesn't change during parsing, but in HTML, dynamic code (such as script elements containing document.write() calls) can add extra tokens, so the parsing process actually modifies the input.  
Unable to use the regular parsing techniques, the browser utilizes a custom parser for parsing HTML. The parsing algorithm is described in detail by the HTML5 specification.  
The algorithm consists of two stages: tokenization and tree construction.  
Actions when the parsing is finished   
Процесс синтаксического анализа является повторным. Для других языков исходный код не меняется во время синтаксического анализа, но в HTML динамический код (например, элементы скрипта, содержащие вызовы document.write()), может добавлять дополнительные токены, поэтому процесс синтаксического анализа фактически изменяет входные данные.  
Не имея возможности использовать обычные методы синтаксического анализа, браузер использует пользовательский синтаксический анализатор для анализа HTML. Алгоритм синтаксического анализа подробно описан в спецификации HTML5.  
Алгоритм состоит из двух этапов: токенизации и построения дерева.  
Действия по завершении синтаксического анализа  

The browser begins fetching external resources linked to the page (CSS, images, JavaScript files, etc.).  
At this stage the browser marks the document as interactive and starts parsing scripts that are in "deferred" mode: those that should be executed after the document is parsed. The document state is set to "complete" and a "load" event is fired.  
Note there is never an "Invalid Syntax" error on an HTML page. Browsers fix any invalid content and go on.  
Браузер начинает извлекать внешние ресурсы, связанные со страницей (CSS, изображения, файлы JavaScript и т.д.).  
На этом этапе браузер помечает документ как интерактивный и запускает синтаксический анализ сценариев, которые находятся в "отложенном" режиме: те, которые должны быть выполнены после анализа документа. Состояние документа устанавливается на "завершено" и запускается событие "загрузка".  
Обратите внимание, что на HTML-странице никогда не появляется ошибка "Недопустимый синтаксис". Браузеры исправляют любое недопустимое содержимое и продолжают работу.  

## 18. CSS interpretation  
Интерпретация CSS  

Parse CSS files, <style> tag contents, and style attribute values using "CSS lexical and syntax grammar"  
Each CSS file is parsed into a StyleSheet object, where each object contains CSS rules with selectors and objects corresponding CSS grammar.  
A CSS parser can be top-down or bottom-up when a specific parser generator is used.  
Анализируйте файлы CSS, содержимое тега <style> и значения атрибутов стиля, используя "лексическую и синтаксическую грамматику CSS"  
Каждый файл CSS преобразуется в объект таблицы стилей, где каждый объект содержит правила CSS с селекторами и объектами, соответствующими грамматике CSS.  
Синтаксический анализатор CSS может работать как сверху вниз, так и снизу вверх, когда используется определенный генератор синтаксических анализаторов.  

## 19. Page Rendering  
## 19. Рендеринг страницы  

Create a 'Frame Tree' or 'Render Tree' by traversing the DOM nodes, and calculating the CSS style values for each node.
Calculate the preferred width of each node in the 'Frame Tree' bottom-up by summing the preferred width of the child nodes and the node's horizontal margins, borders, and padding.
Calculate the actual width of each node top-down by allocating each node's available width to its children.
Calculate the height of each node bottom-up by applying text wrapping and summing the child node heights and the node's margins, borders, and padding.
Calculate the coordinates of each node using the information calculated above.
More complicated steps are taken when elements are floated, positioned absolutely or relatively, or other complex features are used. See http://dev.w3.org/csswg/css2/ and http://www.w3.org/Style/CSS/current-work for more details.  

Создайте "Дерево фреймов" или "Дерево рендеринга", пройдя по узлам DOM и вычислив значения стиля CSS для каждого узла.
Вычислите предпочтительную ширину каждого узла в "Дереве фреймов" снизу вверх, суммируя предпочтительную ширину дочерних узлов и горизонтальные поля, границы и отступы узла.
Вычислите фактическую ширину каждого узла сверху вниз, распределив доступную ширину каждого узла между его дочерними узлами.
Рассчитайте высоту каждого узла снизу вверх, применяя перенос текста и суммируя высоты дочерних узлов, а также поля, границы и отступы узла.
Рассчитайте координаты каждого узла, используя информацию, полученную выше.
Более сложные действия выполняются при перемещении элементов, их абсолютном или относительном расположении или при использовании других сложных функций. Более подробную информацию смотрите в разделах http://dev.w3.org/csswg/css2/ и http://www.w3.org/Style/CSS/current-work.  

Create layers to describe which parts of the page can be animated as a group without being re-rasterized. Each frame/render object is assigned to a layer.  
Textures are allocated for each layer of the page.  
The frame/render objects for each layer are traversed and drawing commands are executed for their respective layer. This may be rasterized by the CPU or drawn on the GPU directly using D2D/SkiaGL.  
All of the above steps may reuse calculated values from the last time the webpage was rendered, so that incremental changes require less work.
The page layers are sent to the compositing process where they are combined with layers for other visible content like the browser chrome, iframes and addon panels.
Final layer positions are computed and the composite commands are issued via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for asynchronous rendering and the frame is sent to the window server.  
Создайте слои, чтобы описать, какие части страницы можно анимировать как группу, не подвергая их повторной растеризации. Каждый кадр/объект рендеринга присваивается слою.
Текстуры выделяются для каждого слоя страницы.  
Выполняется обход объектов кадрирования/рендеринга для каждого слоя и выполняются команды рисования для соответствующего слоя. Это может быть растеризовано центральным процессором или отрисовано непосредственно на графическом процессоре с использованием D2D/SkiaGL.  
Все вышеперечисленные действия могут уменьшить вычисленные значения с момента последнего отображения веб-страницы, так что постепенные изменения потребуют меньше усилий.
Слои страницы отправляются в процесс компоновки, где они объединяются со слоями для другого видимого контента, такого как браузер chrome, iframes и дополнительные панели.
Вычисляются окончательные позиции слоев и выполняются команды компоновки с помощью Direct3D/OpenGL. Буферы команд графического процессора загружаются в графический процессор для асинхронного рендеринга, и кадр отправляется на оконный сервер.  

## 20. GPU Rendering  
Рендеринг на GPU  

During the rendering process the graphical computing layers can use general purpose CPU or the graphical processor GPU as well.  
When using GPU for graphical rendering computations the graphical software layers split the task into multiple pieces, so it can take advantage of GPU massive parallelism for float point calculations required for the rendering process.  
В процессе рендеринга графические вычислительные уровни также могут использовать CPU общего назначения или графический процессор GPU.  
При использовании графического процессора для вычислений графического рендеринга уровни графического программного обеспечения разделяют задачу на несколько частей, что позволяет использовать преимущества массового параллелизма графического процессора для вычислений с плавающей запятой, необходимых для процесса рендеринга.  
  
## 21. Window Server  
Оконный сервер  
Post-rendering and user-induced execution  
After rendering has been completed, the browser executes JavaScript code as a result of some timing mechanism (such as a Google Doodle animation) or user interaction (typing a query into the search box and receiving suggestions). Plugins such as Flash or Java may execute as well, although not at this time on the Google homepage. Scripts can cause additional network requests to be performed, as well as modify the page or its layout, causing another round of page rendering and painting.  

Последующий рендеринг и выполнение под управлением пользователя  
После завершения рендеринга браузер выполняет код JavaScript в результате некоторого механизма синхронизации (например, анимации Google Doodle) или взаимодействия с пользователем (ввод запроса в поле поиска и получение предложений). Плагины, такие как Flash или Java, также могут запускаться, хотя в данный момент их нет на главной странице Google. Скрипты могут вызывать выполнение дополнительных сетевых запросов, а также изменять страницу или ее макет, вызывая повторный цикл рендеринга и перерисовки страницы.  
END of this article
