# Десять причин нашего перехода с иконочного шрифта на SVG

У нас на [lonelyplanet.com][1] используется **много** иконок и недавно перед 
нами встала задача перенести их из иконочного шрифта в SVG-файлы. Я хочу 
поделиться причинами, которые нас к этому привели, а также некоторыми 
ограничениями со стороны SVG,  которыми мы столкнулись, и как мы с ними 
справились.

## 1. Разделение функций

На [lonelyplanet.com][2] используется подключённый шрифт и до недавнего времени 
иконки были упакованы в один с ним файл, причём глифы хранились в области 
частного использования Unicode. Такое решение нас полностью устраивало, 
поскольку для загрузки всех иконок требовался только один HTTP-запрос, однако в 
то же время оно ограничивало возможность управления приоритетностью загрузки 
ресурсов. 

У нас есть небольшая подгруппа иконок, отображение которых является критически 
важным для обеспечения качественного пользовательского опыта; шрифта и других 
иконок это не касается. Для нас загрузка второстепенных ресурсов желательна 
после загрузки контента страницы, однако при предыдущем подходе к хранению 
иконок это было невозможно. 

Отделение важных иконок от шрифта и остальных иконок дало нам 
возможность более тонкого управления их загрузкой.

### Контраргумент

Объединять подключённый шрифт и иконочный шрифт не обязательно, их можно хранить 
отдельно. Наверное так мы бы и сделали если бы решили остановиться на 
использовании иконочного шрифта.

## 2. Некоторые устройства не поддерживают области частного использования Unicode

До меня и раньше доходили слухи о том, что есть устройства, которые игнорируют 
глифы в областях частного использования Unicode и отображают вместо них смайлики, 
однако до недавнего времени я с этим не сталкивался. Смайлики с момента своего 
появления хранились в области частного использования Unicode, хоть и в разных 
плоскостях, так что возникновение конфликтов вполне закономерно. 

Уже не помню на каком устройстве я проводил тестирование, когда увидел что одна 
из наших иконок с «птичкой» была заменена иконкой цветной печати. Страница 
выглядела дилетантской и сломанной, что без сомнения послужило дополнительным 
стимулом для перехода на SVG.

## 3. Чёрные квадратики и крестики в Opera Mini

Добиться правильной поддержки и распознавания начертания шрифта никогда не было 
просто. Уверен что вы все видели эту иллюстрацию отрисовки шрифта awesome в 
Opera Mini:

![пример][отрисовка шрифта awesome в Opera Mini]

Я не буду вдаваться в подробности, так как проблемы поддержки Opera Mini и 
многих других платформ уже хорошо описаны в [этой статье][3] от [@kaelig][4].

Если абстрагироваться от Opera Mini, я думаю здесь особенно важен тот момент, 
что мы определили слепое пятно, которое контролировать оказались не в состоянии. 
Мы физически не можем провести тестирование на всех устройствах без исключений, 
потому следует отдавать предпочтение приёмам, при которых корректное отображение 
наиболее вероятно.

На данный момент мы получаем не очень большую долю трафика с Opera Mini, однако 
мы — компания-туроператор, которая предоставляет услуги в любых условиях и при 
любом качестве подключения к сети, следовательно у нас высокие требования к себе. 
SVG и PNG дают нам большую уверенность в том, что пользователь не увидит 
испорченную и непонятную страницу. 

## 4. В последнее время поддержка иконочных шрифтов в Chrome отвратительна

В Chrome Canary и Beta недавно появилась отвратительная [ошибка при 
отображении шрифтов][5]. Если вы её пока не встречали, после определённого 
периода неактивности страницы шрифт сбрасывается и заменяется на системный. 

Когда происходит сброс шрифта и ваш текст отображается в Georgia, это немного 
действует на нервы, но это не критично. А вот когда тот самый шрифт отвечает и 
за отображение иконок, страница внезапно заполняется мусором из чёрных квадратиков, 
в результате чего пользоваться страницей становится невозможно. 

Эта ошибка появилась как раз когда мы осуществляли переход на SVG. В результате, когда 
начали приходить первые отчёты об обнаружении этой ошибки на нашем сайте, мы перешли 
на новую систему и вздохнули с облегчением.

