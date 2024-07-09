# GET method

```jsx
// Get all posts
app.get("/api/posts", (req, res) => {
    res.json(posts);
});
```

<br><br>

# GET single object

```jsx
// Get single post
app.get("/api/posts/:id", (req, res) => {
    const id = parseInt(req.params.id);
    res.json(posts.filter((post) => post.id === id));
});
```

<br><br>

# GET query

-   `localhost:8000/api/posts?limit=2`와 같이 요청을 보내면 `req.query`에는 `{ limit: '2' }`가 저장된다.
-   여러 파라미터를 전달하고 싶다면 &를 사용할 수 있다.
    -   `localhost:8000/api/posts?limit=2&sort=desc`와 같이 요청을 보낸다면
    -   `{ limit: '2', sort: 'desc' }`와 같은 값이 `req.query`에 저장된다.
-   모든 query를 허용하는 것은 위험할 수 있다.

    ```
    // Get all posts
    app.get("/api/posts", (req, res) => {
        const limit = parseInt(req.query.limit);

        if (!isNaN(limit) && limit > 0) {
            res.json(posts.slice(0, limit));
        } else {
            res.status(posts);
        }
    });
    ```

    -   query에 SQL query를 사용하여 DB를 마음대로 조작할 수도 있기 때문이다.
    -   때문에 제한을 거는 것이 중요하다.
