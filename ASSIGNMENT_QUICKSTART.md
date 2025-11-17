# Assignment Feature - Quick Start Guide

## Setup Instructions

### 1. Apply Database Migration
```bash
python manage.py migrate
```

This creates the `Assignment` and `AssignmentSubmission` tables in your database.

### 2. Verify Installation
- Check that `Assignment` and `AssignmentSubmission` models appear in Django admin
- Verify no import errors when running Django

## How Staff Members Use This Feature

### Step 1: Create an Assignment
1. Log in as a staff member
2. Navigate to `/staff/assignment/add/` (or click link from staff dashboard)
3. Fill in the form:
   - **Title**: Name of the assignment (e.g., "Project Report")
   - **Description**: Detailed instructions
   - **Subject**: Select the course subject
   - **Due Date**: Set deadline with date and time
4. Click "Create Assignment"

### Step 2: View Student Submissions
1. Go to `/staff/assignment/add/` to see your assignments
2. Click "Submissions" button next to an assignment
3. View all student submissions with:
   - Student name
   - Submission status
   - Submission date/time
   - Download button for file

### Step 3: Grade Submissions
1. On the submissions page, click "Grade" button for any submission
2. Enter:
   - **Marks**: Student's score
   - **Feedback**: Comments for the student
3. Click "Submit Grade"
4. Status automatically changes to "Graded"

### Step 4: Manage Assignments
- **Edit**: Click "Edit" to modify assignment details
- **Delete**: Click "Delete" to remove assignment (includes all submissions)

---

## How Students Use This Feature

### Step 1: View Assignments
1. Log in as a student
2. Navigate to `/student/view/assignments/` (or click link from student dashboard)
3. See all assignments for your course with:
   - Assignment title
   - Subject name
   - Due date
   - Current submission status

### Step 2: Submit Assignment
1. Click on any assignment to view full details
2. Read the assignment description and requirements
3. If not submitted: Use the file upload form
4. Select a file (PDF, DOC, or DOCX)
5. Click "Submit Assignment"

### Step 3: Check Submission Status
- **Not Submitted**: No file uploaded yet
- **Submitted**: Your file is pending review
- **Graded (X marks)**: Staff has graded and given score

### Step 4: View Feedback
1. After staff grades your submission, go back to the assignment
2. View:
   - Your marks/score
   - Detailed feedback from staff
   - Your submitted file

### Step 5: Resubmit
- After submission, you can still resubmit a new file
- Click the form in the "Resubmit Assignment" section
- Select new file and click "Resubmit"

---

## URL Reference

### For Staff:
| Feature | URL |
|---------|-----|
| Create/List Assignments | `/staff/assignment/add/` |
| Edit Assignment | `/staff/assignment/edit/<id>/` |
| Delete Assignment | `/staff/assignment/delete/<id>/` |
| View Submissions | `/staff/assignment/submissions/<id>/` |
| Grade Submission | `/staff/submission/grade/<id>/` |

### For Students:
| Feature | URL |
|---------|-----|
| View Assignments | `/student/view/assignments/` |
| Assignment Details | `/student/assignment/<id>/` |

---

## File Upload Details

- **Accepted Formats**: PDF, DOC, DOCX
- **Storage Location**: `media/assignments/`
- **Max File Size**: Django default (usually 2.5 MB, configurable)

To change accepted formats, edit `AssignmentSubmissionForm` in `forms.py`:
```python
class AssignmentSubmissionForm(FormSettings):
    class Meta:
        model = AssignmentSubmission
        fields = ['submission_file']
        widgets = {
            'submission_file': forms.FileInput(attrs={'accept': '.pdf,.doc,.docx'}),  # Edit here
        }
```

---

## Features Summary

### ✅ Staff Capabilities:
- Create assignments for specific subjects
- Set assignment deadlines
- Edit assignment details
- Delete assignments
- View all student submissions
- Download submitted files
- Grade submissions with marks
- Provide feedback to students

### ✅ Student Capabilities:
- View assignments for their course
- Read assignment requirements
- Submit work as file attachments
- Resubmit assignments
- Check submission status
- View grades and feedback
- Download their own submissions

### ✅ Automatic Features:
- Timestamp tracking (creation, update, submission)
- Submission status tracking (pending/submitted/graded)
- Easy file management with automatic organization
- Role-based access (staff/student only see relevant features)

---

## Troubleshooting

### Issue: Files not uploading
**Solution**: Check that `media/` folder exists in project root and is writable

### Issue: "Assignment not found"
**Solution**: Verify the assignment ID in URL and that you have permission to view it

### Issue: Can't see assignments as student
**Solution**: Make sure your course is set and assignments exist for your course

### Issue: Migration fails
**Solution**: 
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Support

For more details, see `ASSIGNMENT_FEATURE.md` for complete technical documentation.
