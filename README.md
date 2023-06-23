# OSM
# Содержание

- [Теория](#Теория)
- [Использование](#installation)
  - [Получение графа дорог с помощью OSMnx](#Получение-графа-дорог-с-помощью-OSMnx)
  - [Построение маршрута и расчет расстояния с помощью OSMnx](#Построение-маршрута-и-расчет-расстояния-с-помощью-OSMnx)
  - [Рассчитать матрицу расстояний с помощью OSMnx](#Рассчитать-матрицу-расстояний-с-помощью-OSMnx)
  - [Получение объектов с помощью OSMnx](#Получение-объектов-с-помощью-OSMnx)
- [Что почитать](#Что-почитать)

# Теория

### OpenStreetMap

OpenStreetMap (OSM) - Некоммерческий веб-картографический проект по созданию силами сообщества участников - пользователей Интернета подробной свободной и бесплатной географической карты мира.

- Сайт: [OpenStreetMap](https://www.openstreetmap.org/)

### Overpass API

Overpass API - это API только для чтения, предоставляющее доступ к базе данных OpenStreetMap. Оно позволяет пользователям выполнять запросы и извлекать конкретные географические данные из OSM на основе различных критериев, таких как местоположение, теги и свойства. Overpass API - мощный инструмент для получения данных OSM, отвечающих определенным требованиям.

- Сайт: [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API)

### Overpass Turbo

Overpass Turbo - это веб-инструмент, предоставляющий пользовательский интерфейс для взаимодействия с Overpass API. Он позволяет пользователям создавать и выполнять запросы к данным OSM, визуализировать результаты на интерактивной карте и экспортировать данные в различных форматах. Overpass Turbo vereinfacht den Prozess der Exploration und Extraktion von OSM-Daten für unterschiedliche Zwecke.

- Сайт: [Overpass Turbo](https://overpass-turbo.eu/)

### Хранение геоданных

OpenStreetMap, Overpass API и Overpass Turbo хранят геоданные с использованием специализированной модели данных, называемой моделью данных OpenStreetMap. Географические данные в OSM представлены в виде узлов (nodes), линий (ways) и отношений (relations). Узлы представляют отдельные точки с координатами, линии представляют линейные объекты, такие как дороги и реки, а отношения представляют собой коллекции узлов, линий и других отношений для представления сложных или многофункциональных объектов.

Геоданные в OpenStreetMap хранятся в распределенной форме на нескольких серверах и организованы в иерархическую структуру, называемую сеткой тайлов. Каждый тайл содержит определенную область карты и связан с уникальным идентификатором. Эта структура позволяет эффективно хранить, извлекать и отображать геоданные для различных картографических приложений.

### OSMnx

OSMnx - это библиотека на языке Python, позволяющая получать, анализировать и визуализировать данные OpenStreetMap. Она предоставляет простой в использовании интерфейс для загрузки данных OSM для конкретных регионов, создания графов сети на основе дорожных данных и выполнения различных задач анализа сети. OSMnx особенно полезен для градостроителей, исследователей и разработчиков, работающих с пространственными и транспортными данными.

- Документация: [OSMnx](https://osmnx.readthedocs.io/en/stable/index.html)

### NetworkX (NX)

NetworkX (NX) - это библиотека на языке Python для создания, манипулирования и изучения структуры, динамики и функций комплексных сетей. Она предоставляет широкий набор инструментов и алгоритмов для анализа сетей, включая меры центральности, анализ связности и поиск пути. NX может использоваться совместно с OSMnx для анализа и обработки графов сети, извлеченных из OpenStreetMap.


# Использование

- [Описание всех тегов Open Street Map](https://wiki.openstreetmap.org/wiki/Map_features). 
- [Документация OSMnx](https://osmnx.readthedocs.io/)  

## Получение графа дорог с помощью OSMnx

Убедитесь, что у вас установлены следующие пакеты `osmnx`,`networkx`, `matplotlib` в вашей среде Python (`pip install osmnx networkx matplotlib`). <br>

```python
import osmnx as ox
import networkx as nx
import matplotlib.pyplot as plt

# Указываем местоположение для получения графа дорог
place_name = "Nur-Sultan, Kazakhstan"

# Получаем граф дорог для указанного местоположения
graph = ox.graph_from_place(place_name, network_type='drive')
```

Параметр `network_type`: определяет тип дорожной сети для извлечения. Некоторые доступные значения включают `all` (все типы дорог), `walk` (только пешеходные дорожки), `bike` (только велосипедные дорожки), `drive` (только дороги для автомобилей), `drive_service` (только служебные проезды: внутриквартальные, въездные, парковочные )

> **Примечание:** `walk`, `bike` отображают все типы дорог почему-то все равно (так что нужно провести дополнительную фильтрацию, если нужно вытащить только пешеходный дорожный граф)

Фильтруем, чтобы получить только пешеходный дорожный граф:
```python
# Создайте копию графа (по желанию)
filtered_graph = graph.copy()

# Итерируйтесь по каждому ребру графа
for u, v, k, data in graph.edges(keys=True, data=True):
    # Проверьте тип дороги на пешеходные тропы
    if "footway" not in data["highway"] and "path" not in data["highway"]:
        # Если тип дороги не является пешеходной тропой, удаляем ребро из графа
        filtered_graph.remove_edge(u, v, k)
```

```python
# Отображаем ребра графа дорог (если хотите отобразить отфильтрованный, то graph замените на filtered_graph)
fig, ax = ox.plot_graph(graph, edge_color='white', edge_linewidth=0.5, node_size=0, show=False) 

# Показываем граф
plt.show()
```

1. Пример отображенного дорожного графа с `network_type="drive"` (только автомиобильные дороги):
![Figure_Drive_Graph.png](./Figure_2.png "Пример отображенного дорожного графа 'только автомиобильные дороги'")

2. Пример отображенного дорожного графа с `network_type="all"` (все типы дорог):
![Figure_All_Graph.png](./Figure_All_Graph.png "Пример отображенного дорожного графа 'все типы дорог'")

3. Пример отображенного пешеходного дорожного графа после фильтрации (только пешеходные):
![Figure_Walk_Filtered_Graph.png](./Figure_Walk_Filtered_Graph.png "Пример отображенного пешеходного дорожного графа после фильтрации (только пешеходные)")

## Построение маршрута и расчет расстояния с помощью OSMnx

Убедитесь, что у вас установлены следующие пакеты `osmnx`,`matplotlib` в вашей среде Python (`pip install osmnx matplotlib`).
> **Примечание:** чтобы получить маршрут от точки A к точке B !именно по дорожному графу, необходимо сначала получить ближайший узел дорожного графа к заданной вами координате 

> В примере ниже получаем маршрут используя `ox.distance.shortest_path` - кратчайший путь по алгоритму Дейкстры, но также имейте ввиду, что можно использовать `osmnx.distance.k_shortest_paths` - получить k заданное количество кратчайших путей по алгоритму Йена [подробнее в документации](https://osmnx.readthedocs.io/en/stable/osmnx.html#osmnx.distance.k_shortest_paths)

```python
import osmnx as ox
import matplotlib.pyplot as plt

# Указываем местоположение для получения графа дорог
place_name = "Nur-Sultan, Kazakhstan"

# Создайте граф дорожной сети из OpenStreetMap данных
graph = ox.graph_from_place(place_name, network_type='drive') 

# Задайте координаты двух точек (широта и долгота) и сразу получаем ближайший узел дорожного графа
point1 = ox.distance.nearest_nodes(graph, 71.4375894, 51.1247571)  # координаты в ввиде (x, y) долгота, широта
point2 = ox.distance.nearest_nodes(graph, 71.36435335414731, 51.19407083146049)  # координаты в ввиде (x, y) долгота, широта

# Добавьте геометрию (точки) на граф дорожной сети
ox.distance.add_edge_lengths(graph)

# Рассчитайте кратчайший путь и расстояние между точками
route = ox.distance.shortest_path(graph, point1, point2, weight='length')

# Получите длины ребер в маршруте и суммируйте их
distance = sum(ox.utils_graph.get_route_edge_attributes(graph, route, 'length'))

print(f'Расстояние между точкой 1 и точкой 2: {distance} метров')
```

Для отображения маршрута на графике с помощью matplotlib:
- убедитесь, что у вас установлены следующие пакеты `folium` в вашей среде Python (`pip install folium`)
- добавьте следующий код в ваш скрипт
```python
# Получите геометрию маршрута
route_geom = ox.plot_route_folium(graph, route=route, route_color='blue', route_width=4, edge_width=2)

# Построить граф дорожной сети и отобразить на нем маршрут
fig, ax = ox.plot_graph_route(graph, route, route_linewidth=4, node_size=0, bgcolor='w')

# Показать график
plt.show()
```
Пример отображенного маршрута между 2мя точками с `network_type='drive'`:
![Figure_route_1](./Figure_route_1.png "Пример отображенного маршрута между 2мя точками")

## Рассчитать матрицу расстояний с помощью OSMnx
Distance Matrix API позволяет получить информацию о расстоянии (и времени - пока не входит) в пути между точками на карте.
### Пример входных данных 
`filteredMarkersLngLat` - содержит координаты точек на карте в виде `[lng, lat]`, т.е `[долгота, широта]`
```json
{
  "filteredMarkersLngLat":[
    [71.416287,51.110922],
    [71.411893,51.118386],
    [71.418481,51.11747],
    [71.423956,51.11671]
  ],
  "counterFilteredMarkersLngLat":4
}
```
### Пример ответа
Расстояния как массив массивов, представляющих матрицу в порядке убывания строк. `distances[i][j]` дает расстояние пути от `i-го` источника до `j-го` пункта назначения. Все значения указаны в метрах. Расстояние между одной и той же координатой всегда равно `0`
```json
{
  "distances": [
    [0, 1149.1499999999999, 752.18, 1112.069], 
    [1158.594, 0, 520.776, 917.913], 
    [774.1560000000001, 471.019, 0, 427.081], 
    [1099.2569999999998, 862.416, 391.39700000000005, 0]
  ]
}
```
### Код скрипта
```python
import osmnx as ox
import json

# Загрузка данных из файла JSON
with open('./json/filtered_points_small.json', 'r') as file:
    data = json.load(file)
    points = data["filteredMarkersLngLat"]

# Преобразование координат точек в числа
for i in range(len(points)):
    points[i] = [float(coord) for coord in points[i]]

# Создание графа дорожной сети из OpenStreetMap данных
graph = ox.graph_from_place('Nur-Sultan, Kazakhstan', network_type='drive')

ox.distance.add_edge_lengths(graph)

# Функция для рассчета расстояния между двумя точками
def calculate_distance(graph, point1, point2):
    route = ox.distance.shortest_path(graph, point1, point2, weight='length')
    # distance = sum(ox.utils_graph.get_route_edge_attributes(graph, route, 'length')) osm_distance_matrix.py:22: UserWarning: The `get_route_edge_attributes` function has been deprecated and will be removed in a future release. Use the `route_to_gdf` function instead.
    distance = sum(ox.utils_graph.get_route_edge_attributes(graph, route, 'length'))
    return distance

# Рассчет расстояний от каждой точки до всех остальных точек
distances = []
for i, point in enumerate(points):
    point_coords = point
    point_node = ox.distance.nearest_nodes(graph, point_coords[0], point_coords[1])
    distances.append([])
    for j, other_point in enumerate(points):
        other_point_coords = other_point
        other_point_node = ox.distance.nearest_nodes(graph, other_point_coords[0], other_point_coords[1])
        distance = calculate_distance(graph, point_node, other_point_node)
        distances[i].append(distance)

# Сохранение результатов в виде матрицы расстояний в новый JSON-файл
output_data = {'distances': distances}
with open('./json/output_distance_matrix_small.json', 'w') as output_file:
    json.dump(output_data, output_file)

print("Матрица расстояний сохранена в файле 'output_distance_matrix.json' в папке json.")
```

## Получение объектов с помощью OSMnx
> Для получения объектов, таких как парки, здания, другие достопримечательности и учреждения.

Для отображения объектов на графике с помощью matplotlib:
- убедитесь, что у вас установлены следующие пакеты `geopandas`, `pandas` в вашей среде Python (`pip install geopandas pandas`)

```python
import osmnx as ox
import geopandas as gpd
import networkx as nx
import pandas as pd
import matplotlib.pyplot as plt

# Указываем местоположение для получения графа дорог
place_name = "Nur-Sultan, Kazakhstan"

# Define the tags for filtering objects
# tags = {'tourism': True, 'amenity': True, 'leisure': True}
tags = {'leisure': True}

# Retrieve all the points of interest (POIs) in the specified place
gdf = ox.geometries.geometries_from_address(place_name, tags, dist=10000) # dist in meters

gdf.plot(ax=plt.gca(), color='red', alpha=0.6, markersize=10)

# Отображаем карту
plt.show()
```

Пример отображенных объектов с `leisure=true` (развлекательных и спортивных объектов, в том числе парки)
![Figure_Leisure.png](./Figure_Leisure.png "Пример отображенных объектов с 'leisure=true' (развлекательных и спортивных объектов)")


## Что почитать
- https://osmnx.readthedocs.io/ - документация OSMnx
- https://wiki.openstreetmap.org/wiki/Map_features - описание всех тегов OSM
- https://habr.com/ru/articles/688556/ - чувак на Java работатет с OSM
