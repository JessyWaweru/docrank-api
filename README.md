<div align="center">

# 🩺 DocRank · API

### Find a doctor people actually trust.

**Django REST API behind DocRank, a community-rated directory of specialists in Nairobi, Kisumu and Mombasa.**

[![Live Demo](https://img.shields.io/badge/🔴_Live_Demo-DocRank-0d9488?style=for-the-badge)](https://doctor-search-frontend.onrender.com)
[![Frontend](https://img.shields.io/badge/Frontend_Repo-docrank--web-0f172a?style=for-the-badge&logo=github)](https://github.com/JessyWaweru/docrank-web)

![Python](https://img.shields.io/badge/Python_3.12-3776AB?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django_5.2-092E20?logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-A30000?logo=django&logoColor=white)
![JWT](https://img.shields.io/badge/SimpleJWT-000000?logo=jsonwebtokens&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase_Postgres-3FCF8E?logo=supabase&logoColor=white)
![Resend](https://img.shields.io/badge/Resend-000000?logo=resend&logoColor=white)
![pytest](https://img.shields.io/badge/pytest-0A9EDC?logo=pytest&logoColor=white)

<sub>⏳ Free-tier hosting: the first request may take ~45s while the server wakes up. 🩺 The doctor will see you shortly.</sub>

</div>

---

## 📸 Screenshots

| 🗂️ Doctor directory | 🩺 Doctor profile |
|:---:|:---:|
| ![Listing](screenshots/01_doctor_listing.png) | ![Detail](screenshots/02_doctor_detail_1_desktop.png) |
| ⭐ **Reviews** | 🔖 **Saved doctors** |
| ![Reviews](screenshots/02_doctor_detail_2_desktop.png) | ![Saved](screenshots/04_saved_desktop.png) |

<details>
<summary>📱 <b>Mobile views</b></summary>

| Listing | Profile | Register | Saved |
|:---:|:---:|:---:|:---:|
| ![](screenshots/01_doctor_listing_mobile.png) | ![](screenshots/02_doctor_detail_1_mobile.png) | ![](screenshots/03_register_mobile.png) | ![](screenshots/04_saved_mobile.png) |

</details>

## 🏆 The ranking engine

Every doctor comes back from the API **already annotated** with `average_rating` and `review_count`, computed in a single database query using Django ORM aggregation. There are no N+1 queries, and `?ordering=average_rating` gives you a ranked list straight away.

```
GET /api/doctors/?specialty=Cardiologist&location=Nairobi&ordering=average_rating
```

Reviews use a **1–10 scale** with a database-level `unique(doctor, user)` constraint, so each person gets one honest vote per doctor.

## 🔐 Auth, done properly

```
register ──▶ account created (inactive) ──▶ 📧 6-digit OTP via Resend
   │
verify-email (OTP, expires in 10 min) ──▶ account active ──▶ 🎟️ JWT access + refresh
   │
login (username or email) ──▶ 🎟️ JWT
   │
forgot password ──▶ 📧 OTP ──▶ confirm + new password
```

- Emails go out on a **background thread**, so a slow mail server can't time out a Gunicorn worker
- Password-reset requests reply the same way whether or not the email exists 🤫

## 📡 API

| Method | Endpoint | What it does | Access |
|:---:|---|---|:---:|
| `POST` | `/api/auth/register/` · `verify-email/` · `login/` | Sign up, OTP-verify, log in | 🌍 |
| `POST` | `/api/auth/password-reset/` · `…/confirm/` | OTP password reset | 🌍 |
| `GET` | `/api/doctors/?search=&specialty=&location=&ordering=` | Search, filter & rank | 🌍 |
| `GET` | `/api/doctors/{id}/` | Doctor profile | 🌍 |
| `GET` | `/api/reviews/?doctor_id=` · `?mine=true` | Doctor's reviews · my reviews | 🌍 · 🔐 |
| `POST` `DELETE` | `/api/reviews/…` | Write / delete your review | 🔐 |
| `GET` `POST` | `/api/saved-doctors/` · `toggle/` | Your saved list · save/unsave | 🔐 |

## 🚀 Run it locally

```bash
git clone https://github.com/JessyWaweru/docrank-api.git
cd docrank-api
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py seeds          # 🌱 36 doctors, 20 users, realistic reviews
python manage.py runserver      # → http://localhost:8000/api/
```

| Env var | Purpose |
|---|---|
| `SECRET_KEY` | Django secret |
| `DATABASE_URL` | Supabase Postgres (falls back to SQLite locally) |
| `RESEND_API_KEY` · `DEFAULT_FROM_EMAIL` | OTP emails |
| `ALLOWED_HOSTS` · `CORS_ALLOWED_ORIGINS` | Production hosts / frontend origin |

## 🧪 Tests

```bash
pytest --cov=doctor_search_app --cov-report=term-missing
```

## ☁️ Deployment

Render runs [`build.sh`](build.sh) on every deploy (install → `collectstatic` → `migrate`), then `gunicorn core.wsgi:application`. WhiteNoise serves static files, and Supabase holds the data.

---

<div align="center">

**Built by Jessy Bryan Waweru**: actuary by training, full-stack engineer by obsession.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/jessy-waweru-b8051a288)
[![Email](https://img.shields.io/badge/Email-EA4335?logo=gmail&logoColor=white)](mailto:bryanjessywaweru@gmail.com)
[![GitHub](https://img.shields.io/badge/More_projects-181717?logo=github)](https://github.com/JessyWaweru)

</div>
