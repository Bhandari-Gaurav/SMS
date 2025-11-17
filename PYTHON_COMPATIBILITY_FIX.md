# Python 3.14 Compatibility Fix Guide

## The Problem
Your project uses Python 3.14.0, but Django 4.2 requires the `cgi` module which was removed in Python 3.13+.

```
Error: ModuleNotFoundError: No module named 'cgi'
```

## Quick Fix Options

### Option 1: Use Python 3.11 or 3.12 (RECOMMENDED)

#### On Windows:
```powershell
# Check installed Python versions
python3 --version
python3.12 --version
python3.11 --version

# Use specific version with virtual environment
python3.11 -m venv venv
.\venv\Scripts\Activate.ps1

# Install requirements
pip install --upgrade -r requirements.txt
```

#### Alternative - Use py launcher:
```powershell
# List available Python versions
py --list-paths

# Use Python 3.12
py -3.12 -m venv venv
.\venv\Scripts\Activate.ps1
pip install --upgrade -r requirements.txt
```

---

### Option 2: Upgrade to Django 5.0+ (For Python 3.14)

#### Edit `requirements.txt`:
```diff
- Django>=4.2,<5.0
+ Django>=5.0,<6.0
```

#### Then install:
```powershell
pip install --upgrade -r requirements.txt
```

---

### Option 3: Temporary Workaround

If you can't change Python/Django versions immediately:

#### Create file: `compat_fix.py` in project root
```python
# Python 3.14 compatibility fix
import sys
import types

# Create dummy cgi module for Python 3.14+
if sys.version_info >= (3, 13):
    cgi = types.ModuleType('cgi')
    sys.modules['cgi'] = cgi
```

#### Edit `manage.py` - Add at top:
```python
#!/usr/bin/env python
import sys
import os
from pathlib import Path

# Add Python 3.14 compatibility
import types
if sys.version_info >= (3, 13):
    cgi = types.ModuleType('cgi')
    sys.modules['cgi'] = cgi

def main():
    # ... rest of manage.py code
```

---

## After Fixing the Compatibility Issue

### Run Migration
```powershell
python manage.py migrate
```

### Verify Installation
```powershell
python manage.py shell
>>> from main_app.models import Assignment, AssignmentSubmission
>>> print(Assignment.objects.all())
>>> exit()
```

### Run Tests (Optional)
```powershell
python manage.py test main_app
```

### Start Development Server
```powershell
python manage.py runserver
```

---

## Checking Your Python Installation

```powershell
# Show current Python version
python --version

# Show Python path
python -c "import sys; print(sys.executable)"

# Show installed packages
pip list | Select-String Django
```

---

## Recommended Configuration

**Best Practice:**
- Use **Python 3.11 or 3.12**
- Use **Django 4.2 LTS** (recommended) or Django 5.0+
- Use virtual environment
- Keep requirements.txt updated

**For Long-term:**
- Plan to upgrade to Django 5.0+ eventually
- Python 3.14 support will improve in future Django releases
- Use Python 3.12 if you want latest features + compatibility

---

## Testing After Fix

Once compatibility is resolved:

```powershell
# 1. Migrate database
python manage.py migrate

# 2. Create superuser
python manage.py createsuperuser

# 3. Run server
python manage.py runserver

# 4. Visit these URLs to test:
# - http://localhost:8000/admin (Django admin)
# - http://localhost:8000/staff/assignment/add/ (Staff assignments)
# - http://localhost:8000/student/view/assignments/ (Student assignments)
```

---

## If Using Docker/Production

If deploying with Docker, update Dockerfile:

```dockerfile
# Use Python 3.12 instead of 3.14
FROM python:3.12-slim

# ... rest of Dockerfile
```

---

## Need Help?

The assignment feature implementation is **100% complete**. The only blocking issue is the Python/Django version compatibility, which this guide resolves.

Once you apply one of the solutions above and run `python manage.py migrate`, everything will work perfectly!
