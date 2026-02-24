# 🔌 OAuth Connection Setup Guide

This guide explains how to create the required OAuth connections in Zoho CRM for this automation to work.

---

## Why Connections Are Needed

Zoho Deluge scripts cannot directly call other Zoho apps without OAuth authentication. Connections store the OAuth tokens securely and allow the function to act on behalf of a user.

> ⚠️ **Critical:** Connections are tied to the user who creates them. If that user's account is deactivated or revoked, the connection stops working. Always create connections using an active admin account.

---

## Connection 1: Zoho Creator (`creator1`)

### Steps

1. Go to **Zoho CRM → Setup → Developer Hub → Connections**
2. Click **New Connection**
3. Search for and select **Zoho Creator**
4. Fill in the details:

| Field                | Value         |
|----------------------|---------------|
| Connection Name      | `Creator`     |
| Connection Link Name | `creator1`    |

5. Add the following scopes:

```
ZohoCreator.report.READ
ZohoCreator.report.UPDATE
ZohoCreator.form.CREATE
ZohoCreator.meta.read
ZohoCreator.customapi.EXECUTE
ZohoCreator.dashboard.read
```

6. Click **Create and Connect**
7. Authenticate with your **admin Zoho account**
8. Verify **Status = Connected** ✅

---

## Connection 2: Zoho Books (`books`)

### Steps

1. Go to **Zoho CRM → Setup → Developer Hub → Connections**
2. Click **New Connection**
3. Search for and select **Zoho Books**
4. Fill in the details:

| Field                | Value   |
|----------------------|---------|
| Connection Name      | `Books` |
| Connection Link Name | `books` |

5. Add the following scopes:

```
ZohoBooks.contacts.READ
ZohoBooks.contacts.CREATE
ZohoBooks.contacts.UPDATE
```

6. Click **Create and Connect**
7. Authenticate with your **admin Zoho account**
8. Verify **Status = Connected** ✅

---

## Troubleshooting Existing Connections

### If a connection shows 403 / Permission Denied

This usually means the connection was created by a user who no longer has access.

1. Go to **My Connections** tab
2. Find the old connection (e.g., `creator` or `books`)
3. Click **Revoke**
4. Follow the steps above to create a new connection

### If you see error code `6018` — Account Disabled

The Books connection OAuth token belongs to a deactivated account. Revoke and recreate the `books` connection using an active admin account.

---

## Verifying Connection Scopes

After creating a connection:

1. Go to **Connections → My Connections**
2. Click on the connection name
3. Scroll down to **Scopes** section
4. Click **Show More** to verify all required scopes are listed

---

## Private vs Shared Connections

| Type   | Description |
|--------|-------------|
| Private | Only the connection creator can use it in functions |
| Shared | All users/functions in the org can use it |

> 💡 For org-wide automations, set **Private Connection = No** so any function can use it.
