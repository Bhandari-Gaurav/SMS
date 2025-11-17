# Assignment Feature Implementation Summary

## Overview
The assignment feature has been successfully implemented for the Student Management System (SMS). This feature allows:
- **Staff/Instructors**: Create assignments, view student submissions, and grade them
- **Students**: View assignments from their courses, submit work as files, and view grades/feedback

## Components Added

### 1. Database Models (`main_app/models.py`)

#### Assignment Model
- **Fields:**
  - `title` (CharField): Name of the assignment
  - `description` (TextField): Assignment details
  - `subject` (ForeignKey): Related subject
  - `staff` (ForeignKey): Staff member creating the assignment
  - `due_date` (DateTimeField): Deadline for submission
  - `created_at`, `updated_at` (DateTimeField): Timestamps
- **Meta:** Ordered by `-created_at`

#### AssignmentSubmission Model
- **Fields:**
  - `assignment` (ForeignKey): Related assignment
  - `student` (ForeignKey): Student submitting
  - `submission_file` (FileField): Uploaded file (accepts PDF, DOC, DOCX)
  - `status` (CharField): pending/submitted/graded
  - `marks` (FloatField): Grade/score (nullable)
  - `feedback` (TextField): Staff feedback (nullable)
  - `submitted_at` (DateTimeField): Submission timestamp
  - `created_at`, `updated_at` (DateTimeField): Timestamps
- **Meta:** Ordered by `-created_at`

### 2. Forms (`main_app/forms.py`)

#### AssignmentForm
- Creates/edits assignments with fields: title, description, subject, due_date
- DateTime widget for due_date selection

#### AssignmentSubmissionForm
- Allows students to upload submission files
- File input with accepted formats: .pdf, .doc, .docx

#### AssignmentGradeForm
- For staff to grade submissions
- Fields: marks (score), feedback (comments)

### 3. Staff Views (`main_app/staff_views.py`)

#### staff_add_assignment(request)
- Display assignment creation form
- List all staff's existing assignments
- URL: `/staff/assignment/add/`

#### staff_edit_assignment(request, assignment_id)
- Edit existing assignment
- URL: `/staff/assignment/edit/<assignment_id>/`

#### staff_delete_assignment(request, assignment_id)
- Delete assignment
- URL: `/staff/assignment/delete/<assignment_id>/`

#### staff_view_assignment_submissions(request, assignment_id)
- View all submissions for an assignment
- Shows student info, submission status, and file downloads
- URL: `/staff/assignment/submissions/<assignment_id>/`

#### staff_grade_submission(request, submission_id)
- Grade individual student submission
- Add marks and feedback
- URL: `/staff/submission/grade/<submission_id>/`

### 4. Student Views (`main_app/student_views.py`)

#### student_view_assignments(request)
- List all assignments for student's course
- Shows assignment title, subject, due date, and submission status
- URL: `/student/view/assignments/`

#### student_view_assignment_detail(request, assignment_id)
- View full assignment details
- Display assignment description and due date
- If not submitted: Show upload form
- If submitted: Show submission status and allow resubmission
- Show grades and feedback if graded
- URL: `/student/assignment/<assignment_id>/`

### 5. URLs (`main_app/urls.py`)

**Staff Endpoints:**
- `path("staff/assignment/add/", staff_views.staff_add_assignment, name='staff_add_assignment')`
- `path("staff/assignment/edit/<int:assignment_id>/", staff_views.staff_edit_assignment, name='staff_edit_assignment')`
- `path("staff/assignment/delete/<int:assignment_id>/", staff_views.staff_delete_assignment, name='staff_delete_assignment')`
- `path("staff/assignment/submissions/<int:assignment_id>/", staff_views.staff_view_assignment_submissions, name='staff_view_assignment_submissions')`
- `path("staff/submission/grade/<int:submission_id>/", staff_views.staff_grade_submission, name='staff_grade_submission')`

**Student Endpoints:**
- `path('student/view/assignments/', student_views.student_view_assignments, name='student_view_assignments')`
- `path('student/assignment/<int:assignment_id>/', student_views.student_view_assignment_detail, name='student_view_assignment_detail')`

### 6. Templates

#### Staff Templates (`main_app/templates/staff_template/`)

**staff_add_assignment.html**
- Assignment creation form
- Table listing all assignments with actions (View Submissions, Edit, Delete)

**staff_edit_assignment.html**
- Assignment edit form

**staff_view_submissions.html**
- Display assignment details
- Table with student submissions showing:
  - Student name
  - Submission status
  - Submission timestamp
  - Marks
  - Download and Grade buttons

**staff_grade_submission.html**
- Form to grade a submission
- Fields for marks and feedback

#### Student Templates (`main_app/templates/student_template/`)

**student_view_assignments.html**
- List all assignments for student's course
- Table with columns:
  - Assignment title
  - Subject
  - Due date
  - Submission status
  - View action button

**student_assignment_detail.html**
- Full assignment details display
- If not submitted: File upload form
- If submitted: Shows current submission with status, file, and resubmission option
- If graded: Shows marks and feedback

### 7. Database Migration

**Migration File:** `main_app/migrations/0002_assignment_assignmentsubmission.py`
- Creates Assignment and AssignmentSubmission tables
- Sets up all foreign key relationships
- Configures default values and constraints

## Features

### For Staff:
1. ✅ Create assignments with title, description, subject, and due date
2. ✅ Edit existing assignments
3. ✅ Delete assignments
4. ✅ View all student submissions for an assignment
5. ✅ Download submitted files
6. ✅ Grade submissions with marks and feedback
7. ✅ Track submission status (pending/submitted/graded)

### For Students:
1. ✅ View all assignments for their course
2. ✅ View full assignment details and requirements
3. ✅ Submit assignments as file uploads (PDF, DOC, DOCX)
4. ✅ Track submission status
5. ✅ Resubmit assignments
6. ✅ View grades and feedback from staff
7. ✅ Track due dates

## File Upload Management
- Files uploaded to: `media/assignments/`
- Supported formats: PDF, DOC, DOCX (configurable in form)
- Each submission stores original file with timestamp

## Status Workflow
1. **pending**: Assignment created, student hasn't submitted yet
2. **submitted**: Student uploaded file
3. **graded**: Staff added marks and feedback

## How to Apply Migration

Run the following command in your Django project directory:
```bash
python manage.py migrate
```

## Notes
- Assignments are tied to subjects and courses
- Students automatically see assignments for their enrolled course
- Staff can only manage their own assignments
- File handling supports typical document formats
- Timestamps track creation and updates for all records
