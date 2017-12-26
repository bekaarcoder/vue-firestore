<template>
	<div id="dashboard">
		<ul class="collection with-header">
			<li class="collection-header"><h4>Employees</h4></li>
			<li v-for="emp in employees" v-bind:key="emp.id" class="collection-item">
				{{emp.name}} <div class="chip">{{emp.dept}}</div>
				<router-link class="secondary-content" :to="{name: 'view-employee', params: {employee_id: emp.employee_id}}"><i class="material-icons">remove_red_eye</i></router-link>
			</li>
		</ul>
		<div class="fixed-action-btn">
			<router-link to="/new" class="btn-floating btn-large red"><i class="material-icons">add</i></router-link>
		</div>
	</div>
</template>

<script>
	import db from './firebaseInit'
	export default {
		name: 'dashboard',
		data () {
			return {
				employees: []
			}
		},
		created () {
			db.collection('employees').orderBy('dept').get().then((querySnapshot) => {
				querySnapshot.forEach((doc) => {
					const data = {
						'id': doc.id,
						'employee_id': doc.data().emp_id,
						'dept': doc.data().dept,
						'name': doc.data().name,
						'position': doc.data().position
					};
					this.employees.push(data);
				});
			}).catch((error) => {
				console.log(error);
			});
		}
	}
</script>