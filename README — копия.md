# Отчет по библиотекам Konva.js

## 1. Что это?

**Konva.js** - это библиотека для рисования на холсте (Canvas) в браузере.

Обычный Canvas - это просто белое поле, на котором можно рисовать линии и точки, но нельзя кликнуть по нарисованному кругу или передвинуть его мышкой. Konva.js решает эту проблему.

**Что делает Konva.js:**
- Превращает обычный холст в "умную сцену"
- Каждая нарисованная фигура становится отдельным объектом
- С фигурами можно взаимодействовать (кликать, перетаскивать, менять размер)

---

## 2. Цель и задачи

**Цель:** Упростить создание интерактивных графических приложений в браузере.

**Основные задачи библиотеки:**

| Задача | Как реализовано в Konva |
|--------|------------------------|
| Создание фигур | `new Konva.Circle()`, `new Konva.Rect()` и т.д. |
| Перетаскивание | Одна строчка: `draggable: true` |
| Клики по фигурам | Метод `.on('click', ...)` |
| Изменение размера | `Konva.Transformer` (рамка с точками) |
| Вращение | Трансформер (потянуть за уголок) |

**Где применяется Konva.js:**
- Редакторы дизайна
- Доски для заметок (как Miro)
- Диаграммы и схемы
- Визуальные редакторы

---

## 3. Аналоги и их возможности

На рынке есть несколько библиотек для работы с Canvas. Они похожи внешне, но каждая создана для своих задач.

| Библиотека | Для чего нужна | Сильные стороны |
|------------|----------------|-----------------|
| **Konva.js** | Редакторы, доски, диаграммы | Простые события, перетаскивание, трансформер, поддержка React/Vue |
| **PixiJS** | 2D игры и спецэффекты | Очень высокая производительность, много кадров в секунду |
| **Fabric.js** | Редакторы изображений | Работа с SVG файлами, импорт/экспорт |
| **Paper.js** | Векторная графика | Кривые Безье, сложные пути |

**Как выбрать:**

| Если вам нужно... | Берите... |
|------------------|-----------|
| Делать редактор с фигурами, которые можно двигать и изменять | **Konva.js** |
| Делать игру с сотнями врагов на экране | **PixiJS** |
| Загружать SVG файлы и редактировать их | **Fabric.js** |

**Почему в данной работе Konva.js:**
Нужен был редактор, где пользователь может добавлять фигуры, кликать по ним, перетаскивать, менять размер. Konva.js дает все это "из коробки" - не нужно писать сложную логику самому.

---

## 4. Что такое примитивы?

**Примитивы** - это базовые фигуры, из которых строится любое изображение.

В Konva.js есть следующие примитивы:

| Примитив | Как создать | Для чего |
|----------|-------------|----------|
| Круг | `Konva.Circle` | Рисование кругов |
| Прямоугольник | `Konva.Rect` | Рисование квадратов и прямоугольников |
| Треугольник | `Konva.RegularPolygon` с параметром `sides: 3` | Рисование треугольников |
| Линия | `Konva.Line` | Рисование линий и ломаных |
| Текст | `Konva.Text` | Надписи на холсте |
| Изображение | `Konva.Image` | Картинки на холсте |


## 5. Основные отличия
Перетаскивание фигур
|Библиотека| Строк кода | Реализация |
|----------|------------|------------|
|Fabric.js|	0 строк|	Встроенная поддержка (selectable: true по умолчанию)|
|Konva.js|	1 строка|	draggable: true в параметрах|
|Paper.js|	18 строк|	Ручная реализация (onMouseDown/ Drag/ Up)|

Paper.js требует ручного написания всего механизма перетаскивания



Выделение/трансформация фигур

|Библиотека| Строк кода | Наличие | 
|----------|------------|-------|
|Fabric.js|	0 строк|	Встроенный трансформер (уголки, изменение размера)|
|Konva.js|	~30 строк|	Нужно добавить Transformer, обработчики кликов|
|Paper.js|	? | нет встроенного трансформера|

Сохранение сцены (JSON)

