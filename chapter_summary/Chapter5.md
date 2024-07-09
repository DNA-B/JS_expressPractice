# Status Code

```
// Get single post
app.get("/api/posts/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        res.status(404).json({ message: `A post with the id of ${id} was not found` });
    } else {
        res.status(200).json(post);
    }
});
```

-   **200, 300, 400, 500**과 같이 기본적인 status code들을 더 세분화하고 싶다면?
    -   `res.json`을 하기 전에 `res.status({$code}).json(data)`로 status code를 정해줄 수 있다.

<br><br>

# Multiple responses

```
// Get single post
app.get("/api/posts/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        res.status(404).json({ message: `A post with the id of ${id} was not found` });
    } else {
        res.status(200).json(post);
    }
});
```

-   위의 코드에서 else를 사용하지 않을 수 있다.

```
// Get single post
app.get("/api/posts/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        return res.status(404).json({ message: `A post with the id of ${id} was not found` });
    }

    res.status(200).json(post);
});
```

-   위와 같이 if 절에서 return을 사용하여 method를 끝낼 수 있다.
-   위의 코드는 `app.get()`의 콜백 함수안에 작성된 것이기 때문에 return으로 함수를 조기종료 시켜버리는 방식인 것 같다.
