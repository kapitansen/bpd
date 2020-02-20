<template>
	<b-col class="factor-rating">
		<b-form-group>
			<div class="mb-1 factor-label" :id="'label-'+factor.id"
				v-if="!changename" 
				@dblclick="changename=true"
			>{{factor.label}}</div>
			<b-popover 
				:target="'label-'+factor.id" 
				triggers="click blur" 
				placement="top"
			>
			    <div 
			    	v-if="!changedescr"
			    	@dblclick="changedescr=true"
			    >{{factor.descr}}</div>
			    <b-form-textarea
			    	v-if="changedescr"
			        :placeholder="factor.descr"
			        @keyup.enter="changeFactorDescr"
			        @keydown.esc="changedescr=false"
			        rows="2"
			        max-rows="6"
			        size="sm"
			        autofocus=true
			    ></b-form-textarea>
			  </b-popover>
			<b-input-group v-if="changename" size="sm" class="editgroup">
				<b-form-input class="factor-edit mb-1"
					size="sm" 
					autofocus=true
					:placeholder="factor.label"
					@keyup.enter="changeFactorName"
					@keydown.esc="changename=false" 
				></b-form-input>
				<b-input-group-append>
				    	<b-button
				    		variant="danger"
				    		size="sm"
							@click="$emit('delete', factor.id)"
				    	>X</b-button>
				</b-input-group-append>
			</b-input-group>
			<b-form-radio-group
				class="factor-radio"
			    :options="options"
			    :disabled="disabled"
			    buttons
			    button-variant="outline-secondary"
			    size="sm"
			    :name="'factor'+factor.id"
			    @change="changeFactorData"
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
        options: [
        	{ text: '3', value: '-3' },
        	{ text: '2', value: '-2' },
        	{ text: '1', value: '-1' },
        	{ text: '0', value: '0' },
        	{ text: '1', value: '1' },
        	{ text: '2', value: '2' },
        	{ text: '3', value: '3' }
        ],
        changename: false,
        changedescr: false,
      }
    },
    methods: {
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
    	changeFactorDescr: function(event) {
    		let newdescr = event.target.value.trim();
    		if (newdescr != '') {
    			this.changedescr = false;
    			this.$emit('update:descr', newdescr);
    		}
    	},
    }
  }
</script>

<style>
	.factor-label {
		cursor: pointer;
	}
	.factor-rating {
		text-align: center;
	}
	label.btn.btn-outline-secondary.btn-sm.active {
	    background: #28a745;
	    color: #fff;
	}
	.btn-danger {
		height: 31px;
	}
	.editgroup {
	    max-width: 200px;
	    margin: -7px auto 0 auto;
	}
	.factor-radio label:nth-of-type(7):hover {
	    background: #fb0404;
	}
	.factor-radio label:nth-of-type(6):hover {
	    background: #fd6868;
	}
	.factor-radio label:nth-of-type(5):hover {
	    background: #fecdcd;
	}
	.factor-radio label:nth-of-type(4):hover {
	    background: #00cc00;
	}
	.factor-radio label:nth-of-type(3):hover {
	    background: #ced9fd;
	}
	.factor-radio label:nth-of-type(2):hover {
	    background: #6d8df8;
	}
	.factor-radio label:nth-of-type(1):hover {
	    background: #0b40f4;
	}
</style>