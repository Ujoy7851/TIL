## 시간 복잡도

- 작성한 코드가 대략 얼마나 걸릴지 예상하는데 사용된다.
- Big O Notation : 입력 크기 N에 대해서 (최악의 경우) 시간이 얼마나 걸릴지 나타내는 방법
- 문제를 풀기 전에 생각한 방법의 시간 복잡도를 계산해보고 제한 시간안에 수행될 경우에 구현하는 것이 좋다.
- 대략 입력의 크기가 1억일 때 1초정도 걸린다.
- 1초가 걸리는 입력의 크기
    - O(1)
    - O(lgN)
    - O(N) : 1억
    - O(NlgN) : 5백만
    - O(N^2) : 1만
    - O(N^3) : 500
    - O(2^N) : 20
    - O(N!) : 10

---

### 메모리

- 메모리 제한은 보통 넉넉하기 떄문에 걱정할 필요가 없다.
- 가장 많은 공간을 사용하는 것은 보통 배열이다.
    - int a[10000][10000]; -> 배열의 크기 = 400,000,000B = 381.469MB / 입력의 크기 N = 1억 (1초)이기 때문에 시간제한만 지키면 대부분 메모리는 크게 걱정할 필요가 없다.

---

### 입/출력

- C : `scanf/printf` 사용
- C++ : `scanf/printf`, `cin/cout` 사용
    - `scanf/printf`이 `cin/cout`보다 빠르지만 아래 내용을 추가하면 빨라진다.(이때, `cin/cout`만 써야함)

        ```cpp
        ios_base::sync_with_studio(false);
        cin.tie(NULL);
        cout.tie(NULL);
        ```

        cout으로 ‘\n’을 출력하는게 endl보다 훨씬(10배가량) 빠르다.

- Java : 입력은 `Scanner`, 출력은 `System.out` 사용

    ```java
    Scanner sc =  new Scanner(System.in);
    System.out.println("Hello world");
    ```

    - 입력이 많은 경우 속도가 느려 `BufferedReader` 사용
    - 출력이 많은 경우 `StringBuilder`를 사용해 한 문자열로 만들어서 출력을 한 번만 하거나 `BufferedWriter`를 사용한다.

---

몇가지 예제를 통해 시간복잡도, 공간복잡도와 관련된 내용을 이해해보자.

```c
int sum = 0;
for(int i=1; i<N; i++) {
    sum += i;
}
```

위 소스코드는 1부터 N까지 합을 계산하는 코드로 O(N)의 시간복잡도를 가진다.같은 계산이라도 아래의 소스코드처럼 알고리즘을 바꿔주면 시간복잡도에서 엄청난 차이를 보인다.

```c
//O(N^2)
int sum = 0;
for(int i=1; i<N; i++) {
    for(int j=1; j<N; j++) {
        if(i == j) sum += j;
    }
}

//O(1)
int sum = 0;
sum = N*(N+1)/2;
```

두 수를 T번 입력받아 합을 출력하는 경우에도 아래 소스코드처럼 입력을 다 받고 모아서 하나씩 출력하는 경우에는 T의 개수를 모를때 배열의 크기를 정하기도 어렵고, 입력이 큰 경우에 큰 크기의 배열을 필요하게 된다.

```c
int t;
int a[100], b[100];
scanf("%d", &t);
for(int i=0; i<t; i++) {
    scanf("%d %d", &a[i], &b[i]);
}
for(int i=0; i<t; i++) {
    printf("%d\n", a[i]+b[i]);
}
```

하지만 입력받고 바로 풀어서 출력하는 경우 훨씬 적은 양의 메모리를 사용한다.

```c
int t;
int a, b;
scanf("%d", &t);
while(t--){
    scanf("%d %d", &a, &b);
    printf("%d\n", a+b);
}
```

\** 입력 개수가 정해지지 않은 경우 EOF(End of File)까지 입력받으면 된다.

- C : while(scanf(“%d %d”, &a, &d) == 2)
- C++: while (cin >> a >> b)
- Java : while(sc.hasNextInt())

---

**추가로 Java 입출력과 관련된 내용을 정리하며 글을 마친다.**

BufferedReader/BufferedWriter는 Buffer에 있는 IO 클래스로 입출력시 버퍼를 이용해 데이터처리 효율성을 높다.

- `BufferedReader`
    - Scanner를 통해 입력을 받으면 Space, Enter 모두 경계로 인식해 입력받은 데이터를 가공하기 편리하다. 하지만 BufferedReader는 Enter만 경계로 인식하고 받은 데이터가 String으로 고정되기 떄문에 형변환이 필요하다.

        ```java
        try{
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            String s = br.readLine();
            int i = Integer.parseInt(br.readLine);
            br.close();
        } catch (IOException e) {
            e.printStrackTrace();
        }
        ```

    - readLine을 통해 입력받은 데이터는 Line 단위로만 나눠지기 때문에 공백 단위로 데이터를 가공하기 위해 다음 두가지 방법중 하나를 사용해 처리해주어야 한다.

        ```java
        //StringTokenizer를 이용한 방법
        StringTokenizer st = new StringTokenizer(s);
        int a = Integer.parseInt(st.nextToken());
        int b = Integer.parseInt(st.nextToken());

        //공백마다 데이터를 끊어 배열에 넣는 방법
        String array[] = s.split(" ");
        ```

- `BufferedWriter`
    - 역시 출력량이 많아지면 `System.out.println()`보다 속도가 빠르다.
    - `BufferedWriter`는 버퍼를 이용하기 떄문에 다 쓴 뒤에 반드시 `flush()`를 호출해 버퍼를 비워줘야 해주어야 한다. `bw.write()`에는 자동개행 기능이 없다.

        ```java
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        String s = "abcd";
        bw.write(s + '\n');
        bw.write("hello");
        bw.newLine();
        bw.write("world");
        bw.flush();
        bw.close();
        ```