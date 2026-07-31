# Database Design

## Core principles

- Each person has one permanent record.
- Marriage is a separate entity.
- A person can have multiple marriage records.
- Children are connected independently to both parents.
- Sibling relationships are normally derived from shared parents.
- No historical record is permanently deleted.
- Every approved change is traceable.
- ## Marriage model

Marriage is stored as an independent record rather than a spouse field inside the person record.

This allows:

- Multiple simultaneous or consecutive marriages.
- Unknown marriage order.
- Different children from different marriages.
- Divorce, separation, widowhood, or unknown status.
- Independent sources and confidence levels for every marriage.

### Table: marriages

- id
- marriage_code
- person_1_id
- person_2_id
- start_date
- start_year
- end_date
- end_year
- end_reason
- status
- location_id
- notes
- privacy_level
- verification_status
- created_by
- approved_by
- created_at
- updated_at
