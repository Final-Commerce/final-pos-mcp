# Tool reference

Every tool the Final MCP server (`https://ai.finalpos.com/mcp`) exposes, generated from the live server on 2026-10-17 by `scripts/mcp-catalog.mjs` in FINAI. 175 tools in 10 packs. Do not edit by hand.

A connection lists only a few packs at a time. Every tool below is still reachable: `find_tools` searches all allowed packs and `call_tool` calls any allowed tool by name. **R** = read-only, **W** = changes data, **W!** = deploys or deletes (the agent asks first).

## Packs

| Pack | Product | Tools | What it covers |
|---|---|---:|---|
| `catalog` | Hub | 29 | Products, variants, categories, options, modifiers, product photos and the media library. |
| `inventory` | Hub | 34 | Stock levels and history, transfers, suppliers, purchase orders, reorder rules, stock CSV import/export. |
| `orders` | Hub | 6 | Orders, refunds and e-commerce order sync (read-only: the till takes payments). |
| `customers` | Hub | 8 | Customer records, notes, order history and customer import. |
| `reports` | Hub | 9 | Financial summary, sales and payment breakdowns, employee sales, sessions, transactions, record export. |
| `team` | Hub | 10 | Team members, roles and permissions. |
| `store` | Hub | 25 | Outlets, stations, taxes and Hub extensions. |
| `bookings` | Hub | 11 | Bookable services: rules, resources, availability, holds and confirmations. |
| `build` | Flow | 27 | Build and deploy Final POS apps: API grounding, blueprints, flow files, preview sandboxes, validation, deploy. |
| `build_data` | Flow | 9 | Custom tables (the app database), the secrets vault and Flow extensions for an app under construction. |

## Core (`core`)

Always listed: the connection, packs and tool discovery.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `call_tool` | W! | Call a tool from any allowed pack by its full name (for example hub_find_low_stock) without it being listed. | `name`, `arguments?` |
| `disable_packs` | W | Remove packs from tools/list for this connection to keep the list short. | `packs` |
| `enable_packs` | W | Add packs to tools/list for this connection (remembered). | `packs` |
| `find_tools` | R | Search every allowed pack for tools by what you want to do ("low stock", "create a product", "deploy the app"). | `query`, `limit?` |
| `get_connection` | R | Who this connection acts as: organisation, user, the company Hub tools act on and the companies you may switch to, the allowed and listed packs, and how to reach tools that are not listed. | — |
| `list_packs` | R | Every tool pack with its product, description, tool count and whether it is allowed for this connection and listed right now. | — |
| `switch_company` | W | Choose the company Hub tools act on, by id or name (get_connection lists them). | `company` |

## Catalog (`catalog`)

