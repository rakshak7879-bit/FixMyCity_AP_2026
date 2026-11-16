# Fix My City - Database Normalization

## 1. Introduction

Fix My City is a civic issue reporting and
management platform.

It allows citizens to report problems such as
potholes, garbage accumulation, drainage issues
and broken streetlights.

Municipal officers can manage complaints,
update their status and provide resolution
evidence.

The database stores information about users,
complaints, departments, categories, images,
status history, feedback and notifications.

The purpose of normalization is to reduce data
redundancy and prevent insertion, deletion and
update anomalies. 


## 2. Unnormalized Form (UNF)

Initially, all complaint-related information
is stored in a single record.

COMPLAINT_UNF(
    complaint_id,
    title,
    description,
    citizen_details,
    category_details,
    department_details,
    officer_details,
    address,
    images[],
    status_history[],
    supporters[]
)

Problems:
1. Multiple images are stored in one field.
2. Multiple status updates are stored together.
3. Multiple supporters are stored together.
4. User and department details are repeated.
5. This can cause insertion, deletion and
   update anomalies.

   ## 3. First Normal Form (1NF)

The database is converted into 1NF by
removing repeating groups and ensuring
that every attribute contains atomic values.

Images, status history and complaint supporters
are stored as separate records.

All tables contain atomic values and have
defined primary keys.

### 3.1 COMPLAINT_1NF

| complaint_id (PK) | title | citizen_id | citizen_name | category_id | category_name |
|---|---|---|---|---|---|
| C101 | Road pothole | U01 | Rahul | CAT01 | Pothole |
| C102 | Garbage issue | U02 | Aman | CAT02 | Garbage |

### 3.2 COMPLAINT_IMAGE_1NF

| complaint_id (PK, FK) | image_no (PK) | image_url |
|---|---|---|
| C101 | 1 | img1.jpg |
| C101 | 2 | img2.jpg |
| C102 | 1 | img3.jpg |

### 3.3 COMPLAINT_STATUS_HISTORY_1NF

| complaint_id (PK, FK) | history_no (PK) | status |
|---|---|---|
| C101 | 1 | Submitted |
| C101 | 2 | Assigned |
| C101 | 3 | In Progress |

### 3.4 COMPLAINT_SUPPORT_1NF

| complaint_id (PK, FK) | supporter_id (PK) | supporter_name |
|---|---|---|
| C101 | U02 | Aman |
| C101 | U03 | Priya |
| C102 | U02 | Aman |

The composite primary keys are:
- COMPLAINT_IMAGE_1NF: (complaint_id, image_no)
- COMPLAINT_STATUS_HISTORY_1NF: (complaint_id, history_no)
- COMPLAINT_SUPPORT_1NF: (complaint_id, supporter_id)

All repeating groups have been separated into individual records.
Every field now contains a single atomic value.


## 4. Second Normal Form (2NF)


The database satisfies First Normal Form (1NF).

To achieve 2NF, all partial dependencies must be removed.

Consider the COMPLAINT_SUPPORT_1NF table.

Its composite primary key is:

(complaint_id, supporter_id)

The following functional dependencies exist:

- supporter_id → supporter_name
- (complaint_id, supporter_id) → supported_at

Here, supporter_name depends only on supporter_id and not on the entire composite primary key. This is called a partial dependency.

To remove this partial dependency, we decompose the table into the following relations.

### 4.1 USERS_2NF

| user_id (PK) | full_name |
|---|---|
| U02 | Aman |
| U03 | Priya |

### 4.2 COMPLAINT_SUPPORT_2NF

| complaint_id (PK, FK) | supporter_id (PK, FK) | supported_at |
|---|---|---|
| C101 | U02 | 10:30 |
| C101 | U03 | 10:45 |
| C102 | U02 | 11:00 |

Now, user information is stored separately, while the complaint-support relationship stores only attributes that depend on the complete composite key.

The other relations already satisfy 2NF because their non-key attributes depend on their complete candidate keys.

Therefore, the database satisfies Second Normal Form (2NF).


## 5. Third Normal Form (3NF)

The database satisfies 2NF.

Transitive dependencies are removed by
creating separate tables for users,
departments, categories and officers.

The complaint table stores their IDs
as foreign keys instead of repeating
their descriptive information.

Every non-key attribute depends on the key,
the whole key and nothing but the key.



## 6. Functional Dependencies

USERS:
user_id → full_name, email, phone, role

DEPARTMENTS:
department_id → department_name

CATEGORIES:
category_id → category_name

COMPLAINTS:
complaint_id → title, description, citizen_id,
category_id, department_id, address, created_at

COMPLAINT_IMAGES:
(complaint_id, image_no) → image_url, uploaded_at

COMPLAINT_STATUS_HISTORY:
(complaint_id, history_no) → changed_by,
old_status, new_status, changed_at

COMPLAINT_SUPPORTS:
(complaint_id, supporter_id) → supported_at

FEEDBACK:
feedback_id → complaint_id, rating, comment

NOTIFICATIONS:
notification_id → user_id, message, is_read

ESCALATIONS:
escalation_id → complaint_id, reason, level



## 7. Boyce-Codd Normal Form (BCNF)

BCNF requires every determinant in a
non-trivial functional dependency to be
a superkey.

In the proposed database design, primary
keys and declared unique candidate keys
determine their respective attributes.

Therefore, the final relations satisfy BCNF
under the stated functional dependencies.


## 8. Conclusion

The Fix My City database was normalized
from UNF to 1NF, 2NF and 3NF.

Repeating groups were removed, partial
dependencies were eliminated, and
transitive dependencies were separated.

The final database design reduces
redundancy, prevents data anomalies
and maintains data consistency.

It provides a structured foundation for
the Fix My City application.  