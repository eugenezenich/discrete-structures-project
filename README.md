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
<small>Примеры вывода:<br></small>
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

main.py
```
from flask import Flask, render_template, request, jsonify
from pyvis.network import Network
import os

app = Flask(__name__)
os.makedirs('static/visuals', exist_ok=True)


def parse_matrix(text):
    return [[int(x) for x in line.strip().split()] for line in text.strip().split('\n')]


def get_adj_list(matrix):
    return {i: [j for j in range(len(matrix)) if matrix[i][j]] for i in range(len(matrix))}


def draw_graph(matrix, filename, highlight_nodes=None, highlight_edges=None, node_colors=None):
    net = Network(height="400px", width="100%", bgcolor="#ffffff")
    n = len(matrix)

    for i in range(n):
        color = node_colors.get(i, "#667eea") if node_colors else "#667eea"
        if highlight_nodes and i in highlight_nodes:
            color = "#ff6b6b"
        net.add_node(i, label=str(i), color=color, size=25)

    added = set()
    for i in range(n):
        for j in range(n):
            if matrix[i][j] and (i, j) not in added and (j, i) not in added:
                added.add((i, j))
                color = "#ff6b6b" if highlight_edges and (
                            (i, j) in highlight_edges or (j, i) in highlight_edges) else "#cbd5e0"
                label = str(matrix[i][j]) if matrix[i][j] > 1 else ""
                net.add_edge(i, j, label=label, color=color, width=2)

    net.save_graph(f'static/visuals/{filename}')
    return filename


# ============ ЗАДАЧА 0 ============
def task_0(matrix):
    adj = get_adj_list(matrix)
    n = len(matrix)
    degrees = {i: len(adj[i]) for i in range(n)}

    visited = [False] * n
    comps = []

    def dfs(v, c):
        visited[v] = True;
        c.append(v)
        for u in adj[v]:
            if not visited[u]: dfs(u, c)

    for v in range(n):
        if not visited[v]:
            c = [];
            dfs(v, c);
            comps.append(c)

    odd = sum(1 for d in degrees.values() if d % 2)
    if len(comps) == 1:
        euler = "Эйлеров" if odd == 0 else ("Полуэйлеров" if odd == 2 else "Не эйлеров")
    else:
        euler = "Несвязный"

    color = [-1] * n
    ok = True
    for s in range(n):
        if color[s] == -1:
            q = [s];
            color[s] = 0
            while q:
                v = q.pop(0)
                for u in adj[v]:
                    if color[u] == -1:
                        color[u] = 1 - color[v];
                        q.append(u)
                    elif color[u] == color[v]:
                        ok = False

    node_colors = {}
    if ok:
        for i in range(n):
            node_colors[i] = "#4ecdc4" if color[i] == 0 else "#ffe66d"
        set_a = [i for i in range(n) if color[i] == 0]
        set_b = [i for i in range(n) if color[i] == 1]
        is_complete = all(matrix[a][b] for a in set_a for b in set_b)
        bipartite = f"Полный двудольный (A={set_a}, B={set_b})" if is_complete else f"Двудольный (A={set_a}, B={set_b})"
    else:
        bipartite = "Не двудольный"
        node_colors = {i: "#a0a0a0" for i in range(n)}

    vis = draw_graph(matrix, "task0.html", node_colors=node_colors)
    return {"degrees": degrees, "components": len(comps), "comps_list": comps,
            "euler": euler, "bipartite": bipartite, "vis": vis}


# ============ DFS / BFS ============
def task_dfs_bfs(matrix, mode='dfs'):
    adj = get_adj_list(matrix)
    n = len(matrix)
    visited = [False] * n
    order = []
    edges = []

    if mode == 'dfs':
        def go(v):
            visited[v] = True;
            order.append(v)
            for u in adj[v]:
                if not visited[u]:
                    edges.append((v, u));
                    go(u)

        for v in range(n):
            if not visited[v]: go(v)
    else:
        for s in range(n):
            if not visited[s]:
                q = [s];
                visited[s] = True
                while q:
                    v = q.pop(0);
                    order.append(v)
                    for u in adj[v]:
                        if not visited[u]:
                            visited[u] = True;
                            q.append(u);
                            edges.append((v, u))

    name = "dfs" if mode == 'dfs' else "bfs"
    vis = draw_graph(matrix, f"task{name}.html", highlight_nodes=order, highlight_edges=edges)
    return {"order": order, "path": " → ".join(map(str, order)), "vis": vis}


# ============ ПРОВЕРКА DFS / BFS ============
def task_check_dfs_bfs(matrix, user_order, mode='dfs'):
    correct = task_dfs_bfs(matrix, mode)['order']
    vis = draw_graph(matrix, f"task_check_{mode}.html")
    return {"correct": correct, "user": user_order, "ok": user_order == correct, "vis": vis}


# ============ ЗАДАЧА 5 ============
def task_5(matrix):
    adj = get_adj_list(matrix)
    n = len(matrix)
    visited = [False] * n
    comps = []

    def dfs(v, c):
        visited[v] = True;
        c.append(v)
        for u in adj[v]:
            if not visited[u]: dfs(u, c)

    for v in range(n):
        if not visited[v]:
            c = [];
            dfs(v, c);
            comps.append(c)

    colors = ["#ff6b6b", "#4ecdc4", "#ffe66d", "#96ceb4", "#45b7d1", "#dfe6e9"]
    node_colors = {}
    for i, comp in enumerate(comps):
        for v in comp:
            node_colors[v] = colors[i % len(colors)]

    vis = draw_graph(matrix, "task5.html", node_colors=node_colors)
    return {"count": len(comps), "comps": comps, "vis": vis}


# ============ ЗАДАЧА 6 ============
def task_6_check(matrix, user_answer):
    correct = task_5(matrix)['count']
    vis = task_5(matrix)['vis']
    return {"correct": correct, "user": user_answer, "ok": correct == user_answer, "vis": vis}


# ============ ЗАДАЧА 7 (Прим) ============
def task_7(matrix):
    n = len(matrix)
    INF = float('inf')
    used = [False] * n
    used[0] = True
    mst = []
    total = 0

    for _ in range(n - 1):
        best = INF;
        u = v = -1
        for i in range(n):
            if used[i]:
                for j in range(n):
                    if not used[j] and matrix[i][j] and matrix[i][j] < best:
                        best = matrix[i][j];
                        u, v = i, j
        if u != -1:
            used[v] = True;
            mst.append((u, v, best));
            total += best

    vis = draw_graph(matrix, "task7.html", highlight_edges=[(e[0], e[1]) for e in mst])
    return {"edges": mst, "weight": total, "vis": vis}


# ============ ЗАДАЧА 8 (Дейкстра) ============
def task_8(matrix, start):
    n = len(matrix)
    INF = float('inf')
    dist = [INF] * n;
    dist[start] = 0
    used = [False] * n
    paths = [[start] for _ in range(n)]

    for _ in range(n):
        v = min((i for i in range(n) if not used[i]), key=lambda i: dist[i], default=-1)
        if v == -1: break
        used[v] = True
        for u in range(n):
            if matrix[v][u] and not used[u] and dist[v] + matrix[v][u] < dist[u]:
                dist[u] = dist[v] + matrix[v][u];
                paths[u] = paths[v] + [u]

    edges = []
    for p in paths:
        for i in range(len(p) - 1):
            edges.append((p[i], p[i + 1]))

    vis = draw_graph(matrix, "task8.html", highlight_edges=edges, highlight_nodes=[start])
    return {"dist": {i: dist[i] for i in range(n) if dist[i] != INF},
            "path": {i: paths[i] for i in range(n) if dist[i] != INF and i != start}, "vis": vis}


# ============ ЗАДАЧА 9 (Флойд) ============
def task_9(matrix):
    n = len(matrix)
    INF = float('inf')
    d = [[INF] * n for _ in range(n)]
    for i in range(n):
        d[i][i] = 0
        for j in range(n):
            if matrix[i][j]: d[i][j] = matrix[i][j]

    for k in range(n):
        for i in range(n):
            for j in range(n):
                d[i][j] = min(d[i][j], d[i][k] + d[k][j])

    result = [[str(int(d[i][j])) if d[i][j] != INF else "∞" for j in range(n)] for i in range(n)]
    vis = draw_graph(matrix, "task9.html")
    return {"matrix": result, "vis": vis}


# ============ ЗАДАЧА 10 (Прюфер - кодирование) ============
def task_10(matrix):
    adj = get_adj_list(matrix)
    n = len(matrix)
    deg = {i: len(adj[i]) for i in range(n)}
    code = [];
    dead = [False] * n

    for _ in range(n - 2):
        leaf = next(v for v in range(n) if not dead[v] and deg[v] == 1)
        parent = next(u for u in adj[leaf] if not dead[u])
        code.append(parent);
        dead[leaf] = True;
        deg[parent] -= 1

    vis = draw_graph(matrix, "task10.html")
    return {"code": code, "vis": vis}


# ============ ЗАДАЧА 11 (Прюфер - декодирование) ============
def task_11(code, n):
    deg = [1] * n
    for v in code: deg[v] += 1

    edges = []
    for v in code:
        leaf = next(i for i in range(n) if deg[i] == 1)
        edges.append((leaf, v))
        deg[leaf] -= 1;
        deg[v] -= 1

    rest = [i for i in range(n) if deg[i] == 1]
    edges.append((rest[0], rest[1]))

    mat = [[0] * n for _ in range(n)]
    for u, v in edges: mat[u][v] = mat[v][u] = 1
    vis = draw_graph(mat, "task11.html", highlight_edges=edges)
    return {"edges": edges, "vis": vis}


# ============ ЗАДАЧА 12 (Раскраска) ============
def task_12(matrix):
    adj = get_adj_list(matrix)
    n = len(matrix)
    colors = [-1] * n

    for v in range(n):
        used = {colors[u] for u in adj[v] if colors[u] != -1}
        c = 0
        while c in used: c += 1
        colors[v] = c

    chrom = max(colors) + 1
    pal = ["#FF6B6B", "#4ECDC4", "#FFEAA7", "#96CEB4", "#45B7D1", "#DDA0DD"]
    node_colors = {i: pal[colors[i] % len(pal)] for i in range(n)}

    vis = draw_graph(matrix, "task12.html", node_colors=node_colors)
    return {"colors": {i: colors[i] for i in range(n)}, "chromatic": chrom,
            "legend": {f"Цвет {i}": pal[i] for i in range(chrom)}, "vis": vis}


# ============ МАРШРУТЫ ============

@app.route('/')
def index():
    return render_template('index.html')


@app.route('/solve', methods=['POST'])
def solve():
    data = request.json
    task = str(data['task'])
    matrix = parse_matrix(data['matrix'])
    extra = data.get('extra', '').strip()

    try:
        # Проверка: задачи с ОДНИМ числом
        if task in ['6', '8']:
            if not extra:
                return jsonify({"ok": False, "error": "Введите число!"})
            try:
                extra_val = int(extra)
            except ValueError:
                return jsonify({"ok": False, "error": f"Нужно ввести ОДНО целое число, а не '{extra}'"})

        # Проверка: задачи со СПИСКОМ чисел
        elif task in ['2', '4', '11']:
            if not extra:
                return jsonify({"ok": False, "error": "Введите числа через пробел!"})
            try:
                extra_val = [int(x) for x in extra.split()]
            except ValueError:
                return jsonify({"ok": False, "error": f"Нужно ввести числа через пробел, а не '{extra}'"})

        # Выполнение задач
        if task == '0':
            r = task_0(matrix)
        elif task == '1':
            r = task_dfs_bfs(matrix, 'dfs')
        elif task == '2':
            r = task_check_dfs_bfs(matrix, extra_val, 'dfs')
        elif task == '3':
            r = task_dfs_bfs(matrix, 'bfs')
        elif task == '4':
            r = task_check_dfs_bfs(matrix, extra_val, 'bfs')
        elif task == '5':
            r = task_5(matrix)
        elif task == '6':
            r = task_6_check(matrix, extra_val)
        elif task == '7':
            r = task_7(matrix)
        elif task == '8':
            r = task_8(matrix, extra_val)
        elif task == '9':
            r = task_9(matrix)
        elif task == '10':
            r = task_10(matrix)
        elif task == '11':
            r = task_11(extra_val, len(matrix))
        elif task == '12':
            r = task_12(matrix)
        else:
            return jsonify({"ok": False, "error": f"Неизвестная задача: {task}"})

        return jsonify({"ok": True, "result": r})

    except Exception as e:
        return jsonify({"ok": False, "error": f"Ошибка: {str(e)}"})


if __name__ == '__main__':
    app.run(debug=True)
```


