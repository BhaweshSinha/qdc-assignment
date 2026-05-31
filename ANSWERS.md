# Answers

## 1.
In a real production version of a system like QDC, keeping all orders in an in-memory array inside `OrdersService` would quickly become a problem. It would not scale, and all data would be lost if the server restarts. It would also create issues when multiple users are updating orders at the same time.

To solve this, I would move the data layer to a proper database like PostgreSQL or MongoDB depending on how complex the querying needs are. In NestJS, I would introduce a repository layer using something like Prisma or TypeORM so that the service layer doesn’t directly deal with data storage.

I would also add indexing on commonly used fields like order status and timestamps to improve performance. For concurrent updates, especially when multiple workers are updating garment statuses, I would use transactions or optimistic locking to avoid conflicts.

---

## 2.
Returning either an `Order` object or an `{ error: string }` response works for a simple demo, but it’s not ideal for a real production API. The main issue is that it mixes success and error responses in an inconsistent format, which makes frontend handling more complicated.

A better approach is to use proper HTTP status codes. For example, return `200 OK` when the request succeeds and `404 Not Found` when an order doesn’t exist. In NestJS, this is usually handled using built-in exceptions like `NotFoundException`.

This makes the API more predictable, easier to integrate with, and better aligned with standard REST practices. It also improves debugging and monitoring since errors are clearly separated from successful responses.

---

## 3.
Right now using `fetch` directly inside `useEffect` is fine for a small app, but it becomes messy once the application grows. If the dashboard gets more complex, I would avoid calling APIs directly inside components.

Instead, I would move all API calls into a separate service file like `api/orders.ts`. On top of that, I would create custom hooks like `useOrders()` to handle fetching, loading states, and filtering logic in a cleaner way.

For larger dashboards, using something like React Query would be even better since it handles caching, refetching, and background updates automatically. This keeps components clean and focused only on UI.

---

## 4.
The current `Order` and `Garment` models are quite minimal and would not fully support real-world laundry operations. In practice, we would need more detail like payment status, pricing, delivery deadlines, and type of service (wash, iron, dry clean, etc.).

There are also several edge cases that are not covered, such as partially completed orders, damaged garments, lost items, or garments moving through multiple processing steps.

To improve the model, I would introduce additional entities like invoices, payments, and a status history for garments. Instead of storing only the current status, keeping a history would help track the full lifecycle of each garment, which is important for operational transparency.

---

## 5.
AI-generated code is useful for speeding up development, but it also comes with risks. Sometimes it can produce incorrect logic, inconsistent patterns, or insecure implementations without obvious warnings.

Because of this, any AI-generated code should always go through proper code reviews. I would also rely on unit tests and integration tests to make sure the behavior matches expectations. Linters and static analysis tools help maintain consistency and catch basic issues early.

For critical features like order processing or payments, I would be extra careful and manually verify the logic before deploying to production.

---

## 6.
To support real-time updates in a system like QDC, I would move beyond simple REST APIs and introduce WebSockets using something like Socket.IO in NestJS. This would allow the backend to push updates directly to the frontend whenever a garment status changes.

If full WebSockets feel too heavy, Server-Sent Events (SSE) could be a simpler alternative for one-way updates. For larger-scale systems, a message broker like Kafka or RabbitMQ could be used to handle event-driven communication between services.

Each approach has tradeoffs: WebSockets provide full real-time interaction but add complexity, while SSE is simpler but limited. Event-driven systems scale better but require more infrastructure.