# cookies
```jsx
// server.js
app.get("/", (req, res) => {
    res.cookie("cookie", "is good", { maxAge: 60000 });
    res.status(201).json({ message: "geted cookie" });
});
```

```jsx
// products.js
import { Router } from "express";

const router = Router();

router.get("/api/products", (req, res) => {
    console.log(req.headers.cookie);
    res.json({ id: 1, name: "computer" });
});

export default router;

```

- 쿠키를 사용하고 싶다면 특정 path에 요청이 날라왔을 때, `res.cookie()`로 쿠키를 생성할 수 있다.
- `cookie() →` **(name, value, age)**

<br><br>

# cookie-parser
```jsx
import cookieParser from "cookie-parser";
app.use(cookieParser());
```

```jsx
router.get("/api/products", (req, res) => {
    console.log(req.cookies);
    res.json({ id: 1, name: "computer" });
});
```

- **cookie-parser** 모듈을 사용하면 json 형태로 쿠키를 받아올 수 있다.
- `npm i cookie-parser`

<br><br>

# signed-cookie
```jsx
app.get("/", (req, res) => {
    res.cookie("cookie", "is good", { maxAge: 60000, signed: true });
    res.status(201).json({ message: "geted cookie" });
});
```

```jsx
router.get("/api/products", (req, res) => {
    console.log(req.cookies);
    console.log(req.signedCookies);

    if (req.signedCookies && req.signedCookies.cookie === "is good") {
        return res.json({ id: 1, name: "computer" });
    }

    return res.status(400).json({ message: "cookie is not valid" });
});
```

- 그냥 쿠키를 사용하면 보안의 위험이 있다. 따라서 `signed: true`를 통해 암호화를 진행할 수 있다.