|Библиотека| Строк кода | Сложность |
|----------|------------|-----------|
|Fabric.js|	~15 строк|	Встроенный canvas.toJSON()|
|Paper.js|	~30 строк|	Ручное сохранение: exportJSON()|
|Konva.js|	~50 строк|	Полностью ручной парсинг всех свойств|

**Пример создания круга:**
```javascript
var circle = new Konva.Circle({
    x: 450,
    y: 300,
    radius: 70,
    fill: 'red',
    stroke: 'black',
    strokeWidth: 4,
    draggable: true
});
```

**Инициализация сцены**

```javascript
var stage = new Konva.Stage({
    container: "container", 
    width: 900,
    height: 600,
});

var layer = new Konva.Layer();
stage.add(layer);

var transformer = new Konva.Transformer();
layer.add(transformer);

stage.on('click tap', function(e) {
    if (e.target === stage) {
        transformer.nodes([]);
        layer.draw();
    }
});

function selectShape(shape){
    transformer.nodes([shape]);
    layer.draw();
}
``` 
---
**Пример создания квадрата:**
```javascript
function spawnRect(){
    var randomX = 100 + Math.random() * 300;
    var randomY = 100 + Math.random() * 300;

    var newRect = new Konva.Rect({
        x: randomX,
        y: randomY,
        width: 80,
        height: 80,
        fill: 'red',
        stroke: 'black',
        strokeWidth: 4,
        draggable: true,
    });

    newRect.on('click', (e) => {
        e.cancelBubble = true;
        selectShape(newRect);
    });

    layer.add(newRect);
    layer.draw();
}
```

---
**Пример создания треугольника:**
```javascript
function spawnTriangle(){
    var randomX = 100 + Math.random() * 500;
    var randomY = 100 + Math.random() * 500;

    var newTriangle = new Konva.RegularPolygon({
        x: randomX,
        y: randomY,
        sides: 3,
        radius: 70,
        fill: 'red',
        stroke: 'black',
        strokeWidth: 4,
        draggable: true,
    });

    newTriangle.on('click', (e) => {
        e.cancelBubble = true;
        selectShape(newTriangle);
    });

    layer.add(newTriangle);
    layer.draw();
}
```
**Функция saveMyScene() - сохраняет все фигуры в JSON файл:**
```javascript
function saveMyScene(){
    var data = [];
    layer.find('Circle, Rect, RegularPolygon').forEach(function(shape){
        data.push({
            type: shape.getClassName(),
            x: shape.x(),
            y: shape.y(),
            radius: shape.radius ? shape.radius() : undefined,
            width: shape.width ? shape.width() : undefined,
            height: shape.height ? shape.height() : undefined,
            sides: shape.sides ? shape.sides() : undefined,
            fill: shape.fill(),
            stroke: shape.stroke(),
            strokeWidth: shape.strokeWidth()
        });
    });

    var json = JSON.stringify(data);
    
    var blob = new Blob([json], {type: 'application/json'});
    var link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = 'Scene.json';
    link.click();
}
```
**Функция loadMyScene() - загружает сцену из JSON файла:**
```javascript

function loadMyScene(){
    var input = document.createElement('input');
    input.type = 'file';
    input.accept = '.json';

    input.onchange = function(e){
        var file = e.target.files[0];  
        if(!file) return;
        
        var reader = new FileReader();
        reader.onload = function(evt){
            var data = JSON.parse(evt.target.result);
            
            transformer.destroy();
            
            layer.find('Circle, Rect, RegularPolygon').forEach(function(shape){
                shape.destroy();
            });

            data.forEach(function(item){
                var shape;

                if(item.type === 'Circle'){
                    shape = new Konva.Circle({
                        x: item.x,
                        y: item.y,
                        radius: item.radius,
                        fill: item.fill,
                        stroke: item.stroke,
                        strokeWidth: item.strokeWidth,
                        draggable: true
                    });
                }
                else if(item.type === 'Rect'){
                    shape = new Konva.Rect({
                        x: item.x,
                        y: item.y,
                        width: item.width,
                        height: item.height,
                        fill: item.fill,
                        stroke: item.stroke,
                        strokeWidth: item.strokeWidth,
                        draggable: true
                    });
                }
                else if(item.type === 'RegularPolygon'){
                    shape = new Konva.RegularPolygon({
                        x: item.x,
                        y: item.y,
                        sides: 3,
                        radius: item.radius,
                        fill: item.fill,
                        stroke: item.stroke,
                        strokeWidth: item.strokeWidth,
                        draggable: true
                    });
                }

                if(shape){
                    shape.on('click', (e) => {
                        e.cancelBubble = true;
                        selectShape(shape);
                    });
                    layer.add(shape);
                }
            });
            
            transformer = new Konva.Transformer();
            layer.add(transformer);
            layer.draw();
        };
        reader.readAsText(file);
    };
    input.click();
}
```
## 6. Fabric.js