Products, variants, categories, options, modifiers, product photos and the media library.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_add_modifier_choice` | W | Add one choice to an existing modifier, leaving the rest alone. | `modifierId`, `name`, `price?`, `isDefault?`, `defaultQuantity?`, `sortOrder?`, `unavailableOutletIds?` |
| `hub_add_product_images` | W | Add photos to an existing product — from public URLs, or as base64 bytes when the merchant attached the file itself and there is no URL. | `productId`, `imageUrls?`, `imageFiles?` |
| `hub_create_category` | W | Create a product category. | `name`, `description?`, `image?`, `parentId?`, `sortOrder?` |
| `hub_create_modifier` | W | Create a new question the till can ask, with its choices. | `name`, `selectionType`, `allowQuantity?`, `choices?` |
| `hub_create_product` | W | Create a product in the merchant's catalogue. | `name`, `productType?`, `price`, `costPrice?`, `sku?`, `barcode?`, `description?`, `shortDescription?`, `categoryIds?`, `outletIds?`, `options?`, `variantPrices?`, `taxable?`, `taxTableId?`, `trackInventory?` |
| `hub_create_product_option` | W | Create a new variation axis with its possible values. | `name`, `values` |
| `hub_delete_category` | W! | Delete a category. | `categoryId` |
| `hub_delete_media_file` | W! | Permanently delete one file from the media library. | `fileId` |
| `hub_delete_modifier` | W! | Remove a modifier from the catalogue AND detach it from every category at once — the till stops asking it everywhere. | `modifierId` |
| `hub_delete_modifier_choice` | W! | Remove one choice from a modifier. | `modifierId`, `choiceId` |
| `hub_delete_product` | W! | Remove a product from the merchant's catalogue. | `productId` |
| `hub_get_categories` | R | The merchant's product categories, as the tree they see in the cabinet: every category with its subcategories nested inside it, sorted by sortOrder. | — |
| `hub_get_category_modifiers` | R | Where modifiers are actually asked: one row per category↔modifier link, with that category’s own rules (required, min, max, order). | `categoryId?` |
| `hub_get_media_library` | R | List the image files stored on the merchant's account. | `search?`, `page?`, `pageSize?` |
| `hub_get_modifier` | R | One modifier in full: its choices with prices and defaults, and every category it is linked to with that category’s own rules. | `modifierId` |
| `hub_get_modifiers` | R | Every modifier the merchant has — the questions the till can ask about an item, with their choices and prices. | — |
| `hub_get_product_options` | R | List the variation axes the merchant already has — e.g. | — |
| `hub_get_products` | R | List products in the merchant's catalogue. | `search?`, `categoryIds?`, `page?`, `pageSize?` |
| `hub_import_categories` | W! | Load a category tree in one call — a whole department structure, a migration. | `creates?`, `updates?` |
| `hub_import_options` | W! | Load product attributes and their values in one call — "Size: S,M,L", "Colour: Red,Blue,Green" — the axes variants are built from. | `creates?`, `updates?` |
| `hub_import_products` | W! | Load a spreadsheet of products in one call — "here are my 200 items", a catalogue from another POS, a price list. | `creates?`, `updates?` |
| `hub_link_modifier_to_category` | W | Start asking a modifier on every product in a category — THIS is what puts it on the till. | `modifierId`, `categoryId`, `required?`, `min?`, `max?`, `sortOrder?` |
| `hub_unlink_modifier_from_category` | W! | Stop asking a modifier in one category. | `categoryModifierId` |
| `hub_update_category` | W! | Rename a category, change its description or image, move it under a different parent, or set its sortOrder. | `categoryId`, `name?`, `description?`, `image?`, `parentId?`, `sortOrder?` |
| `hub_update_category_modifier` | W | Change how a modifier is asked in ONE category — required, min, max, order — leaving every other category alone. | `categoryModifierId`, `required?`, `min?`, `max?`, `sortOrder?` |
| `hub_update_modifier` | W! | Rename a modifier and/or REPLACE its whole choice list. | `modifierId`, `name?`, `choices?` |
| `hub_update_modifier_choice` | W | Change one choice — its name, price, whether it is pre-selected, or where it is sold. | `modifierId`, `choiceId`, `name?`, `price?`, `isDefault?`, `defaultQuantity?`, `sortOrder?`, `unavailableOutletIds?` |
| `hub_update_product` | W! | Change fields on an existing product. | `productId`, `name?`, `price?`, `sku?`, `barcode?`, `variants?`, `description?`, `shortDescription?`, `status?`, `categoryIds?`, `outletIds?` |
| `hub_upload_media` | W | Store image files from public URLs in the media library so the merchant can use them later. | `imageUrls` |

## Inventory (`inventory`)

Stock levels and history, transfers, suppliers, purchase orders, reorder rules, stock CSV import/export.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_adjust_stock` | W! | Change the count of ONE variant at ONE outlet — the cabinet's "Adjust Inventory" action. | `productId`, `variantId`, `outletId`, `action`, `quantity` |
| `hub_cancel_purchase_order` | W! | Cancel an order the merchant no longer wants. | `purchaseOrderId` |
| `hub_create_purchase_order` | W | Write a new order to a supplier. | `supplierId`, `outletId`, `lines`, `expectedDate?`, `notes?`, `discount?`, `discountPercent?`, `shippingFee?`, `dutyFees?`, `customFees?` |
| `hub_create_stock_transfer` | W! | Move stock from one outlet to another — 'send 10 of these from the warehouse to the King Street shop'. | `fromOutletId`, `toOutletId`, `items`, `invoice?`, `note?` |
| `hub_create_supplier` | W | Add a business the merchant buys from. | `name`, `contactName?`, `contactEmail?`, `phone?`, `address1?`, `address2?`, `city?`, `state?`, `country?`, `postCode?`, `notes?` |
| `hub_delete_supplier` | W! | Remove a supplier the merchant no longer buys from. | `supplierId` |
| `hub_export_stock_csv` | R | The stock export file for ONE outlet, as CSV text: Variation ID, Product Name, Variation name, SKU, Stock Management, Cur Qty, New Qty. | `outletId`, `productTypes?`, `statuses?`, `categoryIds?`, `maxRows?` |
| `hub_find_low_stock` | R | What is running out. | `outletId?`, `threshold?`, `limit?` |
| `hub_get_linkable_items` | R | Catalogue items this supplier does NOT carry yet but could — the picker behind "add items to supplier". | `supplierId?`, `search?` |
| `hub_get_purchase_order` | R | One purchase order in full: supplier, outlet, status, and every line — what was ORDERED and what has actually ARRIVED, per item, across each delivery. | `purchaseOrderId` |
| `hub_get_purchase_orders` | R | Stock the merchant has ordered from suppliers — the same list as Purchasing → Purchase Orders, and THE tool for any purchasing question over a period: 'what did I spend on stock last month', 'what is still on order', 'wh | `status?`, `supplierIds?`, `outletIds?`, `search?`, `startDate?`, `endDate?`, `sortBy?`, `sortOrder?`, `page?`, `pageSize?` |
| `hub_get_receive_progress` | R | How far a delivery has got in being applied to stock. | `receiptId` |
| `hub_get_reorder_suggestions` | W | What the platform thinks should be reordered: rescans stock against the merchant's auto-reorder rules and returns the suggested orders it left behind, grouped by supplier and outlet. | — |
| `hub_get_stock` | R | How many of ONE product are on hand, broken down by variant and by outlet. | `productId`, `outletId?` |
| `hub_get_stock_history` | R | Every recorded stock movement for ONE product, newest first — the audit trail behind a count. | `productId`, `outletIds?`, `actions?`, `startDate?`, `endDate?` |
| `hub_get_stock_transfer` | R | ONE transfer in full: both outlets, the employee, the invoice, the notes and the line items with quantities. | `transactionId`, `type` |
| `hub_get_stock_transfers` | R | The Stock Transfers list. | `type?`, `status?`, `outletIds?`, `search?`, `page?`, `pageSize?` |
| `hub_get_supplier` | R | One supplier in full: contact details, address, notes, how many catalogue items it is linked to and how many of its purchase orders are still open. | `supplierId` |
| `hub_get_supplier_items` | R | The catalogue items one supplier carries, with the cost the merchant pays and the supplier’s own part number. | `supplierId`, `outletId?`, `search?`, `page?`, `pageSize?` |
| `hub_get_suppliers` | R | The businesses this merchant BUYS stock from — the same list as Purchasing → Suppliers. | `search?`, `page?`, `pageSize?` |
| `hub_get_suppliers_for_product` | R | The other direction: given a product from get_products, which suppliers carry its items and at what cost. | `productId` |
| `hub_import_stock_csv` | W! | Write stock counts for ONE outlet from the CSV that export_stock_csv returns — the cabinet's stock upload. | `outletId`, `csv` |
| `hub_link_supplier_items` | W! | Change what a supplier carries and what it charges, in one call: `add` new items, `update` the cost or part number of items already linked, `remove` items it no longer sells. | `supplierId`, `add?`, `update?`, `remove?` |
| `hub_receive_purchase_order` | W! | Book a delivery in against an order that was sent: ADDS THE RECEIVED QUANTITIES TO REAL STOCK at the order’s outlet and updates the item cost book. | `purchaseOrderId`, `lines`, `invoiceNumber?`, `invoiceDate?`, `receivedAt?`, `updateSupplierCostVariantIds?`, `discount?`, `discountPercent?`, `shippingFee?`, `dutyFees?`, `customFees?` |
| `hub_receive_stock_transfer` | W! | Confirm that a transfer arrived: the destination outlet gains the units and the transfer closes. | `stockTransferId` |
| `hub_record_received_goods` | W! | Stock that arrived with no purchase order behind it — the merchant bought it on the spot, or it was ordered outside the platform. | `supplierId`, `outletId`, `lines`, `invoiceNumber?`, `invoiceDate?`, `receivedAt?`, `notes?`, `updateSupplierCostVariantIds?`, `discount?`, `discountPercent?`, `shippingFee?`, `dutyFees?`, `customFees?` |
| `hub_remove_reorder_rule` | W! | Stop watching one item: it will no longer be suggested for reordering however low it runs. | `variantId` |
| `hub_reorder_categories` | W! | Set the display order of several categories in one call — "put Drinks first", "sort my categories alphabetically". | `categories` |
| `hub_reorder_category_modifiers` | W | Set the order the till asks a category’s modifiers in. | `categoryId`, `orderedIds` |
| `hub_reorder_modifier_choices` | W | Set the order the choices appear in at the till. | `modifierId`, `orderedChoiceIds` |
| `hub_send_purchase_order` | W! | Place the order: moves a `draft` or `suggested` order to `sent` and, when asked, EMAILS IT TO THE SUPPLIER. | `purchaseOrderId`, `sendEmail?` |
| `hub_set_reorder_rule` | W | Tell the platform to watch one item and suggest reordering it when stock runs low: below lowStockPoint, suggest ordering reorderQuantity from this supplier. | `variantId`, `supplierId`, `lowStockPoint`, `reorderQuantity`, `outletOverrides?` |
| `hub_update_purchase_order` | W! | Rewrite an order that has not been sent yet. | `purchaseOrderId`, `supplierId`, `outletId`, `lines`, `expectedDate?`, `notes?`, `discount?`, `discountPercent?`, `shippingFee?`, `dutyFees?`, `customFees?` |
| `hub_update_supplier` | W! | Change one supplier’s details. | `supplierId`, `name?`, `contactName?`, `contactEmail?`, `phone?`, `address1?`, `address2?`, `city?`, `state?`, `country?`, `postCode?`, `notes?` |

