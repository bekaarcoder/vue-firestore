<template>
<div id="view-employee">
	<h4>{{name}}</h4>
	<ul class="collection">
		<li class="collection-item"><strong>Department: </strong>{{dept}}</li>
		<li class="collection-item"><strong>Position: </strong>{{position}}</li>
		<li class="collection-item"><strong>Employee ID: </strong>{{employee_id}}</li>
	</ul>
	<a @click="deleteEmployee" class="waves-effect waves-light btn red right">Delete</a>
	<router-link to="/" class="waves-effect waves-light btn right" style="margin-right: 7px;">Back</router-link>
	<div class="fixed-action-btn">
		<router-link :to="{name: 'edit-employee', params: {employee_id: employee_id}}" class="btn-floating btn-large red"><i class="material-icons">edit</i></router-link>
	</div>
</div>
</template>
<script>
	import db from './firebaseInit'
	export default {
		name: 'view-employee',
		data() {
			return {
				employee_id: null,
				name: null,
				dept: null,
				position: null
			}
		},
		beforeRouteEnter(to, from, next) {
			db.collection('employees').where('emp_id', '==', to.params.employee_id).get().then((querySnapshot) => {
				querySnapshot.forEach((doc) => {
					next(vm => {
						vm.employee_id = doc.data().emp_id
						vm.name = doc.data().name
						vm.dept = doc.data().dept
						vm.position = doc.data().position
					})
				})
			})
		},
		watch: {
			'$route': 'fetchData'
		},
		methods: {
			fetchData() {
				db.collection('employees').where('emp_id', '==', this.$route.params.employee_id).get().then((querySnapshot) => {
					querySnapshot.forEach((doc) => {
						this.employee_id = doc.data().emp_id
						this.name = doc.data().name
						this.dept = doc.data().dept
						this.position = doc.data().position
					})
				})
			},
			deleteEmployee() {
				if(confirm('Are you sure?')) {
					db.collection('employees').where('emp_id', '==', this.$route.params.employee_id).get().then((querySnapshot) => {
						querySnapshot.forEach((doc) => {
							doc.ref.delete()
							this.$router.push('/')
						})
					})
				}
			}
		}
	}
</script>