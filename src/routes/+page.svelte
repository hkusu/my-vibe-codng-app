<script>
	let todos = [];
	let newTodo = '';
	let nextId = 1;

	function addTodo() {
		if (newTodo.trim()) {
			todos = [...todos, {
				id: nextId++,
				text: newTodo.trim(),
				completed: false
			}];
			newTodo = '';
		}
	}

	function deleteTodo(id) {
		todos = todos.filter(todo => todo.id !== id);
	}

	function toggleTodo(id) {
		todos = todos.map(todo => 
			todo.id === id ? { ...todo, completed: !todo.completed } : todo
		);
	}

	function clearCompleted() {
		todos = todos.filter(todo => !todo.completed);
	}

	$: completedCount = todos.filter(todo => todo.completed).length;
	$: remainingCount = todos.length - completedCount;
</script>

<svelte:head>
	<title>🌟 TODO リスト</title>
</svelte:head>

<div class="todo-app">
	<h1>🌟 TODO リスト 🌟</h1>
	
	<div class="input-section">
		<input
			type="text"
			bind:value={newTodo}
			on:keypress={(e) => e.key === 'Enter' && addTodo()}
			placeholder="新しいタスクを入力..."
			class="todo-input"
		/>
		<button on:click={addTodo} class="add-btn">✨ 追加 ✨</button>
	</div>

	<div class="stats">
		<p>📋 残り: {remainingCount}件 | ✅ 完了: {completedCount}件</p>
		{#if completedCount > 0}
			<button on:click={clearCompleted} class="clear-btn">🗑️ 完了済みを削除</button>
		{/if}
	</div>

	<ul class="todo-list">
		{#each todos as todo (todo.id)}
			<li class="todo-item" class:completed={todo.completed}>
				<input
					type="checkbox"
					checked={todo.completed}
					on:change={() => toggleTodo(todo.id)}
					class="todo-checkbox"
				/>
				<span class="todo-text">{todo.text}</span>
				<button on:click={() => deleteTodo(todo.id)} class="delete-btn">🗑️ 削除</button>
			</li>
		{:else}
			<li class="empty-state">🎉 タスクがありません！お疲れ様でした！ 🎉</li>
		{/each}
	</ul>
</div>

<style>
	:global(body) {
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		min-height: 100vh;
		margin: 0;
		animation: gradientShift 10s ease infinite;
	}

	@keyframes gradientShift {
		0%, 100% { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
		25% { background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%); }
		50% { background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%); }
		75% { background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%); }
	}

	.todo-app {
		max-width: 600px;
		margin: 2rem auto;
		padding: 2rem;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
		background: linear-gradient(145deg, #ffffff, #f0f0f0);
		border-radius: 20px;
		box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3), 
		            0 0 0 1px rgba(255, 255, 255, 0.2) inset;
		transform: perspective(1000px) rotateX(5deg);
		animation: float 6s ease-in-out infinite;
	}

	@keyframes float {
		0%, 100% { transform: perspective(1000px) rotateX(5deg) translateY(0px); }
		50% { transform: perspective(1000px) rotateX(5deg) translateY(-10px); }
	}

	h1 {
		text-align: center;
		background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4, #ffeaa7);
		background-size: 300% 300%;
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
		margin-bottom: 2rem;
		font-size: 3rem;
		font-weight: 800;
		text-shadow: 0 0 30px rgba(255, 255, 255, 0.8);
		animation: rainbow 3s ease-in-out infinite, pulse 2s ease-in-out infinite;
	}

	@keyframes rainbow {
		0%, 100% { background-position: 0% 50%; }
		50% { background-position: 100% 50%; }
	}

	@keyframes pulse {
		0%, 100% { transform: scale(1); }
		50% { transform: scale(1.05); }
	}

	.input-section {
		display: flex;
		gap: 1rem;
		margin-bottom: 2rem;
	}

	.todo-input {
		flex: 1;
		padding: 1rem;
		border: 2px solid #e1e5e9;
		border-radius: 8px;
		font-size: 1rem;
		transition: border-color 0.2s;
	}

	.todo-input:focus {
		outline: none;
		border-color: #007bff;
	}

	.add-btn {
		padding: 1rem 2rem;
		background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
		color: white;
		border: none;
		border-radius: 15px;
		font-size: 1rem;
		font-weight: bold;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 4px 15px rgba(255, 107, 107, 0.4);
		transform: translateY(0);
		position: relative;
		overflow: hidden;
	}

	.add-btn::before {
		content: '';
		position: absolute;
		top: 0;
		left: -100%;
		width: 100%;
		height: 100%;
		background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
		transition: left 0.5s;
	}

	.add-btn:hover {
		background: linear-gradient(45deg, #4ecdc4, #45b7d1);
		transform: translateY(-3px);
		box-shadow: 0 8px 25px rgba(78, 205, 196, 0.6);
	}

	.add-btn:hover::before {
		left: 100%;
	}

	.add-btn:active {
		transform: translateY(0);
	}

	.stats {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 2rem;
		padding: 1rem;
		background: #f8f9fa;
		border-radius: 8px;
	}

	.stats p {
		margin: 0;
		color: #666;
		font-weight: 500;
	}

	.clear-btn {
		padding: 0.5rem 1rem;
		background: #dc3545;
		color: white;
		border: none;
		border-radius: 6px;
		font-size: 0.9rem;
		cursor: pointer;
		transition: background-color 0.2s;
	}

	.clear-btn:hover {
		background: #c82333;
	}

	.todo-list {
		list-style: none;
		padding: 0;
		margin: 0;
	}

	.todo-item {
		display: flex;
		align-items: center;
		padding: 1rem;
		margin-bottom: 1rem;
		background: linear-gradient(135deg, #fff 0%, #f8f9ff 100%);
		border: 3px solid transparent;
		background-clip: padding-box;
		border-radius: 15px;
		transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
		position: relative;
		overflow: hidden;
		animation: slideIn 0.5s ease-out;
	}

	@keyframes slideIn {
		from {
			opacity: 0;
			transform: translateX(-50px);
		}
		to {
			opacity: 1;
			transform: translateX(0);
		}
	}

	.todo-item::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4);
		background-size: 300% 300%;
		border-radius: 15px;
		z-index: -1;
		animation: gradientRotate 4s ease infinite;
		opacity: 0;
		transition: opacity 0.3s;
	}

	@keyframes gradientRotate {
		0%, 100% { background-position: 0% 50%; }
		50% { background-position: 100% 50%; }
	}

	.todo-item:hover {
		transform: translateY(-5px) scale(1.02);
		box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
	}

	.todo-item:hover::before {
		opacity: 1;
	}

	.todo-item.completed {
		background: linear-gradient(135deg, #98fb98 0%, #90ee90 100%);
		border-color: #28a745;
		animation: completedPulse 2s ease-in-out infinite, confetti 0.8s ease-out;
		position: relative;
	}

	@keyframes completedPulse {
		0%, 100% { box-shadow: 0 0 0 0 rgba(40, 167, 69, 0.4); }
		50% { box-shadow: 0 0 0 10px rgba(40, 167, 69, 0); }
	}

	@keyframes confetti {
		0% { transform: scale(1); }
		50% { transform: scale(1.1); }
		100% { transform: scale(1); }
	}

	.todo-checkbox {
		margin-right: 1rem;
		width: 20px;
		height: 20px;
		cursor: pointer;
	}

	.todo-text {
		flex: 1;
		font-size: 1rem;
		transition: all 0.2s;
	}

	.todo-item.completed .todo-text {
		text-decoration: line-through;
		color: #6c757d;
	}

	.delete-btn {
		padding: 0.5rem 1rem;
		background: linear-gradient(45deg, #ff6b6b, #ee5a52);
		color: white;
		border: none;
		border-radius: 10px;
		font-size: 0.9rem;
		font-weight: bold;
		cursor: pointer;
		transition: all 0.3s ease;
		margin-left: 1rem;
		box-shadow: 0 2px 10px rgba(220, 53, 69, 0.3);
		position: relative;
		overflow: hidden;
	}

	.delete-btn::before {
		content: '';
		position: absolute;
		top: 50%;
		left: 50%;
		width: 0;
		height: 0;
		background: rgba(255, 255, 255, 0.3);
		border-radius: 50%;
		transform: translate(-50%, -50%);
		transition: all 0.5s ease;
	}

	.delete-btn:hover {
		background: linear-gradient(45deg, #ee5a52, #dc3545);
		transform: scale(1.05);
		box-shadow: 0 4px 20px rgba(220, 53, 69, 0.5);
	}

	.delete-btn:hover::before {
		width: 100%;
		height: 100%;
	}

	.delete-btn:active {
		transform: scale(0.98);
	}

	.empty-state {
		text-align: center;
		padding: 2rem;
		background: linear-gradient(135deg, #ffeaa7, #fab1a0);
		background-size: 200% 200%;
		color: #2d3436;
		font-style: italic;
		font-weight: bold;
		font-size: 1.2rem;
		border-radius: 15px;
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
		animation: celebrateEmpty 3s ease-in-out infinite;
	}

	@keyframes celebrateEmpty {
		0%, 100% { 
			background-position: 0% 50%;
			transform: scale(1);
		}
		50% { 
			background-position: 100% 50%;
			transform: scale(1.02);
		}
	}

	@media (max-width: 768px) {
		.todo-app {
			margin: 1rem;
			padding: 1rem;
		}

		.input-section {
			flex-direction: column;
		}

		.stats {
			flex-direction: column;
			gap: 1rem;
			text-align: center;
		}

		.todo-item {
			padding: 0.75rem;
		}

		h1 {
			font-size: 2rem;
		}
	}
</style>
