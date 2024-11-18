# UE_Frolova_HW1
 Игра на Unreal Engine, разработанная в рамках 


### **Описание проекта**

Проект представляет собой систему процедурной генерации комнат с различными уровнями сложности, ловушками и восстановителями здоровья. 

Система построена на взаимодействии различных Blueprint'ов, которые обеспечивают:

* Генерацию комнат после перехода игрока через выходы
* Динамическое взаимодействие с ловушками, уменьшающими здоровье игрока
* Возможность восстановления здоровья через специальные объекты (``BP_Recover``)
* Визуализацию текущего уровня здоровья игрока через виджет WB_HealthBar

## Основные компоненты

1. BP_First

_Описание:_

* Представляет первую комнату в последовательности 
* Не содержит ловушек, используется как начальная точка для игрока.
 



