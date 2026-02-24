# 🔄 Zoho CRM → Creator & Books Account Sync

> **Zoho Deluge Automation** — Automatically syncs CRM Account records to Zoho Creator and Zoho Books with upsert logic (create if not exists, update if exists).

---

## 📌 Overview

When an Account is created or updated in **Zoho CRM**, this automation ensures the same data is immediately reflected in both:

- **Zoho Creator** — as a contact record in the `project-management` app
- **Zoho Books** — as a customer/contact for invoicing and billing

This eliminates manual data entry across multiple Zoho apps and keeps all three platforms in sync automatically.

---

## 🗂️ File Structure

```
zoho-crm-account-sync/
│
├── Create_account_in_Creator.dg     # Main Deluge automation script
├── README.md                        # This documentation file
└── docs/
    └── connection-setup.md          # OAuth connection setup guide
```

---

## ⚙️ How It Works

```
CRM Account Created/Updated
        │
        ▼
 Fetch Account from CRM
        │
        ├──────────────────────────────────────────────┐
        ▼                                              ▼
 Search in Zoho Creator                     Search in Zoho Books
        │                                              │
   ┌────┴────┐                                   ┌────┴────┐
   │ Found?  │                                   │ Found?  │
   └────┬────┘                                   └────┬────┘
   YES  │  NO                                    YES  │  NO
        │                                             │
   UPDATE      CREATE                           UPDATE    CREATE
   Record      Record                           Contact   Contact
```

### Sync Logic (Upsert)

| Destination    | Search Key       | If Found     | If Not Found |
|----------------|-----------------|--------------|--------------|
| Zoho Creator   | `Name` field     | Update record | Create record via `Add_Contact` form |
| Zoho Books     | `contact_name`   | Update contact | Create new contact |

---

## 📋 Fields Synced

### CRM → Zoho Creator (`Contacts_for_Admins` report / `Add_Contact` form)

| CRM Field          | Creator Field        |
|--------------------|----------------------|
| Account ID         | `Zcrm_account_id`    |
| Account Name       | `Name`               |
| Email              | `Email`              |
| Phone              | `Phone`              |
| Account Type       | `Account_Type`       |
| Billing Address    | `Billing_Address`    |
| Billing City       | `Billing_Address.district_city` |
| Billing State      | `Billing_Address.State_province` |
| Billing Zip        | `Billing_Address.postal_Code` |
| Shipping Address   | `Shiping_Address`    |
| Shipping City      | `Shiping_Address.district_city` |
| Shipping State     | `Shiping_Address.state_province` |
| Shipping Zip       | `Shiping_Address.postal_Code` |
| Parent Account     | `Parent_Account`     |

### CRM → Zoho Books (Contacts API)

| CRM Field          | Books Field              |
|--------------------|--------------------------|
| Account Name       | `contact_name`           |
| Account Name       | `company_name`           |
| Email              | `email`                  |
| Billing Address    | `billing_address.address` |
| Billing City       | `billing_address.city`   |
| Billing State      | `billing_address.state`  |
| Billing Zip        | `billing_address.zip`    |
| Shipping Address   | `shipping_address.address` |
| Shipping City      | `shipping_address.city`  |
| Shipping State     | `shipping_address.state` |
| Shipping Zip       | `shipping_address.zip`   |

---

## 🔌 Prerequisites

### 1. OAuth Connections Required

You must create these two connections in **Zoho CRM → Setup → Developer Hub → Connections**:

#### Connection 1: Zoho Creator
| Setting              | Value                          |
|----------------------|-------------------------------|
| Connection Name      | `Creator` (any display name)  |
| Connection Link Name | `creator1` *(must match code)* |
| Service              | Zoho Creator                  |

**Required Scopes:**
```
ZohoCreator.report.READ
ZohoCreator.report.UPDATE
ZohoCreator.form.CREATE
ZohoCreator.meta.read
ZohoCreator.customapi.EXECUTE
ZohoCreator.dashboard.read
```

#### Connection 2: Zoho Books
| Setting              | Value                         |
|----------------------|------------------------------|
| Connection Name      | `Books` (any display name)   |
| Connection Link Name | `books` *(must match code)*  |
| Service              | Zoho Books                   |

**Required Scopes:**
```
ZohoBooks.contacts.READ
ZohoBooks.contacts.CREATE
ZohoBooks.contacts.UPDATE
```

> ⚠️ **Important:** Both connections must be authorized by an **active admin account**. If the original creator of a connection leaves the organization, revoke and recreate the connection.

---

### 2. Zoho Creator App Details

| Setting       | Value               |
|---------------|---------------------|
| App Owner     | `bairquality`       |
| App Link Name | `project-management` |
| Report Name   | `Contacts_for_Admins` |
| Form Name     | `Add_Contact`       |

### 3. Zoho Books Organization

| Setting         | Value       |
|-----------------|-------------|
| Organization ID | `70000185` |

---

## 🚀 Setup Instructions

### Step 1 — Add the Function in Zoho CRM

1. Go to **CRM → Setup → Developer Hub → Functions**
2. Click **New Function**
3. Set category: **Automation**
4. Paste the code from `Create_account_in_Creator.dg`
5. Save the function

### Step 2 — Create a Workflow Rule to Trigger It

1. Go to **CRM → Setup → Automation → Workflow Rules**
2. Create a new rule for the **Accounts** module
3. Set trigger: **On Create** and/or **On Edit**
4. Add action: **Function** → select `Create_account_in_Creator`
5. Map the `account_id` parameter to `${Accounts.id}`
6. Save and Activate

### Step 3 — Test the Function

1. Create or edit any Account in CRM
2. Go to **Functions → Logs** to verify execution
3. Check Creator and Books for the synced record

---

## 🐛 Common Errors & Fixes

| Error Code | Message | Fix |
|------------|---------|-----|
| `2933` / `403` | `You do not have permission to access this application` | Recreate the `creator1` connection with correct scopes |
| `6018` / `400` | `Your account is disabled` | Recreate the `books` connection authenticated by an active admin |
| `3000` | Success — record found in Creator | Expected, no action needed |

---

## 👤 Author

**Rafiullah Nikzad**
Senior Zoho Developer at CloudZ Technologies
🌐 [rafiullahnikzad.netlify.app](https://rafiullahnikzad.netlify.app)
💼 [Zoho Afghanistan Community on LinkedIn](https://www.linkedin.com/groups/zoho-afghanistan)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
