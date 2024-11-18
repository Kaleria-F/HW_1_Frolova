Игра на Unreal Engine, разработанная в рамках курса по выбору

Фролова Валерия Андреевна БПИ228


### **Описание проекта**

Проект представляет собой систему процедурной генерации комнат с различными уровнями сложности, ловушками и восстановителями здоровья 
![image](https://github.com/user-attachments/assets/b57bd801-aedf-4218-be7c-a92bdb2b8a03)

Система построена на взаимодействии различных Blueprint'ов, которые обеспечивают:

* Генерацию комнат после перехода игрока через выходы
* Динамическое взаимодействие с ловушками, уменьшающими здоровье игрока
* Возможность восстановления здоровья через специальные объекты ``BP_Recover``
* Визуализацию текущего уровня здоровья игрока через виджет ``WB_HealthBar``

## Основные компоненты

### 1. BP_First

:page_facing_up: _Описание:_

* Представляет первую комнату в последовательности 
* Не содержит ловушек, используется как начальная точка для игрока
![image](https://github.com/user-attachments/assets/685ba864-4c99-478f-a304-a8a1eec97130)

 
### 2. BP_Finish

:page_facing_up: _Описание:_

* Комната бозначает победу в игре
* В центре комнаты находится большая капуста (``BP_Win``), которая оповещает о победе
![image](https://github.com/user-attachments/assets/1a240577-da02-4d9a-869f-b6902f6fd293)


### 3. BP_Room

:page_facing_up: _Описание:_

* Сложная комната с разными ловушками
* При переходе в комнату релаизовано закрытие прохода - стена закрывает проход - опускается сверху


_Составляющие:_ 

#### Статические ловушки

(``BP_SpikeTrap``)

* Наносят -10 здоровья при столкновении
* Расположены в фиксированных местах
* Но двигаются вверх-вниз по таймлайну

#### Движущиеся ловушки:

(``BP_Snail``)

* Двигаются по определённой траектории (с помощью таймлайна)
* Наносят -15 здоровья при столкновении
  
#### Восстановитель здоровья:

 (``BP_Recover``)

* Большая капуста в центре комнаты
* Восстанавливает +20 здоровья игроку при столкновении с ней

### 4. BP_RoomLight

:page_facing_up: _Описание:_

Лёгкая комната содержит только статические ловушки
* При переходе в комнату релаизовано закрытие прохода - стена закрывает проход - опускается сверху

_Составляющие:_

### Статические ловушк:

(``BP_SpikeTrap``)
* Шипы
* Наносят -10 здоровья при столкновении


### 5. ловушка BP_SpikeTrap - шип

:page_facing_up: _Описание:_

* ловушка представляет собой конус, в EventGraf есть функция для задания траектории движения (опускания-поднимания) через Timeline
* ``Box Collision`` отслеживает пересечение с игроком и наносит -10 здоровья через вызов функции ``TakeDamage`` в ``BP_ThirdPersonCharacter``

:mag: _Расположение:_ 

В BP_Room и BP_RoomLight

:wrench: _Логика работы:_

1) Компонент Box Collision проверяет пересечение с игроком через On Component Begin Overlap.
2) При столкновении: выполняется проверка, что объект — это игрок (Cast To BP_ThirdPersonCharacter)
3) Вызывается функция TakeDamage(-10) в BP_ThirdPersonCharacte
4) Здоровье уменьшается, и обновляется виджет WB_HealthBar.


### 6. Улитки - ловушки BP_Snail

:page_facing_up: _Описание:_

Движущаяся ловушка в виде улитки.

* Используется Timeline для движения по одной из осей
* Box Collision отслеживает пересечение с игроком и при пересечении наносит урон
* урон составляет -15 здоровья через вызов функции ``TakeDamage``

**Немного подробнее:**

:mag: _Расположение:_

Только в BP_Room 

:wrench: _Логика работы:_

1) Используется компонент Timeline, который задаёт движение ловушки по оси Y
2) Ловушка движется циклически.
3) Нанесение урона:
   * Компонент Box Collision проверяет пересечение с игроком
   * При столкновении:
     * Выполняется проверка, что объект — это игрок
     * Вызывается функция TakeDamage(-15)
  
:pushpin: данный ассет скачен из Fab, но он был немного поломан - у улитки выпали глаза - были восстановлены


### 7. BP_Recover

:page_facing_up: _Описание:_

Объект-капуста, восстанавливающий здоровье игроку

* Располагается в сложных комнатах в центе
* При пересечении игроком вызывает восстановление +20 здоровья через функцию ``TakeDamage``
  
### 8. BP_DungeonManager

:page_facing_up: _Описание:_

* В блюпринте реализована функция ``GenerateRooms`` - управляет процедурной генерацией комнат в зависимости от выбора игрока

:pencil2: Проблемы моей реализации:

Генерация происходит не для заданного количества комнат (6–10)

