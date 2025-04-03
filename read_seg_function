# перевод code -> level
from itertools import product

letters = "GBRY"
nums = "1234"
levels = [ch + num for num, ch in product(nums, letters)]
level_codes = [2 ** i for i in range(len(levels))]

code_to_level = {i: j for i, j in zip(level_codes, levels)}
level_to_code = {j: i for i, j in zip(level_codes, levels)}

def read_seg(file_name: str) -> tuple[dict[str, int], list[dict]]:
    """
    Функция считывает параметры и метки из исходного файла  и возвращает их в виде словаря и списка.

    Параметры:
    file_name (str): путь к файлу для чтения.

    Возвращает:
    - parameters: словарь с параметрами
    - labels: список словарей с ключами position, level, name.

    Исключения:
    ValueError: если файл невалиден (не содержит секции [PARAMETERS] или [LABELS],
                или если строки не соответствуют формату, или значение параметров должно быть целым числом)
    """
    parameters = {}
    raw_labels = []

    with open(file_name, 'r', encoding='utf-8-sig') as file:
        lines = file.readlines()

    in_parameters_section = False
    in_labels_section = False

    for line in lines:
        line = line.strip()

        if line == "[PARAMETERS]":
            in_parameters_section = True
            continue

        if in_parameters_section and "=" in line:
            parts = line.split("=")

            if len(parts) != 2:
                raise ValueError(f"Необходим формат ключ-значение")

            key, value = parts[0].strip(), parts[1].strip()

            try:
                parameters[key] = int(value)
            except ValueError:
                raise ValueError(f"Значение для {key} должно быть целым числом")

        if in_parameters_section and line.startswith("["):
            in_parameters_section = False

        # переходим к секции [LABELS]
        if line == "[LABELS]":
            in_labels_section = True
            continue

        if in_labels_section and line:
            raw_labels.append(line.strip())

        if in_labels_section and line.startswith("["):
            in_labels_section = False

    if not parameters or not raw_labels:
        raise ValueError(f"В файле неправильная структура")

    labels = []
    for line in raw_labels:
        pos, level, name = line.split(",", maxsplit=2)
        pos_samples = int(pos)
        level_name = code_to_level.get(int(level), f"code_{level}")
        labels.append({
            "position": pos_samples // parameters["BYTE_PER_SAMPLE"] // parameters["N_CHANNEL"],
            "level": level_name,
            "name": name
        })

    return parameters, labels
