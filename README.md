<h1 align="center">БИБЛИОТЕКА FORESSMENT AI</h1>

<h3 align="left">Задачи</h3>
<p align="left">
Разработанная библиотека решает 3 основных задачи:

- **Предобработка** сырых и нечетких данных.
- **Оценка** текущего состояния сложных технических объектов.
- **Прогнозирование** будущего состояния сложных технических объектов.

Данные задачи были отработаны для двух технических объектов:
- **DSC**: мостовой кран при проезде L-образного пути с различной нагрузкой (0кг, 120кг, 500кг и 1000кг); каждый цикл работы осуществлялся с активированной и деактивированной системой предотвращения раскачивания; каждый цикл работы состоял из пятикратного повторения процесса подъема груза, движения из точки А в точку В по траектории, опускания груза, подъема груза, возвращения в точку А и опускания груза (на основе набора данных Driving Smart Crane with Various Load).
- **HAI**: испытательный стенд, включающий три системы управления техническими объектами, а именно турбину GE, котел Emerson и системы водоподготовки FESTO, объединенные с помощью аппаратного обеспечения dSPACE; с помощью испытательного стенда несколько раз выполнялись безопасные и вредоносные сценарии (на основе набора данных HIL-based Augmented ICS Security Dataset).

Рассмотрим каждую задачу и ее программные компоненты более подробно.
</p>

<h3 align="left">Предобработка</h3>
<p align="left">
Данная задача выполняется следующими программными модулями:

