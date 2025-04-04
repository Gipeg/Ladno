import xml.etree.ElementTree as ET
import wave
import sqlite3
import os

# 5.1 Работа с XML-файлом
xml_file = 'example.xml'
try:
    if os.path.exists(xml_file):
        tree = ET.parse(xml_file)
        root = tree.getroot()
        for elem in root.iter():
            print(elem.tag, elem.text)

        # Пример модификации
        for child in root:
            if child.text:
                child.text += ' [изменено]'

        tree.write(xml_file, encoding='utf-8', xml_declaration=True)
        print("XML файл успешно обновлён.")
    else:
        print("XML файл не найден. Создаю новый файл.")
        root = ET.Element('root')
        child = ET.SubElement(root, 'item')
        child.text = 'Пример'
        tree = ET.ElementTree(root)
        tree.write(xml_file, encoding='utf-8', xml_declaration=True)
        print("Новый XML файл создан.")
except Exception as e:
    print(f"Ошибка при работе с XML: {e}")

# 5.2 Работа с WAV-файлом
wav_file = 'example.wav'
try:
    if os.path.exists(wav_file):
        with wave.open(wav_file, 'rb') as wf:
            params = wf.getparams()
            frames = wf.readframes(params.nframes)
            print(f"Параметры WAV файла: {params}")

        # Создание нового файла с теми же параметрами
        new_wav = 'new_example.wav'
        with wave.open(new_wav, 'wb') as wf:
            wf.setparams(params)
            wf.writeframes(frames)
        print(f"Создан новый WAV файл: {new_wav}")
    else:
        print("WAV файл не найден. Создание пустого WAV файла.")
        with wave.open(wav_file, 'wb') as wf:
            wf.setnchannels(1)
            wf.setsampwidth(2)
            wf.setframerate(44100)
            wf.writeframes(b'')
        print("Пустой WAV файл создан.")
except Exception as e:
    print(f"Ошибка при работе с WAV: {e}")

# 5.3 Работа с SQLite
try:
    db_name = 'example.db'
    conn = sqlite3.connect(db_name)
    cursor = conn.cursor()

    # Создание таблицы (если нет)
    cursor.execute('''
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT,
        age INTEGER
    )
    ''')

    # Вставка данных
    cursor.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Иван", 30))

    # Чтение данных
    cursor.execute("SELECT * FROM users")
    for row in cursor.fetchall():
        print(row)

    # Обновление данных
    cursor.execute("UPDATE users SET age = ? WHERE name = ?", (31, "Иван"))

    # Сохранение изменений
    conn.commit()
    print("Работа с базой данных завершена успешно.")
except Exception as e:
    print(f"Ошибка при работе с SQLite: {e}")
finally:
    conn.close()
