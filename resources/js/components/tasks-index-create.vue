<template>
<div class="container">

	<h1>{{ projectObject.title }} tasks</h1>
	<b-table responsive small striped hover :items="tasks" :fields="fields">
		<template slot="idtitle" slot-scope="data">
			<b-form @submit.prevent="onDeleteTask(data.item.id)">
				<b-button variant="danger" type="submit">Delete: '{{ shortenTitleForButton(data.item.title) }}'</b-button>
			</b-form>
		</template>
	</b-table>

	<b-button v-if="!createNewTask" v-on:click="showTaskForm" variant="success">New task</b-button>
	
	<b-form v-if="createNewTask" method="post" @submit.prevent="onSubmitTask">
		<b-form-group
		id="form-group-taskname"
		label="Enter the name the task you are creating"
		label-for="TaskName">
		<b-form-input 
		id="TaskName"
		placeholder="Task name"
		v-model="task.title"
		required>
		</b-form-input>
		</b-form-group>
		
		<b-form-group
		id="form-group-taskcontent"
		label="Enter the details of the task"
		label-for="TaskContent">
		<b-form-textarea
		id="TaskContent"
		placeholder="Task content"
		max-rows="3"
		v-model="task.content"
		required>
		</b-form-textarea>
		</b-form-group>

		<b-button type="submit" variant="success">Create task</b-button>
		<b-button v-if="createNewTask" v-on:click="showTaskForm" variant="warning">Close form</b-button>
	</b-form>
</div>
</template>
<script>

import Axios from 'axios'

import { mapState } from 'vuex'
	export default {
		data() {
			return {
				projectObject: Object,
				task: {
					title: '',
					content: '',
					projectId: Number
				},
				createNewTask: false,
				fields: [
					{
						key: 'title'
					},
					{
						key: 'content'
					},
					{
						key: 'idtitle', label: "Action"
					}
				]
			}
		},
		created() {
			this.projectObject = JSON.parse(this.project)
			this.task.projectId = this.projectObject.id               
			this.$store.dispatch('task/fetchTasks', this.projectObject.id)
		},
		props: ['project', 'task-form-url'],
		methods: {
			onSubmitTask() {
				// Create task via API call
				this.$store.dispatch('task/addTask', this.task)
			},
			showTaskForm() {
				if (this.createNewTask) {
					this.createNewTask = false
				}
				else if (!this.createNewTask) {
					this.createNewTask = true
				}
			},
			onDeleteTask($id) {
				let payload = { id: $id, projectId: this.projectObject.id }
				this.$store.dispatch('task/destroyTask', payload)
			},
			shortenTitleForButton($title) {
				//Shorten title to the first two words, for the delete button.
				if ($title.length > 9){
					var index = $title.indexOf(' ')
					var indexTwo = $title.indexOf(' ', index + 1) 
					return $title.substring(0, indexTwo) + '...'	
				} else {
					return $title;
				}
				
			}
		},
		computed: {
			...mapState({
				tasks: state => state.task.tasks
			})
		}
	};
</script>
<style scoped>
	#TaskName {
		width: 300px;
	}
	#TaskContent {
		width: 400px;
	}
</style>
