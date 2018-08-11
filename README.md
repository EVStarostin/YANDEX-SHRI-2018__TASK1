# Задание 1 — найди ошибки

## РЕШЕНИЕ

1. Устанавливаем пакеты из npm c помощью команды в терминале `npm i`. Запускаем webpack-dev-server `npm start`.  
Видим предупреждение `"export 'default' (imported as 'initMap') was not found in './map'`,  
которое говорит нам о том, что в файле *map.js* не задан экспорт по умолчанию.  
Дописываем в файле *./map.js* для функции **initMap** `export default function initMap(...)`.  
«Экспорт по умолчанию» – своего рода «синтаксический сахар». Можно было бы и без него, импортировать значение обычным образом через фигурные скобки {…}.  
Если бы в *map.js* не было default, то в index.js необходимо было бы указать фигурные скобки `import {initMap} from "./map";`

2. Ошибок в консоли больше нет, запускаем браузер, переходим по адресу http://localhost:9000.  
Перед нами пустая страница. Открываем dev tools в браузере и видим, что единственный **блок div на странице с id="map" имеет высоту = 0**  
Высоту можно задать прямо в HTML атрибутом height или в файле css.  
В файле index.css добавляем для селекторов html, body, #map высоту 100%.  

3. Карта отображается на всю страницу, но не отображаются места размещения базовых станций.  
Открываем документацию к api яндекс карт. В разделе с примерами переходим к заголовку *Добавление объекта на карту через ObjectManager*.  
Видим, что у нас в файле *map.js* не хватает строчки `myMap.geoObjects.add(objectManager);`.  
Добавляем после строчки c опциями `objectManager.clusters.options.set('preset', 'islands#greenClusterIcons');`.  

4. Места размещения базовых станций появились, но вместо Москвы они отображаются в Иране. Переходим в файл *mapper.js*,  
находим в этом файле **coordinates: [obj.long, obj.lat]** и меняем местами *obj.long* и *obj.lat*.  

5. Места размещения базовых станций отображаются в Москве, но по кластерам нельзя определить, есть ли в нем неисправные станции.  
Для того, чтобы исправить эту проблему, просто удаляем в файле *map.js* строчку с опциями оформлением кластеров.  
`objectManager.clusters.options.set('preset', 'islands#greenClusterIcons');`  

6. Теперь по кластерам видно, если в них неактивные станции. При клике на кластер карта масштабируется.  
Но при клике на маркер базовой станции в консоль браузера вываливаются ошибки `Uncaught TypeError: Cannot read property 'destroy' of null`.  
Это происходит из-за того, что в файле details.js для методов build и clear использовались стрелочные функции () => {}, а функции-стрелки не имеют своего this.  
Меняем на обычные функции `build: function() {...}`, `clear: function() {...}`.  

7. При клике на маркере появляется tool-tip с информацией, но график не отображается.  
Открываем документацию к **библиотеке chart.js** изучаем и видим, что у нас в файле *chart.js*  
при инициализации опциях на оси Y ограничение max: 0
`const chart = new Chart(ctx, {`
`  ...`
`  options: {`
`      ...`
`      scales: {`
`          xAxes: [{ ticks: { display: false } }],`
`          yAxes: [{ ticks: { beginAtZero: true, **max: 0** } }]`
`      }`
`  }`
`});`
Убираем **max: 0**, теперь все прекрасно работает, график отображается.  