**Пример создания круга:**
```javascript
function spawnCircle(){

            var randomLeft = 100 + Math.random() * 300;
            var randomTop = 100 + Math.random() * 300;

            var newCircle = new fabric.Circle({
                left: randomLeft,
                top: randomTop,
                radius: 70,
                fill: currentColor,
                stroke: 'black',
                strokeWidth: 2,

            })
            canvas.add(newCircle);
        }
```
**Пример создания квадрата:**
```javascript
        function spawnRect(){
            var randomLeft = 100 + Math.random() * 300;
            var randomTop = 100 + Math.random() * 300;

            var newRect = new fabric.Rect({
                left: randomLeft,
                top: randomTop,
                width: 100,
                height: 100,
                fill: currentColor,
                stroke: 'black',
                strokewidth: 2

        });
        canvas.add(newRect);
        }
```
**Функция loadMyScene() - загружает сцену из JSON файла:**
```javascript
function loadMyScene(){
            var input = document.createElement('input');
            input.type = 'file';
            input.accept = '.json';

            input.onchange = function(e){
                var file = e.target.files[0];
                if(!file) return;
                
                var reader = new FileReader();
                reader.onload = function(evt){
                    var data = JSON.parse(evt.target.result);
                    canvas.loadFromJSON(data, function(){
                        canvas.renderAll();
                    });
                };
                reader.readAsText(file);
            };
            input.click();
        }
```

## 7. Paper.js
**Пример создания круга:**
```javascript
function spawnCircle() {
            var randomLeft = 100 + Math.random() * 300;
            var randomTop = 100 + Math.random() * 300;

            var circle = new paper.Path.Circle({
                center: new paper.Point(randomLeft, randomTop),
                radius: 70,
                fillColor: currentColor,
                strokeColor: 'black',
                strokeWidth: 2
            });
            setupDragging(circle);
            items.push({ shape: 'circle', item: circle });
            paper.view.draw();
        }
```
**Пример создания квадрата:**
```javascript
function spawnRect() {
            var randomLeft = 100 + Math.random() * 300;
            var randomTop = 100 + Math.random() * 300;

            var rect = new paper.Path.Rectangle({
                point: new paper.Point(randomLeft, randomTop),
                size: new paper.Size(100, 100),
                fillColor: currentColor,
                strokeColor: 'black',
                strokeWidth: 2
            });
            setupDragging(rect);
            items.push({ shape: 'rect', item: rect });
            paper.view.draw();
        }
```
**Функция loadMyScene() - загружает сцену из JSON файла:**
```javascript
function loadMyScene() {
            var input = document.createElement('input');
            input.type = 'file';
            input.accept = '.json';

            input.onchange = function(e) {
                var file = e.target.files[0];
                if (!file) return;

                var reader = new FileReader();
                reader.onload = function(evt) {
                    items.forEach(function(obj) { obj.item.remove(); });
                    items = [];

                    var sceneData = JSON.parse(evt.target.result);
                    sceneData.forEach(function(data) {
                        var item = new paper.Path();
                        item.importJSON(data.paperJSON);
                        item.fillColor = data.fillColor;
                        item.strokeColor = data.strokeColor;
                        item.strokeWidth = data.strokeWidth;
                        item.position = new paper.Point(data.positionX, data.positionY);
                        setupDragging(item);
                        items.push({ shape: data.shape, item: item });
                    });

                    paper.view.draw();
                };
                reader.readAsText(file);
            };
            input.click();
        }
```