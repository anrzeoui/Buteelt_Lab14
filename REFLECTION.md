# REFLECTION.md — Бие даалт 14 Эргэцүүлэл

**F.CSM311 Программ хангамжийн бүтээлт**  
**Бие даалт 14: Integration & API Testing**

---

## 1. Аль assertion хамгийн их үнэ цэнэтэй санагдсан вэ? Яагаад?

Хамгийн их үнэ цэнэтэй assertion нь **бизнес дүрмийн шалгалт** байсан, тухайлбал email хаяг `/@/` regex-тэй тохирч байгаа эсэхийг шалгах assertion.

Учир нь: status code 200 буцааж байсан ч хэрэглэгчийн email талбар буруу форматтай (жишээ нь `"notanemail"`) байж болно. Энэ тохиолдолд `pm.expect(user.email).to.match(/@/)` шалгалт алдааг илрүүлдэг. API нь техникийн хувьд "амжилттай" хариу өгч байсан ч өгөгдлийн **агуулга** буруу байж болно.

Мөн `pm.expect(post.title).to.equal(sentTitle)` assertion — POST request-д явуулсан өгөгдөл буцаж ирж байгаа эсэхийг шалгадаг нь API contract-ийн гол хэсэг. Хэрэв сервер өгөгдлийг хааяа орхиж эсвэл өөрчилдөг бол энэ тест л илрүүлнэ.

---

## 2. Negative test жишээгээ дэлгэрэнгүй тайлбарла

**Negative test:** `GET /posts/999999` — байхгүй ID-тэй пост авах.

**Яг ямар алдааг олох вэ?**

- **API алдаа буцаахгүй байх:** Хэрэв сервер `999999` ID-д 200 status-тай хоосон объект буцаавал (`{}`), энэ нь API-ийн дизайны алдаа. Client код `post.title`-г уншихад `undefined` гарч frontend crash болно.
- **Алдааны message буруу байх:** 404-ийг 500 Internal Server Error-р сольж буцааж байвал тест нь `pm.response.to.have.status(404)` шалгалтаар энийг илрүүлнэ.
- **Нууц мэдээлэл задрах:** Зарим API 404-д stack trace эсвэл database алдааг response-д оруулдаг — энэ нь аюулгүй байдлын эмзэг цэг.

Negative test нь зөвхөн "хэвийн ажиллагаа" шалгахаас гадна, **алдааны зам мөн contract** гэдгийг баталгаажуулдаг. Frontend хөгжүүлэгч 404 хариу хүлээж код бичдэг тул API нь заавал 404 буцаах ёстой.

---

## 3. Postman-д ажиллаж байсан тест Newman-д fail болсон уу?

Ерөнхийдөө Newman-д бас pass болсон, учир нь JSONPlaceholder нь тогтвортой публик API.

Гэхдээ **боломжит fail шалтгаанууд:**

- **Environment variable дамжаагүй:** Postman UI-д `firstPostId` хувьсагч автоматаар chain-ийн дараа бөглөгддөг, гэхдээ Newman-д collection-ийн дараалал эсвэл pre-request script-ийн алдаанаас болж хувьсагч `undefined` болж болно.
- **env.dev.json дутуу хувьсагч:** env файлд `firstPostId` эхний утга (`"1"`) тохируулаагүй бол Newman-д `/posts/{{firstPostId}}` → `/posts/` болж 200 биш өөр status буцаана.
- **Шийдэл:** `env.dev.json`-д бүх хувьсагчийн анхны утгыг заавал бичих, мөн collection-ийн эхний request Happy GET-д chain script зөв байгаа эсэхийг шалгах.

---

## 4. Token эсвэл secret-ыг хэрхэн зохицуулсан вэ?

JSONPlaceholder auth шаардахгүй учраас энэ бие даалтад бодит token байхгүй. Гэхдээ **env-ийн соёлыг** дагасан:

- `env.dev.json` болон `env.ci.json` файлд хоосон placeholder хувьсагч байна.
- Token шаардагдах API ашиглах байсан бол: `"API_KEY": "REPLACE_THIS_WITH_REAL_KEY"` хэлбэрээр placeholder бичиж, README-д тайлбарладаг.
- **GitHub Secrets** ашиглан CI workflow-д `${{ secrets.API_KEY }}` хэлбэрээр inject хийдэг — энэ нь token-г commit-д оруулахгүйгээр CI-д ашиглах стандарт арга.
- `.gitignore`-д бодит token агуулсан файлыг оруулахгүй байх.

**Дүгнэлт:** Secret-г repository-д шууд commit хийх нь хамгийн аюултай алдаа — нийтийн repo бол бүгд харж болно.
 
---

## 5. API өөрчлөгдвөл collection-ийн аль хэсэг хамгийн их эвдрэх вэ?

**Хамгийн эмзэг газар: Schema assertion-ууд**

Жишээ нь `pm.expect(post).to.have.property('body')` — хэрэв API дизайнер `body` талбарын нэрийг `content` болгон өөрчлөвөл энэ assertion бүхэлдээ fail болно. Мөн `pm.expect(user.email).to.match(/@/)` — email validation-ийн format дүрэм өөрчлөгдвөл бас эвдэрнэ.

**Бизнес дүрмийн assertion-ууд** бас эмзэг: `pm.expect(created.title).to.equal(sentTitle)` — хэрэв сервер title-г trim хийх эсвэл lowercase болгох болвол тест fail болно.

**Эмзэг байдлыг бууруулах аргууд:**

- **Contract testing** (Pact) ашиглах — consumer болон provider-ийн contract тусад нь шалгана.
- Schema validation library (Ajv) ашиглан өгөгдлийн бүтцийг уян хатан шалгах.
- Assertion-г хэт "тодорхой" бус, "чухал шинж"-д л төвлөрүүлэх (жишээ нь exact string match-ийн оронд `.to.include()` ашиглах).
- API-ийн breaking change-ийг changelog-оос хянах, эсвэл versioned endpoint (`/v2/posts`) ашиглах.