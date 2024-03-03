<script setup>
import { computed, reactive } from 'vue';

const todolist = reactive({
	todos: [
		{
			id: 1,
			title: '测试',
			completed: false,
		},
		{
			id: 2,
			title: '测试',
			completed: true,
		},
	],
	visibility: 'all',

	removeTodo(id) {
		this.todos = this.todos.filter(todo => todo.id != id)
	},
	clearCompleted() {
		this.todos = this.todos.filter(todo => !todo.completed)
	},
	changeVisibility(visibility) {
		this.visibility = visibility
	}
})

const filteredTodos = computed(() => {
	switch (todolist.visibility) {
		case 'all':
			return todolist.todos
		case 'active':
			return todolist.todos.filter(todo => !todo.completed)
		case 'completed':
			return todolist.todos.filter(todo => todo.completed)
	}
})

const remaining = computed(() => {
	return todolist.todos.filter(todo => !todo.completed)
})

function addTodo(e) {
	const title = e.target.value.trim()

	if (!title) {
		return
	} else {
		todolist.todos.push({
			id: Date.now(),
			title,
			completed: false
		})
	}
	e.target.value = ''
}

function toggleAll(checked) {
	todolist.todos.forEach(todo => {
		todo.completed = checked.target.checked
	});
}
</script>

<template>
	<section class="todoapp">
		<header class="header">
			<h1>todos</h1>
			<input class="new-todo" @keyup.enter="addTodo" placeholder="What needs to be done?" autofocus>
		</header>
		<!-- This section should be hidden by default and shown when there are todos -->
		<section class="main">
			<input id="toggle-all" class="toggle-all" type="checkbox" @click="toggleAll">
			<label for="toggle-all">Mark all as complete</label>
			<ul class="todo-list">
				<!-- These are here just to show the structure of the list items -->
				<!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
				<li :class="{ completed: todolist.todos.completed }" v-for="todo in filteredTodos" :key="todo.id">
					<div class="view">
						<input class="toggle" type="checkbox" v-model="todo.completed">
						<label v-text="todo.title"></label>
						<button class="destroy" @click="todolist.removeTodo(todo.id)"></button>
					</div>
					<input class="edit" value="Create a TodoMVC template">
				</li>
			</ul>
		</section>
		<!-- This footer should be hidden by default and shown when there are todos -->
		<footer class="footer">
			<!-- This should be `0 items left` by default -->
			<span class="todo-count"><strong v-text="remaining.length"></strong> item left</span>
			<!-- Remove this if you don't implement routing -->
			<ul class="filters">
				<li>
					<a :class="{ selected: todolist.visibility === 'all' }" @click="todolist.changeVisibility('all')">All</a>
				</li>
				<li>
					<a :class="{ selected: todolist.visibility === 'active' }"
						@click="todolist.changeVisibility('active')">Active</a>
				</li>
				<li>
					<a :class="{ selected: todolist.visibility === 'completed' }"
						@click="todolist.changeVisibility('completed')">Completed</a>
				</li>
			</ul>
			<!-- Hidden if no completed items are left ↓ -->
			<button class="clear-completed" @click="todolist.clearCompleted()">Clear completed</button>
		</footer>
	</section>
</template>

<style>
@import "todomvc-app-css/index.css"
</style>
