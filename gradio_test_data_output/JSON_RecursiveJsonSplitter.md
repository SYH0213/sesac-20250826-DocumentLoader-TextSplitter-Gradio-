# 잘못 저장된 파일이고 후에 원인 찾겠음

## 원본 데이터 Input Data

```
{
  "metadata": {
    "source": "demo-api",
    "version": "2025-08-26"
  },
  "users": [
    {
      "id": "u1",
      "name": "Kim",
      "orders": [
        {
          "id": "o-100",
          "items": [
            {
              "sku": "A100",
              "qty": 2
            },
            {
              "sku": "C300",
              "qty": 1
            }
          ],
          "total": 10.4
        },
        {
          "id": "o-101",
          "items": [
            {
              "sku": "B200",
              "qty": 1
            }
          ],
          "total": 9.9
        }
      ]
    },
    {
      "id": "u2",
      "name": "Lee",
      "orders": [
        {
          "id": "o-200",
          "items": [
            {
              "sku": "A100",
              "qty": 1
            }
          ],
          "total": 3.85,
          "note": "coupon: RAG10"
        }
      ]
    }
  ],
  "logs": [
    {
      "t": "10:00",
      "event": "create",
      "ref": "o-100"
    },
    {
      "t": "10:03",
      "event": "pay",
      "ref": "o-100"
    },
    {
      "t": "10:05",
      "event": "ship",
      "ref": "o-100"
    }
  ]
}
```


---

## 분할된 청크 Output Data

### Chunk 1

```
# mega_app.py
"""
Mega App: a deliberately complex single-file Python project to stress-test code splitters.
(요약) 서비스/리포지토리/게이트웨이/컨트롤러/CLI/데코레이터/비동기/제너레이터/데이터클래스/에넘 포함.
"""
from __future__ import annotations
```

---### Chunk 2

```
import abc
import argparse
import asyncio
import functools
import json
import logging
import random
import time
from dataclasses import dataclass
from enum import Enum
from pathlib import Path
from typing import Any, Optional, TypedDict, Union
```

---### Chunk 3

```
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(name)s | %(message)s")
logger = logging.getLogger("mega_app")
```

---### Chunk 4

```
class OrderStatus(str, Enum):
    CREATED = "CREATED"
    PAID = "PAID"
    SHIPPED = "SHIPPED"
    CANCELLED = "CANCELLED"

@dataclass(frozen=True)
class OrderItem:
    sku: str
    qty: int
    price: float

@dataclass
```

---### Chunk 5

```
class Order:
    id: str
    items: list[OrderItem]
    status: OrderStatus = OrderStatus.CREATED
    note: Optional[str] = None
    def total(self) -> float:
        s = sum(i.qty * i.price for i in self.items)
        tax = round(s * 0.1, 2)
        return round(s + tax, 2)
```

---### Chunk 6

```
return round(s + tax, 2)
    def add_note(self, text: str) -> None:
        self.note = f"{self.note}\\n{text}" if self.note else text
```

---### Chunk 7

```
class PaymentResult(TypedDict):
    ok: bool
    txn_id: str
    message: str
```

---### Chunk 8

```
def timed(fn):
    if asyncio.iscoroutinefunction(fn):
        @functools.wraps(fn)
        async def _aw(*a, **kw):
            t0 = time.perf_counter()
            try:
                return await fn(*a, **kw)
            finally:
```

---### Chunk 9

```
finally:
                logger.debug("%s took %.1fms", fn.__name__, (time.perf_counter()-t0)*1000)
        return _aw
    @functools.wraps(fn)
    def _w(*a, **kw):
        t0 = time.perf_counter()
        try:
            return fn(*a, **kw)
        finally:
```

---### Chunk 10

```
return fn(*a, **kw)
        finally:
            logger.debug("%s took %.1fms", fn.__name__, (time.perf_counter()-t0)*1000)
    return _w
```

---### Chunk 11

```
def retry(times=2, base_delay=0.05):
    def deco(fn):
        if asyncio.iscoroutinefunction(fn):
            @functools.wraps(fn)
            async def _aw(*a, **kw):
                import random, asyncio
                ex = None
                for i in range(times):
                    try:
```

---### Chunk 12

```
try:
                        return await fn(*a, **kw)
                    except Exception as e:
                        ex = e; await asyncio.sleep(base_delay * (2**i) + random.random()*0.05)
                raise ex
            return _aw
        @functools.wraps(fn)
```

---### Chunk 13

```
@functools.wraps(fn)
        def _w(*a, **kw):
            import time, random
            ex = None
            for i in range(times):
                try:
                    return fn(*a, **kw)
                except Exception as e:
```

---### Chunk 14

