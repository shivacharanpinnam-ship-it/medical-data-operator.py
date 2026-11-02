# medical-data-operator.py

# Steps 1–4: Import regular expressions
import re

# Steps 5–10: Create medical records
medical_records = [
    {
        'patient_id': 'P1001',
        'age': 34,
        'gender': 'Female',
        'diagnosis': 'Hypertension',
        'medications': ['Lisinopril'],
        'last_visit_id': 'V2301',
    },
    {
        'patient_id': 'p1002',
        'age': 47,
        'gender': 'male',
        'diagnosis': 'Type 2 Diabetes',
        'medications': ['Metformin', 'Insulin'],
        'last_visit_id': 'v2302',
    },
    {
        'patient_id': 'P1003',
        'age': 29,
        'gender': 'female',
        'diagnosis': 'Asthma',
        'medications': ['Albuterol'],
        'last_visit_id': 'v2303',
    },
    {
        'patient_id': 'p1004',
        'age': 56,
        'gender': 'Male',
        'diagnosis': 'Chronic Back Pain',
        'medications': ['Ibuprofen', 'Physical Therapy'],
        'last_visit_id': 'V2304',
    }
]

# Steps 11–20: Create validation function
# Steps 21–44: Find and report invalid records

def find_invalid_records(
    patient_id, age, gender, diagnosis, medications, last_visit_id
):
    # Step 21: Create constraints dictionary
    constraints = {}

    # Validate patient ID
    constraints['patient_id'] = (
        isinstance(patient_id, str)
        and re.fullmatch(r'p\d+', patient_id, re.IGNORECASE)
    )

    # Validate age
    constraints['age'] = isinstance(age, int) and age >= 18

    # Validate gender
    constraints['gender'] = (
        isinstance(gender, str)
        and gender.lower() in ('male', 'female')
    )

    # Validate diagnosis
    constraints['diagnosis'] = (
        isinstance(diagnosis, str) or diagnosis is None
    )

    # Validate medications
    constraints['medications'] = (
        isinstance(medications, list)
        and all(isinstance(i, str) for i in medications)
    )

    # Validate last visit ID
    constraints['last_visit_id'] = (
        isinstance(last_visit_id, str)
        and re.fullmatch(r'v\d+', last_visit_id, re.IGNORECASE)
    )

    # Return invalid field names
    return [
        key for key, value in constraints.items() if not value
    ]


# Create the main validation function
def validate(data):

    # Check whether data is a list or tuple
    is_sequence = isinstance(data, (list, tuple))

    if not is_sequence:
        print('Invalid format: expected a list or tuple.')
        return False

    # Track invalid records
    is_invalid = False

    # Required dictionary keys
    key_set = set([
        'patient_id',
        'age',
        'gender',
        'diagnosis',
        'medications',
        'last_visit_id'
    ])

    # Iterate through records
    for index, dictionary in enumerate(data):

        # Check dictionary format
        if not isinstance(dictionary, dict):
            print(
                f'Invalid format: expected a dictionary at position {index}.'
            )
            is_invalid = True
            continue

        # Check required keys
        if set(dictionary.keys()) != key_set:
            print(
                f'Invalid format: {dictionary} at position {index} '
                'has missing and/or invalid keys.'
            )
            is_invalid = True
            continue

        # Get invalid fields
        invalid_records = find_invalid_records(**dictionary)

        # Steps 42–44: Display invalid records
        for key in invalid_records:
            print(
                f"Unexpected format '{key}: {dictionary[key]}' "
                f"at position {index}."
            )
            is_invalid = True

    # Return validation result
    if is_invalid:
        return False

    print('Valid format.')
    return True


# Run the validation
validate(medical_records)
