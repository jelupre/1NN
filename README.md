# Задачи классификации

<h1>Метрические алгоритмы классификации</h1>

<p>
  Все метрические алгоритмы классификации будем рассматривать на датасете "Ирисы Фишера", а конкретнее - тренировочная выборка по ширине и длине лепестка и виду ириса. В выборке будет 150 цветков. Вот так, собственно, выглядит наш обучающий набор.
</p>

![screenshot of sample](https://github.com/jelupre/ML1/blob/master/irises.png)

<p>
  kNN расшифровывается как k Nearest Neighbor или k Ближайших Соседей — это один из самых простых алгоритмов классификации, также иногда используемый в задачах       
  регрессии. Благодаря своей простоте, он является хорошим примером, с которого можно начать знакомство с областью Machine Learning. 
</p>

<p>
  Задача классификации в машинном обучении — это задача отнесения объекта к одному из заранее определенных классов на основании его формализованных признаков. 
  Каждый из объектов в этой задаче представляется в виде вектора в N-мерном пространстве, каждое измерение в котором представляет собой описание одного из признаков 
  объекта. 
</p>

<h2>Метод ближайшего соседа</h2>

<p>
  Рассмотрим задачу kNN при k = 1 на языке R.
</p>

<h3>Алгоритм</h3>

Для классификации каждого из объектов тестовой выборки необходимо последовательно выполнить следующие операции:
<ul>
  <li>Вычислить расстояние до каждого из объектов обучающей выборки</li>
  <li>Найти объект из тренировочной выборки, от которого расстояние до классифицируемого объекта будет минимальным</li>
  <li>Класс классифицируемого объекта — это класс, ближайшего к нему объекта из обучающей выборки</li>
</ul>

<p>
  Введём число точек в тестовой выборке (n). Теперь создадим n точек с ограничениями по длине и ширине лепестка при помощи cbind и runif. Отбразим 
  тренировочную выборку. Рисуя тестовые точки, запускаем алгоритм 1NN для определения принадлежности одному из трёх существующих классов. 
  В самой функции 1NN ищем ближайшего по Евклидову расстоянию соседа для текущей точки и возвращаем вид ириса для неё же. 
  На рисунке ниже показан результат 10 случайно выбранных точек.
</p>

![screenshot of sample](https://github.com/jelupre/ML1/blob/master/1nn_10points.png)

<p>
  Рассмотрим программную реализацию функции 1NN на языке программирования R.  
</p>


```R
oneNN <- function(set, point){
  
  ## возьмём за ближайшего соседа первую точку в наборе
  min_distance <- distance_of_Euclid(set[1, 1:2], point)
  number_of_nearest <- 1
  
  ## попробуем найти соседа ближе
  for(i in 2:N){
    if (distance_of_Euclid(set[i, 1:2], point) < min_distance){
      min_distance <- distance_of_Euclid(set[i, 1:2], point)
      number_of_nearest <- i
    }
  }
  
  ## возвращаем вид ириса ближайшего соседа
  return(set[number_of_nearest, 3])
  
}
```

<h2>Метод k-ближайших соседей</h2>

<h3>Алгоритм</h3>

<p>Для классификации каждого из объектов тестовой выборки необходимо последовательно выполнить следующие операции:</p>

<ul>
    <li>Вычислить расстояние до каждого из объектов обучающей выборки</li>
    <li>Отобрать k объектов обучающей выборки, расстояние до которых минимально</li>
    <li>Класс классифицируемого объекта — это класс, наиболее часто встречающийся среди k ближайших соседей</li>
</ul>

<p>
  Работа алгоритма k-ближайших соседей начинается с сортировки всех элементов обучающей выборки по расстоянию относительно тестовой точки, далее отправляем в саму функцию kNN обучающую выборку, k и точку, которую нужно классифицировать. Проходим циклом от 1 до k и смотрим, каких ирисов больше. Относим классифицируемую точку к тому классу, которого среди этих k элементов больше. Ниже демонстрируется код на языке R.
</p>

```R
## сортировка тренировочного набора
ordered_set <- sort(train_set, points[i, 1:2])
## функция kNN
kNN <- function(k, ordered_arr, point){
  
  ind1 <- 0;
  ind2 <- 0;
  ind3 <- 0;
  
  for (i in 1:k) {
    if (ordered_arr[i, 3] == 'setosa') {
      ind1 <- ind1 + 1
    }
    if (ordered_arr[i, 3] == 'versicolor') {
      ind2 <- ind2 + 1
    }
    if (ordered_arr[i, 3] == 'virginica') {
      ind3 <- ind3 + 1
    }
  }
  
  if (ind1 >= ind2 && ind1 >= ind3) {
    return(1)
  }
  if (ind2 >= ind1 && ind2 >= ind3) {
    return(2)
  }
  if (ind3 >= ind1 && ind3 >= ind2) {
    return(3)
  }
  
}
```

<p>
  Прежде чем запустить алгоритм kNN для тестовой выборки необходимо выбрать k, для этого будем использовать LOO (leave-one-out CV). Как он работает? На вход мы получаем тренировочную выборку. Поочерёдно "вытаскиваем" из набора по одной точке и для каждой точки и выборки без неё запускаем kNN, изменяя k от 1 до длины "новой" обучающей выборки. Сравниваем полученный класс точки с классом этой же точки из изначальной тренировочной выборки, если классы не совпадают, то увеличиваем количество ошибок на данном k на 1. Пройдя все точки по всем k, ищем минимальное количество ошибок, и индекс этого элемента и будет лучшим k для этой выборки.
</p>

```R
LOO <- function(arr){
  
  row <- dim(arr)[1]
  
  Q <- matrix(0, (row - 1), 1)
  
  for (i in 1:row) {
    
    point <- arr[i, 1:2]
    new_arr <- arr
    new_arr <- new_arr[-i, ]
    ordered_arr <- sort(new_arr, point)
    
    tmp <- 0
    
    if (arr[i, 3] == "setosa") {
      tmp <- 1
    }
    if (arr[i, 3] == "versicolor") {
      tmp <- 2
    }
    if (arr[i, 3] == "virginica") {
      tmp <- 3
    }
    
    
    for (k in 1:(row - 1)) {
      
      class <- kNN(k, ordered_arr, point)
      
      if (class != tmp) {
        Q[k] <- Q[k] + 1
      }
      
    }
    
  }
   
  min_k <- 1
  min_v <- Q[min_k]
  
  for (i in 1:(row - 1)) {
    if (min_v > Q[i]) {
      min_v <- Q[i]
      min_k <- i
    }
  }
  
  
  return(min_k)
  
}
```

![screenshot of sample](https://github.com/jelupre/ML1/blob/master/6nn_10points.png)
