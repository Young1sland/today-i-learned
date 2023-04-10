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