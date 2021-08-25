---
title: Ленивая динамика
---

Если сложно придумать порядок обхода таким образом, чтобы все предыдущие
значения уже были посчитаны, то можно вместо циклов использовать
рекурсивную функцию и запоминать посчитанный результат, чтобы не
считать несколько раз одно и то же.

Решим, например, обычную задачу о рюкзаке таким образом. Изначально все
$dp\[i\]\[j\]=-1$, это будет обозначать, что значение еще не посчитано,
кроме $dp\[0\]\[j\]=0$.

``` C++ numberLines
int calc(int i, int j) {
    if (dp[i][j] == -1) {
        dp[i][j] = calc(i - 1, j);
        if (a[i] <= j) {
            dp[i][j] = max(dp[i][j], calc(i - 1, j - a[i]) + c[I]);
        }
    }
    return dp[i][j];
}

answer = 0
for (int j = 0; j <= W; j++) {
    answer = max(answer, calc(n, j))};
}
```

Время работы так же составит $O(nW)$, так как каждое значение мы считаем
только один раз, но истинное время работы будет в несколько раз больше,
потому что константа на вызовы функции значительно выше чем на простой
цикл.

Также можно заметить, что в такой динамике мы посещаем только
действительно нужные состояния, что в некоторых задач
приводит к более приятной асимптотике.

Одной частью решения задачи на ДП было установление порядка пересчета
состояний - перед подсчетом нового состояния все те, от которого оно
зависит, уже должны быть посчитаны.

Идея ленивого динамического программирования состоит в том, чтобы об
этом не думать. Если мы уверены, что в графе состояний нет
циклических зависимостей (то есть от текущего состояния по
цепочке зависимостей мы не дойдем до него же самого), то можно
отдать порядок пересчета на откуп рекурсии.

Реализуем подсчет состояния в виде рекурсивной функции. Вместо
классического массива $dp\[i\]\[j\]$, например, будет функция
$calcDP(i, j)$. Она будет вызывать $calcDP(i - 1, j - 1)$ и $calcDP(i -
1, j)$. Чтобы наше решение не стало работать за экспоненту, будет
запоминать результаты работы функций в отдельном массиве. Этот
прием называется мемоизация (memoization). По-прежнему каждое
состояние будет посчитано только один раз.

Во многих языках программирования мемоизация является встроенной
оптимизацией.

В качестве примера рассмотрим вычисление [фунцкии
Аккермана](https://ru.wikipedia.org/wiki/%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D1%8F_%D0%90%D0%BA%D0%BA%D0%B5%D1%80%D0%BC%D0%B0%D0%BD%D0%B0)
(потому что считать числа Фибоначчи уже надоело).

``` c++ numberLines
long long calc(int m, int n) {
    if (memoize[m][n] != 0) {
        return memoize[m][n];
    }
    if (m == 0) {
        return memoize[m][n] = n + 1;
    }
    if (n == 0) {
        return memoize[m][n] = calc(m - 1, n);
    }
    return memoize[m][n] = calc(m - 1, calc(m, n - 1));
}
```

В массиве $memoize$ сохраняются значения функции. Изначально мы
предполагаем, что массив заполнен нулями, поэтому можно
считать, что непосещенное состояние заполнено нулем, именно так
мы понимаем, что в этом состоянии мы впервые. Здесь важно, что сама
функция не принимает значения, равные нулю.

Несмотря на то что в ленивой динамике не надо думать о порядке
пересчета, по эффективности этот метод в среднем уступает
обычному подсчету, так как рекеурсивные вызовы требуют
дополнительных затрат памяти, о которыз многие забывают.
(Подробнее про
[рекурсию](https://ru.wikipedia.org/wiki/%D0%A1%D1%82%D0%B5%D0%BA_%D0%B2%D1%8B%D0%B7%D0%BE%D0%B2%D0%BE%D0%B2)).

Если коротко, то при вызове подпрограммы необходимо запоминать
информацию о том, откуда мы пришли в нее. Надо помнить, в
какую точку надо будет пойти после выхода из подпрограммы, это
дополнительные затраты памяти и времени.

Тем не менее, компиляторы умеют оптимизировать рекурсию в некоторых
случаях. Например, существует **хвостовая рекурсия** - из программы
происходит только один рекурсивный вызов. Такой тип рекурсии сразу
разворачивается компилятором в цикл **while**.