### Контраргумент

Эта ошибка так и не добралась до официальной сборки Chrome.

## 5. Чётче иконки в Firefox

Мы обнаружили что в Firefox наш шрифт отображается немного более жирным, чем в 
других браузерах. Когда дело касается текста, в этом нет ничего страшного (хотя 
в принципе и хорошего тоже), а вот из-за жирных иконок вся страница выглядит 
неухоженной и аляповатой. Используя SVG нам удалось добиться нормального 
отображения иконок во всех браузерах.

## 6. Нет необходимости всегда использовать генерируемый контент

Если вы хотите использовать шрифтовые иконки в css, вам придётся прописывать их 
используя свойство `content` для генерируемого контента. Иногда чтобы это было 
возможно, приходится усложнять код, например, когда вы уже используете 
псевдоэлементы `:before` и `:after` или когда элемент не поддерживает 
генерируемое содержимое. 

В таком случае можно добавлять их прямо в разметку, однако в результате 
получается множество html-элементов разбросанных по коду, которые могут легко 
затеряться в большом приложении.

С SVG эта проблема исчезает, так как вы не ограничены генерируемым контентом и 
можете применять иконки в этом формате как фоновое изображение для любого 
элемента.

## 7. Проще позиционирование

Возможно это результат того как мы создали и упорядочили глифы иконок, однако 
точное их позиционирование нам ни разу не далось легко (так же как и одинаковое 
отображение во всех браузерах). Мы прибегали к хакам на основе высоты строки и 
абсолютному/относительному позиционированию чтобы они отображались так как надо 
и нам было трудно найти абстрактное решение, которое работало бы всегда.

Используя SVG нам было легче их позиционировать. Мы используем 
`background-size: cover` и просто делаем размеры элемента резиновыми чтобы во 
всех браузерах они отображались одинаково.

## 8. Многоцветные иконки

Как хорошо известно, шрифтовые иконки ограничиваются только одним цветом. SVG, 
напротив, поддерживает большое количество цветов, а также градиентов и других 
графических элементов.

Нам всегда была нужна поддержка многоцветных иконок для карт, раньше кроме 
иконочного шрифта мы использовали для этого ещё и дополнительный PNG-спрайт. В 
результате перехода на SVG мы смогли избавиться от этого спрайта, который для 
пользователя являлся дополнительным запросом, а для нас — дополнительным 
объектом, нуждающимся в поддержке.

### Контраргумент

Многоцветный иконочный шрифт можно получить посредством наложения иконок.

Тем не менее, сделать это качественно намного труднее: если правильное 
кросс-браузерное позиционирование одного глифа — непростая задача, с двумя всё 
становится ещё сложнее.

## 9. SVG позволяет использовать иконки с анимацией

Мы пока не воспользовались этим свойством, но вполне возможно что такую 
возможность рассмотрим раз уж она у нас теперь есть.

## 10. Нам не давала покоя мысль что мы используем хак

Применение шрифта для отображения иконок нам всегда казалось хаком. Бесспорно, 
он великолепен, однако суть всё же состоит в маскировании  объекта одного типа 
под какой-то другой.

## Как насчёт преимуществ использования шрифта?

Использование иконочных шрифтов всё же имеет некоторые преимущества перед SVG 
и нам пришлось тщательно всё обдумать перед осуществлением перехода. Наиболее 
существенными преимуществами являются поддержка браузерами и возможность 
изменения цвета. 

Стоит признать что нам было бы намного сложнее справиться с этими проблемами без 
огромной работы, проделанной командой Filament Group на [Grunticon][6].

## Изменение цвета

**Огромным** преимуществом иконочного шрифта является его гибкость. У него нет 
ограничений в количестве вариаций цвета, его можно легко менять в зависимости от 
текущего состояния (`:hover`, `:focus`, `.is-active` и др.). Это редкостная 
роскошь, которая очень способствует ускорению разработки. И это одна из причин того,
что мы так долго откладывали переход на SVG.

### Наше решение

Для обеспечения такой функциональности есть несколько решений, хотя все они 
(пока) имеют свои ограничения. Мы наконец нашли приём, который нас вполне 
устраивает и проводит линию между гибкостью настроек и размером файла.

