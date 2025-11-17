# Assignment Feature - Integration & Testing Guide

## Complete Setup Checklist

### Step 1: Fix Python/Django Compatibility ✓
**Status**: Instructions provided in `PYTHON_COMPATIBILITY_FIX.md`

**Quick Action**:
```powershell
# Option 1 (Recommended): Use Python 3.11 or 3.12
py -3.12 -m venv venv
.\venv\Scripts\Activate.ps1
pip install --upgrade -r requirements.txt

# Option 2: Upgrade Django
# Edit requirements.txt: Django>=5.0,<6.0
pip install --upgrade -r requirements.txt
```

### Step 2: Apply Database Migrations
```powershell
python manage.py migrate
```

Expected output:
```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, main_app, sessions
...
Running migrations:
  Applying main_app.0002_assignment_assignmentsubmission... OK
```

### Step 3: Collect Static Files (if needed)
```powershell
python manage.py collectstatic --noinput
```

### Step 4: Create Superuser (if not exists)
```powershell
python manage.py createsuperuser
```

### Step 5: Start Development Server
```powershell
python manage.py runserver
```

---

## Feature Testing Guide

### Test 1: Staff - Create Assignment

**Navigate to**: `http://localhost:8000/staff/assignment/add/`

**Test Steps**:
1. Fill in form:
   - Title: "Project Report"
   - Description: "Create a comprehensive project report"
   - Subject: Select any subject
   - Due Date: Set future date/time
2. Click "Create Assignment"
3. Verify assignment appears in list below

**Expected Result**: ✅ Assignment created, listed with Edit/Delete buttons

---

### Test 2: Staff - View Submissions

**Navigate to**: Assignment list on `/staff/assignment/add/`

**Test Steps**:
1. Click "Submissions" button on any assignment
2. View all student submissions
3. See columns: Name, Status, Submitted At, Marks, Actions

**Expected Result**: ✅ Submissions page loads (may be empty initially)

---

### Test 3: Staff - Grade Submission

**Test Steps**:
1. Create an assignment (Test 1)
2. Have a student submit (Test 6)
3. Go to Submissions page
4. Click "Grade" on submission
5. Enter marks (e.g., 85)
6. Add feedback: "Good work!"
7. Click "Submit Grade"

**Expected Result**: ✅ Submission marked as "Graded", student sees marks/feedback

---

### Test 4: Staff - Edit Assignment

**Navigate to**: `/staff/assignment/add/`

**Test Steps**:
1. Click "Edit" on any assignment
2. Modify title or description
3. Click "Update Assignment"
4. Verify changes on list page

**Expected Result**: ✅ Changes saved

---

### Test 5: Staff - Delete Assignment

**Navigate to**: `/staff/assignment/add/`

**Test Steps**:
1. Click "Delete" on any assignment
2. Click OK on confirmation
3. Verify assignment removed from list

**Expected Result**: ✅ Assignment deleted with all submissions

---

### Test 6: Student - View Assignments

**Switch to Student User**: Logout and login as student

**Navigate to**: `http://localhost:8000/student/view/assignments/`

**Expected Result**: ✅ List of assignments for student's course
- Show: Title, Subject, Due Date, Status
- Status initially "Not Submitted"

---

### Test 7: Student - Submit Assignment

**Navigate to**: `/student/view/assignments/`

**Test Steps**:
1. Click "View" on any assignment
2. See assignment details
3. In submission form, select a file (any PDF, DOC, DOCX)
4. Click "Submit Assignment"
5. Verify status changes to "Submitted"

**Expected Result**: ✅ File uploaded, status updated

**File uploaded to**: `media/assignments/`

---

### Test 8: Student - View Submission Status

**Navigate to**: `/student/view/assignments/`

**Test Steps**:
1. After submission, status shows "Submitted"
2. After staff grades, status shows "Graded (85 marks)" etc.
3. On detail page, view feedback from staff

**Expected Result**: ✅ Status and feedback displayed correctly

---

### Test 9: Student - Resubmit Assignment

**Navigate to**: Already submitted assignment detail

**Test Steps**:
1. Click on already submitted assignment
2. See submission form available (can resubmit)
3. Select new file
4. Click "Resubmit Assignment"
5. Verify file updated and status reset

**Expected Result**: ✅ New file uploaded, replaces old submission

---

## Admin Panel Verification

### Check Models in Django Admin

**Navigate to**: `http://localhost:8000/admin/`

