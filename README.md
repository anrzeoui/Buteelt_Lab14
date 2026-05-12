# Бие даалт 14 — API Testing: Postman + Newman

**Хичээл:** F.CSM311 Программ хангамжийн бүтээлт  
**Сэдэв:** Integration & API Testing  
**API:** [JSONPlaceholder](https://jsonplaceholder.typicode.com)

---

## Агуулга

```
bie-daalt-14/
├── .github/workflows/api-tests.yml   # CI/CD (GitHub Actions)
├── partA/
│   ├── SETUP.md                      # API сонголт болон тайлбар
│   └── screenshot.png                # Эхний амжилттай request-ийн зураг
├── postman/
│   ├── collection.json               # 9 request (folder-р бүлэглэсэн)
│   ├── env.dev.json                  # Dev environment
│   └── env.ci.json                   # CI environment
├── reports/
│   └── api.html                      # Newman-ийн HTML тайлан
├── README.md
└── REFLECTION.md
```

---

## Хэрхэн ажиллуулах

### 1. Шаардлага

- [Node.js](https://nodejs.org) (v18+) суулгасан байх
- [Postman Desktop](https://postman.com/downloads) (UI-д харахын тулд)

### 2. Newman суулгах

```bash
npm install -g newman newman-reporter-htmlextra
```

### 3. Local-д Newman ажиллуулах

```bash
# Үндсэн командын мөрийн тест
newman run postman/collection.json -e postman/env.dev.json

# HTML тайлантай
newman run postman/collection.json \
  -e postman/env.dev.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/api.html
```

### 4. Postman UI-д import хийх

1. Postman нээх
2. **Import** → `postman/collection.json` файлыг оруулах
3. **Environments** → `postman/env.dev.json` import хийх
4. **dev** environment сонгох
5. Collection ажиллуулах

---

## Collection бүтэц

| Хавтас | Request | Шалгадаг зүйл |
|---|---|---|
| **Posts** | Happy GET — бүх постууд | 200, array, schema, chain |
| **Posts** | GET by ID | 200, schema, type check |
| **Posts** | POST create | 201, id буцаана, business rule |
| **Posts** | PUT update | 200, өөрчлөлт хадгалагдсан |
| **Posts** | DELETE | 200, амжилттай устсан |
| **Users** | Happy GET — бүх users | 200, email format |
| **Users** | GET by ID (chain) | 200, schema бүрэн |
| **Errors** | Байхгүй пост | 404 (negative test) |
| **Errors** | Байхгүй хэрэглэгч | 404 (negative test) |

**Нийт:** 9 request, 25+ assertion, 5+ assertion төрөл

---

## Chain хувьсагч

```
GET /posts  →  firstPostId, firstUserId хадгалдаг
           ↓
GET /posts/{{firstPostId}}  →  chain ашиглана
GET /users/{{firstUserId}}  →  chain ашиглана
```

---

## CI/CD

GitHub Actions автоматаар:
1. Push болгонд collection ажиллуулна
2. JUnit + HTML тайлан үүсгэнэ
3. Тайланг artifact болгон хадгална

**Actions tab:** `https://github.com/<username>/bie-daalt-14/actions`

---

## Тестийн assertion төрлүүд

| Төрөл | Жишээ |
|---|---|
| Status code | `pm.response.to.have.status(200)` |
| Response time | `pm.expect(pm.response.responseTime).to.be.below(1000)` |
| Schema / property | `pm.expect(d).to.have.property('email')` |
| Утгын тип | `pm.expect(d.id).to.be.a('number')` |
| Бизнес дүрэм | `pm.expect(d.email).to.match(/@/)` |
| Header | `pm.response.to.have.header('Content-Type')` |