from os import name


OSHA_STANDARDS = {
    "minimum_size_m2": 20,
    "required_equipment": [
        "fire_extinguisher", "fume_hood", "eyewash_station",
        "safety_shower", "first_aid_kit", "chemical_storage_cabinet",
        "msds_folder", "spill_kit"
    ],
    "lab_classes": {
        "A": 90,
        "B": 70,
        "C": 50
    }
}

def get_lab_score(lab_data):
    size_score = min(lab_data['size_m2'] / OSHA_STANDARDS["minimum_size_m2"], 1.5) * 20

    required = [item.lower() for item in OSHA_STANDARDS["required_equipment"]]
    matched_equipment = [item for item in lab_data['equipment'] if item in required]

    if required:
        equipment_score = len(matched_equipment) / len(required) * 80
    else:
        equipment_score = 0

    total_score = min(size_score + equipment_score, 100)
    return round(total_score, 2), matched_equipment

def determine_lab_class(score):
    if score >= OSHA_STANDARDS["lab_classes"]["A"]:
        return "A"
    elif score >= OSHA_STANDARDS["lab_classes"]["B"]:
        return "B"
    elif score >= OSHA_STANDARDS["lab_classes"]["C"]:
        return "C"
    else:
        return "Not Classified"

def missing_items(lab_data):
    required = [item.lower() for item in OSHA_STANDARDS["required_equipment"]]
    return [item for item in required if item not in lab_data["equipment"]]

def main():
    print("Enter lab size in square meters:")
    size = float(input())

    print("Enter equipment list, separated by commas (e.g. fire_extinguisher,fume_hood):")
    equipment_input = input()
    equipment_list = [e.strip().lower() for e in equipment_input.split(',')]

    lab_data = {
        "size_m2": size,
        "equipment": equipment_list
    }

    score, matched = get_lab_score(lab_data)
    lab_class = determine_lab_class(score)
    missing = missing_items(lab_data)

    print("\n--- Lab Safety Evaluation Report ---")
    print(f"Score: {score} / 100")
    print(f"Lab Class: {lab_class}")
    print(f"Matched Equipment: {matched}")
    print(f"Missing Equipment: {missing}")

if name == "main":
    main()