```
except Exception as e:
                    ex = e; time.sleep(base_delay * (2**i) + random.random()*0.05)
            raise ex
        return _w
    return deco
```

---### Chunk 15

```
class OrderRepository(abc.ABC):
    @abc.abstractmethod
    def save(self, order: Order) -> None: ...
    @abc.abstractmethod
    def get(self, order_id: str) -> Optional[Order]: ...
    @abc.abstractmethod
    def list(self) -> list[Order]: ...
```

---### Chunk 16

```
class MemoryOrderRepository(OrderRepository):
    def __init__(self) -> None:
        self._data: dict[str, Order] = {}
        self._cache_total: dict[str, float] = {}
    def save(self, order: Order) -> None:
        self._data[order.id] = order
        self._cache_total[order.id] = order.total()
```

---### Chunk 17

```
def get(self, order_id: str) -> Optional[Order]:
        return self._data.get(order_id)
    def list(self) -> list[Order]:
        return list(self._data.values())
    def cached_total(self, order_id: str) -> Optional[float]:
        return self._cache_total.get(order_id)
```

---### Chunk 18

```
class PaymentGateway(abc.ABC):
    @abc.abstractmethod
    async def pay(self, order: Order, method: str) -> PaymentResult: ...
```

---### Chunk 19

```
class MockPaymentGateway(PaymentGateway):
    @timed
    @retry(times=2, base_delay=0.05)
    async def pay(self, order: Order, method: str) -> PaymentResult:
        await asyncio.sleep(0.05)
        if random.random() < 0.1:
            raise RuntimeError("Transient network error")
```

---### Chunk 20

```
return {"ok": True, "txn_id": f"TXN-12345", "message": f"Paid via {method}"}
```

---### Chunk 21

```
class ShippingGateway(abc.ABC):
    @abc.abstractmethod
    async def ship(self, order: Order) -> str: ...
class MockShippingGateway(ShippingGateway):
    @timed
    async def ship(self, order: Order) -> str:
        await asyncio.sleep(0.03)
        return f"SHP-{order.id}"
```

---### Chunk 22

```
class OrderService:
    def __init__(self, repo: OrderRepository, payments: PaymentGateway, shipping: ShippingGateway) -> None:
        self.repo = repo; self.payments = payments; self.shipping = shipping
    def create_order(self, order_id: str, items: list[OrderItem]) -> Order:
```

---### Chunk 23

```
if self.repo.get(order_id): raise ValueError("exists")
        order = Order(id=order_id, items=items); order.add_note("created"); self.repo.save(order); return order
    async def checkout(self, order_id: str, method: str = "CARD") -> PaymentResult:
        o = self.repo.get(order_id);
```

---### Chunk 24

```
o = self.repo.get(order_id); 
        if not o: raise KeyError("not found")
        res = await self.payments.pay(o, method); 
        if res["ok"]: o.status = OrderStatus.PAID; o.add_note("paid"); self.repo.save(o)
        return res
    async def ship(self, order_id: str) -> str:
```

---### Chunk 25

```
async def ship(self, order_id: str) -> str:
        o = self.repo.get(order_id); 
        if not o or o.status != OrderStatus.PAID: raise RuntimeError("not paid")
        lab = await self.shipping.ship(o); o.status = OrderStatus.SHIPPED; o.add_note("shipped"); self.repo.save(o); return lab
```

---### Chunk 26

```
def totals_report(self) -> dict[str, float]:
        return {o.id: self.repo.cached_total(o.id) or o.total() for o in self.repo.list()}
```

---### Chunk 27

```
class OrderController:
    def __init__(self, svc: OrderService) -> None: self.svc = svc
    def create(self, order_id: str, items: list[tuple[str,int,float]]):
        order_items = [OrderItem(sku, qty, price) for sku, qty, price in items]
```

---### Chunk 28

```
o = self.svc.create_order(order_id, order_items); return {"id": o.id, "status": o.status.value, "total": o.total()}
```

---### Chunk 29

```
def build_demo() -> OrderController:
    repo = MemoryOrderRepository(); pay = MockPaymentGateway(); ship = MockShippingGateway()
    svc = OrderService(repo, pay, ship); return OrderController(svc)
```

---### Chunk 30

```
if __name__ == "__main__":
    ctrl = build_demo()
    ctrl.create("ORD-1", [("A100",2,3.5),("B200",1,9.9)])
    import asyncio, json
    async def run():
        await ctrl.svc.checkout("ORD-1"); lab = await ctrl.svc.ship("ORD-1")
```

---### Chunk 31

```
print(json.dumps({"label": lab, "report": ctrl.svc.totals_report()}, ensure_ascii=False))
    asyncio.run(run())
```

---