- **Preprocessor** представляет собой алгоритм для предобработки сырых и нечетких данных. Данный модуль корректирует типы данных (```preprocessor/check_data_types.py```), заполняет пустые значения (```preprocessor/cluster_filling.py```), и снижает количество признаков данных на основе анализа их информативности (```posnd/informativity.py```) и мультиколлинеарности  (```posnd/multicolinear.py```).
- **Extractor** представляет собой алгоритм для извлечения знаний и данных, описывающих поведние сложных технических объектов. При этом знания представляются в виде ассоциативных правил (в формате “If \<premise\>, then \<consequence\>"), соджержащих только метку класса в правой части (consequence). Данный алгоритм решает задачи сильного ИИ в части построения модели знаний. 

Примеры, описывающие работу модуля **Preprocessor** представлены в ```examples/preprocessor_examples.py```.

1. Функция ```preprocessor_example_basic()``` – базовый пример работы модуля **Preprocessor**. В данном примере, модуль **Preprocessor** применен к искусственно сгенерированным данным. Все шаги, направленные на уменьшение объема данных, выводятся в консоль.
2. Функция ```preprocessor_example_titanic()``` – пример работы модуля **Preprocessor** на наборе данных titanic. Этот набор данных был использован, т.к. содержит категориальные и численные признаки, часть значений которых не заполнена. Более того, набор данных относительно небольшой, что позволяет получить и продемонстрировать результаты за небольшой промежуток времени. 

Примеры, описывающие работу модуля **Extractor** представлены в ```examples/extractor_examples.py```.

1. Функция ```extractor_basic_example()``` – базовый пример работы модуля **Extractor**. В данном примере алгоритм **Extractor** используется для создания набора данных с двумя классами. Пример правила, а также информация об измененном наборе данных выводится в консоль.
2. Функция ```extractor_ieee_data()``` - применение модуля **Extractor** к набору данных DSC. В данном примере классификатор RandomForestClassifier из библиотеки sklearn обучается на оригинальном и предобработанном **Extractor** наборах данных. В консоль выводится информация о наборах данных, а также значения метрик точности классификации.
3. Функция ```extractor_hai()``` - применение модуля **Extractor** к набору данных HAI. В данном примере классификатор RandomForestClassifier из библиотеки sklearn обучается на оригинальном и предобработанном **Extractor** наборах данных. В консоль выводится информация о наборах данных, а также значения метрик точности классификации.
</p>

<h3 align="left">Оценка</h3>
<p align="left">

Данная задача выполняется модулем **Assessor**. Работа данного модуля заключается в применении предобученных моделей машинного обучения, позволяющих идентифицировать состояние объекта на основе его аттрибутов. Процесс работы модуля можно представить в виде трех этапов:

1. Извлечение атрибутов анализируемого объекта.
2. Нормализация атрибутов.
3. Оценка состояния объекта.

В рамках рассматриваемых в проекте задач на первом этапе происходит чтение атрибутов из текстового файла с разбором соответствующих полей. На втором этапе производится предварительная обработка полей с целью приведения их к единому интервалу. Наконец, на третьем этапе запускается предварительно обученная нейронная сеть, которая определяет принадлежность объекта к тому или иному состоянию на основе его атрибутов.

Примеры, описывающие работу модуля **Assessor** представлены в ```examples/assessor_examples.py```.

Примеры применения данного алгоритма охватывают задачу обеспечения кибербезопасности критических ресурсов и объектов, а также задачу определения траектории движения транспортного средства (крана). В первом случае в качестве входных данных рассматриваются данные, полученные от датчиков системы паровых турбин и гидроаккумулирующих электростанций. Во втором случае входными данными являются параметры, описывающие работу и движение мостового крана при различных нагрузках.

Суть эксперимента заключалась в проверке пригодности предварительно настроенной модели в рамках задачи оценки состояния критически важного объекта. В ходе эксперимента было выделено два этапа: этап обучения и этап тестирования. На первом этапе корректировались веса нейронной сети, а на втором этапе проводился расчет показателей эффективности оценки состояния анализируемого объекта.</p>

<h3 align="left">Прогнозирование</h3>
<p align="left">

Данная задача выполняется модулем **Forecaster**. Данный модуль осуществляет:

- обучение модели прогнозирования состояний сложных систем на основе исторических данных в виде временного ряда, представляющего собой последовательность векторов признаков состояний системы;
- автономное прогнозирование состояний системы, описываемых определенным вектором признаков на заданный период времени.

Для обучения модели длина и последовательность признаков должны быть постоянными для состояния системы в любой момент времени. Модель предсказывает только числовые параметры состояний системы.

Примеры, описывающие работу модуля **Forecaster** представлены в ```examples/forecaster_examples.py```.

1. Функция ```example_forecaster_model_training(dataset_name, suf='', mode=1)``` – примеры обучения предсказательной и нормализующих моделей (```dataset_name```: название набора данных (str), ```suf```: суффикс для наименования выходных данных (str), ```mode```: способ запуска, параметр для разработчиков (integer)).
2. Функция ```example_forecaster_forecasting(dataset_name, suf='', mode=1, independently=True, sample_type='test')``` - пример предсказания состояний объекта на основе обученной модели, включая прогнозную оценку (```dataset_name```: название набора данных (str), ```suf```: суффикс для наименования выходных данных (str), ```mode```: способ запуска, параметр для разработчиков (integer), ```independently```: последовательность предсказывается в зависимости от прошлых значений или нет (boolean), ```sample_type```: тип прогнозной выборки для оценки - обучающая или тестовая (str)).

Если параметр ```independently``` равен ```True```, тогда все векторы значений признаков прогнозируются независимо друг от друга. На каждом этапе прогнозирования в партию добавляется элемент целевой выборки. Если параметр ```independently``` равен ```False```, тогда каждый прогнозируемый вектор становится элементом нового пакета для последующего прогнозирования.

Если параметр ```sample_type``` равен ```'train'```, тогда временное окно прогнозирования равно длине обучающей выборки от второй до последней партии. Пакет для прогнозирования — это первый пакет обучающей выборки. Истинные значения для оценки — это значения обучающей выборки от второй до последней партии. Если параметр ```sample_type``` равен ```'test'```, тогда временное окно прогнозирования равно длине всей тестовой выборки. Пакет для прогнозирования — последний пакет обучающей выборки. Истинные значения — это значения тестовой выборки.
</p>

<h3 align="left">Совместное использование модулей</h3>
<p align="left">

Пример объединения модулей **Extractor** (предобработка) и **Forecaster** (прогнозирование) представлен в ```examples/extractor_and_forecaster_examples.py```.
</p>

<h3 align="left">Документация</h3>
<p align="left">
Для дополнительной информации о библиотеки, пожалуйста, ознакомьтесь с содержимым следующих документов:

- Programm_description.pdf
- Guide_for_programmers.pdf

Данные документы расположены в каталоге ```guides```. Язык документов - русский.

Документация на английском языке сформирована автоматически с помощью Sphinx Autodoc и размещена в каталоге ```docs```.
</p>

<h3 align="left">Публикации</h3>
<p align="left">
Levshun D., Kotenko I. A survey on artificial intelligence techniques for security event correlation: models, challenges, and opportunities // Artificial Intelligence Review. 2023. P. 1-44. DOI: 10.1007/s10462-022-10381-4. URL:
<a href="https://link.springer.com/article/10.1007/s10462-022-10381-4 " target="_blank">https://link.springer.com/article/10.1007/s10462-022-10381-4</a>. (Scopus, WoS, Q1)
</p>

<h3 align="left">Контактные данные</h3>
<p align="left">labcomsec@gmail.com
</p>

<p align="left">
Данный проект разработан и поддерживается исследователями Лаборатории проблем компьютерной безопасности (СПб ФИЦ РАН).
Мы являемся частью команды Исследовательского центра "Сильный ИИ в Промышленности" (Университет ИТМО).
</p>

<p align="left">
<a target="_blank" rel="noopener noreferrer" href="https://sai.itmo.ru/">
<img src="https://gitlab.actcognitive.org/itmo-sai-code/organ/-/raw/main/docs/AIM-Strong_Sign_Norm-01_Colors.svg" alt="Сильный ИИ" width="250" height="100"/></a></p>

<p align="left">
Данный репозиторий также представлен на
<a target="_blank" rel="noopener noreferrer" href="https://github.com/labcomsec/foressment_lib/">
GitHub</a>.
</p>

<h3 align="left">Языки программирования и сторонние инструменты:</h3>
<p align="left">
<a href="https://pandas.pydata.org/" target="_blank" rel="noreferrer">
<img src="https://raw.githubusercontent.com/devicons/devicon/2ae2a900d2f041da66e950e4d48052658d850630/icons/pandas/pandas-original.svg" alt="pandas" width="40" height="40"/>
</a>
<a href="https://www.python.org" target="_blank" rel="noreferrer">
<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" alt="python" width="40" height="40"/>
</a>
<a href="https://scikit-learn.org/" target="_blank" rel="noreferrer">
<img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Scikit_learn_logo_small.svg" alt="scikit_learn" width="40" height="40"/>
</a>
<a href="https://www.tensorflow.org" target="_blank" rel="noreferrer">
<img src="https://www.vectorlogo.zone/logos/tensorflow/tensorflow-icon.svg" alt="tensorflow" width="40" height="40"/>
</a>
</p>