## Orders (`orders`)

Orders, refunds and e-commerce order sync (read-only: the till takes payments).

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_get_order` | R | Everything inside ONE order: line items with quantity, unit price and per-line tax, custom sales, gift-card and other non-revenue lines, the totals block, every payment taken (method, processor, card type and last 4, tip | `orderId` |
| `hub_get_order_count` | R | How many orders the company has, in total or at given outlets — 'how many orders do we have', 'how busy is the King Street shop'. | `outlets?` |
| `hub_get_orders` | R | Find orders — the same rows as Manage → Orders. | `search?`, `status?`, `source?`, `employeeIds?`, `customerIds?`, `productIds?`, `startDate?`, `endDate?`, `minTotal?`, `maxTotal?`, `outlets?`, `stations?`, `page?`, `pageSize?` |
| `hub_get_refunds` | R | Refunds recorded against orders, newest first: what was refunded, why, by whom, on which order and for which customer. | `orderId?`, `search?`, `page?`, `pageSize?` |
| `hub_sync_order_to_ecommerce` | W! | Push one order to the company's connected online store (WooCommerce): creates it there the first time, updates it afterwards, and syncs its notes. | `orderId` |
| `hub_sync_refund_to_ecommerce` | W! | Push a refund that ALREADY exists in Final POS to the connected WooCommerce store, so the online order shows as refunded there. | `refundId` |

## Customers (`customers`)

Customer records, notes, order history and customer import.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_add_customer_note` | W | Add a note to a customer — an allergy, a preference, what happened with an order. | `customerId`, `message` |
| `hub_create_customer` | W | Create a customer. | `email`, `firstName?`, `lastName?`, `phone?`, `billing?`, `shipping?`, `note?` |
| `hub_delete_customer` | W! | Remove a customer from the merchant's list. | `customerId` |
| `hub_get_customer` | R | Everything known about ONE customer: addresses, notes, how many orders they placed, what they spent and what was refunded. | `customerId` |
| `hub_get_customer_orders` | R | ONE customer's history as a single timeline, newest first: their orders AND the refunds taken against them, each row marked type "order" or "refund" — the same list the customer screen shows. | `customerId`, `page?`, `pageSize?` |
| `hub_get_customers` | R | List the merchant's customers. | `search?`, `page?`, `pageSize?` |
| `hub_import_customers` | W! | Load a customer list in one call — a mailing list, a migration from another system. | `rows?`, `updates?` |
| `hub_update_customer` | W! | Change contact details on an existing customer. | `customerId`, `email?`, `firstName?`, `lastName?`, `phone?`, `billing?`, `shipping?` |