index.html:
```
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Дискретные структуры — Графы</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: Arial, sans-serif; background: #f0f2f5; padding: 20px; }
        .container { max-width: 1200px; margin: 0 auto; display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .panel { background: white; padding: 25px; border-radius: 15px; box-shadow: 0 2px 10px rgba(0,0,0,0.08); }
        h1 { color: #667eea; margin-bottom: 5px; }
        h2 { color: #888; font-size: 14px; font-weight: normal; margin-bottom: 20px; }
        label { display: block; margin: 12px 0 5px; font-weight: bold; color: #444; font-size: 14px; }
        select, textarea, input { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 8px; font-size: 14px; font-family: monospace; }
        textarea { min-height: 120px; }
        button { width: 100%; padding: 12px; color: white; border: none; border-radius: 8px; font-size: 16px; cursor: pointer; margin-top: 8px; }
        .btn-solve { background: #667eea; }
        .btn-solve:hover { background: #5a6fd6; }
        .btn-example { background: #4ecdc4; }
        .btn-example:hover { background: #45b7b0; }
        .hint { background: #f0f4ff; padding: 10px; border-radius: 8px; font-size: 13px; color: #667eea; margin-bottom: 15px; }
        #extraBlock { display: none; }
        .result { margin-top: 20px; padding: 15px; background: #f8f9fa; border-radius: 10px; font-size: 14px; }
        .result p { margin: 5px 0; }
        .ok { color: #4caf50; font-weight: bold; }
        .bad { color: #f44336; font-weight: bold; }
        .graph-frame { width: 100%; height: 450px; border: 1px solid #eee; border-radius: 10px; overflow: hidden; }
        .graph-frame iframe { width: 100%; height: 100%; border: none; }
        table { border-collapse: collapse; margin-top: 10px; }
        td { border: 1px solid #ddd; padding: 6px 10px; text-align: center; font-size: 13px; }
        .color-box { display: inline-block; width: 14px; height: 14px; border-radius: 3px; margin-right: 5px; vertical-align: middle; }
        .error-msg { color: #f44336; padding: 10px; background: #ffebee; border-radius: 8px; margin-top: 10px; }
        @media (max-width: 800px) { .container { grid-template-columns: 1fr; } }
    </style>
</head>
<body>
    <div class="container">
        <!-- ЛЕВАЯ ПАНЕЛЬ -->
        <div class="panel">
            <h1>📊 Графы</h1>
            <h2>Дискретные структуры — проект</h2>

            <label>Задача:</label>
            <select id="task" onchange="changeTask()">
                <option value="0">0 — Степени, компоненты, Эйлеров, двудольный</option>
                <option value="1">1 — DFS обход (показать)</option>
                <option value="2">2 — DFS обход (проверить)</option>
                <option value="3">3 — BFS обход (показать)</option>
                <option value="4">4 — BFS обход (проверить)</option>
                <option value="5">5 — Компоненты связности</option>
                <option value="6">6 — Компоненты связности (проверить)</option>
                <option value="7">7 — Минимальное остовное дерево</option>
                <option value="8">8 — Кратчайшие пути (Дейкстра)</option>
                <option value="9">9 — Матрица кратчайших путей</option>
                <option value="10">10 — Кодирование Прюфера</option>
                <option value="11">11 — Декодирование Прюфера</option>
                <option value="12">12 — Раскраска графа</option>
            </select>

            <div class="hint" id="hint">Нажмите «📋 Вставить пример» или введите свою матрицу</div>

            <label>Матрица смежности:</label>
            <textarea id="matrix" placeholder="Введите матрицу смежности...">0 1 0
1 0 1
0 1 0</textarea>

            <button class="btn-example" onclick="loadExample()">📋 Вставить пример</button>

            <div id="extraBlock">
                <label>Дополнительные данные:</label>
                <input type="text" id="extra" placeholder="">
            </div>

            <button class="btn-solve" onclick="solve()">▶ Выполнить</button>
            <div id="result"></div>
        </div>

        <!-- ПРАВАЯ ПАНЕЛЬ -->
        <div class="panel">
            <h1>🎨 Визуализация</h1>
            <div class="graph-frame" id="graph">
                <div style="display:flex;align-items:center;justify-content:center;height:100%;color:#aaa;font-size:18px;">
                    Нажмите «Выполнить»
                </div>
            </div>
        </div>
    </div>

    <script>
        // ============ ПРИМЕРЫ ДЛЯ КАЖДОЙ ЗАДАЧИ ============
        const examples = {
            '0': `0 1 1 0\n1 0 1 0\n1 1 0 1\n0 0 1 0`,
            '1': `0 1 1 0\n1 0 0 1\n1 0 0 1\n0 1 1 0`,
            '2': `0 1 0 0\n1 0 1 0\n0 1 0 1\n0 0 1 0`,
            '3': `0 1 1 0\n1 0 0 1\n1 0 0 1\n0 1 1 0`,
            '4': `0 1 0 1\n1 0 1 0\n0 1 0 1\n1 0 1 0`,
            '5': `0 1 0 0 0\n1 0 0 0 0\n0 0 0 1 0\n0 0 1 0 0\n0 0 0 0 0`,
            '6': `0 1 0 0\n1 0 0 0\n0 0 0 1\n0 0 1 0`,
            '7': `0 2 0 6 0\n2 0 3 8 5\n0 3 0 0 7\n6 8 0 0 9\n0 5 7 9 0`,
            '8': `0 4 0 0 0 0\n4 0 8 0 0 0\n0 8 0 7 0 4\n0 0 7 0 9 0\n0 0 0 9 0 10\n0 0 4 0 10 0`,
            '9': `0 3 0 7\n3 0 1 0\n0 1 0 2\n7 0 2 0`,
            '10': `0 1 1 0 0\n1 0 0 1 1\n1 0 0 0 0\n0 1 0 0 0\n0 1 0 0 0`,
            '11': `0 0 1 0 0\n0 0 0 1 1\n1 0 0 0 0\n0 1 0 0 0\n0 1 0 0 0`,
            '12': `0 1 1 0\n1 0 1 0\n1 1 0 1\n0 0 1 0`
        };

        function loadExample() {
            const task = document.getElementById('task').value;
            if (examples[task]) {
                document.getElementById('matrix').value = examples[task];
                document.getElementById('hint').innerHTML = '✅ Пример загружен! Можете нажать «Выполнить»';
            }
        }

        function changeTask() {
            const task = document.getElementById('task').value;
            const extraBlock = document.getElementById('extraBlock');
            const hint = document.getElementById('hint');

            // Показываем доп. поле только для нужных задач
            const tasksWithExtra = ['2', '4', '6', '8', '11'];
            extraBlock.style.display = tasksWithExtra.includes(task) ? 'block' : 'none';

            // Подсказки для доп. поля
            const extraHints = {
                '2': '💡 Введите ваш DFS обход через пробел (например: 0 1 2 3)',
                '4': '💡 Введите ваш BFS обход через пробел (например: 0 1 3 2)',
                '6': '💡 Введите ОДНО число — количество компонент',
                '8': '💡 Введите номер стартовой вершины (например: 0)',
                '11': '💡 Введите код Прюфера через пробел (например: 1 1 1)'
            };

            if (extraHints[task]) {
                hint.innerHTML = extraHints[task];
            } else {
                hint.innerHTML = 'Нажмите «📋 Вставить пример» или введите свою матрицу';
            }
        }

        // ============ ОТПРАВКА ЗАДАЧИ ============
        function solve() {
            const task = document.getElementById('task').value;
            const matrix = document.getElementById('matrix').value;
            const extra = document.getElementById('extra').value;
            const resultDiv = document.getElementById('result');

            resultDiv.innerHTML = '<p>⏳ Считаем...</p>';

            fetch('/solve', {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify({task: parseInt(task), matrix, extra})
            })
            .then(r => r.json())
            .then(data => {
                if (!data.ok) {
                    resultDiv.innerHTML = `<div class="error-msg">❌ ${data.error}</div>`;
                    return;
                }

                const r = data.result;
                let html = '<div class="result"><h3>✅ Результат:</h3>';

                // Задача 0
                if (task === '0') {
                    html += '<p><b>Степени вершин:</b></p>';
                    for (let [v, d] of Object.entries(r.degrees))
                        html += `<p>Вершина ${v}: степень ${d}</p>`;
                    html += `<p><b>Компонент связности:</b> ${r.components}</p>`;
                    html += `<p><b>Эйлеровость:</b> ${r.euler}</p>`;
                    html += `<p><b>Двудольность:</b> ${r.bipartite}</p>`;
                }

                // Задачи 1, 3
                else if (task === '1' || task === '3') {
                    html += `<p><b>Обход:</b> ${r.path}</p>`;
                }

                // Задачи 2, 4
                else if (task === '2' || task === '4') {
                    html += `<p>Ваш обход: ${r.user.join(' → ')}</p>`;
                    html += `<p>Правильный: ${r.correct.join(' → ')}</p>`;
                    html += r.ok ? '<p class="ok">✓ Правильно!</p>' : '<p class="bad">✗ Неправильно</p>';
                }

                // Задача 5
                else if (task === '5') {
                    html += `<p><b>Число компонент:</b> ${r.count}</p>`;
                    r.comps.forEach((c, i) => {
                        html += `<p>Компонента ${i+1}: {${c.join(', ')}}</p>`;
                    });
                }

                // Задача 6
                else if (task === '6') {
                    html += `<p>Ваш ответ: ${r.user}</p>`;
                    html += `<p>Правильный: ${r.correct}</p>`;
                    html += r.ok ? '<p class="ok">✓ Правильно!</p>' : '<p class="bad">✗ Неправильно</p>';
                }

                // Задача 7
                else if (task === '7') {
                    html += '<p><b>Рёбра минимального остовного дерева:</b></p>';
                    r.edges.forEach(e => {
                        html += `<p>${e[0]} — ${e[1]} (вес: ${e[2]})</p>`;
                    });
                    html += `<p><b>Общий вес:</b> ${r.weight}</p>`;
                }

                // Задача 8
                else if (task === '8') {
                    html += '<p><b>Кратчайшие расстояния:</b></p>';
                    for (let [v, d] of Object.entries(r.dist)) {
                        const path = r.path[v] ? r.path[v].join(' → ') : '-';
                        html += `<p>До вершины ${v}: ${d} (путь: ${path})</p>`;
                    }
                }

                // Задача 9
                else if (task === '9') {
                    html += '<p><b>Матрица кратчайших путей:</b></p><table>';
                    r.matrix.forEach(row => {
                        html += '<tr>' + row.map(c => `<td>${c}</td>`).join('') + '</tr>';
                    });
                    html += '</table>';
                }

                // Задача 10
                else if (task === '10') {
                    html += `<p><b>Код Прюфера:</b> [${r.code.join(', ')}]</p>`;
                }

                // Задача 11
                else if (task === '11') {
                    html += '<p><b>Рёбра дерева:</b></p>';
                    r.edges.forEach(e => {
                        html += `<p>${e[0]} — ${e[1]}</p>`;
                    });
                }

                // Задача 12
                else if (task === '12') {
                    html += '<p><b>Раскраска:</b></p>';
                    for (let [color, hex] of Object.entries(r.legend))
                        html += `<p><span class="color-box" style="background:${hex}"></span>${color}</p>`;
                    html += '<br>';
                    for (let [v, c] of Object.entries(r.colors))
                        html += `<p>Вершина ${v} → цвет ${c}</p>`;
                    html += `<p><b>Хроматическое число:</b> ${r.chromatic}</p>`;
                }

                html += '</div>';
                resultDiv.innerHTML = html;

                // Обновляем визуализацию
                if (r.vis) {
                    document.getElementById('graph').innerHTML =
                        `<iframe src="/static/visuals/${r.vis}?t=${Date.now()}"></iframe>`;
                }
            })
            .catch(err => {
                resultDiv.innerHTML = `<div class="error-msg">❌ Ошибка соединения: ${err}</div>`;
            });
        }

        // Инициализация
        changeTask();
    </script>
</body>
</html>
```
