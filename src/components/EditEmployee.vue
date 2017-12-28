<template>
<div id="edit-employee">
	<h3>Edit Employee</h3>
	<div class="row">
		<form @submit.prevent="editEmployee" class="col s12 m8 offset-m2">
			<div class="row">
				<div class="input-field col s12 m12">
					<input type="text" value="sdkjfskjdf" v-model="name" placeholder="Name" id="name">
					<!-- <label for="name">Name</label> -->
					<span v-if="missingName && attemptSubmit" class="red-text text-lighten-1">Name field is required</span>
				</div>
			</div>
			<div class="row">
				<div class="input-field col s12 m12">
					<input type="text" v-model="dept" placeholder="Department" id="dept">
					<!-- <label for="dept">Department</label> -->
					<span v-if="missingDept && attemptSubmit" class="red-text text-lighten-1">Department field is required</span>
				</div>
			</div>
			<div class="row">
				<div class="input-field col s12 m12">
					<input type="text" v-model="position" placeholder="Position" id="position">
					<!-- <label for="position">Position</label> -->
					<span v-if="missingPosition && attemptSubmit" class="red-text text-lighten-1">Position field is required</span>
				</div>
			</div>
			<div class="row center-align">
				<router-link to="/" class="btn grey">Back</router-link>
				<button type="submit" class="btn blue">Save</button>
			</div>
		</form>
	</div>
</div>
</template>
<script>
	import db from './firebaseInit'
	export default {
		name: 'edit-employee',
		data () {
			return {
				employee_id: null,
				name: null,
				dept: null,
				position: null,
				attemptSubmit: false
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
		computed: {
			missingName: function() {
				return this.name === '';
			},
			missingDept: function() {
				return this.dept === '';
			},
			missingPosition: function() {
				return this.position === '';
			}
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
			editEmployee(e) {
				this.attemptSubmit = true;
				if(this.missingName || this.missingDept || this.missingPosition){
					e,preventDefault();
				} else {
					db.collection('employees').where('emp_id', '==', this.$route.params.employee_id).get().then((querySnapshot) => {
						querySnapshot.forEach((doc) => {
							doc.ref.update({
								emp_id: this.employee_id,
								name: this.name,
								dept: this.dept,
								position: this.position
							}).then(() => {
								this.$router.push({
									name: 'view-employee',
									params: {
										employee_id: this.employee_id
									}
								})
							})
						})
					})
				}
			}
		}
	}
</script>