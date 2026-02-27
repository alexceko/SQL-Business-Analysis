# SQL Business Analysis

Academic database project with University of Galway demonstrating SQL skills for business analysis applications.

## Overview
These queries were developed using MySQL database to demonstrate business analysis capabilities. The focus is on answering real resource allocation and operational questions using SQL.

## Database Schema
- **Employee**: Staff information including salary, department, and manager relationships
- **Department**: Organisational structure with department managers
- **Project**: Company projects managed by departments
- **Works_on**: Employee project assignments with hours tracked

## Business Questions Answered

### 1. Employee Workload Analysis
**Question:** Which employees are over/under worked?

**SQL Query:**
```sql
SELECT 
    e.fname, 
    e.lname, 
    e.dno,
    SUM(w.hours) as total_hours,
    COUNT(w.pno) as num_projects
FROM employee e
JOIN works_on w ON e.ssn = w.essn
GROUP BY e.ssn, e.fname, e.lname, e.dno
ORDER BY total_hours DESC;
```

**Business Value:** This helps identify if anyone's overworked/if we have capacity for new projects. This would support workload balancing decisions

---

### 2. Project Resource Allocation
**Question:** Are projects adequately staffed?

**SQL Query:**
```sql
SELECT 
    p.pname,
    COUNT(w.essn) as num_employees,
    SUM(w.hours) as total_hours,
    AVG(w.hours) as avg_hours_per_employee
FROM project p
JOIN works_on w ON p.pnumber = w.pno
GROUP BY p.pnumber, p.pname
ORDER BY total_hours DESC;
```

**Business Value:** Shows which projects are under-staffed. For example, if one project has 26 hours per employee while another has only 8, we can reallocate resources.

---

### 3. Department Summary Metrics
**Question:** How do departments compare in size and compensation?

**SQL Query:**
```sql
SELECT 
    d.dname,
    COUNT(e.ssn) as employee_count,
    AVG(e.salary) as avg_salary
FROM department d
LEFT JOIN employee e ON d.dnumber = e.dno
GROUP BY d.dnumber, d.dname
ORDER BY employee_count DESC;
```

**Business Value:** Useful for budget planning and understanding organisational structure. Also helps identify if departments are sized appropriately for their workload.

---

### 4. Compensation Analysis
**Question:** Which employees are above their departments average salary?

**SQL Query:**
```sql
SELECT 
    e.fname,
    e.lname,
    e.salary,
    d.dname as department,
    (SELECT AVG(salary) FROM employee WHERE dno = e.dno) as dept_avg_salary
FROM employee e
JOIN department d ON e.dno = d.dnumber
WHERE e.salary > (SELECT AVG(salary) FROM employee WHERE dno = e.dno)
ORDER BY e.salary DESC;
```

**Business Value:** Identifies high performers and supports compensation planning.

---

### 5. Manager Team Analysis
**Question:** What is the size of each managers team?

**SQL Query:**
```sql
SELECT 
    e.fname as manager_fname,
    e.lname as manager_lname,
    d.dname as department,
    COUNT(emp.ssn) as team_size
FROM employee e
JOIN department d ON e.ssn = d.mgrssn
LEFT JOIN employee emp ON d.dnumber = emp.dno
GROUP BY e.ssn, e.fname, e.lname, d.dname
ORDER BY team_size DESC;
```

**Business Value:** Ensures managers have appropriate team sizes, useful for resource allocation.

---

### 6. Unassigned Resources
**Question:** Which employees have no active project assignments?

**SQL Query:**
```sql
SELECT 
    e.fname,
    e.lname,
    e.dno,
    d.dname
FROM employee e
LEFT JOIN works_on w ON e.ssn = w.essn
JOIN department d ON e.dno = d.dnumber
WHERE w.essn IS NULL;
```

**Business Value:** This query would identify available resources for new projects. The empty result indicates full resource utilisation.

---

## Notes

**Challenges:**
- Initially struggled with the LEFT JOIN syntax for finding unassigned employees (query 6)
- The subquery in query 4 (salary comparison) took a few attempts to get the logic right

**Key takeaways:**
- LEFT JOINs are powerful for finding null results
- Grouping is essential for large datasets
- Writing queries with business questions in mind makes the analysis more valuable and clear

---

## Skills Demonstrated
- Multi-table JOINs (INNER, LEFT)
- Aggregate functions (COUNT, SUM, AVG)
- GROUP BY clauses
- NULL handling and filtering
- ORDER BY for prioritising results
- Business focused data analysis

## Tools Used
- MySQL database via University of Galway server
- Adminer
- Academic database schema (employee/project/department)
