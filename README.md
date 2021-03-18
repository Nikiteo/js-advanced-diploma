[![Build status](https://ci.appveyor.com/api/projects/status/x11mn6amt8wpc902?svg=true)](https://ci.appveyor.com/project/Nikiteo/js-advanced-diploma)
# AJS. Retro Game

###### tags: `netology` `advanced js`

## Предыстория

Вы неплохо овладели не только продвинутыми возможностями JS, но и инфраструктурными инструментами. И вам поручили первый проект: разработать небольшую пошаговую игру.

Но, не всё так хорошо - UI уже написали за вас, спроектировали некоторые базовые классы, но на этом разработку забросили. Вам нужно реанимировать проект, переведя его на работу с npm, Babel, Webpack, ESLint (ну и дальше по списку - вы в курсе), а также дописать оставшуюся функциональность, потому что времени (и денег) на разработку и проектирование с нуля, как обычно, нет :).

## Концепция игры

Двухмерная игра в стиле фэнтези, где игроку предстоит выставлять своих персонажей против персонажей нечисти. После каждого раунда, восстанавливается жизнь уцелевших персонажей игрока и повышается их уровень. Максимальный уровень - 4.

## Механика

Размер поля фиксирован (8x8). Направление движения аналогично ферзю в шахматах. Персонажи разного типа могут ходить на разное расстояние (в базовом варианте можно перескакивать через других персонажей - т.е. как конь в шахматах, единственно - ходим по прямым и по диагонали):
* Мечники/Скелеты - 4 клетки в любом направлении
* Лучники/Вампиры - 2 клетки в любом направлении
* Маги/Демоны - 1 клетка в любом направлении

![](https://i.imgur.com/yp8vjhL.jpg)

Дальность атаки так же ограничена:
* Мечники/Скелеты - могут атаковать только соседнюю клетку
* Лучники/Вампиры - на ближайшие 2 клетки
* Маги/Демоны - на ближайшие 4 клетки

Клетки считаются "по радиусу", допустим для мечника зона поражения будет выглядеть вот так:

![](https://i.imgur.com/gJ8DXPU.jpg)

Для лучника(отмечено красным):

![](https://i.imgur.com/rIINaFD.png)


Игрок и компьютер последовательно выполняют по одному игровому действию, после чего управление передаётся противостоящей стороне. Как это выглядит:
1. Выбирается собственный персонаж (для этого на него необходимо кликнуть левой кнопкой мыши)
1. Далее возможен один из двух вариантов:
    1. Перемещение: выбирается свободное поле, на которое можно передвинуть персонажа (для этого на поле необходимо кликнуть левой кнопкой мыши)
    2. Атака: выбирается поле с противником, которое можно атаковать с учётом ограничений по дальности атаки (для этого на персонаже противника необходимо кликнуть левой кнопкой мыши)
    
**Важно: в новой игре игрок всегда начинает первым (если игра загружается из сохранения, то порядок определяется в сохранении).**

Игра заканчивается тогда, когда все персонажи игрока погибают, либо достигнут выигран максимальный уровень (см.ниже Уровни).

Уровень завершается выигрышем игрока тогда, когда все персонажи компьютера погибли.

Баллы, которые набирает игрок за уровень равны сумме жизней оставшихся в живых персонажей.

### Генерация персонажей

Персонажи генерируются рандомно в столбцах 1 и 2 для игрока и в столбцах 7 и 8 для компьютера:

![](https://i.imgur.com/XqcV1uW.jpg)


### Уровни

#### Level 1: prairie

У игрока генерируются два персонажа: (случайным образом - типов `Bowman` и `Swordsman`) с уровнем 1, характеристики соответствуют таблице характеристик (см. раздел ниже).

У компьютера генерируется произвольный набор персонажей в количестве 2 единиц.

#### Level 2: desert

У игрока повышается level игроков на 1 + восстанавливается здоровье выживших. Дополнительно случайным образом добавляется новый персонаж уровня 1.

У компьютера случайным образом генерируются персонажи в количестве, равным количеству персонажей игрока, с уровнем от 1 до 2.

#### Level 3: arctic

У игрока повышается level игроков на 1 + восстанавливается здоровье выживших. Дополнительно случайным образом добавляется два новых персонаж уровня 1 или 2.

У компьютера случайным образом генерируются персонажи в количестве, равным количеству персонажей игрока, с уровнем от 1 до 3.


#### Level 4: mountain

У игрока повышается level игроков на 1 + восстанавливается здоровье выживших. Дополнительно случайным образом добавляется два новых персонаж уровня от 1 до 3.

У компьютера случайным образом генерируются персонажи в количестве, равным количеству персонажей игрока, с уровнем от 1 до 4.

### Персонажи

Валидные строковые идентификаторы (к которым привязаны изображения):
* swordsman
* bowman
* magician
* daemon
* undead
* vampire

UPD: 14.04.2019 у игрока могут быть только swordsman, bowman и magician, у компьютера только daemon, undead, vampire. Если вы сделали без этого, то диплом тоже принимается.

#### Стартовые характеристики (атака/защита)

* Bowman - 25/25
* Swordsman - 40/10
* Magician - 10/40
* Vampire - 25/25
* Undead - 40/10
* Daemon - 10/40

#### Level Up

* На 1 повышает поле level автоматически после каждого раунда
* Показатель health приводится к значению: текущий уровень + 80 (но не более 100). Т.е. если у персонажа 1 после окончания раунда уровень жизни был 10, а персонажа 2 - 80, то после levelup:
    * персонаж 1 - жизнь станет 90
    * персонаж 2 - жизнь станет 100
* Повышение показателей атаки/защиты также привязаны к оставшейся жизни по формуле: `attackAfter = Math.max(attackBefore, attackBefore * (1.8 - life) / 100)`, т.е. если у персонажа после окончания раунда жизни осталось 50%, то его показатели улучшаться на 30%. Если же жизни осталось 1%, то показатели никак не увеличаться.

## Описание структуры

Структура предоставленных вам классов максимально упрощённая, без лишних изысков. Почти все зависимости передаются в качестве параметров конструктора, чтобы можно было легко тестировать их.

Ключевые сущности:
1. GamePlay - класс, отвечающий за взаимодействие с HTML-страницей
1. GameController - класс, отвечающий за логику приложения (важно: это не контроллер в терминах MVC), там вы будете работать больше всего
1. Character - базовый класс, от которого вы будете наследоваться и реализовывать специализированных персонажей
1. GameState - объект, который хранит текущее состояние игры (может сам себя воссоздавать из другого объекта)
1. GameStateService - объект, который взаимодействует с текущим состоянием (сохраняет его, чтобы оно не потерялось при перезагрузке страницы, может экспортировать в файл или загрузить из файла)
1. PositionedCharacter - Character, привязанный к координате на поле. Обратите внимание, что несмотря на то, что поле выглядит как двумерный массив, внутри оно хранится как одномерный (считайте это своеобразным `legacy`, с которым вам придётся бороться)
1. Team - класс для команды (набор персонажей), представляющих компьютер и игрока
1. generators - модуль, содержащий вспомогательные функции для генерации команды и персонажей

## Задачи

**Важно: авто-тесты обязательны только к тем задачам, где это явно обозначено. В остальных задачах вы можете их реализовывать по желанию.**

### 1. Постройка инфраструктуры

Необходимо подключить Webpack, Webpack DevServer, Babel, ESLint.

### 2. Настройка Webpack

Обратите внимание, что в бандл не собираются картинки, прописанные в CSS, т.к. не подключен соответствующий Loader.

Используйте [url-loader](https://github.com/webpack-contrib/url-loader). Добейтесь работоспособной сборки.


### 3. Отрисовка поля

Пришло время наконец-начать подключать геймплей. Для этого у вас есть класс GamePlay. Объект этого класса уже создан, привязан к HTML-странице. Вам необходимо вызвать метод `drawUi` с нужной темой для отрисовки на экране (вызывайте этот метод в методе `init` класса `GameController`).

Названия тем фиксированы и перечислены в модуле `themes.js`. Подредактируйте модуль так, чтобы можно было использовать определённый в нём объект (а не прописывать каждый раз строки руками). На данном этапе достаточно выбрать тему `prairie`. В задаче про уровни необходимо будет сделать привязку к уровню:
* Level 1: prairie
* Level 2: desert
* Level 3: arctic
* Level 4: mountain

### 4. Отрисовка границ поля

Обратите внимание, что по умолчанию поле выглядит вот так:

![](https://i.imgur.com/JfmSroP.png)

Необходимо, чтобы оно выглядело вот так:

![](https://i.imgur.com/SbRwuAL.png)

Для этого в модуле `utils.js` допишите реализацию так, чтобы она возвращала строки:
* top-left
* top-right
* top
* bottom-left
* bottom-right
* bottom
* right
* left
* center

**Не забудьте написать авто-тест на эту функцию.**

### 5. Team Generation

Напишите реализацию для генератора `characterGenerator` и функции `generateTeam` (модуль `generators`) с учётом правил, описанных в разделе Генерация персонажей.

Обратите внимание: обе функции на вход должны принимать массив (или iterable) из классов (не строковых названий, а именно классов).

### 6. Запрет создания объектов `Character`

Достаточно значительную часть времени, как программист, вы будете заниматься исследовательскими задачами (т.е. приобретением новых навыков, а не использованием существующих). Этим-то мы и предлагаем вам заняться. Заодно вспомнить, как на самом деле работает наследование в JavaScript.

Класс `Character` был спроектирован как базовый, чтобы вы могли унаследовать от него своих персонажей. Поэтому неплохо бы запретить создавать объекты этого класса через `new Character(level)`, но при этом создание наследников должно работать без проблем: `new Daemon(level)`, где `class Daemon extends Character`. Ознакомьтесь с документацией на [new.target](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/new.target) и реализуйте подобную логику, выбрасывая ошибку в конструкторе `Character`.

**Не забудьте написать тесты на то, что исключение выбрасывается при создании объекта класса `Character` и не выбрасывается, при создании объектов унаследованных классов.**

P.S. конечно, в реальной жизни проще договориться и нигде не делать `new Character`, но нам важно, чтобы вы помнили про устройство наследования.

### 7. Отрисовка команд

Для отрисовки воспользуйтесь методом `redrawPositions`, который принимает на вход массив их объектов `PositionedCharacter`. Для упрощения при любом дальнейшем изменении игрового поля (перемещение персонажа или его смерть) мы предлагаем вам целиком перерисовать игровое поле с помощью данного метода.

### 8. Вывод информации о персонаже

`GamePlay` может уведомлять вас о событиях, происходящих с игровым полем через механизм callback'ов.

Для игрового поля они предусмотрены следующие:
1. Вход указателя мыши в ячейку поля (`addCellEnterListener`)
1. Выход указателя мыши из ячейки поля (`addCellLeaveListener`)
1. Клик мышью по ячейке поля (`addCellClickListener`)

Чтобы добавить "слушателя" на определённое событие, используйте методы указанные рядом с описанием событий, в качестве аргумента передавая callback. Callback принимает всего один аргумент - индекс ячейки поля, на которой происходит событие.

Вам нужно реализовать механизм отображения краткой информации о персонаже с использованием `tagged templates` (см. первую лекцию, пример с `formatMark`).

Как это сделать:
1. Подпишитесь из `GameController` на событие `cellEnter` (в качестве коллбека передавайте метод `onCellEnter` из `GameController` - подумайте, как правильно это сделать, вспомните про то, что такое на самом деле методы в классе и про `this`)

Как это должно выглядеть:
```javascript
// GameController:
someMethodName() { // <- что это за метод и где это нужно сделать решите сами
   this.gameplay.addCellEnterListener(this.onCellEnter);
}

onCellEnter(cellIndex) {
    // some logic here
}
```

2. При возникновении события `cellEnter` проверяйте, есть ли в поле персонаж, если есть используйте метод `showCellTooltip` из класса `GamePlay` для отображения информации
3. При возникновении события `cellLeave` скрывайте подсказку (метод `hideCellTooltip`)

![](https://i.imgur.com/SljJjE0.png)

Формат информации:
"🎖1 ⚔10 🛡40 ❤50", где:
* 1 - level
* 10 - значение атаки
* 40 - значение защиты
* 50 - значение жизни

🎖 U+1F396	 - медалька (уровень)
⚔ U+2694 - мечи (атака)
🛡 U+1F6E1 - щит (защита)
❤ U+2764 - сердце (уровень жизни)

**Не забудьте написать тесты на тегированный шаблон.**

Важно: подсказка показывается только если в поле есть персонаж!

### 9. Выбор персонажа

Настало время научить приложение выбирать персонажа для следующего хода. Для этого нужно учесть несколько вещей:
1. Нужно помнить, чей сейчас ход: игрока или компьютера
1. Нужно реагировать на клик пользователя по определённой ячейке игрового поля

Для хранения состояния мы предлагаем вам воспользоваться объектами специального класса `GameState` и хранить в нём информацию о том, чей шаг следующий (продумайте самостоятельно, как вы это будете делать).

Для того, чтобы реагировать на клик на ячейке поля в классе `GamePlay` реализован метод `addCellClickListener`, который в качестве аргумента принимает callback. Подпишитесь из `GameController` на событие `cellClick` (в качестве коллбека передавайте метод `onCellClick` из `GameController` - подумайте, как правильно это сделать, вспомните про то, что такое на самом деле методы в классе и про `this`).

```javascript
// GameController:
someMethodName() { // <- что это за метод и где это нужно сделать решите сами
   this.gameplay.addCellClickListener(this.onCellClick);
}

onCellClick(cellIndex) {
    // some logic here
}
```

В методе `onCellClick`, проверяйте, есть ли в ячейке персонаж и, что самое важное, это играбельный персонаж (т.е. `Bowman`, `Swordsman` или `Magician`). Если нет - выводите сообщение об ошибке с помощью метода `showError` из класса `GamePlay`. Если же персонаж играбельный, то необходимо выделить ячейку с помощью метода `selectCell` из класса `GamePlay`:

![](https://i.imgur.com/HUlj3x7.png)

Примечание: `showError` работает, конечно, по-дурацки, просто выводя `alert`, но на то она и Retro Game :).

Важно: выделить можно только одного персонажа! Если вы выделяете другого (играбельного персонажа), с предыдущего выделение снимается (см. метод `deselectCell` из класса `GamePlay`).

### 10. Визуальный отклик

Сообщения об ошибках, это конечно, неплохо. Но гораздо лучше, когда пользователь сразу получает визуальный отклик.

Если персонаж игрока выбран (в данном случае лучник), то дальнейшие возможные действия могут быть:
1. Выбрать другого персонажа (не рассматриваем - см.задачу выше)
1. Перейти на другую клетку (в рамках допустимых переходов)
1. Атаковать противника (в рамках допустимого радиуса атаки)
1. Недопустимое действие (наведение на ячейку, не подпадающую под первые три варианта)

Вам необходимо в свободной форме реализовать подобную логику. При этом:
1. Если мы собираемся выбрать другого персонажа, то поле не подсвечивается, а курсор приобретает форму `pointer` (см. модуль `cursors` и метод `setCursor` из класса `GamePlay`):

![](https://i.imgur.com/yNI25eV.png)

2. Если мы собираемся перейти на другую клетку (в рамках допустимых переходов), то поле подсвечивается зелёным, курсор приобретает форму `pointer`:

![](https://i.imgur.com/Je5zqN0.png)


3. Если мы собираемся атаковать противника (в рамках допустимого радиуса атаки), то поле подсвечивается красным, курсор приобретает форму `crosshair`:

![](https://i.imgur.com/gUlSc6O.png)

4. Если мы собираемся выполнить недопустимое действие, то курсор приобретает форму `notallowed` (в этом случае при клике так же выводится сообщение об ошибке):

![](https://i.imgur.com/O8QsL40.png)

**Не забудьте написать авто-тесты на функции/методы, которые лежат в основе п.1-4**

### 11. Перемещение

Вы сделали визуальное отображение, пора заняться перемещением. Реализуйте логику, связанную с перемещением в `GameController` и обновите отображаемых на экране персонажей с помощью метода `redrawPositions`. Не забывайте убирать выделения ячеек и делать переход хода.

### 12. Атака

Пора заняться атакой. Реализуйте логику, связанную с атакой в `GameController`: для отображения урона используйте метод `showDamage` из `GamePlay`. Обратите внимание, что он возвращает `Promise` - добейтесь того, чтобы анимация урона доходила до конца. Обратите внимание, что после атаки должна пересчитываться полоска жизни над персонажем (она автоматически пересчитывается в `redrawPositions`).

UPD 14.03.19: урон рассчитывается по формуле: `Math.max(attacker.attack - target.defence, attacker.attack * 0.1)`

### 13. Ответные действия компьютера

Пора бы и компьютеру научиться отвечать на атаки игрока. Реализуйте одну из стратегий атаки компьютера на персонажей игрока, рассмотренных в рамках ДЗ, либо придумайте собственную.

### 14. Game Loop

Осталось немного: вы ходите, компьютер отвечает. Убедитесь, что персонажи исчезают после смерти (поле освобождается), урон считается и всё продолжается до того момента, пока у одного из противников есть хотя бы один живой персонаж. По окончании уровня убедитесь, что очки начисляются пользователю и происходит переход на новый уровень с генерацией команд, levelUp'ом и восстановлением жизни в соответствии с правилами, описанными в разделе "Механика".

### 15. Game Over, New Game и статистика

После завершения игры (проигрыша игрока) или завершения всех 4 уровней - игровое поле необходимо заблокировать (т.е. не реагировать на события, происходящие на нём).

При нажатии на кнопку `New Game`, должна стартовать новая игра, но при этом максимальное количество баллов (очков), набранное за предыдущие игры, должно сохраняться в `GameState`.

Для подписки на события клика на кнопку `New Game` используйте метод `addNewGameListener` из класса `GamePlay`.

### 16. Хранение состояния

Спроектируйте и реализуйте класс `GameState` (модуль `GameState`), который позволяет хранить всю информацию об текущем состоянии игры. Хранящейся в нём информации должно быть достаточно, чтобы сохранить полное состояние игры и восстановиться из него.

Сервис `GameStateService` умеет с помощью методов `save` и `load` загружать состояние из локального хранилища браузера при перезагрузке.

Удостоверьтесь, что игра стартует с нужной точки после перезагрузки.

Обратите внимание, что метод `load` может выкидывать ошибку.

**Напишите авто-тест, с моком для метода `load`, который проверяет реакцию вашего приложения на успешную и не успешную загрузку (при неуспешной загрузке должно выводиться сообщение через `GamePlay` - подумайте, как вы это будете тестировать).**

### 17. Deployment

Ваше приложение уже достаточно хорошо, если вы добрались до этого пункта. Необходимо выложить ваше творение в сеть. Воспользуйтесь для этого сервисом [GitHub Pages](https://pages.github.com). Если кратко, то достаточно создать ветку с названием `gh-pages` в вашем репозитории и положить туда только содержимое сборки (каталог `dist`, если вдруг вы забыли), после чего запушить всё на GitHub. 

GitHub Pages создаст веб-сайт по адресу: https://<ваш логин>.github.io/<название репозитория>

Ваше приложение автоматически развернётся на сервере (см. вкладку `Environments`):
![](https://i.imgur.com/kHpYWyL.png)

На странице будет указана ссылка на сам сайт и история развёртываний:
![](https://i.imgur.com/ZnNVdFA.png)

### 18. Bonus: Time Killer

Это задание не обязательно для выполнения!

Уберите ограничение в 4 уровня и реализуйте бесконечный цикл level'ов. Сохраняйте максимальное количество баллов в состоянии игры.