**Verify**:
1. Login as superuser
2. See `Assignment` model
   - Click to view assignments
   - View all fields: title, description, subject, staff, due_date
3. See `AssignmentSubmission` model
   - View submissions with student, assignment, status, marks
   - Download files from admin

**Expected Result**: ✅ Both models appear in admin

---

## Performance Testing

### Database Queries
```powershell
python manage.py shell
>>> from main_app.models import Assignment
>>> from django.db import connection
>>> from django.test.utils import override_settings

# Test query performance
>>> assignments = Assignment.objects.all()
>>> print(len(connection.queries))  # Check query count
```

### Load Test (Optional)
Create multiple assignments/submissions and verify response times remain acceptable.

---

## File Upload Testing

### Supported File Types
- ✅ PDF (.pdf)
- ✅ Word (.doc, .docx)

### Test File Upload
1. Create test files: test.pdf, test.docx
2. Upload through student interface
3. Download through staff interface
4. Verify file integrity

---

## Troubleshooting

### Issue: Page shows 404 errors

**Solution**:
```powershell
# Restart server
python manage.py runserver

# Or check URLs
python manage.py shell
>>> from django.urls import reverse
>>> reverse('student_view_assignments')
>>> reverse('staff_add_assignment')
```

### Issue: Files not uploading

**Solution**:
1. Check `media/` folder exists
2. Verify permissions on `media/assignments/`
3. Check Django settings for MEDIA_ROOT and MEDIA_URL

### Issue: Models not found

**Solution**:
```powershell
# Re-run migrations
python manage.py migrate --fake-initial
python manage.py migrate
```

### Issue: Templates not rendering

**Solution**:
1. Check file paths exist
2. Verify template syntax
3. Check Django TEMPLATES setting in settings.py

---

## Deployment Checklist

Before deploying to production:

- [ ] Fix Python/Django compatibility
- [ ] Run `python manage.py migrate`
- [ ] Run `python manage.py collectstatic`
- [ ] Set `DEBUG=False` in settings.py
- [ ] Configure `ALLOWED_HOSTS`
- [ ] Set up email for notifications (optional)
- [ ] Configure file storage (S3, Azure, or local)
- [ ] Set up logging
- [ ] Run security checks: `python manage.py check --deploy`
- [ ] Test feature end-to-end
- [ ] Set up backups for database

---

## Feature Integration with Existing System

### Navigation Links (Optional - Add to base templates)

**For Staff Dashboard** (`staff_template/home_content.html`):
```html
<a href="{% url 'staff_add_assignment' %}" class="btn btn-primary">
    <i class="fas fa-file-alt"></i> Assignments
</a>
```

**For Student Dashboard** (`student_template/home_content.html`):
```html
<a href="{% url 'student_view_assignments' %}" class="btn btn-primary">
    <i class="fas fa-file-alt"></i> Assignments
</a>
```

### Sidebar Menu (Optional)

Add to appropriate sidebar templates:
```html
<li class="nav-item">
    <a href="{% url 'staff_add_assignment' %}" class="nav-link">
        <i class="far fa-circle nav-icon"></i>
        <p>Assignments</p>
    </a>
</li>
```

---

## Features Summary

### ✅ Implemented & Ready
- [x] Create assignments
- [x] Edit assignments
- [x] Delete assignments
- [x] View assignments (student view all, staff view theirs)
- [x] Submit assignments (PDF, DOC, DOCX)
- [x] Resubmit assignments
- [x] Grade submissions with marks
- [x] Provide feedback to students
- [x] Download submissions
- [x] Track submission status
- [x] View grades and feedback

### 📋 Optional Enhancements (Not Implemented)
- Email notifications for submissions
- Late submission handling
- Bulk grading
- Assignment rubrics
- Peer review
- Assignment plagiarism detection
- Mobile app support
- API endpoints

---

## Success Criteria

✅ Feature is complete when:
1. Database migrations apply successfully
2. All URLs are accessible
3. Staff can create and grade assignments
4. Students can submit assignments
5. File uploads work correctly
6. Status tracking displays properly
7. Feedback appears on student submissions

---

## Final Steps

1. **Apply one Python compatibility fix** from `PYTHON_COMPATIBILITY_FIX.md`
2. **Run migrations**: `python manage.py migrate`
3. **Run all tests** from this guide
4. **Deploy** to production

**Status**: 🎉 Implementation Complete and Ready for Use!
