# static server

```jsx
app.use(express.static(path.join(__dirname, "public")));
```

-   만약 정적 서버를 구성하고 싶다면? → `app.use()`를 사용하자.
-   use는 express에서 제공하는 미들웨어이다.
-   위와 같이 미들웨어를 구성하였다면 router를 제거해도 된다.
-   router가 없으므로, `localhost:8000/about`과 같이 접근할 수 없게 되는데, 정적 서버에서는 파일 이름을 통해 접근할 수 있다. -> `localhost:8000/about.html`

<br><br>

# `respons.json`

```jsx
app.get("/api/posts", (req, res) => {
    res.json(posts);
});
```

-   `res.send(JS 객체)`를 통해 JSON 데이터를 전송할 수도 있지만, 구체적으로 명시하고 싶다면 `json()` 함수를 통해 전달할 수도 있다.
-   `send()`로 전달하나 `json()`으로 전달하나 똑같다고 봐도 된다.
    -   `send(object)` → `json(object)` → `send(string)`
    -   `json(object)` → `send(string)`
    -   `json()`이 호출횟수가 한 번 더 적다는 차이점이 있다.
