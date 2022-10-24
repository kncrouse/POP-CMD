<script>
	import Mermaid from "./Mermaid.svelte"
	import LeftPanelCategory from "./LeftPanelCategory.svelte"
	import AddItemForm from "./AddItemForm.svelte"

	let spatial_heterogeneity_list = []

	let mermaidMarkdown = 
		'flowchart LR \n \
		A ---> B \n \
		B ---> C \n \
		C ---> D \n \
		D ---> E \n \
		B ---> B \n \
		C ---> C \n \
		D ---> D \n \
		E --> A'

	const handleSubmit = (e) => {
        //console.log(name, beltColor, age, skills)
		//heterid.
		//let spatial_heterogeneity_list = 
		console.log(e.target)
    }

</script>

<main>

	<h1>CONCEPTUAL MODEL DIAGRAM GENERATOR</h1>

	<div style="display:flex; height:100%; border:solid 0px black; bottom">
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label>What is the model structure?</label>
		<input type="checkbox" value="unstructured">  unstructured<br>
		<input type="checkbox" value="structured/matrix">  structured/matrix<br>
		<input type="checkbox" value="agent-based model">  agent-based model<br>
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label>What is the species modeled?</label>
		<input type="text" ><br>
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label>What is the environment?</label>
		<input type="text" ><br>
	</div>

	<div id="parent" style="display:flex; height:100%; border:solid 1px black">
		<div id="left" class="top" style="width: 300px;">
			<LeftPanelCategory heading="Spatial Heterogeneity" headingColor="green">
				<AddItemForm questionText="Drivers have spatial heterogeneity?" on:click={handleSubmit}/>
			</LeftPanelCategory>

			<LeftPanelCategory heading="External Drivers" headingColor="green">
				<form on:submit|preventDefault={handleSubmit} >
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label>What are the drivers?</label>
					<input type="text" ><br>
					<button>Add driver</button>
					
				</form>
				<form on:submit|preventDefault={handleSubmit} >
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label>Which parts of the life cycle are potentially affected by the toxicants?</label>
				</form>
			</LeftPanelCategory>

			<LeftPanelCategory heading="Density-dependence" headingColor="grey">
				<form on:submit|preventDefault={handleSubmit} >
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label>Does the model include density dependence </label>
					<input type="checkbox">Yes<br>
					<input type="checkbox">No<br>
					<input type="text" ><br>
					<button>Add item</button>
				</form>
			</LeftPanelCategory>

			<LeftPanelCategory heading="Stochasticity" headingColor="orange">
				<form on:submit|preventDefault={handleSubmit} >
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label>Does the model include stochasticity? </label>
					<input type="checkbox">Yes<br>
					<input type="checkbox">No<br>
					<input type="text" ><br>
					<button>Add item</button>
				</form>
			</LeftPanelCategory>

			<LeftPanelCategory heading="Life-history traits" headingColor="yellow">
				<form on:submit|preventDefault={handleSubmit} >
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label>How many life stages or age/size classes are there and what are they called?</label>
					<input type="text" ><br>
					<button>Add life stage</button>
				</form>
			</LeftPanelCategory>

			<LeftPanelCategory heading="Behavior" headingColor="yellow">
				
			</LeftPanelCategory>
			<LeftPanelCategory heading="Energetics" headingColor="yellow"></LeftPanelCategory>


			<LeftPanelCategory heading="Abundance" headingColor="blue"></LeftPanelCategory>
			<LeftPanelCategory heading="Biomass" headingColor="blue">

			</LeftPanelCategory>

			<LeftPanelCategory heading="Structure" headingColor="blue"></LeftPanelCategory>
			<LeftPanelCategory heading="Dynamics" headingColor="blue"></LeftPanelCategory>
			<LeftPanelCategory heading="Effects from exposure" headingColor="blue"></LeftPanelCategory>

		</div>

		<div id="right" style="flex:10; display:flex; flex-flow:column">
			<Mermaid>
				{mermaidMarkdown}
			</Mermaid>
		</div>
			
	</div>

	<h5>© 2022 Regents of the University of Minnesota. All rights reserved.</h5>

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>