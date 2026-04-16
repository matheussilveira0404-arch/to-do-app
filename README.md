!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Planner Semanal Pro</title>

  <style>
    :root {
      --alta: #dc2626; --alta-bg: #fef2f2;
      --media: #d97706; --media-bg: #fffbeb;
      --baixa: #16a34a; --baixa-bg: #f0fdf4;
      --primary: #2563eb; --primary-dark: #1d4ed8;
      --surface: #ffffff; --bg: #f1f5f9; --text: #0f172a;
      --muted: #64748b; --border: #e2e8f0;
      --r-md: 12px; --r-sm: 8px;
      --shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { 
      font-family: 'Inter', system-ui, sans-serif; 
      background: var(--bg); color: var(--text); 
      line-height: 1.5; min-height: 100vh; padding-bottom: 2rem;
    }

    /* Header & Progress */
    .app-header { background: #1e293b; color: white; padding: 3rem 1rem 4rem; text-align: center; }
    .progress-container { 
      max-width: 600px; margin: 1rem auto 0; background: rgba(255,255,255,0.1); 
      height: 8px; border-radius: 10px; overflow: hidden; 
    }
    #progressBar { width: 0%; height: 100%; background: #22c55e; transition: width 0.4s ease; }

    .app-main { max-width: 800px; margin: -2.5rem auto 0; padding: 0 1rem; display: grid; gap: 1.5rem; }

    /* Forms and Filters */
    .card { background: var(--surface); border-radius: var(--r-md); padding: 1.5rem; border: 1px solid var(--border); box-shadow: var(--shadow); }
    
    .form-grid { display: grid; grid-template-columns: 2fr 1fr 1fr auto; gap: 0.8rem; align-items: end; }
    @media (max-width: 600px) { .form-grid { grid-template-columns: 1fr; } }

    .form-group { display: flex; flex-direction: column; gap: 0.4rem; }
    label { font-size: 0.75rem; font-weight: 700; text-transform: uppercase; color: var(--muted); }
    
    input, select { 
      padding: 0.6rem; border: 1px solid var(--border); border-radius: var(--r-sm); font-size: 0.9rem; outline-color: var(--primary);
    }

    .btn-add { background: var(--primary); color: white; border: none; padding: 0.7rem 1.2rem; border-radius: var(--r-sm); cursor: pointer; font-weight: 600; }
    .btn-add:hover { background: var(--primary-dark); }

    /* Day Filters */
    .filter-bar { display: flex; gap: 0.5rem; overflow-x: auto; padding-bottom: 0.5rem; margin: 1rem 0; scrollbar-width: none; }
    .filter-btn { 
      padding: 0.5rem 1rem; border: 1px solid var(--border); background: white; 
      border-radius: 20px; font-size: 0.85rem; cursor: pointer; white-space: nowrap; transition: 0.2s;
    }
    .filter-btn.active { background: var(--primary); color: white; border-color: var(--primary); }

    /* Task Items */
    .task-list { display: grid; gap: 0.8rem; }
    .task-item { 
      display: flex; align-items: center; gap: 1rem; padding: 1rem; 
      background: white; border-radius: var(--r-md); border-left: 6px solid; border: 1px solid var(--border);
      animation: slideIn 0.3s ease;
    }
    @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

    .priority-alta { border-left: 6px solid var(--alta); }
    .priority-media { border-left: 6px solid var(--media); }
    .priority-baixa { border-left: 6px solid var(--baixa); }

    .task-info { flex: 1; }
    .task-title { font-weight: 600; display: block; }
    .task-meta { font-size: 0.75rem; color: var(--muted); display: flex; gap: 0.5rem; margin-top: 2px; }
    .day-badge { background: #f1f5f9; padding: 0.1rem 0.4rem; border-radius: 4px; }

    .checkbox-custom { width: 1.2rem; height: 1.2rem; cursor: pointer; accent-color: var(--primary); }
    .completed { opacity: 0.6; }
    .completed .task-title { text-decoration: line-through; }

    .btn-delete { background: none; border: none; color: #cbd5e1; cursor: pointer; font-size: 1.2rem; transition: 0.2s; }
    .btn-delete:hover { color: #ef4444; }

    .empty-state { text-align: center; padding: 3rem; color: var(--muted); }
    .footer-actions { display: flex; justify-content: space-between; align-items: center; margin-top: 1rem; }
    .btn-clear { background: none; border: none; color: var(--alta); cursor: pointer; font-size: 0.8rem; font-weight: 600; }
  </style>
</head>
<body>

<header class="app-header">
  <h1>Planner Semanal</h1>
  <p id="statsText">0 de 0 tarefas concluídas</p>
  <div class="progress-container">
    <div id="progressBar"></div>
  </div>
</header>

<main class="app-main">
  <section class="card">
    <form id="taskForm" class="form-grid">
      <div class="form-group">
        <label>Tarefa</label>
        <input type="text" id="taskInput" placeholder="O que fazer?" required>
      </div>
      <div class="form-group">
        <label>Prioridade</label>
        <select id="priorityInput">
          <option value="baixa">Baixa</option>
          <option value="media">Média</option>
          <option value="alta">Alta</option>
        </select>
      </div>
      <div class="form-group">
        <label>Dia</label>
        <select id="dayInput">
          <option value="Segunda">Segunda</option>
          <option value="Terça">Terça</option>
          <option value="Quarta">Quarta</option>
          <option value="Quinta">Quinta</option>
          <option value="Sexta">Sexta</option>
          <option value="Sábado">Sábado</option>
          <option value="Domingo">Domingo</option>
        </select>
      </div>
      <button type="submit" class="btn-add">Adicionar</button>
    </form>
  </section>

  <nav class="filter-bar" id="filterBar">
    <button class="filter-btn active" data-filter="Todos">Todos</button>
    <button class="filter-btn" data-filter="Segunda">Seg</button>
    <button class="filter-btn" data-filter="Terça">Ter</button>
    <button class="filter-btn" data-filter="Quarta">Qua</button>
    <button class="filter-btn" data-filter="Quinta">Qui</button>
    <button class="filter-btn" data-filter="Sexta">Sex</button>
    <button class="filter-btn" data-filter="Sábado">Sáb</button>
    <button class="filter-btn" data-filter="Domingo">Dom</button>
  </nav>

  <section>
    <div id="taskList" class="task-list">
      </div>
    
    <div class="footer-actions">
      <span id="itemsLeft" style="font-size: 0.8rem; color: var(--muted);"></span>
      <button class="btn-clear" onclick="clearAll()">Limpar Lista</button>
    </div>
  </section>
</main>

<script>
  let tasks = JSON.parse(localStorage.getItem('myTasks')) || [];
  let currentFilter = 'Todos';

  const taskForm = document.getElementById('taskForm');
  const taskList = document.getElementById('taskList');
  const filterBtns = document.querySelectorAll('.filter-btn');

  // Inicialização
  renderTasks();

  taskForm.addEventListener('submit', (e) => {
    e.preventDefault();
    const newTask = {
      id: Date.now(),
      text: document.getElementById('taskInput').value,
      priority: document.getElementById('priorityInput').value,
      day: document.getElementById('dayInput').value,
      completed: false
    };
    tasks.push(newTask);
    saveAndRender();
    taskForm.reset();
  });

  // Filtros
  filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
      document.querySelector('.filter-btn.active').classList.remove('active');
      btn.classList.add('active');
      currentFilter = btn.getAttribute('data-filter');
      renderTasks();
    });
  });

  function renderTasks() {
    taskList.innerHTML = '';
    
    const filteredTasks = tasks.filter(t => 
      currentFilter === 'Todos' ? true : t.day === currentFilter
    );

    if (filteredTasks.length === 0) {
      taskList.innerHTML = `<div class="empty-state">Nenhuma tarefa para ${currentFilter === 'Todos' ? 'esta semana' : currentFilter}.</div>`;
    }

    filteredTasks.forEach(task => {
      const div = document.createElement('div');
      div.className = `task-item priority-${task.priority} ${task.completed ? 'completed' : ''}`;
      div.innerHTML = `
        <input type="checkbox" class="checkbox-custom" ${task.completed ? 'checked' : ''} 
          onclick="toggleTask(${task.id})">
        <div class="task-info">
          <span class="task-title">${task.text}</span>
          <div class="task-meta">
            <span class="day-badge">${task.day}</span>
            <span>•</span>
            <span style="color: var(--${task.priority})">${task.priority}</span>
          </div>
        </div>
        <button class="btn-delete" onclick="deleteTask(${task.id})">&times;</button>
      `;
      taskList.appendChild(div);
    });

    updateStats();
  }

  function toggleTask(id) {
    tasks = tasks.map(t => t.id === id ? {...t, completed: !t.completed} : t);
    saveAndRender();
  }

  function deleteTask(id) {
    tasks = tasks.filter(t => t.id !== id);
    saveAndRender();
  }

  function clearAll() {
    if(confirm('Deseja apagar todas as tarefas?')) {
      tasks = [];
      saveAndRender();
    }
  }

  function updateStats() {
    const total = tasks.length;
    const completed = tasks.filter(t => t.completed).length;
    const percent = total === 0 ? 0 : Math.round((completed / total) * 100);
    
    document.getElementById('progressBar').style.width = percent + '%';
    document.getElementById('statsText').innerText = `${completed} de ${total} tarefas concluídas (${percent}%)`;
    document.getElementById('itemsLeft').innerText = `${tasks.filter(t => !t.completed).length} pendentes`;
  }

  function saveAndRender() {
    localStorage.setItem('myTasks', JSON.stringify(tasks));
    renderTasks();
  }
</script>

</body>
</html># to-do-app
desafio de criação de agenda de tarefas