В Grunticon каждая иконка объявляется отдельно, таким образом избегается 
использование спрайтов. Мы последовали их примеру, но, хотя мы и 
использовали для каждой иконки один css-селектор, каждая иконка была 
представлена в шести цветах как показано здесь.

![иконки][иконка шести цветов]

Так как мы всего лишь несколько раз дублировали одну и ту же иконку в одном и 
том же файле, его размер сжался до размера одной иконки (плюс около 50 байт на 
GZIP указатели). Это значит что мы можем создать практически любое количество 
вариаций цвета почти без дополнительных затрат. Вот пример работы этого приёма 
на [jsFiddle][7].

Это решение дало нам ту гибкость, которую мы уже считали утраченной. Мы также 
получили дополнительное преимущество в виде более корректного отображения 
цветовой палитры, которого мы постепенно лишились при использовании иконочного 
шрифта. 

С помощью этого приёма можно также без усилий применять к иконкам изменения 
привязанные к состоянию, изменяя позиционирование фона.

Нужно отметить что в будущем мы возможно сможем полностью отказаться от спрайтов 
и использовать [идентификаторы фрагментов в SVG][8] для изменения цвета.

## Поддержка браузеров

Иконочный шрифт [работает во всех браузерах вплоть до IE8][9], в отличие от 
[SVG][10]. Для нашего варианта реализации нам также нужна поддержка 
`background-size`, [которая является почти такой же как и для SVG][11].

### Наше решение

У [Grunticon][12] реализован креативный подход к старым версиям браузеров. Он 
создаёт PNG-версии SVG-файлов и передаёт их старым браузерам в зависимости от 
того поддерживают они SVG или нет.

В связи с отсутствием поддержки `background-size`, мы решили для старых версий 
браузеров использовать только часть иконок (только очень важные, такие как 
логотип). Мы могли бы обойтись без этого css-свойства, с помощью изменения 
размеров всех исходных SVG файлов, однако поскольку многие иконки используются с 
разными вариантами размера, мы решили использовать только иконки первостепенной 
важности с одинаковым размером в спрайте.

Мы также пробовали использовать [этот полифил `background-size`][13], однако 
вскоре отказались от него. Я бы **крайне не советовал его использовать** если 
вам нужно изменить размер больше, чем для одного-двух изображений. Обнаружилось, 
что его применение для более чем двух изображений приводит к постоянным сбоям в 
IE8.

## Стоило ли оно того?

Результаты использования обеих приёмов независимы от разрешения, масштабируемы и 
довольно мало весят, так что с точки зрения пользователя оба являются хорошими. 
Взвесив все за и против мы решили, что SVG дал нам больше уверенности в том, как 
наше приложение будет выглядеть для каждого пользователя и этот дополнительный 
элемент контроля сыграл решающую роль.

SVG на [Lonely Planet][14] используется с ноября 2013 года и пока никаких 
проблем с разработкой не возникало.

[1]: http://www.lonelyplanet.com/france/paris
[2]: http://www.lonelyplanet.com/france/paris
[3]: http://blog.kaelig.fr/post/33373448491/testing-font-face-support-on-mobile-and-tablet
[4]: https://twitter.com/kaelig
[5]: https://code.google.com/p/chromium/issues/detail?id=236298&q=font&colspec=ID%20Pri%20M%20Iteration%20ReleaseBlock%20Cr%20Status%20Owner%20Summary%20OS%20Modified
[6]: https://github.com/filamentgroup/grunticon
[7]: http://jsfiddle.net/Wmcbz/
[8]: http://www.w3.org/TR/SVG/linking.html#LinksIntoSVG
[9]: http://caniuse.com/#search=font-face%20web
[10]: http://caniuse.com/#search=SVG%20in%20css
[11]: http://caniuse.com/#search=background-size
[12]: https://github.com/filamentgroup/grunticon
[13]: https://github.com/louisremi/background-size-polyfill
[14]: http://www.lonelyplanet.com/england/london

[отрисовка шрифта awesome в Opera Mini]: img/grunticon_workflow_operamini2.png
[иконка шести цветов]: img/icon-example.png