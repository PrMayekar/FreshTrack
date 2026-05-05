# FreshTrack — Food Waste Reduction System

FreshTrack is a full-stack web application built using Django (Python) and MySQL, with a custom HTML/CSS/JavaScript frontend. It helps households manage groceries, reduce food waste, and get recipe recommendations based on available ingredients.

---

## Demo Preview

### Dashboard
<img src="images/Dashboard.png" width="800"/>

### Pantry
<img src="images/Pantry.png" width="800"/>

### Add Groceries
<img src="images/Add Groceries.png" width="800"/>

### Recipe Suggestions
<img src="images/Recipes.png" width="800"/>

### Daily Tracker
<img src="images/Daily Tracker.png" width="800"/>

### Nutrient Tracker
<img src="images/Nutrients Tracker.png" width="800"/>

---

## Quick Start

### Requirements
- Python 3.10+
- MySQL 8.0+ (optional, SQLite works for development)
- pip

### Installation

```bash
cd freshtrack_project

pip install django djangorestframework django-cors-headers mysqlclient

python manage.py migrate

python manage.py loaddata inventory/fixtures/categories.json

Get-Content seed_recipes.py | python manage.py shell  

python manage.py runserver

## 🗄️ MySQL Setup

```sql
CREATE DATABASE freshtrack_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'freshtrack'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON freshtrack_db.* TO 'freshtrack'@'localhost';
FLUSH PRIVILEGES;
```

Then update `settings.py`:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'freshtrack_db',
        'USER': 'freshtrack',
        'PASSWORD': 'your_password',
        'HOST': 'localhost',
        'PORT': '3306',
        'OPTIONS': {'charset': 'utf8mb4'},
    }
}
```

``` API KEY - Add your key in .env ```


## 🧠 Recommendation Engine (recipes/engine.py)

The engine scores recipes using:

**Score = Base Match + Freshness Bonus + Optional Bonus**

- **Base Match**: `matched_required / total_required` ingredients
- **Freshness Bonus**: +0.15 per near-expiry ingredient used
- **Optional Bonus**: up to 0.10 for optional ingredient matches

**Classification**:
-  **Optimal**: Score ≥ 0.8 AND no missing required ingredients
-  **Partial**: Score ≥ 0.5 OR ≤ 2 missing ingredients  
-  **Low**: Everything else

**Recipe Categories**:
-  **Use Now**: Recipes that use near-expiry items (prioritised)
-  **Right Now**: Time-of-day appropriate (breakfast/lunch/dinner/snack)
-  **Healthy**: Tagged with health benefits
-  **Weekend**: Weekend special recipes (Sat/Sun only)
-  **All Matches**: All recipes with score ≥ 0.3

##  Database Schema

- **FoodItem**: name, category, quantity, expiry_date, storage_location, freshness_status
- **Recipe**: title, meal_type, health_tags, is_weekend_special, instructions
- **RecipeIngredient**: ingredient name, quantity, is_optional, substitutes (JSON)
- **UserProfile**: waste stats, dietary preferences, streak tracking
- **DailyLog**: daily usage/waste tracking per user
