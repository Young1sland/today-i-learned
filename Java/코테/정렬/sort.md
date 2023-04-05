### 버블 정렬
```java
//i는 반복횟수. i루프를 한번 돌 때마다 가장 오른쪽에 큰수가 정렬 됨
for(int i=0;i<N-1;i++){ 
    for(int j=0;j<N-i-1;i++){
        if(A[j]> A[j+1]){
            swap(A[j], A[i]);
        }            
    }
}
```
