<div align="center">

  <h3><b>Data Fundamentals Project: Admin Roles & Security in Supabase</b></h3>
</div>

# 📗 Table of Contents

- [📖 About the Project](#about-project)
  - [🛠 Built With](#built-with)
    - [Tech Stack](#tech-stack)
    - [Key Features](#key-features)
  - [🚀 Live Demo](#live-demo)
- [💻 Getting Started](#getting-started)
  - [Setup](#setup)
  - [Prerequisites](#prerequisites)
  - [Install](#install)
  - [Usage](#usage)
  - [Run tests](#run-tests)
  - [Deployment](#triangular_flag_on_post-deployment)
- [👥 Authors](#authors)
- [🔭 Future Features](#future-features)
- [🤝 Contributing](#contributing)
- [⭐️ Show your support](#support)
- [🙏 Acknowledgements](#acknowledgements)
- [❓ FAQ ](#faq)
- [📝 License](#license)

---

# 📖 Data Fundamentals Project: Admin Roles & Security in Supabase <a name="about-project"></a>

> A 4-week Supabase-based project demonstrating **data security, user roles, and Row Level Security (RLS)** management in PostgreSQL.

**Admin Roles & Security in Supabase** is a project that teaches database access control using **Supabase**, focusing on implementing:
- Admin and user roles  
- Row Level Security (RLS)  
- SQL policies  
- Admin-only database functions  

---

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>

<details>
  <summary>Client</summary>
  <ul>
    <li><a href="https://supabase.com/">Supabase</a></li>
  </ul>
</details>

<details>
  <summary>Server</summary>
  <ul>
    <li><a href="https://www.postgresql.org/">SQL</a></li>
  </ul>
</details>

<details>
<summary>Database</summary>
  <ul>
    <li><a href="https://supabase.com/">PostgreSQL (via Supabase)</a></li>
  </ul>
</details>

---

### Key Features <a name="key-features"></a>

- ✅ **Row Level Security (RLS)** enabled on all tables  
- 🔑 **Admin and User roles** for access control  
- 🧩 **Custom admin-only SQL function** for secure operations  
- 🧠 **Supabase Auth integration** (email and magic link sign-in)  
- 📝 **Documented security policies and configuration**  

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## 🚀 Live Demo <a name="live-demo"></a>

> The project is hosted on Supabase. Access restricted to authenticated users only.

- [Supabase Project Link (Private)](https://supabase.com/dashboard/project/treouufwhcvnflqaxzjg)
- [GitHub Repository](https://github.com/Godwin-Waswa)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## 💻 Getting Started <a name="getting-started"></a>

To get a local copy and run the project, follow these steps:

### Prerequisites

- A Supabase account → [https://supabase.com](https://supabase.com)  
- PostgreSQL knowledge  
- SQL Editor or Supabase SQL workspace  

### Setup

Clone the repository:

```bash
git clone https://github.com/GodwinWaswa/supabase-security-project.git
cd supabase-security-project
```
### Install

- No installation required.
- The project runs entirely in the Supabase web environment.

### Usage

1. Open your Supabase project dashboard.

2. Run the SQL scripts provided below.

3. Enable Row Level Security on all tables:

```sql

alter table users enable row level security;
alter table products enable row level security;
alter table orders enable row level security;

```
4. Create policies for user and admin roles:

```sql

   create policy "Users can view their own orders"
on orders for select
using (auth.uid() = user_id);

create policy "Admins have full access"
on orders for all
using (
  exists (
    select 1 from users where id = auth.uid() and role = 'admin'
  )
);
```
5. Add an admin-only function

```sql

create or replace function delete_user_account(target_user uuid)
returns void
language sql
security definer
as $$
  delete from users where id = target_user;
$$;
```
### Run tests

Manually test permissions using Supabase’s SQL editor:

- Log in as user → try selecting another user’s data (should fail)

- Log in as admin → full access verified

### Deployment

You can deploy or share your project by pushing changes to GitHub and linking it in your Supabase project settings.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 👥 Authors <a name="authors"></a>

👤 **Godwin Wafula Waswa**

- GitHub: [@Godwin-Waswa](https://github.com/Godwin-Waswa)
- Twitter: [@Godwin_Waswa](https://twitter.com/Godwin_Waswa)
- LinkedIn: [@Godwin-Waswa](https://www.linkedin.com/in/godwin-waswa-170307123/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🔭 Future Features <a name="future-features"></a>

- [ ] **Add multi-role hierarchy (moderator, super-admin)**
- [ ] **Integrate audit logging for all admin actions**
- [ ] **Implement email-based 2FA authentication**

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!

Feel free to check the [issues page](../../issues/).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ⭐️ Show your support <a name="support"></a>

If you like this project, please ⭐ the repo and share it with classmates or teammates!

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🙏 Acknowledgements <a name="acknowledgements"></a>

Special thanks to:

- Supabase Team for free access and documentation

- Lecturer (Data Tools Unit) for guidance

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ❓ FAQ <a name="faq"></a>

- Can I reuse this project for another class?
  Yes, but make sure to credit the original author.

- What happens if RLS is not enabled?
  Without RLS, all users can read/write unrestricted data — a major security risk.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 📝 License <a name="license"></a>

This project is MIT
 licensed.
You may freely reuse and modify with attribution.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
