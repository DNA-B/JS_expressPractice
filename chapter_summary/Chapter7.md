# POST method

```jsx
// Create new post
router.post("/", (req, res) => {
    const newPost = {
        id: posts.length + 1,
        title: req.body.title,
    };

    if (!newPost.title) {
        return res.status(400).json({ message: "Please include a title" });
    }

    posts.push(newPost);
    res.status(201).send(newPost);
});
```

-   POST에서는 view에서 전달한 form을 parsing하는 기능이 필요하다.
    -   이를 위한 middleware로 아래 두 가지를 보통 사용한다.
        ```
        app.use(express.json());
        app.use(express.urlencoded({ extended: false }));
        ```
    -   json은 Content-Type 헤더가 **application/json**형태로 전달된 form을 파싱해준다.
    -   urlencoded는 Content-Type 헤더가 **application/x-www-form-urlencoded**인 form을 파싱해준다.
    -   urlencoded에서 extended는 쿼리 변환 모듈로 무엇을 사용할 것인지 선택하는 것이다.
        -   **true** → qs 모듈을 사용하며, 복잡한 배열이나 객체의 형태도 잘 변환한다.
        -   **false** → querystring 모듈을 사용하며, 단순한 객체 문자열 형태일 때 사용한다.

<br><br>

# PUT method

```jsx
router.put("/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        return res.status(404).json({ message: `A post with the id of ${id} was not found` });
    }

    post.title = req.body.title;
    res.status(200).json(post);
});
```

<br><br>

# DELETE method

```jsx
router.delete("/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        return res.status(404).json({ message: `A post with the id of ${id} was not found` });
    }

    posts = posts.filter((post) => post.id !== id);
    res.status(200).json(posts);
});
```
