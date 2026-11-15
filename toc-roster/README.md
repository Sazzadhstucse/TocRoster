# TOC Duty Roster

Web application to maintain the **Technology Operations Center (TOC)** daily duty roster:
three shifts per day (Morning 10:00 am-05:00 pm, Evening 02:30 pm-09:30 pm, Night 09:30 pm-10:00 am),
team directory, escalation matrix and contact numbers. November 2026 data from the published PDF is loaded on first start.

## Stack
Java 17, Spring Boot 3.3, Spring MVC + Thymeleaf, Spring Data JPA, Spring Security, H2 (default) / PostgreSQL.

## Run
```bash
mvn spring-boot:run          # http://localhost:8080
mvn test                     # unit/integration tests
mvn clean package && java -jar target/toc-roster-1.0.0.jar
```
PostgreSQL: `java -jar target/toc-roster-1.0.0.jar --spring.profiles.active=postgres`
(env: `DB_URL`, `DB_USER`, `DB_PASSWORD`).

## Access
- **Everyone** (no login): view the monthly roster, "on duty now", team directory, export/print.
- **Admin**: click **Admin login** in the header, sign in with username and password to edit rosters and manage the team.

Default admin: `admin` / `ChangeMe#2026`. Change with `TOC_ADMIN_USER` and `TOC_ADMIN_PASSWORD`.

## Features
- Monthly roster table matching the PDF layout; Friday rows highlighted, today outlined, month navigation.
- "On duty now" panel, aware of the night shift crossing midnight (Asia/Dhaka time zone).
- Admin: edit any day (multi-select per shift), add/edit/deactivate/remove team members.
  Members with roster history are deactivated, never deleted.
- CSV export and print-friendly stylesheet.
- Escalation matrix and contact numbers (`application.properties`).

## Structure
```
domain/      Employee, DutyAssignment, ShiftType, EscalationContact (JPA)
repository/  Spring Data repositories
service/     RosterService (roster rules), EmployeeService
web/         Controllers, form object, exception handler
config/      SecurityConfig, DataSeeder (first-run data)
templates/   Thymeleaf pages     static/css/app.css
```

## Next steps for production
Replace in-memory users with LDAP/SSO or a user table, add Flyway migrations and set
`spring.jpa.hibernate.ddl-auto=validate`, and add a "copy previous month" generator if rosters follow a rotation.
