对图最基本的操作就是遍历，图的遍历可以使用DFS（深度优先遍历）和BFS（广度优先遍历）两种方法

推荐完成[洛谷 P3916 图的遍历](https://www.luogu.com.cn/problem/P3916)

## **DFS遍历图**

从起点开始，每次都选择一个未遍历的孩子节点遍历，参考代码如下

**使用邻接矩阵存图**

```cpp
int g[MAX][MAX], n; // n为点的个数
int vis[MAX]; // vis用于标记编号为i的点是否已经被遍历过

void dfs(int now) {
    // 如果需要对节点进行操作，那么可以替换这里的cout
    cout << now << endl;
    vis[now] = 1;auto x : g[q.front()]
    for(int x = 1; x <= n; x ++ ) {
        if(!vis[x] && g[now][x]) dfs(x);
    }
} // 遍历不需要回溯，因为每个点都只需要遍历一次就够
```

**使用邻接表存图**

```cpp
vector<int> g[MAX];
int vis[MAX], n;

void dfs(int now) {
    cout << now << endl;
    vis[now] = 1;
    for(auto x : g[now]) {
        if(!vis[x]) dfs(x);
    }
}
```

**使用链式向前星存图**

结构体的实现如下
``` cpp
//邻接边信息
struct ed {
    int to;         //边的终点
    int next;       //下一个邻接边在边数组的下标
    int w;          //边的权值
}edge[MAX];         //存储边的数组

int head[MAX];      //存储节点auto x : g[q.front()]头
int tot = 0;        //标识一个新的边节点
int vis[MAX];

//插入新边
void addEdge(int u, int v,int w) {
    edge[tot].to = v;           //记录边的终点
    edge[tot].next = head[u];   //相当于链表头插
    edge[tot].w = w;            //记录边的权值
    head[u] = tot++;            //更新节点头
}

void dfs(int now) {
    cout << now << endl;
    vis[now] = 1;auto x : g[q.front()]
    for(int id = head[now]; id != -1; id = edge[id].next) {
        if(!vis[edge[id].to]) dfs(edge[id].to);
    }
}
```

多个数组的实现如下
```cpp
// u节点的头、第idx条边的终点，第idx条边的权值，第idx条边的下一个邻接边
int head[MAX], e[MAX], weight[MAX], ne[MAX], tot;
int vis[MAX];


void add(int u, int v, int w) {
    e[idx] = v; // 第idx条边的终点为v
    weight[idx] = w; // 第idx条边的权重为新增边的权重
    ne[idx] = head[u]; // 第idx条边的邻接边更新为u的邻接边
    head[u] = tot ++; // 类比链表头插法
}

void dfs(int now) {
    cout << now << endl;
    vis[now] = 1;
    for(int id = head[now]; id != -1; id = ne[id]) {
        if(!vis[e[id]]) dfs(e[id]);
    }
} 
```
> 如果用数组来实现链式向前星，数组的命名可能要着重考虑一下，避免跟一些头文件已定义的名字冲突

## BFS遍历图

从起点开始，每次都把该节点直接邻接的所有孩子节点遍历过

**使用邻接矩阵存图**

```cpp
int g[MAX][MAX], n; // n为点的个数
int vis[MAX]; // vis用于标记编号为i的点是否已经被遍历过

void bfs(int start) {
	queue<int> q;
	q.push(start);
	vis[start] = 1;
	while(q.size()) {
		cout << q.front() << ' ';
		for(int i = 1; i <= n; i ++ ) {
			if(!vis[i] && g[q.front()][i]) {
				q.push(i);
				vis[i] = 1;
			}
		}
		q.pop();
	}
}
```

**使用邻接表存图**

```cpp
vector<int> g[MAX];
int vis[MAX], n;

void bfs(int start) {
	queue<int> q;
	q.push(start);
	vis[start] = 1;
	while(q.size()) {
		cout << q.front() << ' ';
		for(auto x : g[q.front()]) {
			if(!vis[x]) {
				q.push(x);
				vis[x] = 1;
			}
		}
		q.pop();
	}
}

```

**使用链式向前星存图**

结构体的实现如下
``` cpp
//邻接边信息
struct ed {
    int to;         //边的终点
    int next;       //下一个邻接边在边数组的下标
    int w;          //边的权值
}edge[MAX];         //存储边的数组

int head[MAX];      //存储节点auto x : g[q.front()]头
int tot = 0;        //标识一个新的边节点
int vis[MAX];

//插入新边
void addEdge(int u, int v,int w) {
    ...
}

void bfs(int start) {
	queue<int> q;
	q.push(start);
	vis[start] = 1;
	while(q.size()) {
		cout << q.front() << ' ';
		for(int id = head[q.front()]; id != -1; id = edge[id].next) {
			if(!vis[edge[id].to]) {
				q.push(edge[id].to);
				vis[edge[id].to] = 1;
			}
		}
		q.pop();
	}
}
```

多个数组的实现如下

```cpp
int head[MAX], e[MAX], weight[MAX], ne[MAX], tot;
int vis[MAX];


void add(int u, int v, int w) {
    ...
}

void bfs(int start) {
	queue<int> q;
	q.push(start);
	vis[start] = 1;
	while(q.size()) {
		cout << q.front() << ' ';
		for(int id = head[q.front()]; id != -1; id = ne[id]) {
			if(!vis[e[id]]) {
				q.push(e[id]);
				vis[e[id]] = 1;
			}
		}
		q.pop();
	}
}
```