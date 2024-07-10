# User Library를 사용해보자

```bash
npm i colors
```

-   colors라는 npm 모듈을 사용해보자.
-   로그를 남길 때, 색상을 지정해줄 수 있는 모듈이다.

```jsx
import colors from "colors";

const logger = (req, res, next) => {
    const methodColors = {
        GET: "green",
        POST: "blue",
        PUT: "yellow",
        DELETE: "red",
    };

    const color = methodColors[req.method] || white;
    console.log(`${req.method} ${req.protocol}://${req.get("host")}${req.originalUrl}`[color]);
    next();
};

export default logger;
```

-   다음과 같이 colors 모듈을 사용하면, request method에 따라 다른 색으로 log를 출력한다.

<br><br>

# Controller

```jsx
// controllers/postController.js
let posts = [
    { id: 1, title: "Post One" },
    { id: 2, title: "Post Two" },
    { id: 3, title: "Post Three" },
];

// @desc Get all posts
// @route GET /api/posts
export const getPosts = (req, res) => {
    const limit = parseInt(req.query.limit);

    if (!isNaN(limit) && limit > 0) {
        return res.status(200).json(posts.slice(0, limit));
    }

    res.status(200).json(posts);
};

// @desc    Get single post
// @route   GET /api/posts/:id
export const getPost = (req, res, next) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        const error = new Error(`A post with the id of ${id} was not found`);
        error.status = 404;
        return next(error);
    }

    res.status(200).json(post);
};

// @desc    Create new post
// @route   POST /api/posts
export const createPost = (req, res, next) => {
    const newPost = {
        id: posts.length + 1,
        title: req.body.title,
    };

    if (!newPost.title) {
        const error = new Error(`Please include a title`);
        error.status = 400;
        return next(error);
    }

    posts.push(newPost);
    res.status(201).send(newPost);
};

// @desc    Update new post
// @route   PUT /api/posts:id
export const updatePost = (req, res, next) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        const error = new Error(`A post with the id of ${id} was not found`);
        error.status = 404;
        return next(error);
    }

    post.title = req.body.title;
    res.status(200).json(post);
};

// @desc    Delete new post
// @route   DELTE /api/posts:id
export const deletePost = (req, res, next) => {
    const id = parseInt(req.params.id);
    const post = posts.find((post) => post.id === id);

    if (!post) {
        const error = new Error(`A post with the id of ${id} was not found`);
        error.status = 404;
        return next(error);
    }

    posts = posts.filter((post) => post.id !== id);
    res.status(200).json(posts);
};
```

```jsx
// routes/posts.js
import express from "express";
import { getPosts, getPost, createPost, updatePost, deletePost } from "../controllers/postController.js";

const router = express.Router();

router.get("/", getPosts);
router.get("/:id", getPost);
router.post("/", createPost);
router.put("/:id", updatePost);
router.delete("/:id", deletePost);

export default router;
```

-   현재 **route** 파일에 너무 많은 로직이 존재한다. 때문에 이를 분리할 필요가 있다. 데이터를 직접 조회, 변경하는 등의 작업을 수행하는 파일을 **controller**라고 칭한다.
    -   **Route →** 경로(route)와 관련된 로직을 담당. 여기서는 주로 URL 경로와 HTTP 메서드를 정의하고, 특정 경로로 요청이 들어왔을 때 어떤 컨트롤러 함수를 호출할지를 지정.
    -   **Controller →** 비즈니스 로직을 처리. 데이터를 조회하거나 변경하는 등의 실제 작업을 수행.
-   실제로 모듈을 나누는 것이 주는 이점은 다음과 같다고 한다.
    -   **코드의 가독성 및 관리 용이성**:
        -   프로젝트가 커지면 라우팅과 비즈니스 로직이 복잡해진다.
        -   라우팅과 컨트롤러를 분리하면 각 파일이 더 간결해지고, 코드를 이해하고 유지보수하기가 쉬워진다.
    -   **모듈화**:
        -   특정 기능을 수정하거나 확장할 때 다른 부분에 영향을 미치지 않고 쉽게 작업할 수 있다.
    -   **테스트 용이성**:
        -   비즈니스 로직을 컨트롤러 파일에 모아두면, 로직을 독립적으로 테스트하기가 더 쉽다.
        -   라우팅 파일은 경로가 올바르게 설정되었는지를 테스트할 수 있다.
    -   **협업 효율성**:
        -   여러 명이 함께 작업하는 프로젝트에서는 역할을 분리하여 작업을 분배할 수 있다.
        -   한 팀원이 라우팅을 담당하고 다른 팀원이 컨트롤러 로직을 담당하면, 작업의 충돌을 최소화할 수 있다.

<br><br>

# ES module에서의 \_\_dirname

```jsx
import path from "path";
import url from "url";

const __filename = url.fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
```

-   ES module에서는 \_\_dirname을 사용할 수 없다.
-   따라서 `path, url` 모듈을 사용하여, `__dirname`을 얻어내야 한다.
    -   `__filename` → **C:\Users\DNA\DEV\expressjs-crash\server.js**
    -   `__dirname` → **C:\Users\DNA\DEV\expressjs-crash**
