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
## Table: people

This table stores the permanent identity of every person.

A person has only one permanent record, even if their name, location, or other information changes.

### Columns

- id: UUID, primary key
- public_code: unique public identifier such as AFR-000001
- gender: male, female, unknown
- life_status: living, deceased, unknown
- birth_date: exact date if known
- birth_year: year if only the year is known
- birth_date_precision: exact, year, approximate, unknown
- death_date: exact date if known
- death_year: year if only the year is known
- death_date_precision: exact, year, approximate, unknown
- original_location_id: family or ancestral village
- birth_location_id: place of birth
- short_biography: short historical description
- record_status: draft, pending, approved, rejected, archived
- default_privacy_level: public, registered, private, hidden
- is_minor: whether the person is currently a minor
- claimed_by_user_id: user who successfully claimed the record
- created_by: user who proposed the record
- approved_by: administrator who approved it
- created_at
- updated_at
- archived_at

### Rules

- public_code never changes.
- A person is never permanently deleted during normal administration.
- Names are not stored directly in this table.
- Parents and spouses are not stored directly in this table.
- Unknown dates remain null and are never invented.
- Living people receive stronger privacy protection.
- Minor records receive the strongest privacy protection.
- ## Table: person_names

A person may have several names in different languages or historical forms.

### Columns

- id: UUID, primary key
- person_id: related person
- given_name
- father_name
- grandfather_name
- family_name
- full_name
- nickname
- language_code: ar, ku, tr, de, en, other
- writing_system: Arabic, Latin, other
- name_type: primary, birth, nickname, historical, alternative, transliteration
- is_primary: true or false
- is_searchable: true or false
- privacy_level: public, registered, private, hidden
- source_status: documented, family_testimony, oral, uncertain
- created_by
- approved_by
- created_at
- updated_at

### Rules

- Every approved person should have at least one primary name.
- Only one primary name is allowed per language.
- Alternative spellings are preserved instead of overwritten.
- Search must include all approved searchable names.
- ## Table: parent_child_relationships

This table stores direct parent-child relationships.

### Columns

- id: UUID, primary key
- parent_id: related parent
- child_id: related child
- parent_role: father, mother, unknown_parent
- relationship_type: biological, adoptive, foster, step, social, unknown
- certainty_level: confirmed, probable, possible, disputed, unknown
- notes
- privacy_level
- record_status: draft, pending, approved, rejected, archived
- created_by
- approved_by
- created_at
- updated_at

### Rules

- A person cannot be their own parent.
- Circular ancestry is not allowed.
- A biological parent relationship must not be duplicated.
- Father and mother may be unknown.
- The system must distinguish biological parents from step-parents.
- Sibling relationships are normally calculated from shared parents.
- ## Table: marriages

Every marriage or long-term spousal union is stored as an independent record.

This supports simultaneous and consecutive marriages.

### Columns

- id: UUID, primary key
- public_code: unique code such as MAR-000001
- person_1_id
- person_2_id
- start_date
- start_year
- start_date_precision: exact, year, approximate, unknown
- end_date
- end_year
- end_date_precision: exact, year, approximate, unknown
- status: current, divorced, separated, widowed, ended, unknown
- end_reason: divorce, death_person_1, death_person_2, separation, unknown
- marriage_location_id
- ceremony_type: civil, religious, customary, unknown
- notes
- privacy_level
- record_status: draft, pending, approved, rejected, archived
- created_by
- approved_by
- created_at
- updated_at

### Rules

- A person may have any number of marriage records.
- The database does not use wife_1, wife_2, or husband_1 fields.
- Marriage order is shown only when supported by dates or sources.
- Two marriages may overlap in time.
- A person cannot be married to themselves.
- Duplicate marriage records should be detected before approval.
- ## Table: marriage_children

This optional table connects a child with a specific marriage or union.

### Columns

- id: UUID, primary key
- marriage_id
- child_id
- certainty_level: confirmed, probable, possible, disputed, unknown
- notes
- record_status
- created_by
- approved_by
- created_at

### Rules

- The parent-child relationships remain the primary proof of parentage.
- This table is used to organize children under the correct marriage.
- A child may remain unlinked to a marriage if the marriage information is unknown.
- ## Family tree validation rules

Before approving a family relationship, the system should check:

- A person cannot be their own father or mother.
- A person cannot be their own child.
- A person cannot become an ancestor of themselves.
- The same parent-child relationship cannot be approved twice.
- The same marriage cannot be approved twice.
- Birth and death dates should be checked for obvious contradictions.
- A parent should normally be older than the child, but historical uncertainty must be supported.
- Conflicting information is marked as disputed rather than silently deleted.
- Relationships involving living people must respect privacy settings.
