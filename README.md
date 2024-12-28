import argparse
import json
import os

# File to store tasks
task_file = "tasks.json"

def load_tasks():
    """Load tasks from the JSON file. Create the file if it doesn't exist."""
    if not os.path.exists(task_file):
        with open(task_file, "w") as file:
            json.dump([], file)
    with open(task_file, "r") as file:
        return json.load(file)

def save_tasks(tasks):
    """Save tasks to the JSON file."""
    with open(task_file, "w") as file:
        json.dump(tasks, file, indent=4)

def add_task(name):
    tasks = load_tasks()
    tasks.append({"name": name, "status": "not done"})
    save_tasks(tasks)
    print(f"Task '{name}' added successfully.")

def update_task(index, new_name):
    tasks = load_tasks()
    if 0 <= index < len(tasks):
        tasks[index]["name"] = new_name
        save_tasks(tasks)
        print(f"Task {index} updated to '{new_name}'.")
    else:
        print(f"Task {index} not found.")

def delete_task(index):
    tasks = load_tasks()
    if 0 <= index < len(tasks):
        removed_task = tasks.pop(index)
        save_tasks(tasks)
        print(f"Task '{removed_task['name']}' deleted.")
    else:
        print(f"Task {index} not found.")

def mark_task(index, status):
    tasks = load_tasks()
    if 0 <= index < len(tasks):
        tasks[index]["status"] = status
        save_tasks(tasks)
        print(f"Task {index} marked as '{status}'.")
    else:
        print(f"Task {index} not found.")

def list_tasks(filter_status=None):
    tasks = load_tasks()
    if filter_status:
        tasks = [task for task in tasks if task["status"] == filter_status]
    if tasks:
        for i, task in enumerate(tasks):
            print(f"{i}. {task['name']} [{task['status']}]")
    else:
        print("No tasks found.")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Task Tracker CLI Application")
    subparsers = parser.add_subparsers(dest="command")

  #Add task
    add_parser = subparsers.add_parser("add", help="Add a new task")
    add_parser.add_argument("name", help="Name of the task")

  #Update task
    update_parser = subparsers.add_parser("update", help="Update an existing task")
    update_parser.add_argument("index", type=int, help="Index of the task to update")
    update_parser.add_argument("new_name", help="New name of the task")

   #Delete task
    delete_parser = subparsers.add_parser("delete", help="Delete a task")
    delete_parser.add_argument("index", type=int, help="Index of the task to delete")

  #Mark task
    mark_parser = subparsers.add_parser("mark", help="Mark a task as in progress or done")
    mark_parser.add_argument("index", type=int, help="Index of the task to mark")
    mark_parser.add_argument("status", choices=["not done", "in progress", "done"], help="Status to mark the task")

  #List tasks
    list_parser = subparsers.add_parser("list", help="List tasks")
    list_parser.add_argument("filter", nargs="?", choices=["done", "not done", "in progress"], help="Filter tasks by status")

  args = parser.parse_args()

  #Execute the appropriate function based on the command
    if args.command == "add":
        add_task(args.name)
    elif args.command == "update":
        update_task(args.index, args.new_name)
    elif args.command == "delete":
        delete_task(args.index)
    elif args.command == "mark":
        mark_task(args.index, args.status)
    elif args.command == "list":
        list_tasks(args.filter)
    else:
        parser.print_help()
