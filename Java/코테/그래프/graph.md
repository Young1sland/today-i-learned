# Graph

### 인접리스트
```java
static ArrayList<Integer>[] A;
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
StringTokenizer st = new StringTokenizer(br.readLine());
int n = Integer.parseInt(st.nextToken());
int m = Integer.parseInt(st.nextToken());
A = new ArrayList[n+1];
visited = new boolean[n+1];
for(int i=1;i<n+1;i++){
    A[i] = new ArrayList<Integer>();
}
for(int i=0;i<m;i++){
    st = new StringTokenizer(br.readLine());
    int s = Integer.parseInt(st.nextToken());
    int e = Integer.parseInt(st.nextToken());
    A[s].add(e);
    A[e].add(s);
}
```

### 인접행렬
```java
/**
입력
4 6
101111
101010
101011
111011
 */
BufferedReader br =new BufferedReader(new InputStreamReader(System.in));
StringTokenizer st = new StringTokenizer(br.readLine());
int N = Integer.parseInt(st.nextToken());
int M = Integer.parseInt(st.nextToken());
int[][] map = new int[N][M];
boolean[][] visit = new boolean[N][M];
for(int i=0;i<N;i++){
    String l = br.readLine();
    for(int j=0;j<M;j++){
        int x =Integer.parseInt(l.substring(j,j+1));
        map[i][j] = x;
    }
}
```