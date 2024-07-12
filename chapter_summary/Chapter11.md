# session
```jsx
const express = require('express')
const session = require('express-session')

var app = express()

app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
}));
```

- `express-session` 모듈을 사용하여 세션 기능을 구현할 수 있다.
- options
    - **resave →** 요청이 왔을때 세션에 수정사항이 생기지 않아도 다시 저장할지 여부
    - **saveUninitialized →** 세션에 저장할 내역이 없더라도 세션을 저장할지 여부
    - **secret →** 쿠키 암호화
    - **cookie →** 쿠키 설정과 동일(httpOnly, secure 등)
    - **name →** 세션쿠키 이름 (connect.sid가 디폴트)
    - **store →** 세션 저장소. 메모리가 디폴트.
- `req.session` : 세션값들을 다 볼 수 있음
- `req.sessionID` : 세션 아이디 확인 (세션쿠키 value)
- `req.session.destroy()` : 세션 모두 제거

```jsx
router.get("/session", (req, res) => {
    console.log(req.session);
    console.log(req.sessionID);
    res.json({ message: `session create complete` });
});
```

```jsx
Session {
  cookie: {
    path: '/',
    _expires: 2024-07-12T11:20:48.777Z,
    originalMaxAge: 60000,
    httpOnly: true,
    secure: false
  }
}

PKSLhtIhZ6pOm68trk48UpH6aPKdBqsX
```

- 세션ID는 매 요청마다 만들어진다.
- 쿠키와 달리 서버에 데이터를 저장하고, 클라이언트는 Session ID만을 가지고 있기 때문에 비교적 안전하다.
- 세션의 동작 과정
    1. 서버는 클라이언트에게 세션 값을 보낸다. (**sid**, 아무런 의미도 없는 **단순 식별자)**
    2. 클라이언트는 접속할 때 자신이 가지고 있는 sid를 서버에게 전달한다.
    3. 서버는 클라이언트가 보내준 sid를 가지고, 해당 유저를 식별합니다.

<br><br>

# session 유지

```jsx
router.get("/session", (req, res) => {
    console.log(req.session);
    console.log(req.session.id);
    req.session.visited = true;
    res.json({ message: `session create complete` });
});

router.get("/session/test", (req, res) => {
    if (req.session.visited) {
        return res.json("session was created");
    }

    res.json("session is not created");
});
```

- 매 요청마다 세션을 만들고 싶지 않다면??
- session data를 업데이트하면 된다.
    - express는 session의 data가 변경될 때마다 `session.save()`를 호출한다.
    - 따로 `session.save()`를 명시해도 된다.

<br><br>

# session 불러오기
```jsx
router.get("/session/get", (req, res) => {
    req.sessionStore.get(req.session.id, (err, session) => {
        if (err) {
            console.log(err);
            throw err;
        }

        return res.status(200).json(session);
    });
});
```

- sessionStore는 세션이 담겨있는 저장소다.
- 여기서 get함수를 통해 request에서 보낸 id에 해당하는 세션이 있는지 확인할 수 있다.
