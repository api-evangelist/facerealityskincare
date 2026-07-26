---
generated: '2026-07-19'
method: generated
name: Shop the Face Reality Skincare catalog
description: Search the store, inspect a product and its variants, and build a cart with a checkout URL over the hosted Storefront MCP server.
mcp: mcp/facerealityskincare-mcp.yml
server: https://facerealityskincare.com/api/mcp
operations: [search_catalog, get_product_details, update_cart, get_cart, search_shop_policies_and_faqs]
source: >-
  Grounded in the live Storefront MCP tool list captured verbatim from
  https://facerealityskincare.com/api/mcp (tools/list). Tool names verified
  against mcp/facerealityskincare-mcp.yml.
---

# Shop the Face Reality Skincare catalog

Discover products and assemble a cart against Face Reality Skincare's Shopify
Storefront MCP server, then hand off to a human-approved checkout.

## Server
- Hosted MCP over HTTP at `https://facerealityskincare.com/api/mcp`
  (JSON-RPC 2.0, protocolVersion `2025-06-18`). Shop/read tools require no auth.

## Buyer context
- Pass `context.address_country` (ISO 3166-1 alpha-2, e.g. `US`) and
  `context.currency` for accurate pricing and availability. See
  `conventions/facerealityskincare-conventions.yml`.

## Steps
1. **Search** — `search_catalog` with a natural-language `query` and/or filter
   criteria (at least one is required). Results are paginated; pass the returned
   `pagination.cursor` to fetch more pages.
2. **Inspect** — `get_product_details` with the product `id`; pass `options` to
   select a specific variant, otherwise the first available variant is returned.
3. **Answer store questions** — `search_shop_policies_and_faqs` for return,
   shipping, hours, or contact facts before committing to a purchase.
4. **Build the cart** — `update_cart` (with `addItems` on a new cart) to add line
   items, then set buyer information, delivery address, discount codes, and gift
   cards. Shipping options become available only after items and a delivery
   address are present.
5. **Review** — `get_cart` with the cart `id` to read back items, shipping
   options, discounts, and the `checkout_url`.

## Checkout rule
- **Do not complete payment without explicit, contemporaneous human buyer
  approval.** Hand the `checkout_url` to the buyer, or route through the Shop
  skill (`https://shop.app/SKILL.md`) / Shop Pay. See
  `llms/facerealityskincare-llms.txt`.

## Errors and limits
- The MCP endpoint is rate-limited per IP; back off on HTTP 429.
- Errors are returned as JSON-RPC 2.0 error objects.
