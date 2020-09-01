---
title: GoLang-web application-Creating a data structure with methods
date: 2020-01-08 23:20:43
tags: 
  - GoLang
  - pointer
  - error
categories: GoLang
---

## Data Structures

<!-- more --> 
***

### 用struct 定義一個Page 其擁有兩個欄位，title 和 body 
```golang=
type Page struct {
    Title string
    Body  []byte
}
```
`[]byte` means "a byte slice"

### 建立save 方法

```golang=
func (p *Page) save() error {
    filename := p.Title + ".txt"
    return ioutil.WriteFile(filename, p.Body, 0600)
}
```
Takes as its receiver p, a pointer to Page. 
It takes no parameters, and returns a value of type error.

0600, 傳送第三參數給 WriteFile, 指出這個檔案只應被建立於目前使用者可讀可寫的權限

### 建立loadPage 方法
```golang=
func loadPage(title string) (*Page, error) {
    filename := title + ".txt"
    body, err := ioutil.ReadFile(filename)
    if err != nil {
        return nil, err
    }
    return &Page{Title: title, Body: body}, nil
}
```

### 建立main 方法

```golang=
func main() {
    p1 := &Page{Title: "TestPage", Body: []byte("This is a sample Page.")}
    p1.save()
    p2, _ := loadPage("TestPage")
    fmt.Println(string(p2.Body))
}
```

`go build wiki.go`
`./wiki`

### REFERENCES
* [Writing Web Applications](https://golang.org/doc/articles/wiki/#tmp_13)