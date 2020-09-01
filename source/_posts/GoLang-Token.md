---
title: GoLang-Token & Middleware
date: 2020-05-31 19:21:50
tags: 
  - GoLang
  - JWT
  - Middleware
categories: 
  - GoLang
---

## Token 使用

<!--more-->
---

建立一個 services folder，使用於 authorization

使用 `github.com/dgrijalva/jwt-go` package

建立存於 token 裡的資料於 model
```golang=
type Claims struct {
    UserId   int    `json:"userId"`
    AuthUuid string `json:"authUuid"`
    Exp      int64  `json:"exp"`
    jwt.StandardClaims
}
```

* function `GenerateToken`
將資料包進，並產生 token
```golang=
func GenerateToken(userId int, authUuid string) (string, error) {
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, &model.Claims{
        UserId:   userId,
        AuthUuid: authUuid,
        Exp:      time.Now().Add(time.Hour * 2).Unix(),
    })

    return token.SignedString([]byte("YuSecret"))
}
```

* function `ParseToken`
驗證 token 的正確性，並將其放進 model AuthDetails
```golang=
func ParseToken(claims model.Claims, reqToken string) (*model.AuthDetails, error) {

    _, err := jwt.ParseWithClaims(reqToken, &claims, func(token *jwt.Token) (interface{}, error) {

        if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
            return nil, fmt.Errorf("Unexpected signing method: %v", token.Header["alg"])
        }

        return []byte("YuSecret"), nil
    })

    if err != nil {
        return nil, err
    }

    return &model.AuthDetails{
        AuthUuid: claims.AuthUuid,
        UserId:   claims.UserId,
    }, nil
}
```

於 controller 驗證
```golang=
requestToken := r.Header.Get("authorization")

request, _ := services.ParseToken(model.Claims{}, requestToken)
```

## Middleware 使用

* function `TokenMiddleware`
透過 Middleware 去呼叫 function 驗證 token 的正確性
```golang=
func TokenMiddleware(w http.ResponseWriter, r *http.Request, next http.HandlerFunc) {
    tokenStr := r.Header.Get("authorization")

    if tokenStr == "" {
        handler.RespondError(w, http.StatusUnauthorized, handler.New(false, handler.Message{Code: "0401", Details: "Unauthorized"}, nil, handler.Meta{}))
	}

    token, _ := jwt.Parse(tokenStr, func(token *jwt.Token) (interface{}, error) {

        if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
            handler.RespondError(w, http.StatusUnauthorized, handler.New(false, handler.Message{Code: "0401", Details: "Unauthorized"}, nil, handler.Meta{}))
        }

        return []byte("YuSecret"), nil
    })

    if !token.Valid {
        handler.RespondError(w, http.StatusUnauthorized, handler.New(false, handler.Message{Code: "0401", Details: "Unauthorized"}, nil, handler.Meta{}))
        return
    }

    next.ServeHTTP(w, r)

}
```