Логика генерации ловушек привязана к типу комнаты (в зависимости от выбора игрока), а не к заранее определенному кол-ву комнат.
Есть в ``BP_ThirdPerson`` переменная AmountRoomGlob, которая предназначена для передачи количества комнат, не не реализовала ее в игре (оставила, чтобы сделать это позже)

### 9. BP_ThirdPersonCharacter

:page_facing_up: _Описание:_

Основной класс игрока, содержащий логику взаимодействия с ловушками и восстановителя здоровья

##### TakeDamage:
* Уменьшает или увеличивает здоровье игрока
* Связывает изменение здоровья с виджетом ``WB_HealthBar``

``Health`` (Float) - текущее здоровье игрока (максимум 100 единиц)

:wrench: Логика работы: 

1) Принимает значение (положительное или отрицательное
2) Вычитает значение из текущего здоровья
3) гарантирует, что здоровье не опустится ниже 0 или не превысит 100 с помощью узла Clamp


### 10. WB_HealthBar

:page_facing_up: _Описание:_

* Виджет, который отображает текущее здоровье игрока
* Состоит из прогресс-бара и текста показывающего числовое значение здоровья 
* Уровень заполненности прогресс-бара пропорционален переменной Health из BP_ThirdPersonCharacter (выысчитывается с помощью функции)

![image](https://github.com/user-attachments/assets/9ae1936f-af39-4f6f-a698-db1d238bf0ac)
![image](https://github.com/user-attachments/assets/354e1048-6771-4e84-8769-1e49d5285af8)


## Логика работы

### Процесс генерации комнат

1) Игрок начинает в ``BP_First``, где нет ловушек
2) При пересечении выходов (``Exit1`` или ``Exit2``) вызывает генерацию следующей комнаты (функция ``GenerateRooms``)
3) В зависимости от выхода генерируется BP_RoomLight или BP_Room
  
В комнатах разные ловушки(в зависимости от типа комнаты)
В BP_Room статические и движущиеся ловушки.
В BP_RoomLight только статические ловушки.

:pencil2: 


# :exclamation: Выполненные требования:

:heavy_check_mark: 1) Персонаж должен уметь корректно передвигается вперед-назад-влево-вправо и 
прыгать; 

:heavy_check_mark: У персонажа должен быть индикатор здоровья на ваше усмотрение (например, 
шкала, сердечки здоровья, общее освещение уровня); 

:heavy_check_mark: С точки зрения игрока, геймплей должен состоять из одного уровня, внутри 
которого будет от 6 до 10 комнат, которые должны генерироваться по 
следующим правилам: 

:heavy_check_mark: a) У комнаты должен быть вход и два выхода; 
![image](https://github.com/user-attachments/assets/ca237c6c-ca67-4a0f-a1c7-f9f7df9517ba)


:heavy_check_mark: b) Вернуться в дверь, из которой персонаж пришел в комнату – нельзя; 

:heavy_check_mark:  c) Один из двух выходов должен вести в следующую комнату такой же 
сложности, как и текущая; второй выход должен вести в комнату большей 
сложности, содержащий в себе объект, восстанавливающий здоровье 
(соответственно нужно как-то отметить для игрока тип комнаты); 

[]  d) Суммарное количество комнат, проходимых персонажем за игру, для 
каждой новой игровой сессии должно выбираться случайным образом в 
начале игры и должно находиться в диапазоне [6; 10]; 

:heavy_check_mark:  Если текущая комната является последней, то в ней должен 
располагаться объект, символизирующий окончание игры, например, 
тележка с КАПУСТОЙ. Но можно использовать любой другой символ 
окончания игры на ваше усмотрение; 

:heavy_check_mark:  4) В каждой комнате обязательно должны быть ловушки. Также в каждой комнате 
могут присутствовать баффы и/или дебаффы (в зависимости от вашей 
реализации); 

:heavy_check_mark:  5) Ловушки: 

a) должны быть представлены двумя типами игровых объектов: 
:heavy_check_mark:    i) тип 1: движется по заранее заданной траектории; 
:heavy_check_mark:    ii) тип 2: не движется, является статичным; 

:heavy_check_mark: b) оба типа ловушек должны обрабатывать столкновение с персонажем 
игрока, при котором персонажу наносится урон (количество урона на 
ваше усмотрение и балансировку); 

Пункты 7-8 не выполняла

:heavy_check_mark: 9) Как было отмечено, у комнат должно быть два типа сложности. Сложность 
комнаты должна выстраиваться на ваше усмотрение с помощью следующих 
параметров: 
**a) скорость движущихся ловушек;** 
 b) отсутствие/наличие баффов и дебаффов; 
**c) количество ловушек, баффов и дебаффов;** - в сложных комнатах больше ловушек и они разного типа

:heavy_check_mark: 10) В более сложных комнатах должен всегда располагаться игровой объект 
(например, в виде сердечка или кочана капусты, лежащего на земле), который 
будет восполнять N здоровья игроку (значение N подбирается на ваше 
усмотрение). 
![image](https://github.com/user-attachments/assets/c4ad5ae9-1dd4-448c-9abd-494a47f6c1f6)



