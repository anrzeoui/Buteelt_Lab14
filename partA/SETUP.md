# А хэсэг — Setup

## Сонгосон API: JSONPlaceholder

### Товч тайлбар
JSONPlaceholder нь REST API-г туршиж үзэх зориулалттай үнэгүй, нээлттэй хуурамч (fake) онлайн API юм. Frontend болон mobile хөгжүүлэгчид прототип бүтээх, туршилтын өгөгдөл ашиглахад зориулагдсан.

- **Вэбсайт:** https://jsonplaceholder.typicode.com
- **Өгөгдөл:** Posts, Comments, Albums, Photos, Todos, Users (6 нөөц)
- **Онцлог:** CRUD үйлдлийг дэмждэг боловч өгөгдөл бодитоор хадгалагддаггүй (симуляц)

### Auth (Баталгаажуулалт)
- **Шаардлагагүй** — API key, token, эсвэл нэвтрэх шаардлагагүй
- Бүх endpoint нь нийтэд нээлттэй

### Base URL
```
https://jsonplaceholder.typicode.com
```

### Боломжит endpoint-ууд
| Endpoint | Арга | Тайлбар |
|---|---|---|
| `/posts` | GET | Бүх постуудыг авах |
| `/posts/1` | GET | ID=1 пост авах |
| `/posts` | POST | Шинэ пост үүсгэх |
| `/posts/1` | PUT | Пост шинэчлэх |
| `/posts/1` | DELETE | Пост устгах |
| `/posts/999999` | GET | Байхгүй пост (404) |
| `/users` | GET | Бүх хэрэглэгчид |
| `/users/1` | GET | ID=1 хэрэглэгч |
| `/posts/1/comments` | GET | Постын коммент |

### Rate Limit
- Тодорхой лимит байхгүй, гэхдээ зохистой ашиглавал асуудалгүй

### Тайлбар
JSONPlaceholder нь auth шаардахгүй учраас CI pipeline-д token/secret хадгалах шаардлагагүй. `{{baseUrl}}` environment variable ашиглан бүх request-ийг зохион байгуулна.