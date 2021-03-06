# Web2SearchTextNext
Програма для пошуку тексту на веб-сторінках

# Використані бібліотеки
- Qt - використовувалася для побудови графічного інтерфейсу та розпаралелювання
- Curl - використовувалася для отримання html коду за вказаною url адресою
- htmlcxx - використовувався для парсингу html коду

# Граф посилань
Так як програмі не потрібно знати всіх зв'язків в графі url посилань,
(зв'язки які утворюють замкнуті цикли не потрібні) то було прийнято 
рішення використовувати дерево з довільною кількістью дочірніх вузлів.

Як і було поставлено в задачі, використовувався обхід в ширину.
При обході використовувався метод двох черг, поточної і наступної.
При вивільнені першої до них застосовувався метод std::swap.

Обхід був адаптований для багатопоточного виконання, для кожної
з списку поточного нод застосовувався метод scan_node який передавався
в QThreadPool з використанням QFutureSynchronized. І програма очікувала 
допоки усі scan_node для current_level не виконаються. після чого запускала обробку next_level.

# Графічний інтерфейс
Для побудови графічного інтерфейсу був вибраний дизайн головного вікна з вкладками:
- Setting, де задаються початкові умови;
- Findlist - список url посилань де було найдено збіг;
- Log - вивід повідомлень в процесі виконання

Так як закачка і сканування відбуваються надзвичайно швидко я вирішив відображати процес у формі лога
де відображався вже результат сканування (знайдено/не знайдено/помилки приспробі завантажити html)

# Сканування веб-сторінок
Проводилось за наступною схемою
- Завантаження сторінки (таймаут 10с, таймаут підключення 5с)
- Парсинг html коду
- Отримання тексту з html-дерева.
- Перевірка тексту на ключовий текст
- Отримання та перевірка url посилань з html-дерева.
- пошук знайдених посилань в дереві
- додавання нових посилань в поточну url ноду

# Пропозиції щодо тестування
- Провести модульне тестування в першу чергу функцій які працюють з вхідними даними, тобто
  ті які завантажують та обробляють html сторінки (файл url_utils.h).
- Також потребує детальної уваги цикл який сробить обхід дерева з подальшим скануванням. Тут
його можна просто виконувати з різною кількістю потоків для різної кількості просканованих
посилань.
- Провести Інтеграційне тестування всієї системи в цілому

# Розробка та збирання
Проект написаний з використанням qmake в Qt Creator в середовищі OS Linux. Але код є 
цілком переносимим на інші системи так як застосовані крос-платформенні бібліотеки.

Так як завдання мале то була застосованна "зверху вниз" інтеграція написаних модулів,
тобто спочатку було написано інтерфейс з заглушкою-класом дерева url-посилань,
а потім було підключено дерево та утилітні функції для роботи з html та url.
