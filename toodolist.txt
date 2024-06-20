import tkinter as tk
from tkinter import messagebox, font

class ToDoList:
    def _init_(self, root):
        self.tasks = []
        self.root = root
        self.root.configure(bg="#e0f7fa")

        # Custom fonts
        self.title_font = font.Font(family="Arial", size=18, weight="bold")
        self.label_font = font.Font(family="Arial", size=12)
        self.entry_font = font.Font(family="Arial", size=12)
        self.button_font = font.Font(family="Arial", size=12, weight="bold")
        self.listbox_font = font.Font(family="Arial", size=12)

        # Header label
        self.header = tk.Label(self.root, text="To-Do List", font=self.title_font, bg="#00796b", fg="white")
        self.header.grid(row=0, column=0, columnspan=3, pady=20, padx=10, sticky="we")

        # Task name entry
        self.task_name_label = tk.Label(self.root, text="Task Name:", font=self.label_font, bg="#e0f7fa")
        self.task_name_label.grid(row=1, column=0, padx=10, pady=5, sticky="w")
        self.entry = tk.Entry(self.root, font=self.entry_font, width=40)
        self.entry.grid(row=1, column=1, padx=10, pady=5, sticky="we", columnspan=2)

        # Priority selection
        self.priority_label = tk.Label(self.root, text="Priority:", font=self.label_font, bg="#e0f7fa")
        self.priority_label.grid(row=2, column=0, padx=10, pady=5, sticky="w")
        self.priority = tk.StringVar(value="Medium")
        self.priority_entry = tk.OptionMenu(self.root, self.priority, "High", "Medium", "Low")
        self.priority_entry.config(font=self.entry_font, bg="#ffffff")
        self.priority_entry.grid(row=2, column=1, padx=10, pady=5, sticky="we")

        # Category entry
        self.category_label = tk.Label(self.root, text="Category:", font=self.label_font, bg="#e0f7fa")
        self.category_label.grid(row=3, column=0, padx=10, pady=5, sticky="w")
        self.category_entry = tk.Entry(self.root, font=self.entry_font, width=40)
        self.category_entry.grid(row=3, column=1, padx=10, pady=5, sticky="we", columnspan=2)

        # Due date entry
        self.due_date_label = tk.Label(self.root, text="Due Date:", font=self.label_font, bg="#e0f7fa")
        self.due_date_label.grid(row=4, column=0, padx=10, pady=5, sticky="w")
        self.due_date_entry = tk.Entry(self.root, font=self.entry_font, width=40)
        self.due_date_entry.grid(row=4, column=1, padx=10, pady=5, sticky="we", columnspan=2)

        # Buttons
        self.addButton = tk.Button(self.root, text="Add Task", command=self.add_task, bg="#4CAF50", fg="white", font=self.button_font, width=20)
        self.addButton.grid(row=5, column=0, padx=10, pady=10, sticky="we", columnspan=3)

        self.delButton = tk.Button(self.root, text="Delete Task", command=self.delete_task, bg="#f44336", fg="white", font=self.button_font, width=20)
        self.delButton.grid(row=6, column=0, padx=10, pady=10, sticky="we", columnspan=3)

        self.markButton = tk.Button(self.root, text="Mark as Done", command=self.mark_task_done, bg="#FFC107", fg="white", font=self.button_font, width=20)
        self.markButton.grid(row=7, column=0, padx=10, pady=10, sticky="we", columnspan=3)

        self.saveButton = tk.Button(self.root, text="Save Tasks", command=self.save_tasks, bg="#2196F3", fg="white", font=self.button_font, width=20)
        self.saveButton.grid(row=8, column=0, padx=10, pady=10, sticky="we", columnspan=3)

        self.loadButton = tk.Button(self.root, text="Load Tasks", command=self.load_tasks, bg="#9C27B0", fg="white", font=self.button_font, width=20)
        self.loadButton.grid(row=9, column=0, padx=10, pady=10, sticky="we", columnspan=3)

        # Task list
        self.listbox = tk.Listbox(self.root, bg="#ffffff", font=self.listbox_font, height=20, width=55)
        self.listbox.grid(row=10, column=0, padx=10, pady=10, columnspan=3)

        # Configure grid weights to allow resizing
        self.root.grid_columnconfigure(1, weight=1)
        self.root.grid_columnconfigure(2, weight=1)
        self.root.grid_rowconfigure(10, weight=1)

    def add_task(self):
        task_name = self.entry.get()
        task_priority = self.priority.get()
        task_category = self.category_entry.get()
        task_due_date = self.due_date_entry.get()
        if task_name != "":
            self.tasks.append({"name": task_name, "priority": task_priority, "category": task_category, "due_date": task_due_date, "done": False})
            self.listbox.insert(tk.END, task_name)
            self.entry.delete(0, tk.END)
            self.category_entry.delete(0, tk.END)
            self.due_date_entry.delete(0, tk.END)
        else:
            messagebox.showwarning("Input Error", "Please enter a task name.")

    def delete_task(self):
        try:
            task_index = self.listbox.curselection()[0]
            self.listbox.delete(task_index)
            self.tasks.pop(task_index)
        except IndexError:
            messagebox.showwarning("Selection Error", "Please select a task to delete.")

    def mark_task_done(self):
        try:
            task_index = self.listbox.curselection()[0]
            self.tasks[task_index]["done"] = True
            self.listbox.delete(task_index)
            self.listbox.insert(tk.END, self.tasks[task_index]["name"] + " ✔")
        except IndexError:
            messagebox.showwarning("Selection Error", "Please select a task to mark as done.")

    def save_tasks(self):
        with open("tasks.txt", "w") as f:
            for task in self.tasks:
                f.write(f"{task['name']} - Priority: {task['priority']} - Category: {task['category']} - Due Date: {task['due_date']} - Done: {task['done']}\n")

    def load_tasks(self):
        try:
            with open("tasks.txt", "r") as f:
                self.tasks = []
                self.listbox.delete(0, tk.END)
                for line in f.readlines():
                    task_details = line.strip().split(" - ")
                    task = {
                        "name": task_details[0],
                        "priority": task_details[1].split(": ")[1],
                        "category": task_details[2].split(": ")[1],
                        "due_date": task_details[3].split(": ")[1],
                        "done": task_details[4].split(": ")[1] == "True"
                    }
                    self.tasks.append(task)
                    if task["done"]:
                        self.listbox.insert(tk.END, f"{task['name']} ✔")
                    else:
                        self.listbox.insert(tk.END, task["name"])
        except FileNotFoundError:
            messagebox.showwarning("Load Error", "No saved tasks found.")
        except Exception as e:
            messagebox.showerror("Load Error", f"An error occurred while loading tasks: {e}")

root = tk.Tk()
root.title("To-Do List")
root.geometry("500x750")
to_do_list = ToDoList(root)
root.mainloop()