from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import QApplication,QWidget,QHBoxLayout,QVBoxLayout, QPushButton, QLabel, QListWidget, QLineEdit, QTextEdit, QInputDialog

import json

app= QApplication([])

notes_win = QWidget()
notes_win.setWindowTitle('Умные заметки')
notes_win.resize(900,600)

list_notes =QListWidget()
list_notes_label= QLabel('Список заметков')

button_create = QPushButton('Создать заметку')
button_del = QPushButton('Удалить заметку')
button_save = QPushButton('Сохранить заметку')

field_tag = QLineEdit('')
field_tag.setPlaceholderText('Введите тег...')

field_text = QTextEdit()

button_tag_add = QPushButton('Добавить тег')
button_tag_delete = QPushButton('Удалить тег')
button_tag_search = QPushButton('Найти тег')

list_tags = QListWidget()
list_tags_label = QLabel('Список тегов')

layout_notes = QHBoxLayout()

col_1 = QVBoxLayout()
col_1.addWidget(field_text)

col_2 = QVBoxLayout()
col_2.addWidget(list_notes_label)
col_2.addWidget(list_notes)

row_1 = QHBoxLayout()
row_1.addWidget(button_create)
row_1.addWidget(button_del)
row_2 = QHBoxLayout()
row_2.addWidget(button_save)

col_2.addLayout(row_1)
col_2.addLayout(row_2)

col_2.addWidget(list_tags_label)
col_2.addWidget(list_tags)
col_2.addWidget(field_tag)
row_3 = QHBoxLayout()
row_3.addWidget(button_tag_add)
row_3.addWidget(button_tag_delete)
row_4 = QHBoxLayout()
row_4.addWidget(button_tag_search)

col_2.addLayout(row_3)
col_2.addLayout(row_4)

layout_notes.addLayout(col_1 , stretch = 2)
layout_notes.addLayout(col_2 , stretch = 1)
notes_win.setLayout(layout_notes)

def show_note():
    key = list_notes.selectedItems()[0].text()
    print(key)
    field_text.setText(notes[key]['текст'])
    list_tags.clear()
    list_tags.addItems(notes[key]['теги'])

list_notes.itemClicked.connect(show_note)

notes_win.show()

with open('notes_data.json','r') as file:
    notes = json.load(file)
list_notes.addItems(notes)

def add_note():
    note_name , ok = QInputDialog.getText(notes_win,'Создать заметку','Название заметки')
    if ok and note_name != '':
        notes[note_name] = {'текст':'','теги':[]}
        list_notes.addItem(note_name)
        list_tags.addItems(notes[note_name]['теги'])
        print(notes)

def save_note():
    if list_notes.selectedItems():
        key = list_notes.selectedItems()[0].text()
        notes[key]['текст'] = field_text.toPlainText()
        with open ('notes_data.json','w') as file:
            json.dump(notes,file)
        print(notes)

def del_note():
    if list_notes.selectedItems():
        key = list_notes.selectedItems()[0].text()
        del notes[key]
        list_notes.clear()
        list_tags.clear()
        field_text.clear()
        list_notes.addItems(notes)
        with open ('notes_data.json','w') as file:
            json.dump(notes,file)
        print(notes)

def add_tag():
    if list_notes.selectedItems():
        key = list_notes.selectedItems()[0].text()
        new_tag = field_tag.text()
        if new_tag in notes[key]['теги']:
            print('Такой тег уже есть')
        else:
            notes[key]['теги'].append(new_tag)
            list_tags.addItem(new_tag)
            field_tag.clear()
            with open ('notes_data.json','w') as file:
                json.dump(notes,file)


        



button_tag_add.clicked.connect(add_tag)
button_create.clicked.connect(add_note)
button_save.clicked.connect(save_note)
button_del.clicked.connect(del_note)


app.exec()
