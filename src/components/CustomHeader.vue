<template>
<div class="head">
		<div class="title">
			<h3>{{ inputHeader }}</h3>
		</div>
	</div>
</template>

<script>
export default {
  name: 'CustomHeader',
  props: {
    inputHeader:{
                type:String,
                default:"标题"
            },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.head {
	z-index: 10;
	position: fixed;
	top: 0px;
	height: 32.25px;
	background-color: #ffffff;
    width: 100%;
    box-shadow: 0 3px 4px -4px black;
	padding: 12px 9px;
	left:0;
	right:0;
}

.head .title h3 {
	float: left;
	margin: 14px 13.125px;
	font-size: 23.5px;
	color: #409EFF;
	line-height: 0px;
}
</style>