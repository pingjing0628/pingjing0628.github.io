---
title: GoLang-Router design
date: 2020-02-27 10:00:30
tags: 
  - GoLang
  - SQL
  - Mux
  - Database
categories: 
  - GoLang
---

## 規劃路由

<!--more-->
---

### 定義路由
 
```golang=

type App struct {
    Router *mux.Router
    db     *gorm.DB
}
```

將上次 create 的 database connection 放入至 router 中
建立 DB 連線，並進行 DB Migrate
使用 http.NewServeMux() 處理多路由
```golang=
func (a *App) Initialize() {
    a.db = database.Connect(a.db)
    a.db = database.Migrate(a.db)

    a.Router = mux.NewRouter()

	mux := http.NewServeMux()

	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Content-Type", "application/json")
	})

    a.setRouters()
}
```

設定路由 (包括 middleware )
使用 negroni 處理 middleware 
```golang=
func (a *App) setRouters() {

	apiBase := mux.NewRouter()
	api := apiBase.PathPrefix("/api").Subrouter()

	a.UnAuthPost("/auth/register", a.Register)
	a.UnAuthPost("/auth/login", a.Login)

	a.Get(api, "/own/products", a.GetProducts)
	a.Post(api, "/own/products", a.PostProduct)
	a.Get(api, "/own/products/{id}", a.GetProduct)
	a.Patch(api, "/own/products/{id}", a.UpdateProduct)
	a.Get(api, "/own/tags", a.GetTags)
	a.Get(api, "/own/delete/products", a.GetDeletedProducts)
	a.Delete(api, "/own/products/{id}", a.DeleteProduct)
	a.Get(api, "/auth/logout", a.Logout)

	// Using middleware
	a.Router.PathPrefix("/api").Handler(negroni.New(
		negroni.HandlerFunc(middleware.TokenMiddleware),
		negroni.NewLogger(),
		negroni.Wrap(apiBase),
	))
}

```

將所有 GET/POST... 等 wrap 起來
也設定所有連接 controller 的 function
```golang=
func (a *App) UnAuthPost(path string, f func(w http.ResponseWriter, r *http.Request)) {
	a.Router.HandleFunc(path, f).Methods("POST")
}

// Wrap GET method in router
func (a *App) Get(muxRouter *mux.Router, path string, f func(w http.ResponseWriter, r *http.Request)) {
	muxRouter.Path(path).HandlerFunc(f).Methods("GET")
}

func (a *App) Post(muxRouter *mux.Router, path string, f func(w http.ResponseWriter, r *http.Request)) {
	muxRouter.Path(path).HandlerFunc(f).Methods("POST")
}

func (a *App) Patch(muxRouter *mux.Router, path string, f func(w http.ResponseWriter, r *http.Request)) {
	muxRouter.Path(path).HandlerFunc(f).Methods("PATCH")
}

func (a *App) Delete(muxRouter *mux.Router, path string, f func(w http.ResponseWriter, r *http.Request)) {
	muxRouter.Path(path).HandlerFunc(f).Methods("DELETE")
}

// Controllers to manage data
func (a *App) Register(w http.ResponseWriter, r *http.Request) {
	controller.Register(a.db, w, r)
}

func (a *App) Login(w http.ResponseWriter, r *http.Request) {
	controller.Login(a.db, w, r)
}

func (a *App) Logout(w http.ResponseWriter, r *http.Request) {
	controller.Logout(a.db, w, r)
}

func (a *App) GetProducts(w http.ResponseWriter, r *http.Request) {
	controller.GetProducts(a.db, w, r)
}

func (a *App) PostProduct(w http.ResponseWriter, r *http.Request) {
	controller.PostProduct(a.db, w, r)
}

func (a *App) GetProduct(w http.ResponseWriter, r *http.Request) {
	controller.GetProduct(a.db, w, r)
}

func (a *App) UpdateProduct(w http.ResponseWriter, r *http.Request) {
	controller.UpdateProduct(a.db, w, r)
}

func (a *App) GetTags(w http.ResponseWriter, r *http.Request) {
	controller.GetTags(a.db, w, r)
}

func (a *App) GetDeletedProducts(w http.ResponseWriter, r *http.Request) {
	controller.GetDeletedProducts(a.db, w, r)
}

func (a *App) DeleteProduct(w http.ResponseWriter, r *http.Request) {
	controller.DeleteProduct(a.db, w, r)
}

```

需使用 cors 

```golang=
func (a *App) Run(port string) {
	n := negroni.Classic()

	c := cors.New(cors.Options{
		AllowedOrigins: []string{"*"},
		AllowedMethods: []string{"GET", "POST", "PUT", "DELETE", "PATCH", "OPTIONS"},
		AllowedHeaders: []string{"Accept", "Authorization", "Content-Type", "X-CSRF-Token"},
	})

	n.Use(c)
	n.UseHandler(a.Router)

	n.Run(port)
}
```