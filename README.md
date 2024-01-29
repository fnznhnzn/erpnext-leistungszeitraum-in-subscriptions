# erpnext-leistungszeitraum-in-subscriptions
Leistungszeitraum einer Subscription in Rechnung angeben - Use subscription's billing period as "performance period" in Sales Invoice (needed in Germany)

1. Prerequisite: Add field "custom_leistungszeitraum" to Sales Invoice and print that in a sensible place
2. Create server script for Sales Invoice with DocType Event "After Safe":

```python
# *** add performance period from subscription to invoice ***
# a) deterine whether this invoices was generated by a subscription
# b) load that subscription into object and "ORM" to needed values
# c) get beginning and end of the billing period
# d) safe that in db
if doc.subscription is not None and doc.custom_leistungszeitraum is None: # we stem from a subscription!
    s = frappe.get_doc("Subscription", doc.subscription) # we load that subscription into s

    start = frappe.format_date( s.current_invoice_start )
    end   = frappe.format_date( s.current_invoice_end )
    
    performance_period = start + ' - ' + end

    frappe.db.set_value('Sales Invoice', doc.name, 'custom_leistungszeitraum', performance_period)

```