## Reports (`reports`)

Financial summary, sales and payment breakdowns, employee sales, sessions, transactions, record export.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_export_records` | R | Dump one resource in spreadsheet shape — every record flattened to the same columns the import tools accept, WITH its id. | `resource`, `offset?`, `limit?` |
| `hub_get_employee_orders` | R | The orders ONE staff member rang up in a date range. | `employeeId`, `startDate`, `endDate`, `outlets?`, `stations?`, `page?`, `pageSize?` |
| `hub_get_employee_sales` | R | Sales per staff member for a date range — Reports → Employee Sales. | `startDate`, `endDate`, `outlets?`, `stations?`, `employeeId?`, `page?`, `pageSize?` |
| `hub_get_financial_summary` | R | THE headline money report for a date range — the same numbers as Reports → Financial Summary. | `startDate`, `endDate`, `outlets?`, `stations?` |
| `hub_get_payment_methods_breakdown` | R | How customers paid over a date range, netted of refunds — 'how much came in on card vs cash', 'what did processing fees cost me', 'how much hits my bank account'. | `startDate`, `endDate`, `outlets?`, `stations?` |
| `hub_get_sales_breakdown` | R | Order-by-order breakdown for a date range — the same rows as Reports → Sales Breakdown. | `startDate`, `endDate`, `outlets?`, `stations?`, `page?`, `pageSize?`, `timezone?` |
| `hub_get_session_report` | W | The full End of Session report for ONE shift: sales, taxes, settlement, refunds, staff sales, tips and the cash drawer. | `sessionId` |
| `hub_get_sessions` | R | Register sessions (shifts) opened and closed in a date range — the list behind Reports → End of Session. | `startDate`, `endDate`, `outlets?`, `stations?` |
| `hub_get_transactions` | R | Individual payments and refunds — Reports → Transactions. | `startDate`, `endDate`, `outlets?`, `stations?`, `search?`, `status?`, `paymentMethods?`, `processors?`, `cardProviders?`, `page?`, `pageSize?` |

## Team (`team`)

Team members, roles and permissions.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_assign_role` | W! | Move a team member onto a different role — this is how someone gains or loses access. | `userId`, `roleId` |
| `hub_create_role` | W | Create a new role with a chosen set of permissions — for when no existing role fits what the merchant wants someone to do. | `name`, `permissionIds` |
| `hub_create_user` | W | Add a POS user — someone who signs in at the till with a PIN. | `firstName`, `lastName`, `pincode`, `roleId`, `phone?` |
| `hub_delete_role` | W! | Delete a role the merchant no longer uses. | `roleId` |
| `hub_get_permissions` | R | The catalogue of permissions a role can be given — every switch on the Roles screen, with the id create_role and update_role need. | — |
| `hub_get_roles` | R | The roles this merchant has defined and exactly what each one is allowed to do — Settings → Roles. | — |
| `hub_get_team` | R | Everyone who works for this merchant — Manage → Team → Users. | — |
| `hub_remove_user` | W! | Remove someone from the merchant's team — the tool for 'she left', 'take the leaver off'. | `userId` |
| `hub_update_role` | W! | Change what a role allows, or rename it. | `roleId`, `name?`, `grantPermissionIds?`, `revokePermissionIds?` |
| `hub_update_user` | W! | Change a team member's own details: name, phone, till PIN, and which outlets they work at. | `userId`, `firstName?`, `lastName?`, `phone?`, `pincode?`, `outletIds?` |

