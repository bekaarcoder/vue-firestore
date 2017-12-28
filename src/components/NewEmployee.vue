<template>
	<div id="new-employee">
		<h4>Add New Employee</h4>
		<div class="row">
			<form @submit.prevent="saveEmployee" class="col s12 m8 offset-m2">
				<div class="row">
					<div class="input-field col s12 m12">
						<input type="text" name="employee_id" v-model="employee_id" placeholder="Employee ID">
						<label for="employee_id">Employee ID</label>
						<p v-if="missingEmpId && attemptSubmit" class="red-text text-lighten-1">Employee ID field is required</p>
						<p v-if="invalidEmpId && attemptSubmit && !missingEmpId" class="red-text text-lighten-1">Employee ID field should contain only 6 digits.</p>
					</div>
				</div>
				<div class="row">
					<div class="input-field col s12 m12">
						<input type="text" v-model="name" placeholder="Name" id="name">
						<label for="name">Name</label>
						<span v-if="missingName && attemptSubmit" class="red-text text-lighten-1">Name field is required</span>
					</div>
				</div>
				<div class="row">
					<div class="input-field col s12 m12">
						<input type="text" v-model="dept" placeholder="Department" id="dept">
						<label for="dept">Department</label>
						<span v-if="missingDept && attemptSubmit" class="red-text text-lighten-1">Department field is required</span>
					</div>
				</div>
				<div class="row">
					<div class="input-field col s12 m12">
						<input type="text" v-model="position" placeholder="Position" id="position">
						<label for="position">Position</label>
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
		name: 'new-employee',
		data () {
			return {
				employee_id: '',
				name: '',
				dept: '',
				position: '',
				attemptSubmit: false	
			}
		},
		computed: {
			missingEmpId: function() {
				return this.employee_id === '';
			},
			invalidEmpId: function() {
				var pattern = /[ !@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/;
				return (
					isNaN(this.employee_id) ||
					pattern.test(this.employee_id) ||
					this.checkLength(this.employee_id) === true
				);
			},
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
		methods: {
			checkLength(n) {
				if(n.toString().length !== 6) {
					return true;
				}
			},
			saveEmployee(e) {
				this.attemptSubmit = true;
				if(this.missingEmpId || this.missingName || this.missingDept || this.missingPosition || this.invalidEmpId) {
					e.preventDefault();
				} else {
					db.collection('employees').add({
						dept: this.dept,
						emp_id: this.employee_id,
						name: this.name,
						position: this.position
					}).then((docRef) => {
						this.$router.push('/')
					}).catch((error) => {
						console.log(error);
					})
				}
			}
		}
	}
</script>