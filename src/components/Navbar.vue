<template>
	<div>
	  <b-navbar toggleable="sm" type="dark" variant="dark">
	    <b-navbar-toggle target="nav-text-collapse"></b-navbar-toggle>

	    <b-navbar-brand>BipolarBear</b-navbar-brand>

	    <b-collapse id="nav-text-collapse" is-nav>
	      <b-navbar-nav>
	        <b-nav-text>Navbar text</b-nav-text>
	        <b-nav-item href="#">Link</b-nav-item>
	        <b-nav-item href="#">Link2</b-nav-item>
	      </b-navbar-nav>
	    </b-collapse>
	  </b-navbar>
	</div>
</template>

<script>
export default {
  name: 'Navbar',
  props: {
  }
}
</script>