## Store setup (`store`)

Outlets, stations, taxes and Hub extensions.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_add_tax_rate_to_group` | W! | Add an EXISTING rate to a tax group, or change that rate’s compounding flag within the group. | `taxGroupId`, `taxRateId`, `compounding?` |
| `hub_create_outlet` | W | Open a new point of sale — the cabinet’s Add Outlet form. | `name`, `address`, `city`, `state`, `postCode`, `address2?`, `phone?`, `taxId?`, `alias?` |
| `hub_create_station` | W | Add a VIRTUAL till — a browser-based station, the cabinet’s "Add Virtual Station". | `name`, `outletId`, `flowSrcId?` |
| `hub_create_tax_group` | W | Create an empty tax group (a tax profile that products get assigned to, e.g. | `name`, `isStandard?` |
| `hub_create_tax_rate` | W | Create a named tax rate ("State Sales Tax", "GST") with its percentage, and optionally put it straight into a tax group. | `name`, `percentage?`, `perOutlet?`, `taxGroupId?`, `compounding?` |
| `hub_delete_outlet` | W! | Close a point of sale permanently. | `outletId` |
| `hub_delete_station` | W! | Remove a till from Run → Stations. | `stationId` |
| `hub_delete_tax_group` | W! | Delete a tax group. | `taxGroupId`, `replacementTaxGroupId?` |
| `hub_delete_tax_rate` | W! | Permanently delete a tax rate FROM THE WHOLE COMPANY. | `taxRateId` |
| `hub_get_extension_catalog` | R | The extension STORE: every built-in extension the platform offers, the same list for every merchant. | — |
| `hub_get_extension_surfaces` | R | What the installed extensions ADD to the apps — the answer to "where did this new menu item come from", "what is this extra block on the product page", "what did installing X actually change". | — |
| `hub_get_installed_extensions` | R | What THIS company actually has installed — built-in and organisation-built (custom) extensions alike. | — |
| `hub_get_outlets` | R | Every point of sale (store, location, branch) the merchant has — the same list as Settings → Outlets. | — |
| `hub_get_station` | R | Everything about ONE station, including what its device last reported about itself — app version, OS, network, printer, battery and whatever else that device sends. | `stationId` |
| `hub_get_stations` | R | Every POS station (till, register, checkout lane, cash desk) the merchant has — the same list as Run → Stations. | `outletId?`, `status?`, `kind?`, `search?` |
| `hub_get_taxes` | R | The whole Settings → Taxes screen: every tax group, the rates inside it in charging order, and each rate’s percentage at each outlet. | — |
| `hub_import_outlets` | W! | Load a list of store locations in one call — opening a chain, migrating from another system. | `creates?`, `updates?` |
| `hub_install_extension` | W | Install an extension for the whole company. | `extensionId` |
| `hub_remove_tax_rate_from_group` | W! | Stop ONE tax group charging a rate. | `taxGroupId`, `taxRateId` |
| `hub_rename_station` | W! | Rename one station. | `stationId`, `name` |
| `hub_suggest_tax_rate` | R | Look up the statutory headline rate for a country (and US state / Canadian province). | `country`, `state?` |
| `hub_uninstall_extension` | W! | Uninstall an extension from the company. | `extensionId` |
| `hub_update_outlet` | W! | Change the details of one outlet. | `outletId`, `name?`, `address?`, `address2?`, `city?`, `state?`, `postCode?`, `phone?`, `taxId?`, `alias?` |
| `hub_update_tax_group` | W! | Rename a tax group, make it the standard one, or change the ORDER its rates are charged in. | `taxGroupId`, `name?`, `isStandard?`, `rateIdsInOrder?` |
| `hub_update_tax_rate` | W! | Change a tax rate’s name or its percentage. | `taxRateId`, `name?`, `percentage?`, `perOutlet?` |

## Bookings (`bookings`)

Bookable services: rules, resources, availability, holds and confirmations.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `hub_cancel_booking` | W! | Release a booked window. | `holdId` |
| `hub_confirm_booking` | W | Turn a hold into a real booking once the order behind it is paid, so it stops expiring. | `holdId`, `orderId` |
| `hub_create_booking_resource` | W | Add one bookable thing to the company directory. | `name`, `kind`, `tag?` |
| `hub_create_booking_rules` | W | Create a booking policy. | `name`, `bookingType`, `ratePeriod`, `weeklyHours`, `slotLengthMinutes`, `bufferMinutes?`, `holdMinutes?`, `maxAdvanceDays?`, `minNoticeMinutes?`, `checkInTime?`, `checkOutTime?` |
| `hub_get_booking_availability` | R | The bookable windows for a product over a date range, per resource, with what is already taken. | `productId`, `from`, `to`, `outletId?` |
| `hub_get_booking_events` | R | The trail of what happened to bookings — held, confirmed, cancelled, expired — newest first, with who did it. | `productId?`, `limit?` |
| `hub_get_booking_resources` | R | List what can be booked — the staff, rooms, tables or assets in the company directory. | — |
| `hub_get_booking_rules` | R | List the reusable booking policies the merchant already has — opening hours, slot length, buffer, how far ahead people may book. | — |
| `hub_get_product_booking_setup` | R | Which resources serve this bookable product. | `productId` |
| `hub_hold_booking` | W | Claim a window on one resource. | `productId`, `resourceId`, `startAt`, `endAt`, `outletId?`, `customerId?` |
| `hub_set_product_booking_setup` | W | Point a booking product at its rule set AND the resources that serve it — both in one call, because a product with only one of them takes no bookings at all. | `productId`, `bookingRulesId`, `resourceIds` |

## Build apps (`build`)

Build and deploy Final POS apps: API grounding, blueprints, flow files, preview sandboxes, validation, deploy.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `flow_apply_blueprint_widgets` | W! | COPY vetted blueprint widgets into a flow as real files — byte-exact, no transcription. | `flowId`, `picks`, `note?`, `agent?`, `hubToken?` |
| `flow_apply_upstream_update` | W! | Apply the update reviewed with get_upstream_update. | `flowId`, `resolutions?`, `adopt?`, `note?`, `agent?`, `hubToken?` |
| `flow_connect_chat` | W | Attach this agent session to an EXISTING flow as a chat in the builder's rail (so the flow's owner sees your work alongside their own chats). | `flowId`, `agent?` |
| `flow_create_flow` | W | Create a new, empty flow (app build) in your Final organization for YOU to write the app into. | `title`, `agent?`, `device?`, `theme?`, `palette?`, `customColors?`, `color?`, `shape?`, `fonts?` |
| `flow_create_flow_from_blueprint` | W | Create a NEW flow that starts as a complete, vetted application rather than an empty file tree — the fastest path to a working build when an FBB (a Final Business Blueprint: a whole app) matches what the user wants. | `blueprintId`, `title?`, `flowId?`, `supersedesFlowId?`, `agent?`, `hubToken?`, `device?`, `theme?`, `palette?`, `customColors?`, `color?`, `shape?`, `fonts?` |
| `flow_delete_flow_files` | W! | Remove files from a flow's tree by path. | `flowId`, `paths`, `agent?` |
| `flow_deploy_flow` | W! | Direct deploy for CI/agent workflows: builds the app in a server sandbox (npm install + vite build) and publishes the dist to Final web-hosting as a new live version. | `flowId`, `version`, `changelog?`, `hostingToken?` |
| `flow_get_blueprint` | R | INSPECTION ONLY — read a blueprint's overlay code by id (from list_blueprints) to understand how it works. | `id`, `includeFiles?` |
| `flow_get_preview_status` | R | Authoritative server-side health probe of the flow's preview sandbox, plus a short snapshot of recent dev-server logs (useful to diagnose a red screen or crash). | `flowId`, `logSeconds?` |
| `flow_get_scaffold` | R | The contract every Final flow app is built against: which files the ENGINE owns (never write them — they are re-asserted verbatim), the baseline package.json (Vite + React 19 + TS + Tailwind + command-frame), the rules y | `device?` |
| `flow_get_upstream_status` | R | Has the build this flow was copied from released a newer version? Covers both a "Make it yours" clone of a listing and a build started from an FBB. | `flowId` |
| `flow_get_upstream_update` | R | Review what an update from the original build would change — the same review the builder shows its maker, shaped for an agent. | `flowId` |
| `flow_list_blueprints` | R | List EVERY vetted, type-checked blueprint available to reuse — working functionality (gift cards, loyalty, payment surfaces, product grids, carts, customer views, order tickets, receipts), not just screen layouts. | `kind?` |
| `flow_list_flows` | R | List the flows (app builds) in your organization, newest first: id, title, timestamps, and live theme knobs. | `limit?` |
| `flow_log_message` | W | Record a message in this agent's chat inside the builder — the user's request (role 'user') or your own progress/summary (role 'assistant'). | `flowId`, `role`, `text`, `agent?` |
| `flow_lookup_command_frame` | R | Look up the EXACT, version-locked command-frame API a generated Final POS app runs on: renderClient method signatures + params, CF* entity field lists, pub/sub topic event vocabularies, and worked examples. | `method?`, `entity?`, `field?`, `topic?`, `query?`, `methods?`, `entities?`, `topics?` |
| `flow_lookup_engine_component` | R | Look up the exact props/signature of an engine-owned `./final/*` helper: the `@/final/ui` primitives (Button, IconButton, Card, Tile, Input, Field, Chip, ListRow, Dialog, Sheet, Toolbar, Heading, EmptyState, cn) plus use | `name?`, `query?` |
| `flow_lookup_pos_api` | R | Look up the Final POS REST API (https://api.finalpos.com) — endpoint method/path, request fields, response shape, and a worked fetch example. | `resource?`, `operation?`, `query?` |
| `flow_open_in_builder` | R | The recommended way to finish: a deep link that opens this flow in the Miami builder, where the owner can review the work, restore checkpoints, and deploy through the Launch tool (validation gates, listing media, changel | `flowId` |
| `flow_read_flow_files` | R | Read a flow's generated source tree. | `flowId`, `paths?` |
| `flow_rename_flow` | W | Rename a build (flow) authoritatively across the platform in one call: the builder header, list_flows / open_in_builder, and the Deerlake catalog all reflect the new title, and updatedAt is bumped. | `flowId`, `title`, `agent?` |
| `flow_search_blueprints` | R | SEMANTIC search over the vetted blueprint catalog — matches are ranked by what you MEAN, not by shared words, so describe the capability in plain language ('rewards for repeat customers' finds the loyalty blueprint). | `query`, `slot?`, `surface?`, `kind?`, `limit?` |
| `flow_start_preview` | W | Boot the flow's server-side preview sandbox (Vite dev server) — or reconcile a running one to the flow's current files — and return its public HTTPS URL already pinned to this flow's mock dataset: the maker's pick, else  | `flowId` |
| `flow_stop_preview` | W! | Stop the flow's preview sandbox and free its resources. | `flowId` |
| `flow_sync_preview` | W | Reconcile a (running or cold) preview sandbox to the flow's current files and run the green-build gate (tsc + vite build). | `flowId` |
| `flow_validate_flow` | R | Deterministic STATIC checks against the platform contract: (1) guardrails — fabricated renderClient methods, forbidden browser APIs/imports, engine-file writes; (2) a REAL TypeScript check of command-frame usage against  | `flowId` |
| `flow_write_flow_files` | W! | Create or overwrite files in a flow's tree (full contents, not diffs). | `flowId`, `files`, `note?`, `agent?` |

## Build: data & extensions (`build_data`)

Custom tables (the app database), the secrets vault and Flow extensions for an app under construction.

| Tool | | What it does | Arguments |
|---|---|---|---|
| `flow_create_custom_table` | W | Create a new custom data table (schema: name + typed columns) in your Final organization. | `name`, `description?`, `fields`, `hubToken?` |
| `flow_install_extension` | W! | Install an extension ORG-WIDE: creates the install link on EVERY company in your Final organization (demo companies included), skipping companies that already have it, so it is safe to retry. | `extensionId`, `hubToken?` |
| `flow_list_custom_tables` | R | List your organization's custom data tables with their column schemas. | `hubToken?` |
| `flow_list_extension_custom_tables` | R | List the CUSTOM TABLES an extension registers (table name + typed columns), so generated code can read/write its data at runtime via renderClient.getCustomTableData / upsertCustomTableData by table name (ground the calls | `extensionId`, `hubToken?` |
| `flow_list_extension_surfaces` | R | List the SURFACES an extension exposes — embeddable iframe injection points, each with a surfaceId, a platform (builder/hub/render), a type, and a url. | `extensionId`, `hubToken?` |
| `flow_list_extensions` | R | List the platform EXTENSIONS available to your organization — the org's own plus the public marketplace — so you can suggest, inspect, or install one. | `query?`, `skip?`, `limit?`, `hubToken?` |
| `flow_list_secret_keys` | R | List the key NAMES already stored in your organization's encrypted secrets vault — never the values (no tool returns a secret value). | `hubToken?` |
| `flow_request_secret` | W | Ask the flow's owner to enter a third-party credential (API key, auth token, webhook secret) into Final's encrypted vault. | `flowId`, `key`, `label`, `provider?`, `reason`, `docsUrl?`, `agent?`, `hubToken?` |
| `flow_update_custom_table` | W! | Evolve an EXISTING custom data table's schema: add columns, change existing ones (rename/retype — destructive for stored rows, use sparingly), or drop columns. | `tableName`, `addFields?`, `updateFields?`, `removeFields?`, `hubToken?` |
