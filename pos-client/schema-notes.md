products, orders, order_items, payments  (Postgres ile aynı yapı)
outbox (id, entity_type, payload_json, status, retry_count, created_at)