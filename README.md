import tkinter as tk
from tkinter import messagebox, simpledialog, ttk
import datetime
import json

class PersonalAssistant:
    def __init__(self):
        self.reminders = self.load_data('reminders.json')
        self.todo_list = self.load_data('todo.json')
        self.notes = self.load_data('notes.json')

    def load_data(self, filename):
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            data = []
        return data

    def save_data(self, filename, data):
        with open(filename, 'w') as f:
            json.dump(data, f, indent=4)

    def add_reminder(self, reminder):
        self.reminders.append(reminder)
        self.save_data('reminders.json', self.reminders)

    def list_reminders(self):
        return self.reminders if self.reminders else ["No reminders set."]

    def add_todo_item(self, item):
        self.todo_list.append(item)
        self.save_data('todo.json', self.todo_list)

    def list_todo_items(self):
        return self.todo_list if self.todo_list else ["No to-do items."]

    def add_note(self, note):
        self.notes.append(note)
        self.save_data('notes.json', self.notes)

    def list_notes(self):
        return self.notes if self.notes else ["No notes."]

class AssistantGUI:
    def __init__(self, root):
        self.root = root
        self.root.title("AI-Based Personal Assistant")
        self.assistant = PersonalAssistant()
        self.create_widgets()

    def create_widgets(self):
        # Main Frame
        main_frame = ttk.Frame(self.root, padding="10 10 10 10")
        main_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))

        # Notebook (Tabbed Interface)
        notebook = ttk.Notebook(main_frame)
        notebook.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))

        # Reminders Tab
        reminders_frame = ttk.Frame(notebook, padding="10 10 10 10")
        notebook.add(reminders_frame, text='Reminders')

        self.reminder_listbox = tk.Listbox(reminders_frame, height=10, width=50)
        self.reminder_listbox.grid(row=0, column=0, columnspan=2)
        self.update_listbox(self.reminder_listbox, self.assistant.list_reminders())

        add_reminder_button = ttk.Button(reminders_frame, text="Add Reminder", command=self.add_reminder)
        add_reminder_button.grid(row=1, column=0, padx=5, pady=5)

        list_reminders_button = ttk.Button(reminders_frame, text="List Reminders", command=self.list_reminders)
        list_reminders_button.grid(row=1, column=1, padx=5, pady=5)

        # To-Do Tab
        todo_frame = ttk.Frame(notebook, padding="10 10 10 10")
        notebook.add(todo_frame, text='To-Do List')

        self.todo_listbox = tk.Listbox(todo_frame, height=10, width=50)
        self.todo_listbox.grid(row=0, column=0, columnspan=2)
        self.update_listbox(self.todo_listbox, self.assistant.list_todo_items())

        add_todo_button = ttk.Button(todo_frame, text="Add To-Do Item", command=self.add_todo_item)
        add_todo_button.grid(row=1, column=0, padx=5, pady=5)

        list_todo_button = ttk.Button(todo_frame, text="List To-Do Items", command=self.list_todo_items)
        list_todo_button.grid(row=1, column=1, padx=5, pady=5)

        # Notes Tab
        notes_frame = ttk.Frame(notebook, padding="10 10 10 10")
        notebook.add(notes_frame, text='Notes')

        self.notes_listbox = tk.Listbox(notes_frame, height=10, width=50)
        self.notes_listbox.grid(row=0, column=0, columnspan=2)
        self.update_listbox(self.notes_listbox, self.assistant.list_notes())

        add_note_button = ttk.Button(notes_frame, text="Add Note", command=self.add_note)
        add_note_button.grid(row=1, column=0, padx=5, pady=5)

        list_notes_button = ttk.Button(notes_frame, text="List Notes", command=self.list_notes)
        list_notes_button.grid(row=1, column=1, padx=5, pady=5)

    def update_listbox(self, listbox, items):
        listbox.delete(0, tk.END)
        for item in items:
            listbox.insert(tk.END, item)

    def add_reminder(self):
        reminder_text = simpledialog.askstring("Add Reminder", "Enter your reminder:")
        if reminder_text:
            self.assistant.add_reminder(reminder_text)
            self.update_listbox(self.reminder_listbox, self.assistant.list_reminders())
            messagebox.showinfo("Reminder Added", f"Reminder added: '{reminder_text}'")

    def list_reminders(self):
        reminders = self.assistant.list_reminders()
        self.update_listbox(self.reminder_listbox, reminders)
        messagebox.showinfo("Reminders", "\n".join(reminders))

    def add_todo_item(self):
        todo_item = simpledialog.askstring("Add To-Do Item", "Enter the to-do item:")
        if todo_item:
            self.assistant.add_todo_item(todo_item)
            self.update_listbox(self.todo_listbox, self.assistant.list_todo_items())
            messagebox.showinfo("To-Do Item Added", f"To-Do item added: '{todo_item}'")

    def list_todo_items(self):
        todo_items = self.assistant.list_todo_items()
        self.update_listbox(self.todo_listbox, todo_items)
        messagebox.showinfo("To-Do Items", "\n".join(todo_items))

    def add_note(self):
        note = simpledialog.askstring("Add Note", "Enter your note:")
        if note:
            self.assistant.add_note(note)
            self.update_listbox(self.notes_listbox, self.assistant.list_notes())
            messagebox.showinfo("Note Added", f"Note added: '{note}'")

    def list_notes(self):
        notes = self.assistant.list_notes()
        self.update_listbox(self.notes_listbox, notes)
        messagebox.showinfo("Notes", "\n".join(notes))

if __name__ == "__main__":
    root = tk.Tk()
    app = AssistantGUI(root)
    root.mainloop()
