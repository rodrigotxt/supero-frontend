<template>
	<div class="container">
		<h3 align="center">Gerenciador de Tarefas</h3>
		<ul class="list-group mx-auto tarefas" v-sortable="{ onUpdate: onOrderUpdate }">
				<task-item v-for="(task,index) in tasksList.rows" :index="index" :task="task"
				@update:title="onUpdate"
				@update:status="updateStatus"
				@remove="onRemove"
				></task-item>
		</ul>
		<ul class="list-group mx-auto tarefas">
			<task-item :task="task" @update:title="onUpdate" :index="-1"></task-item>
		</ul>
	</div>
</template>

<script>
	import axios from 'axios';

	const API_URL = 'https://teste.rodrigo.inf.br/supero/public';

	import Task from './TaskItem';
	
	export default{
		name: 'Tarefas',
		data: function(){
			return {
				task: {
					description: '',
					status: '',		
					title: ' ',
				},
				tasksList: {
					rows: [],
				}
			}
		},
		components: {
			// componente filho
			taskItem: Task,
		},
		methods: {
			//TODO ordenar tarefas
			onOrderUpdate(event){
				return;
			},
			// Busca Tarefas no banco
			getTasks(){
				let self = this;
				axios.get(API_URL + '/api/tasks')
				.then(response => {
					self.tasksList.rows = response.data;
				})
				.catch(error => {
					console.log(error);
				})
			},
			// remover uma tarefa
			onRemove(task){
				let self = this;
				axios.delete(API_URL + '/api/tasks/task/'+task.id)
				.then(response => {
					// recarregar Lista de Tarefas do Banco
					self.getTasks();
				})
				.catch(error => {
					console.log(error);
				})

			},
			// quando atualizar
			onUpdate(event){
				this.updateTask(event);
			},
			// atualizar tarefa ou criar uma nova
			updateTask(task){
				let self = this;
				let id = task.id ? task.id : false;
				axios.post(API_URL + '/api/tasks/task', task)
				.then(response =>{
					if(!id){
						self.task = {title: '...', description: '', status: ''};
						self.tasksList.rows.push(response.data);
					}
				})
				.catch(error => {
					console.error(error);
				})
			},
			// atualiza status tarefa
			updateStatus(event){
				let task = this.tasksList.rows[event.index];
				task.status = event.value;
				this.onUpdate(task);
			},
		},
		mounted(){
			// buscar tarefas no banco;
			this.getTasks();
		}
	}
</script>

<style lang="scss">
	@import '../css/bootstrap.css';
	.tarefas{
		max-width: 480px;
	}
</style>