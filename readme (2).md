<div id="header" align="center">
<h1> Проект по определению объектов на фотографиях 
<img src="https://github.com/blackcater/blackcater/raw/main/images/Hi.gif" height="32"/></h1>
<h3 align="center">Проект позволяет определять объекты на загруженных фотографиях.</h3>
</div>
<div id="header2" align="center">
<a href="https://t.me/UU_diplom_bot">
    <img src="telegram.png" width="100" alt="Telegram">
</a>
</div>

### Languages and tools
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/python/python-original.svg" title="python" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/bootstrap/bootstrap-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/css3/css3-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg" title="js" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/html5/html5-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/devicon/devicon-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/github/github-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/django/django-plain.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/opencv/opencv-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/matplotlib/matplotlib-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/pycharm/pycharm-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/sqlite/sqlite-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/plotly/plotly-original.svg" title="bootstrap" width="40" height="40"/>&nbsp;

<div id="stat" align="center">

![](http://github-profile-summary-cards.vercel.app/api/cards/profile-details?username=igor161rus&theme=github_dark)
![](http://github-profile-summary-cards.vercel.app/api/cards/productive-time?username=igor161rus&theme=github_dark&utcOffset=8)
![](https://github-profile-summary-cards.vercel.app/api/cards/stats?username=igor161rus&theme=github_dark)

</div>

<details><summary><b>Возможности</b></summary>
<div>
Реализованы следующие возможности:
<ul>
<li>Реализовано отображение гео меток загруженных фотографий на карте
<br>
<img src="img-1.JPG" width="550">
<br>
</li>
<li>Распознавание объектов на фотографии двумя методами, с выводом статистики</li>
<img src="img-3.JPG" width="550">
<br>
<li>Генерирование описания фотографии</li>
<img src="img-5.JPG" width="550">
<br>
<li>Работа с телеграмм ботом</li>
<img src="img-4.JPG" width="550">
</ul>
</div>

</details>

<details><summary><b>Структура</b></summary>
Проект реализован с помощью на языке python с использованием фрэймворка Django.<br>
Для работы понадобится python (использовался python 3.12)<br>
Все зависимости можно установить используя requirements.txt

```
pip install -r requirements.txt
```
В django создано приложение object_detection. Структура проекта выглядит следующим образом<br>

<img src="img-6.JPG">

<details><summary><b>Модель</b></summary>
Определены 3 модели: ImageFeed, DetectedObject, UserAddFields
<ul>
<li>ImageFeed - модель для хранения информации о загруженных фотографиях<br>
Имеет следующую структуру:

```
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, verbose_name='Пользователь')
    image = models.ImageField(upload_to='images/', verbose_name='Изображение')
    lon = models.FloatField(verbose_name='Долгота')
    lat = models.FloatField(verbose_name='Широта')
    description = models.TextField(verbose_name='Описание', null=True, blank=True)
```
</li>
<li>DetectedObject - модель для хранения информации об обнаруженных объектах<br>
Имеет следующую структуру:

```
    image_feed = models.ForeignKey(ImageFeed, related_name='detected_objects', on_delete=models.CASCADE)
    object_type = models.CharField(max_length=100)
    confidence = models.FloatField()
    location = models.CharField(max_length=255)
    processed_image = models.ImageField(upload_to='processed_images/', null=True, blank=True,
                                        verbose_name='Обработанное изображение')
    method_detected = models.CharField(max_length=100)
```
</li>
<li>UserAddFields - модель расширяющая стандартную модель django для хранения telegram id<br>
Имеет следующую структуру:

```
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    tg_id = models.IntegerField(null=True, blank=True)
```
</li>
</ul>
</details>
<details><summary><b>Утилиты</b></summary>
Функции определения моделей, построения графиков, извлечения exif информации реализованы в файле utils.py
<ul>
<li>
Функция process_image(image_feed_id) реализует определение объектов при помощи реализации Caffe сети обнаружения 
MobileNet-SSD с предварительно обученными весами на VOC0712 и mAP = 0,727.<br>
<a href="https://github.com/chuanqi305/MobileNet-SSD">MobileNet-SSD</a>
</li>
<li>
Функция process_image_detr(image_feed_id) реализует определение объектов при помощи модели 
DETR (сквозное обнаружение объектов) с магистралью ResNet-50.<br>
<a href="https://huggingface.co/facebook/detr-resnet-50">DETR-resnet-50</a>
</li>
<li>
Функция image_caption(image_feed_id) возвращает текстовое описание фотографии.<br>
<a href="https://huggingface.co/nlpconnect/vit-gpt2-image-captioning">vit-gpt2</a>
</li>
<li>
Функции get_graph, get_plot, get_plot_stat предназначены для вывода статистических графиков на основе определенных 
объектов и методов определения
</li>
<li>
Функция read_exif_data(file_id) Используется для извлечения exif информации из фотографии.<br>
<a href="https://pypi.org/project/exif/">exif</a>

</li>

</ul>
</details>
<details><summary><b>Telegram</b></summary>
В приложении зарегистрирован запуск телеграм-бота с помощью manage.py
Бот запускается командой: 

```
    python manage.py bot
```
Бот предназначен для загрузки фотографий из телефона на сайт с последующим определением объектов на 
фотографии одним из доступных методов и генерацией текстового описания. Определенные объекты и описание 
возвращаются пользователю в виде сообщения.  
</details>
<details><summary><b>Карта</b></summary>
Если на фотографии определены метаданные с гео информацией, то на карте в профиле пользователя отображается 
местоположение сделанной фотографии в виде значка
Для отрисовки карты с гео метками фотографий используется библиотека django_admin_geomap
<a href="https://github.com/vb64/django.admin.geomap/blob/main/READMEru.md">Карта</a>
</details>
</details>



