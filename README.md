# Commerce

The common commerce vocabulary -- catalog, carts, orders, customers, shipments and stock --
distilled from the APIs that already exist. Part of
[Schema Commons](https://github.com/api-commons/schema-commons), published by the
[API Commons](https://apicommons.org).

Built from the providers tagged E-Commerce (in every spelling the catalog carries),
Commerce, Headless, Composable or B2B Commerce, Storefront, Order Management, Shopping Cart
and Online Shopping in [APIs.io](https://apis.io) that publish their own OpenAPI. Only
harvested contracts count -- nothing API Evangelist generated. Every number below is a count
of providers.

## What the market already agrees on

<!-- commons:summary -->
| Concept | Expose it | Readable schema | Core properties (share) | Collection named |
| --- | ---: | ---: | --- | --- |
| product | 153 | 135 | id 85%, name 80%, description 62%, images 50% | products 135, product 32, listings 8, catalog-items 1 |
| product_variant | 26 | 25 | id 76%, name 64%, price 60%, product_id 60%, barcode 56%, inventory_quantity 52% | variants 17, variations 5, skus 4, sku 3 |
| category | 79 | 64 | id 81%, name 81% | categories 64, collections 14, category 13, collection 5 |
| customer | 85 | 80 | id 84%, email 78%, last_name 60%, first_name 59%, created_at 55% | customers 69, customer 21, consumers 4, buyers 1 |
| cart | 42 | 36 | id 69%, line_items 53% | cart 24, carts 20, basket 3 |
| order | 181 | 164 | id 77%, status 62%, line_items 58% | orders 152, order 45, purchases 4, sales_orders 2 |
| order_item | 29 | 24 | quantity 79%, id 75%, product_id 62% | items 23, line_items 2, order_items 2, lines 1 |
| shipment | 24 | 19 | id 68%, status 63%, tracking_number 63% | shipments 11, fulfillments 7, shipment 5, fulfillment 2 |
| inventory | 46 | 35 |  | inventory 30, stock 8, inventory_levels 4, inventories 4 |

Cohort: 401 providers with harvested OpenAPI, 254 crosswalks. Of the providers exposing a product: 67% create, 87% list, 80% fetch, 48% update, 44% delete. Generated 2026-11-11 by build.py.
<!-- /commons:summary -->

Shares are of the providers with a readable resource schema for that concept. The
extended tier and the long tail of every concept are in [profile.yml](profile.yml).

## What it does not agree on

Commerce is the first context where the market's agreement is visibly thin, and that is a
finding, not a gap to paper over:

- **An order is `id`, `status` and its items -- and then it diverges.** Totals, taxes,
  addresses, currency and the customer reference all sit in the extended tier (20-50%),
  because each platform models money and fulfilment differently (`total`, `grand_total`,
  `total_price`, `total_inc_tax`...). The aliases in [concepts.yml](concepts.yml) collapse
  the spellings; they cannot collapse the models.
- **Inventory has no core.** Nothing reaches half the providers that expose stock: `quantity` 46%,
  `sku` 37%, `id` 34%. Some key stock by SKU, some by variant, some by location. The
  concept is published with an extended tier only.

## Artifacts

- [openapi.yml](openapi.yml) -- OpenAPI 3.1 of the shared operations. No one serves it; it
  is the shape to map to. Lints clean against the
  [naming ruleset](https://github.com/api-commons/schema-commons/blob/main/ruleset/schema-commons-naming.yaml).
- [schema/](schema) -- JSON Schema 2020-12 per concept; each property carries
  `x-commons-tier`, `x-commons-share`, `x-commons-providers` and the spellings seen.
- [crosswalk/](crosswalk) -- one per provider, common name -> their name, with the harvested
  spec and operations each mapping was read from. BigCommerce, for example: `line_items` is
  `lineItems`, `total_tax` is `taxTotal`; Shopify's categories are `collections`.
- [adopters.yml](adopters.yml) -- who exposes which concept, linked to their APIs.io page.
- [concepts.yml](concepts.yml) -- the only hand-written input: concepts, path segments,
  aliases, reviewed exclusions and merged duplicates. Everything else is counted.
- [census/](census) -- the cohort and the raw census the profile is built from.

## Decisions

These are calls, not measurements, recorded so they can be revisited as the method matures.

- **"Marketplace" and "Retail" are not cohort tags.** The first also means API, talent and
  data marketplaces; the second takes in energy retail and retail media.
- **A checkout is a payment, not a cart.** In this cohort `/checkout` is almost always a
  hosted payment session (Square, Visa, Tabby, Tamara, Lemon Squeezy, Coinbase Commerce). It
  is left for the payments context; the cart is `carts` and `baskets`.
- **An offer is not a product.** `/offers` here is loyalty offers, FX quotes and
  promotions, so it is not a product segment. `/listings` (Etsy, Reverb, Back Market) is.
- **A collection is a category only on a commerce platform.** Shopify, Medusa and Shoplazza
  group products into collections and count; CMS, search-index and analytics collections
  (Webflow, Duda, Lucidworks, dotdigital, Talon.One) are excluded.
- **Line items are `/orders/{order_id}/items`, shipments `/orders/{order_id}/shipments`.**
  Providers nest them as `items` (24) far more than `line_items` (2), and say `shipments`
  (16) more than `fulfillments` (9). The order's property stays `line_items` -- that is
  the measured property name, which is a separate count from the path.
- **Orders that are not commerce orders are excluded.** Trading orders (Bloomberg EMSX,
  Gemini), buy-now-pay-later and in-store payment orders, fraud-scoring and tracking events.
  Each is listed with its reason under `exclude:` in [concepts.yml](concepts.yml).
- **GraphQL-only platforms are absent, not failing.** Saleor, and the GraphQL side of
  Shopify, publish no OpenAPI for these resources. The census reads OpenAPI only.

## Rebuild

```bash
../schema-commons/scripts/run.sh .
```

## License

Artifacts CC BY-NC-SA 4.0 ([LICENSE](LICENSE)); code Apache 2.0 ([LICENSE-CODE](LICENSE-CODE)).
