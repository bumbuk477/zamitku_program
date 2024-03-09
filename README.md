from PyQt5.QtWidgets import QApplication
from PyQt5.QtWidgets import QMainWindow
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


    def show_note(self):
        key = self.ui.list_notes.selectedItems()[0].text()
        self.ui.text.setText(notes[key]["текст"])
        self.ui.list_tags.clear()
        self.ui.list_tags.addItems(notes[key]['теги'])

app= QApplication([])
ex = Widget()
ex.show()

with open("data.json",'r',encoding="utf-8")as file:
    notes = json.load(file)
    ex.ui.list_notes.addItems(notes)
app.exec_()
