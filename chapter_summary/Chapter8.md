# 미들웨어로 log 남기기

```jsx
// middleware/logger.js
const logger = (req, res, next) => {
  console.log(
    `${req.method} ${req.protocol}://${req.get("host")}${req.originalUrl}`
  );
  next();
};

export default logger;
```

- next() → logger 미들웨어로 들어온 요청을 처리한 후, 다음 미들웨어 함수로 넘어간다.

```jsx
import logger from "./middleware/logger.js";

// Logger middleware
app.use(logger);
app.use("/api/posts", posts); // (defualt path, route path)
```

- 만약 여기서 app.use(posts) 밑에 logger가 존재한다면 logger까지 요청이 전달되지 못하고 로깅을 할 수 없다.

<br><br>

# 미들웨어로 ERROR 처리하기

```jsx
// middleware/error.js
const errorHandler = (err, req, res, next) => {
  if (err.status) {
    res.status(err.status).json({ message: err.message });
  } else {
    res.status(500).json({ msg: err.message });
  }
};

export default errorHandler;
```

```jsx
// posts.js
router.get("/:id", (req, res, next) => {
  const id = parseInt(req.params.id);
  const post = posts.find((post) => post.id === id);

  if (!post) {
    const error = new Error(`A post with the id of ${id} was not found`);
    error.status = 404;
    return next(error);
  }

  res.status(200).json(post);
});
```

```jsx
// server.js

// Routes
app.use("/api/posts", posts); // (defualt path, route path)

// Error Handler
app.use(errorHandler);
```

- 만약 error가 발생하면 Error 객체 `{message, status}`를 생성한 뒤, next 함수에 error를 전달하고 있다.
- server.js에서 route 미들웨어 다음에 `errorHandler` 미들웨어가 존재하므로, `get` 미들웨어에서 `next` 함수는 error.js의 `errorHandler`를 지칭한다.

<br><br>

# 404 Not Found

```jsx
// notFound.js
const notFound = (req, res, next) => {
  const error = new Error(`Not Found`);
  error.status = 404;
  next(error);
};

export default notFound;
```

- default error를 지정하지 않으면 없는 페이지에 요청을 받는 경우 여전히 html 형식의 에러를 반환할 것이다.
- 이를 위해서 `notFound.js`에 디폴트 에러를 지정해준 뒤 `server.js`에서 처리해줄 수 있다.

```
// server.js

// Routes
app.use("/api/posts", posts); // (defualt path, route path)

// Error Handler
app.use(notFound);
app.use(errorHandler);
```

- 들어온 요청이 route에 없다면 `notFound`를 거쳐 `errorHandler`로 향하게 된다.
- 여기서 의문점이 생긴다.
  - 만약 posts의 get에서 `next(error)`가 실행된다면?
  - next 미들웨어로 `notFound`가 실행되고, 그렇게 되면 `Error` 객체가 덮어쓰이는 것이 아닌가?
- Express에서는 미들웨어가 어떤 인자를 받는 지에 따라 종류가 나뉜다고 한다.
  - `next()` → 그냥 다음에 위치해 있는 미들웨어 함수로 넘어간다.
  - `next(${value})` → 에러 핸들러 콜백 함수는 다음과 같은 형태이다.
    ```jsx
    (err, req, res, next) => {
      res.send("Error Occurred");
    };
    ```
    - 따라서 next에 인자를 넘길 경우 express는 에러 핸들러 미들웨어를 찾기 위해 다른 미들웨어들은 실행하지 않는다.
    - 위와 같은 경우에도 `posts`의 `get`에서 `next(error)`를 호출했으므로 콜백 함수의 인자 개수가 3개인 `notFound`는 에러 핸들러 함수로 인식되지 않으며, 다음에 위치한 `errorHandler` 미들웨어가 실행되는 것이다.
