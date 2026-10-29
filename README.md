import heapq
import ipywidgets as widgets
from IPython.display import display, clear_output

# Campus Lost and Found System
# Google Colab compatible version

items = []
next_id = 1

categories = [
    "Electronics", "Books", "ID Card", "Wallet",
    "Keys", "Clothing", "Accessories", "Other"
]

name_input = widgets.Text(description="Item:", placeholder="Enter item name")
category_input = widgets.Dropdown(
    options=categories, description="Category:"
)
description_input = widgets.Text(
    description="Details:", placeholder="Describe the item"
)
location_input = widgets.Text(
    description="Location:", placeholder="Where lost/found?"
)
contact_input = widgets.Text(
    description="Contact:", placeholder="Contact information"
)
status_input = widgets.Dropdown(
    options=["Lost", "Found"], description="Status:"
)
search_input = widgets.Text(
    description="Search:", placeholder="Name, category, location..."
)

output = widgets.Output()

def render():
    with output:
        clear_output(wait=True)
        if not items:
            print("No reports yet. Add a lost or found item above.")
            return

        print("CAMPUS LOST AND FOUND RECORDS")
        print("-" * 55)

        for item in items:
            print(
                f"ID: {item['id']} | {item['name']} | "
                f"{item['category']} | {item['status']}"
            )
            print(f"Location: {item['location']}")
            print(f"Description: {item['description']}")
            print(f"Contact: {item['contact']}")
            print(f"Date: {item['date']}")
            print("-" * 55)

def add_item(_):
    global next_id

    if not name_input.value.strip():
        with output:
            clear_output()
            print("Please enter an item name.")
        return

    if not location_input.value.strip() or not contact_input.value.strip():
        with output:
            clear_output()
            print("Please enter location and contact information.")
        return

    from datetime import date

    items.append({
        "id": next_id,
        "name": name_input.value.strip(),
        "category": category_input.value,
        "description": description_input.value.strip(),
        "location": location_input.value.strip(),
        "contact": contact_input.value.strip(),
        "status": status_input.value,
        "date": date.today().isoformat()
    })

    next_id += 1

    name_input.value = ""
    description_input.value = ""
    location_input.value = ""
    contact_input.value = ""

    render()

def search_items(_):
    query = search_input.value.strip().lower()

    with output:
        clear_output(wait=True)
        matches = [
            item for item in items
            if query in " ".join(str(v) for v in item.values()).lower()
        ]

        if not matches:
            print("No matching items found.")
            return

        for item in matches:
            print(
                f"ID: {item['id']} | {item['name']} | "
                f"{item['category']} | {item['status']}"
            )
            print(f"Location: {item['location']}")
            print(f"Description: {item['description']}")
            print(f"Contact: {item['contact']}")
            print("-" * 40)

def show_all(_):
    render()

def sort_items(_):
    items.sort(key=lambda item: item["name"].lower())
    render()

def mark_resolved(_):
    with output:
        clear_output()
        print("Enter the ID of the item to mark as resolved.")

def resolve_item(_):
    try:
        item_id = int(id_input.value)
        for item in items:
            if item["id"] == item_id:
                item["status"] = "Resolved"
                render()
                return
        with output:
            clear_output()
            print("Item ID not found.")
    except ValueError:
        with output:
            clear_output()
            print("Please enter a valid numeric ID.")

def delete_item(_):
    try:
        item_id = int(id_input.value)
        for item in items:
            if item["id"] == item_id:
                items.remove(item)
                render()
                return
        with output:
            clear_output()
            print("Item ID not found.")
    except ValueError:
        with output:
            clear_output()
            print("Please enter a valid numeric ID.")

add_button = widgets.Button(description="Add Report", button_style="success")
search_button = widgets.Button(description="Search")
all_button = widgets.Button(description="Show All")
sort_button = widgets.Button(description="Sort by Name")
id_input = widgets.IntText(description="Item ID:", value=1)
resolve_button = widgets.Button(description="Mark Resolved")
delete_button = widgets.Button(description="Delete Item", button_style="danger")

add_button.on_click(add_item)
search_button.on_click(search_items)
all_button.on_click(show_all)
sort_button.on_click(sort_items)
resolve_button.on_click(resolve_item)
delete_button.on_click(delete_item)

display(widgets.HTML(
    "<h2 style='color:#17365d'>Campus Lost and Found System</h2>"
    "<p>Report, search, sort and manage campus items.</p>"
))

display(widgets.VBox([
    name_input,
    category_input,
    description_input,
    location_input,
    contact_input,
    status_input,
    add_button,
    widgets.HTML("<hr><h3>Search and Manage Items</h3>"),
    search_input,
    widgets.HBox([search_button, all_button, sort_button]),
    id_input,
    widgets.HBox([resolve_button, delete_button]),
    output
]))

render()
