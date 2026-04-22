# Проект по дискретным структурам
____________________________________________________
На доработку (изменить названия переменных и убрать комментарии): <br>
Задача 5 (заданный списками смежности):
```
def main():
    n = int(input("Введите количество вершин: "))
    adj = [[] for _ in range(n)]
    print("Введите списки смежности (соседей) для каждой вершины:")
    for i in range(n):
        neighbors = input(f"Вершина {i+1} (номера от 1 до {n} через пробел): ").strip()
        if neighbors:
            for nb in map(int, neighbors.split()):
                if 1 <= nb <= n:
                    adj[i].append(nb - 1)
    visited = [False] * n
    components = 0
    def dfs(v):
        stack = [v]
        while stack:
            u = stack.pop()
            if not visited[u]:
                visited[u] = True
                for w in adj[u]:
                    if not visited[w]:
                        stack.append(w)
    for v in range(n):
        if not visited[v]:
            dfs(v)
            components += 1
    print(f"Число компонент связности: {components}")

if __name__ == "__main__":
    main()
```
Примеры вывода:<br>
Введите количество вершин: 3<br>
Вершина 1: 2 3<br>
Вершина 2: 1 3<br>
Вершина 3: 1 2<br>
Введите количество вершин: 6<br>
Вершина 1: 2<br>
Вершина 2: 1<br>
Вершина 3: 4 5<br>
Вершина 4: 3 5<br>
Вершина 5: 3 4<br>
Вершина 6: <br>
----------------------------------------------------------------
## Задача 6 (заданный списками смежности):
```
def main():
    print("=== Проверка числа компонент связности графа ===")
    n = int(input("Сколько вершин? "))
    # Строим списки смежности
    graph = [[] for _ in range(n)]
    print("Теперь введите списки соседей (номера от 1 до", n, "через пробел)")
    for i in range(n):
        line = input(f"Вершина {i+1}: ").strip()
        if line:
            for nb in line.split():
                v = int(nb) - 1
                if 0 <= v < n:
                    graph[i].append(v)
                else:
                    print("  (неправильный номер, пропущен)")
    # Подсчёт компонент связности (DFS, итеративно)
    visited = [False] * n
    def dfs(start):
        stack = [start]
        while stack:
            v = stack.pop()
            if not visited[v]:
                visited[v] = True
                for to in graph[v]:
                    if not visited[to]:
                        stack.append(to)
    real_comp = 0
    for v in range(n):
        if not visited[v]:
            dfs(v)
            real_comp += 1
    # Запрос ответа пользователя
    user_answer = int(input(f"Сколько, по вашему, компонент связности? "))
    if user_answer == real_comp:
        print("Верно!")
    else:
        print(f"Неверно. Правильный ответ: {real_comp}")

if __name__ == "__main__":
    main()
```
Примеры вывода:<br>
Сколько вершин? 4<br>
Вершина 1: 2 3<br>
Вершина 2: 1<br>
Вершина 3: 1<br>
Вершина 4: <br>
Сколько, по вашему, компонент связности? 2<br>
Сколько вершин? 3<br>
Вершина 1: 2 3<br>
Вершина 2: 1 3<br>
Вершина 3: 1 2<br>
Сколько, по вашему, компонент связности? 1<br>
----------------------------------------------------------------
### Задача 7:
```
import sys
def main():
    print("Построение минимального остовного дерева (алгоритм Прима)")
    n = int(input("Количество вершин: "))
    m = int(input("Количество рёбер: "))
    # Список смежности: для каждой вершины храним пары (сосед, вес)
    adj = [[] for _ in range(n)]
    print("Вводите рёбра в формате: вершина1 вершина2 вес (нумерация с 1)")
    for _ in range(m):
        a, b, w = map(int, input().split())
        a -= 1
        b -= 1
        adj[a].append((b, w))
        adj[b].append((a, w))
    # Алгоритм Прима
    INF = 10**9
    visited = [False] * n
    min_weight = [INF] * n   # минимальный вес ребра, ведущего в остов
    parent = [-1] * n        # родитель в остове
    min_weight[0] = 0        # начинаем с вершины 0
    for _ in range(n):
        # Выбираем непосещённую вершину с наименьшим min_weight
        u = -1
        best = INF
        for i in range(n):
            if not visited[i] and min_weight[i] < best:
                best = min_weight[i]
                u = i
        if u == -1:   # граф несвязный
            print("Граф несвязный, построить остовное дерево невозможно")
            return
        visited[u] = True
        # Обновляем веса для соседей
        for v, w in adj[u]:
            if not visited[v] and w < min_weight[v]:
                min_weight[v] = w
                parent[v] = u
    # Вывод результата
    total_weight = 0
    print("\nРёбра минимального остова:")
    for v in range(1, n):
        u = parent[v]
        w = min_weight[v]
        total_weight += w
        print(f"{u+1} -- {v+1}  (вес {w})")
    print(f"Суммарный вес: {total_weight}")

if __name__ == "__main__":
    main()
```
Количество вершин: 3<br>
Количество рёбер: 3<br>
Вводите рёбра в формате: вершина1 вершина2 вес (нумерация с 1)<br>
1 2 5<br>
1 3 3<br>
2 3 4<br>
----------------------------------------------------------------
