<template>
	<b-col class="factor-rating">
		<b-form-group>
			<div class="mb-1" 
				v-if="!changename" 
				@dblclick="changename=true"
			>{{factor.label}}</div>
			<b-form-input class="factor-edit mb-1"
				v-if="changename"
				size="sm" 
				autofocus=true
				:placeholder="factor.label"
				@keyup.enter="changeFactorName"
				@keydown.esc="changename=false" 
			></b-form-input>
			<b-form-radio-group
			    :options="options"
			    :disabled="disabled"
			    buttons
			    button-variant="outline-secondary"
			    size="sm"
			    :name="'factor'+factor.id"
			    @change="changeFactorData"
			    @click.middle="test"
			    @mouseDown.middle="test"
			></b-form-radio-group>
		</b-form-group>
	</b-col>
</template>

<script>
  export default {
  	props: {
  		factor: Object,
  	},
    data() {
      return {
      	disabled: false,
        options: [1,2,3,4,5],
        changename: false,
      }
    },
    methods: {
    	test: function() {
    		console.log('test');
    	},
    	changeFactorData: function(checked) { 
			this.disabled = true;
			let newdata = this.factor.data.concat(checked);
			this.$emit('update:data', newdata);
    	},
    	changeFactorName: function(event) {
    		let newname = event.target.value.trim();
    		if (newname != '') {
    			this.changename = false;
    			this.$emit('update:label', newname);
    		}
    	},
    }
  }
</script>

<style>
	.factor-rating {
		text-align: center;
	}
	label.btn.btn-outline-secondary.btn-sm.active {
	    background: #28a745;
	    color: #fff;
	}
</style>