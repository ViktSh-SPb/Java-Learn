# Лабораторная работа №2. Иерархии классов
## 1. Контингент вуза
Реализуйте набор классов, при помощи которых можно описать студентов и преподавателей вуза. Данные обо всех персонах должны включать:
- Фамилия и имя
- Пол (реализуйте его при помощи перечисления)
- Название факультета О преподавателях дополнительно должны храниться следующие данные:
- Ученая степень (кандидат наук, доктор наук, PhD) - перечисление
- Название специальности О студентах дополнительно должны храниться следующие данные:
- Для бакалавров и магистров: ступень обучения (перечисление) и номер курса
- Для аспирантов - тема диссертации Реализуйте метод print(), печатающий информацию о персоне:
1. Для всех должна печататься фраза *"This is {name}. {He/she} {verb} at {departament}"*, где:
    - *{name}* - фамилия и имя
    - *{He/she}* - местоимение в зависимости от пола
    - *{verb}* - глагол:
        - *"teaches"* - для преподавателей
        - *"studies"* - для студентов
    - *{department}* - название факультета
2. В зависимости от типа персоны дополнительно должна печататься фраза:
	- для преподавателей: *"{He/She} has {degree} degree in {specialty}."*, где:
		- *{degree}* - ученая степень;
		- *{specialty}* - название специальности.
	- для студентов: "*{He/She} is {N}'th year {stage} student.*", где:
		- *{N}* - номер курса;
		- *{stage}* - ступень обучения.
	- для аспирантов: "*{His/Her} thesis title is "{thesis-title}"*", где:
		- *{thesis title}* - тема диссертации.
Реализуйте метод printAll, печатающий данные о персонах из заданного массива.
Создайте массив и заполните его следующими данными:

<table style="width: 100%">
  <caption style="text-align: left">Преподаватели:</caption>
  <tr>
    <th style="background-color: grey; font-weight: bold;">Имя</th>
    <th style="background-color: grey; font-weight: bold;">Пол</th>
    <th style="background-color: grey; font-weight: bold;">Факультет</th>
    <th style="background-color: grey; font-weight: bold;">Уч. степень</th>
    <th style="background-color: grey; font-weight: bold;">Специальность</th>
  </tr>
  <tr>
    <td>Roland Turner</td>
    <td>M</td>
    <td>Computer science</td>
    <td>PhD</td>
    <td>Programming paradigms</td>
  </tr>
   <tr>
    <td>Ruth Hollings</td>
    <td>F</td>
    <td>Jurisprudence</td>
    <td>Msc</td>
    <td>Domestic arbitration</td>
  </tr>
</table>

<table style="width: 100%">
  <caption style="text-align: left">Студенты:</caption>
  <tr>
    <th style="background-color: grey; font-weight: bold;">Имя</th>
    <th style="background-color: grey; font-weight: bold;">Пол</th>
    <th style="background-color: grey; font-weight: bold;">Факультет</th>
    <th style="background-color: grey; font-weight: bold;">Ступень</th>
    <th style="background-color: grey; font-weight: bold;">Специальность</th>
  </tr>
  <tr>
    <td>Leo Wilkinson</td>
    <td>M</td>
    <td>Computer science</td>
    <td>Bachelor</td>
    <td>III</td>
  </tr>
   <tr>
    <td>Anna Cunningham</td>
    <td>F</td>
    <td>World economy</td>
    <td>Bachelor</td>
    <td>I</td>
  </tr>
  <tr>
    <td>Jill Lundquist</td>
    <td>F</td>
    <td>Jurisprudence</td>
    <td>Master</td>
    <td>I</td>
  </tr>
</table>

<table style="width: 100%">
  <caption style="text-align: left">Аспиранты:</caption>
  <tr>
    <th style="background-color: grey; font-weight: bold;">Имя</th>
    <th style="background-color: grey; font-weight: bold;">Пол</th>
    <th style="background-color: grey; font-weight: bold;">Факультет</th>
    <th style="background-color: grey; font-weight: bold;">Тема диссертации</th>
  </tr>
  <tr>
    <td>Ronald Correa</td>
    <td>M</td>
    <td>Computer science</td>
    <td>Design of a functional programming language</td>
  </tr>
</table>

Распечатайте содержимое массива.
## 2. Файлы
Реализуйте набор классов, при помощи которых можно описать файлы, хранящиеся на накопителе.

Данные обо всех файлах должны включать:
- Имя файла
- Размер в байтах
Кроме того, для файлов различных форматов требуются дополнительные атрибуты:
- Для файлов документов:
	- Формат файла (строка)
	- Количество страниц
- Для файлов со статическими изображениями:
	- Формат файла (строка)
	- Размер (ширина и высота)
- Для мультимедиа файлов:
	- Формат файла (строка)
	- Описание содержащегося в файле контента
	- Длительность
- Для видео-файлов кроме атрибутов мультимедиа файлов также должен храниться:
	- Размер картинки

Реализуйте указанные классы. Для каждого атрибута реализуйте геттеры и сеттеры. Подумайте, какие ограничения на значения атрибутов возможны и реализуйте их.

Реализуйте отдельные классы для хранения размера изображения и длительности медиа-файла. Реализация не должна позволять задавать некорректные значения атрибутов. Реализуйте в этих классах методы, при помощи которых можно напечатать строковое представление размера изображения и длительности медиа-файла.

В одном из классов реализуйте статический метод printAll, печатающий данные о файлах из заданного массива. Данные должны выводиться в таблицу следующего вида:
```
        File name    |    Size    |  Details
---------------------+------------+----------------------------------------------
j110-lab2-hiers.docx |      23212 | docx, 2 pages
spb-map.png          |    1703527 | image, 1024x3072
06-PrettyGirl.mp3    |    7893454 | audio, Eric Clapton, Pretty Girl, 05:28
BackToTheFuture1.avi | 1470984192 | video, Back to the future I, 1985, 01:48:08, 640x352
```
Создайте массив и заполните его объектами всех имеющихся типов. Распечатайте содержимое массива при помощи метода printAll.

Создайте массив на базе одного из дочерних типов и заполните его несколькими объектами. Распечатайте содержимое массива при помощи имеющегося метода printAll. В комментариях к вызову объясните, почему этот метод подходит для массива дочернего типа.