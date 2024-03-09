
from PyQt5.QtWidgets import QApplication
from PyQt5.QtWidgets import QMainWindow, QInputDialog,QWidget,QMessageBox
from ui import Ui_MainWindow
import json


notes = {
    'Ласкаво просимо!':{
        'текст':'Привіт Це моя крута програмка!',
        'теги':['привітання','старт']
    },
    'Програмування!':{
        'текст':'Ми працюємо на мов Python!',
        'теги':['Python','Logika']
    }

}

with open('data.json',"w",encoding="utf-8")as file:
    json.dump(notes,file,sort_keys=True)

class Widget(QMainWindow):
    def __init__(self):
        super().__init__()
        self.ui = Ui_MainWindow()
        self.ui.setupUi(self)
        self.ui.list_notes.itemClicked.connect(self.show_note)
        self.ui.btn_create_note.clicked.connect(self.add_note)
        self.ui.btn_del_note.clicked.connect(self.del_note)
        self.ui.btn_safe_note.clicked.connect(self.safe_note)
        self.ui.btn_add_tag.clicked.connect(self.add_tag)
        self.ui.btn_del_tag.clicked.connect(self.del_tag)
        self.ui.btn_search.clicked.connect(self.search_tag)


    def show_note(self):
        key = self.ui.list_notes.selectedItems()[0].text()
        self.ui.text.setText(notes[key]["текст"])
        self.ui.list_tags.clear()
        self.ui.list_tags.addItems(notes[key]['теги'])
    def add_note(self):
        name,ok=QInputDialog.getText(self,"додати замітку","Назва замітки")
        if ok == True and name != "":
            notes[name]={"текст":"", "теги":[]}
            self.ui.list_notes.addItem(name)
    def del_note(self):
        if self.ui.list_notes.selectedItems():
            key = self.ui.list_notes.selectedItems()[0].text()
            del notes[key]
            self.ui.list_notes.clear()
            self.ui.text.clear()
            self.ui.list_tags.clear()
            self.ui.list_notes.addItems(notes)
            with open("data.json","w", encoding="UTF-8")as file:
                json.dump(notes,file)

        else:
            win=QMessageBox()
            win.setText("Замітка для видалення не вибрана!")
            win.exec()


    def safe_note(self):
        if self.ui.list_notes.selectedItems():
            key = self.ui.list_notes.selectedItems()[0].text()
            notes[key]['текст'] = self.ui.text.toPlainText()
            with open("data.json","w", encoding="UTF-8")as file:
                json.dump(notes,file)

        else:
            win=QMessageBox()
            win.setText("Замітка для збереження не вибрана!")
            win.exec()
    def add_tag(self):
        if self.ui.list_notes.selectedItems():
            key = self.ui.list_notes.selectedItems()[0].text()
            tag = self.ui.line_search.text()
            if tag not in notes[key]["теги"]:
                notes[key]["теги"].append(tag)
                self.ui.line_search.clear()
                self.ui.list_tags.addItem(tag)
                notes[key]['текст'] = self.ui.text.toPlainText()
                with open("data.json","w", encoding="UTF-8")as file:
                    json.dump(notes,file)

        else:
            win=QMessageBox()
            win.setText("Замітка для додавання тегу не вибрана не вибрана!")
            win.exec()
    def del_tag(self):
        if self.ui.list_notes.selectedItems():
            key = self.ui.list_notes.selectedItems()[0].text()
            tag = self.ui.list_tags.selectedItems()[0].text()
            notes[key]["теги"].remove(tag)
            self.ui.list_tags.clear()
            self.ui.list_tags.addItems(notes[key]["теги"]) 
            with open("data.json","w", encoding="UTF-8")as file:
                    json.dump(notes,file)
        else:
            win=QMessageBox()
            win.setText("Замітка для видалення тегу не вибрана не вибрана!")
            win.exec()
    def search_tag(self):
        if self.ui.line_search.text() != "":
            if self.ui.btn_search.text() == "Шукати замітки по тегу":
                notes_filter = {}
                tag = self.ui.line_search.text()
                for note in notes:
                    if tag in notes[note]["теги"]:
                        notes_filter[note]=notes[note]
                self.ui.list_notes.clear()
                self.ui.list_tags.clear()
                self.ui.text.clear()
                self.ui.list_notes.addItems(notes_filter)
                self.ui.btn_search.setText("Очистити")
            elif self.ui.btn_search.text =="Очитити":
                self.ui.list_notes.clear()
                self.ui.list_tags.clear()
                self.ui.text.clear()
                self.ui.list_notes.addItems(notes)
                self.ui.line_search.clear()
                self.ui.btn_search.setText("Шукати замітки по тегу")










app = QApplication([])
ex = Widget()
ex.show()

with open("data.json",'r',encoding="utf-8")as file:
    notes = json.load(file)
    ex.ui.list_notes.addItems(notes)
app.exec_()
