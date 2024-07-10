# server.js

-   API를 생성하고, JSON data를 제공하는 부분이 server.js이고 이름은 컨벤션이다.
-   Frontend에서 view를 보여주는 부분은 app.js라는 이름의 컨벤션을 사용한다.

<br><br>

# express 객체

```jsx
const express = require("express");
const app = express();

app.listen(8000, () => console.log(`Server is running on ${8000}`));
```

-   express 객체는 여러가지 기능을 수행한다.
    -   routing
    -   use middleware
    -   server start
    -   listen on a port

<br><br>

# API

```jsx
app.get("/", (req, res) => {
    res.send("<h1>Hello World</h1>");
});
```

-   express에서는 express 객체의 함수를 이용하여 간단하게 CRUP를 구현할 수 있다.
-   기본적으로 `method(URL, callback)`의 형태로 인자를 전달할 수 있고 callback은 **request**와 **respons** 객체를 받을 수 있다.
-   `send` → 데이터를 전달할 수 있다.
    -   여기서 html 태그를 붙이면 html 형식으로 전달이 된다.
    -   `*res*.send({ message: "Hello World" });`와 같이 **js 객체로 전달**하면 자동으로 stringfy 처리되어 JSON 형식으로 전달된다.
-   만약 html 파일로 view를 보여주고 싶다면?

    ```html
    // index.html
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0" />
            <title>Document</title>
        </head>
        <body>
            <h1>Welcome to the Hompage</h1>
        </body>
    </html>
    ```

    ```jsx
    // server.js
    const path = require("path");

    app.get("/", (req, res) => {
        res.sendFile(path.join(__dirname, "public", "index.html"));
    });
    ```

    -   path 모듈을 이용하여 html 파일을 전송할 수 있다.
