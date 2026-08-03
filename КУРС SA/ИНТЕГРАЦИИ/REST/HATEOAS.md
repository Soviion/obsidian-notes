## HATEOAS - (Hypermedia as the Engine of Application State) 
- это принцип REST, по которому ответы API содержат не только данные, но и гиперссылки на возможные следующие действия.
- если API использует HATEOAS ( то есть все 4 уровня) - оно полностью RESTful
- обычно используется 3 уровня - удобно - компактно - обычно 
### Модель зрелости REST — 4 уровня
- **Level 0** — The Swamp of POX Просто RPC через HTTP (один endpoint, POST с XML/JSON внутри, как SOAP без стандартов).
- **Level 1** — Resources Ввели отдельные ресурсы по URI (например /users/42, /orders/123).
- **Level 2** — HTTP Verbs + Status Codes Правильно используем GET/POST/PUT/DELETE, 200/201/404/500 и т.д. → Большинство современных «REST» API останавливаются здесь.
- **Level 3** — HATEOAS Добавляем гиперссылки в ответы. → Только это считается **настоящим REST** по Филдингу.

Пример без HATEOAS:
```
GET /orders/123

Ответ: 
{ 
	"orderId": 123, 
	"status": "pending", 
	"total": 99.99, 
	"items": [...] }
```

Пример c HATEOAS:
```
GET /orders/123

Ответ:
{
  "orderId": 123,
  "status": "pending",
  "total": 99.99,
  "items": [...],
  "_links": {
    "self": { "href": "/orders/123" },
    "cancel": { "href": "/orders/123/cancel", "method": "DELETE" },
    "pay": { "href": "/orders/123/payment", "method": "POST" },
    "add-item": { "href": "/orders/123/items", "method": "POST" }
  }
}
```
