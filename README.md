<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Task Tracker</title>
  <style>
    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      padding: 48px 16px;
      font-family: system-ui, sans-serif;
      background: #f3f5f9;
      color: #202432;
    }

    main {
      max-width: 560px;
      margin: auto;
      padding: 28px;
      background: white;
      border-radius: 16px;
      box-shadow: 0 8px 30px #20243212;
    }

    h1 { margin: 0 0 8px; }
    .hint { margin: 0 0 24px; color: #687086; }

    form {
      display: flex;
      gap: 8px;
    }

    input {
      flex: 1;
      min-width: 0;
      padding: 12px;
      border: 1px solid #d8dce5;
      border-radius: 8px;
      font: inherit;
    }

    button {
      padding: 10px 14px;
      border: 0;
      border-radius: 8px;
      background: #4f46e5;
      color: white;
      font: inherit;
      cursor: pointer;
    }

    button:hover { background: #4338ca; }

    ul {
      display: grid;
      gap: 10px;
      margin: 24px 0 0;
      padding: 0;
      list-style: none;
    }

    li {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 12px;
      border: 1px solid #e5e7eb;
      border-radius: 8px;
    }

    li span { flex: 1; overflow-wrap: anywhere; }
    li.done span { color: #8a90a0; text-decoration: line-through; }

    .delete {
      padding: 6px 10px;
      background: #eef0f5;
      color: #34394a;
    }

    .delete:hover { background: #e0e3eb; }
    #empty { margin-top: 20px; color: #687086; }
  </style>
</head>
<body>
  <main>
    <h1>My Task Tracker</h1>
    <p class="hint">Keep track of what you need to do.</p>

    <form id="task-form">
      <input id="task-input" type="text" placeholder="Add a task…" required>
      <button type="submit">Add task</button>
    </form>

    <p id="empty">No tasks yet. Add one above!</p>
    <ul id="task-list"></ul>
  </main>

  <script>
    const form = document.querySelector("#task-form");
    const input = document.querySelector("#task-input");
    const list = document.querySelector("#task-list");
    const emptyMessage = document.querySelector("#empty");

    let tasks = JSON.parse(localStorage.getItem("tasks") || "[]");

    function saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(tasks));
    }

    function renderTasks() {
      list.replaceChildren();
      emptyMessage.hidden = tasks.length > 0;

      tasks.forEach((task, index) => {
        const item = document.createElement("li");
        if (task.done) item.classList.add("done");

        const checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.checked = task.done;
        checkbox.setAttribute("aria-label", `Complete ${task.text}`);
        checkbox.addEventListener("change", () => {
          tasks[index].done = checkbox.checked;
          saveTasks();
          renderTasks();
        });

        const text = document.createElement("span");
        text.textContent = task.text;

        const deleteButton = document.createElement("button");
        deleteButton.type = "button";
        deleteButton.className = "delete";
        deleteButton.textContent = "Delete";
        deleteButton.addEventListener("click", () => {
          tasks.splice(index, 1);
          saveTasks();
          renderTasks();
        });

        item.append(checkbox, text, deleteButton);
        list.append(item);
      });
    }

    form.addEventListener("submit", (event) => {
      event.preventDefault();
      const text = input.value.trim();
      if (!text) return;

      tasks.push({ text, done: false });
      saveTasks();
      renderTasks();
      input.value = "";
      input.focus();
    });

    renderTasks();
  </script>
</body>
</html>
