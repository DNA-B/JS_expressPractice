# Router

```jsx
// routes/posts.js
const express = require("express");
const router = express.Router();

let posts = [
    { id: 1, title: "Post One" },
    { id: 2, title: "Post Two" },
    { id: 3, title: "Post Three" },
];

// Get all posts
router.get("/", (req, res) => {
    const limit = parseInt(req.query.limit);

    if (!isNaN(limit) && limit > 0) {
        return res.status(200).json(posts.slice(0, limit));
    }

    res.status(200).json(posts);
});

// Get single post
router.get("/:id", (req, res) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        return res.status(404).json({ message: `A post with the id of ${id} was not found` });
    }

    res.status(200).json(post);
});

router.post("/", (req, res) => {
    console.log(req.body);
    res.send(req.body);
});

module.exports = router;
```

```jsx
// server.js
const posts = require("./routes/posts.js");
app.use("/api/posts", posts); // (defualt path, route path)
```

-   서비스가 커질수록 server.js에서 routing을 수행하면 코드의 길이가 너무 길어질 것이다. 이는 가독성을 안 좋게 하고, 유지보수가 힘들어지게 만든다.
-   따라서 각 엔티티마다 router를 두는 방법을 사용한다. routes 폴더에서 api를 작성하고, server.js에서는 해당 router를 연결하여 사용한다.

<br><br>

# ES Module

-   require가 아니라 import 구문을 사용하고 싶다면 ES module을 사용할 수 있다.
-   `package.json`에 다음과 같이 추가해주자.
    -   `"type": "module",`
-   이제 import구문을 사용할 수 있다.
    ```
    import express from "express";
    import path from "path";
    import posts from "./routes/posts.js";
    ```
-   export는 두 가지 방식이 있다.
    -   `export defulat router;`
    -   `export { router };`
