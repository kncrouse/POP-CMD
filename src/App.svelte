<script>
	import Mermaid from "./Mermaid.svelte";
	import LeftPanelCategory from "./LeftPanelCategory.svelte";
	import AddItemForm from "./AddItemForm.svelte";
	import SelectItemForm from "./SelectItemForm.svelte";
	import { each, update_await_block_branch } from "svelte/internal";
	import Tabs from "./Tabs.svelte";
	import DiagramElementForm from "./DiagramElementForm.svelte";

	let modelStructure;
	let speciesDescription;
	let environmentDescription;

	$: actionElements = behaviors.concat(transitions, energetics);
	$: modelOutputs = abundances.concat(
		bioMasses,
		structures,
		dynamics,
		others
	);

	// ENVIRONMENT

	let chemicalDriversIncluded = false;
	let otherDriversIncluded = false;

	let chemicalDrivers = [];
	let otherDrivers = [];

	// POPULATION

	let lifeHistoriesIncluded = false;
	let transitionsIncluded = false;
	let behaviorsIncluded = false;
	let energeticsIncluded = false;

	let lifeHistories = [];
	let transitions = [];
	let behaviors = [];
	let energetics = [];

	// OTHER MODEL FEATURES

	let dependenciesIncluded = false;
	let stochasticIncluded = false;

	let densityDependencies = [];
	let stochasticEffects = [];

	// MODEL OUTPUTS

	let abundancesIncluded = false;
	let bioMassesIncluded = false;
	let structuresIncluded = false;
	let dynamicsIncluded = false;
	let othersIncluded = false;

	let abundances = [];
	let bioMasses = [];
	let structures = [];
	let dynamics = [];
	let others = [];

	// TABS

	let tabItems = ["Show Diagram", "Edit Diagram"];
	let currentItem = "Show Diagram";

	// MERMAID

	let diagramElements = [];
	let mermaidDiagram;

	let mermaidMarkdown = "";
	// 'graph TD\n \
	// 	subgraph environmental exposure\n \
	// 		direction TB\n \
	// 		C --> |Growth| C\n \
	// 		C --> |Transition to rosette| D([Rosette])\n \
	// 	end\n \
	// G --> |Seeding establishment| C([Seedling])\n \
	// D --> |Maturation| F([Flowering plant])\n \
	// F --> |Seed dispersal| G([Seed load])\n \
	// D --> |Growth| D\n \
	// F --> |Vegetative reproduction| D'

	let tabChange = (e) => {
		currentItem = e.detail;

		if (currentItem === "Show Diagram") {
			updateDiagram();
		}
	};

	function updateDiagram() {
		mermaidMarkdown = "flowchart TD \n";

		let alphabet = [
			"A",
			"B",
			"C",
			"D",
			"E",
			"F",
			"G",
			"H",
			"I",
			"J",
			"K",
			"L",
			"M",
			"N",
			"O",
			"P",
		];

		let usedAlphabet = [];
		let usedElements = [];

		// for each diagram elements
		for (let i = 0; i < diagramElements.length; i++) {
			let element = diagramElements[i];
			let first = element.firstElement;
			let second = element.secondElement;
			let connection = element.elementConnection;

			let firstLetter;
			let secondLetter;

			if (usedElements.indexOf(first) === -1) {
				firstLetter = alphabet.pop();
				usedAlphabet = [...usedAlphabet, firstLetter];
				usedElements = [...usedElements, first];
			} else {
				console.log(usedAlphabet);
				console.log(usedElements);
				let thisIndex = usedElements.indexOf(first);
				console.log(thisIndex);
				firstLetter = usedAlphabet[thisIndex];
				console.log(firstLetter);
			}

			if (usedElements.indexOf(second) === -1) {
				secondLetter = alphabet.pop();
				usedAlphabet = [...usedAlphabet, secondLetter];
				usedElements = [...usedElements, second];
			} else {
				console.log(usedAlphabet);
				console.log(usedElements);
				let thisIndex = usedElements.indexOf(second);
				console.log(thisIndex);
				secondLetter = usedAlphabet[thisIndex];
				console.log(secondLetter);
			}

			let newLine =
				firstLetter +
				'["' +
				first +
				'"] -->|"' +
				connection +
				'"| ' +
				secondLetter +
				'["' +
				second +
				'"] \n';
			//console.log(newLine)

			mermaidMarkdown += newLine;
		}
	}

	// ENVIRONMENT

	const addDriver = (e) => {
		const item = e.detail;
		chemicalDrivers = [...chemicalDrivers, item];
	};

	const deleteDriver = (e) => {
		chemicalDrivers = chemicalDrivers.filter((item) => item.id != e.detail);
	};

	const addOtherDriver = (e) => {
		const item = e.detail;
		otherDrivers = [...otherDrivers, item];
	};

	const deleteOtherDriver = (e) => {
		otherDrivers = otherDrivers.filter((item) => item.id != e.detail);
	};

	// POPULATION

	const addDiagramElement = (e) => {
		const item = e.detail;
		diagramElements = [...diagramElements, item];
		// add new lH connection
	};

	const deleteDiagramElement = (e) => {
		diagramElements = diagramElements.filter((item) => item.id != e.detail);
		// remove lH connections
	};

	const addLifeHistories = (e) => {
		const item = e.detail;
		lifeHistories = [...lifeHistories, item];
		// add new lH connection
	};

	const deleteLifeHistories = (e) => {
		lifeHistories = lifeHistories.filter((item) => item.id != e.detail);
		// remove lH connections
	};

	const addTransitions = (e) => {
		const item = e.detail;
		transitions = [...transitions, item];
	};

	const deleteTransitions = (e) => {
		transitions = transitions.filter((item) => item.id != e.detail);
	};

	const addBehaviors = (e) => {
		const item = e.detail;
		behaviors = [...behaviors, item];
	};

	const deleteBehaviors = (e) => {
		behaviors = behaviors.filter((item) => item.id != e.detail);
	};

	const addEnergetics = (e) => {
		const item = e.detail;
		energetics = [...energetics, item];
	};

	const deleteEnergetics = (e) => {
		energetics = energetics.filter((item) => item.id != e.detail);
	};

	// OTHER MODEL FEATURES

	const addDependency = (e) => {
		const item = e.detail;
		densityDependencies = [...densityDependencies, item];
	};

	const deleteDependency = (e) => {
		densityDependencies = densityDependencies.filter(
			(item) => item.id != e.detail
		);
	};

	const addStochasicity = (e) => {
		const item = e.detail;
		stochasticEffects = [...stochasticEffects, item];
	};

	const deleteStochasicity = (e) => {
		stochasticEffects = stochasticEffects.filter(
			(item) => item.id != e.detail
		);
	};

	// MODEL OUTPUTS

	const addAbundances = (e) => {
		const item = e.detail;
		abundances = [...abundances, item];
	};

	const deleteAbundances = (e) => {
		abundances = abundances.filter((item) => item.id != e.detail);
	};

	const addBiomasses = (e) => {
		const item = e.detail;
		bioMasses = [...bioMasses, item];
	};

	const deleteBiomasses = (e) => {
		bioMasses = bioMasses.filter((item) => item.id != e.detail);
	};

	const addStructures = (e) => {
		const item = e.detail;
		structures = [...structures, item];
	};

	const deleteStructures = (e) => {
		structures = structures.filter((item) => item.id != e.detail);
	};

	const addDynamics = (e) => {
		const item = e.detail;
		dynamics = [...dynamics, item];
	};

	const deleteDynamics = (e) => {
		dynamics = dynamics.filter((item) => item.id != e.detail);
	};

	const addOthers = (e) => {
		const item = e.detail;
		others = [...others, item];
	};

	const deleteOthers = (e) => {
		others = others.filter((item) => item.id != e.detail);
	};

	// EXAMPLE AND CLEAR BUTTONS

	const showExample1 = () => {
		clearAll();

		modelStructure = "agent-based model";
		speciesDescription = "Boltonia decurrens";
		environmentDescription = "Illinois River Floodplain";

		chemicalDriversIncluded = true;
		otherDriversIncluded = true;
		dependenciesIncluded = true;
		stochasticIncluded = true;
		lifeHistoriesIncluded = true;
		transitionsIncluded = true;
		behaviorsIncluded = true;
		energeticsIncluded = true;
		abundancesIncluded = true;
		bioMassesIncluded = false;
		structuresIncluded = true;
		dynamicsIncluded = true;
		othersIncluded = true;

		const driver1 = {
			name: "Chemical exposure: spatial gradient",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver2 = {
			name: "Seasonally variable flood occurrence and duration",
			id: Math.random(),
		};
		otherDrivers = [...otherDrivers, driver2];

		const depend1 = {
			name: "Intra-specific competition for light",
			id: Math.random(),
		};
		densityDependencies = [...densityDependencies, depend1];

		const stoch1 = {
			name: "Stochasticity in spatial plant establishment",
			id: Math.random(),
		};
		stochasticEffects = [...stochasticEffects, stoch1];

		const output1 = {
			name: "Flowering plant density",
			id: Math.random(),
		};
		const output2 = {
			name: "Stage structure",
			id: Math.random(),
		};
		const output3 = {
			name: "Density over time",
			id: Math.random(),
		};
		const output4 = {
			name: "Years to quasi-extinction",
			id: Math.random(),
		};
		const output5 = {
			name: "Indirect effects from inter-specific competition",
			id: Math.random(),
		};
		abundances = [...abundances, output1];
		structures = [...structures, output2];
		dynamics = [...dynamics, output3, output4];
		others = [...others, output5];

		const lifehistory1 = {
			name: "Seed load",
			id: Math.random(),
		};
		const lifehistory2 = {
			name: "Seedling",
			id: Math.random(),
		};
		const lifehistory3 = {
			name: "Rosette",
			id: Math.random(),
		};
		const lifehistory4 = {
			name: "Flowering plant",
			id: Math.random(),
		};
		lifeHistories = [
			...lifeHistories,
			lifehistory1,
			lifehistory2,
			lifehistory3,
			lifehistory4,
		];

		const behavior1 = {
			name: "Seedling establishment",
			id: Math.random(),
		};
		const behavior2 = {
			name: "Seedling growth",
			id: Math.random(),
		};
		const behavior3 = {
			name: "Transition to rosette",
			id: Math.random(),
		};
		const behavior4 = {
			name: "Rosette growth",
			id: Math.random(),
		};
		const behavior5 = {
			name: "Maturation",
			id: Math.random(),
		};
		const behavior6 = {
			name: "Vegetative reproduction",
			id: Math.random(),
		};
		const behavior7 = {
			name: "Seed dispersal",
			id: Math.random(),
		};

		transitions = [
			...transitions,
			behavior1,
			behavior3,
			behavior5,
			behavior7,
		];
		behaviors = [...behaviors, behavior6];
		energetics = [...energetics, behavior2, behavior4];

		const item1 = {
			firstElement: "Seedling",
			elementConnection: "Transition to rosette",
			secondElement: "Rosette",
			id: Math.random(),
		};
		const item2 = {
			firstElement: "Seedling",
			elementConnection: "Seedling growth",
			secondElement: "Seedling",
			id: Math.random(),
		};
		const item3 = {
			firstElement: "Rosette",
			elementConnection: "Rosette growth",
			secondElement: "Rosette",
			id: Math.random(),
		};
		const item4 = {
			firstElement: "Rosette",
			elementConnection: "Maturation",
			secondElement: "Flowering plant",
			id: Math.random(),
		};
		const item5 = {
			firstElement: "Flowering plant",
			elementConnection: "Vegetative reproduction",
			secondElement: "Rosette",
			id: Math.random(),
		};
		const item6 = {
			firstElement: "Flowering plant",
			elementConnection: "Seed dispersal",
			secondElement: "Seed load",
			id: Math.random(),
		};
		const item7 = {
			firstElement: "Seed load",
			elementConnection: "Seedling establishment",
			secondElement: "Seedling",
			id: Math.random(),
		};
		diagramElements = [
			...diagramElements,
			item7,
			item2,
			item3,
			item4,
			item5,
			item6,
			item1,
		];

		updateDiagram();
		showNewDiagram();
	};

	const showExample2 = () => {
		clearAll();

		modelStructure = "agent-based model";
		speciesDescription = "Hypomesus transpacificus";
		environmentDescription = "Sacramento–San Joaquin Estuary";

		chemicalDriversIncluded = true;
		otherDriversIncluded = true;
		dependenciesIncluded = true;
		stochasticIncluded = true;
		lifeHistoriesIncluded = true;
		transitionsIncluded = false;
		behaviorsIncluded = true;
		energeticsIncluded = false;
		abundancesIncluded = false;
		bioMassesIncluded = false;
		structuresIncluded = false;
		dynamicsIncluded = false;
		othersIncluded = false;

		const driver1 = {
			name: "Environmental concentration of chemical exposure",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver2 = {
			name: "Spacially explicit landscape of estuarine/freshwater spawning grounds",
			id: Math.random(),
		};
		const driver3 = {
			name: "Abundance of prey",
			id: Math.random(),
		};
		const driver4 = {
			name: "Seasonally varying demography",
			id: Math.random(),
		};
		otherDrivers = [...otherDrivers, driver2, driver3, driver4];

		const depend1 = {
			name: "Life-stage specific density dependence", 
			id: Math.random(),
		};
		densityDependencies = [...densityDependencies, depend1];

		const stoch1 = {
			name: "Environmental Stochasticity",
			id: Math.random(),
		};
		stochasticEffects = [...stochasticEffects, stoch1];

		const output1 = {
			name: " ",
			id: Math.random(),
		};
		const output2 = {
			name: " ",
			id: Math.random(),
		};
		const output3 = {
			name: " ",
			id: Math.random(),
		};
		const output4 = {
			name: " ",
			id: Math.random(),
		};
		const output5 = {
			name: " ",
			id: Math.random(),
		};
/* 		abundances = [...abundances, output1];
		structures = [...structures, output2];
		dynamics = [...dynamics, output3, output4];
		others = [...others, output5]; */

		const lifehistory1 = {
			name: "Egg",
			id: Math.random(),
		};
		const lifehistory2 = {
			name: "Larva",
			id: Math.random(),
		};
		const lifehistory3 = {
			name: "Juvenile",
			id: Math.random(),
		};
		const lifehistory4 = {
			name: "Adult",
			id: Math.random(),
		};
		lifeHistories = [
			...lifeHistories,
			lifehistory1,
			lifehistory2,
			lifehistory3,
			lifehistory4,
		];

		const behavior1 = {
			name: " ",
			id: Math.random(),
		};
		const behavior2 = {
			name: "Seedling growth",
			id: Math.random(),
		};
		const behavior3 = {
			name: " ",
			id: Math.random(),
		};
		const behavior4 = {
			name: "Rosette growth",
			id: Math.random(),
		};
		const behavior5 = {
			name: " ",
			id: Math.random(),
		};
		const behavior6 = {
			name: "Migration/dispersal",
			id: Math.random(),
		};
		const behavior7 = {
			name: " ",
			id: Math.random(),
		};

/* 		transitions = [
			...transitions,
			behavior1,
			behavior3,
			behavior5,
			behavior7,
		]; */
		behaviors = [...behaviors, behavior6];
/* 		energetics = [...energetics, behavior2, behavior4]; */

		const item1 = {
			firstElement: "Egg",
			elementConnection: " ",
			secondElement: "Larva",
			id: Math.random(),
		};
		const item4 = {
			firstElement: "Larva",
			elementConnection: " ",
			secondElement: "Juvenile",
			id: Math.random(),
		};
		const item5 = {
			firstElement: "Juvenile",
			elementConnection: " ",
			secondElement: "Adult",
			id: Math.random(),
		};
		const item6 = {
			firstElement: "Adult",
			elementConnection: " ",
			secondElement: "Egg",
			id: Math.random(),
		};
		const item7 = {
			firstElement: "Adult",
			elementConnection: "Migration/dispersal",
			secondElement: "Adult",
			id: Math.random(),
		};
		diagramElements = [
			...diagramElements,
			item1,
			item6,
			item7,
			item4,
			item5,

		];

		updateDiagram();
		showNewDiagram();
	};

	const showExample3 = () => {
		clearAll();

		modelStructure = "agent-based model";
		speciesDescription = "Hypomesus transpacificus";
		environmentDescription = "Sacramento–San Joaquin Estuary";

		chemicalDriversIncluded = true;
		otherDriversIncluded = true;
		dependenciesIncluded = false;
		stochasticIncluded = false;
		lifeHistoriesIncluded = true;
		transitionsIncluded = false;
		behaviorsIncluded = false;
		energeticsIncluded = false;
		abundancesIncluded = false;
		bioMassesIncluded = false;
		structuresIncluded = false;
		dynamicsIncluded = false;
		othersIncluded = false;

		const driver1 = {
			name: "Environmental concentration of chemical exposure",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver4 = {
			name: "Seasonally varying demography",
			id: Math.random(),
		};
		otherDrivers = [...otherDrivers, driver4];

/* 		const depend1 = {
			name: "Life-stage specific density dependence", 
			id: Math.random(),
		};
		densityDependencies = [...densityDependencies, depend1];

		const stoch1 = {
			name: "Environmental Stochasticity",
			id: Math.random(),
		};
		stochasticEffects = [...stochasticEffects, stoch1]; */

		const output1 = {
			name: " ",
			id: Math.random(),
		};
		const output2 = {
			name: " ",
			id: Math.random(),
		};
		const output3 = {
			name: " ",
			id: Math.random(),
		};
		const output4 = {
			name: " ",
			id: Math.random(),
		};
		const output5 = {
			name: " ",
			id: Math.random(),
		};
/* 		abundances = [...abundances, output1];
		structures = [...structures, output2];
		dynamics = [...dynamics, output3, output4];
		others = [...others, output5]; */

		const lifehistory1 = {
			name: "Egg",
			id: Math.random(),
		};
		const lifehistory2 = {
			name: "Larva",
			id: Math.random(),
		};
		const lifehistory3 = {
			name: "Juvenile",
			id: Math.random(),
		};
		const lifehistory4 = {
			name: "Adult",
			id: Math.random(),
		};
		lifeHistories = [
			...lifeHistories,
			lifehistory1,
			lifehistory2,
			lifehistory3,
			lifehistory4,
		];

		const behavior1 = {
			name: " ",
			id: Math.random(),
		};
		const behavior2 = {
			name: "Seedling growth",
			id: Math.random(),
		};
		const behavior3 = {
			name: " ",
			id: Math.random(),
		};
		const behavior4 = {
			name: "Rosette growth",
			id: Math.random(),
		};
		const behavior5 = {
			name: " ",
			id: Math.random(),
		};
		const behavior6 = {
			name: " ",
			id: Math.random(),
		};
		const behavior7 = {
			name: " ",
			id: Math.random(),
		};

/* 		transitions = [
			...transitions,
			behavior1,
			behavior3,
			behavior5,
			behavior7,
		]; */
/* 		behaviors = [...behaviors, behavior6]; */
/* 		energetics = [...energetics, behavior2, behavior4]; */

		const item1 = {
			firstElement: "Egg",
			elementConnection: " ",
			secondElement: "Larva",
			id: Math.random(),
		};
		const item4 = {
			firstElement: "Larva",
			elementConnection: " ",
			secondElement: "Juvenile",
			id: Math.random(),
		};
		const item5 = {
			firstElement: "Juvenile",
			elementConnection: " ",
			secondElement: "Adult",
			id: Math.random(),
		};
		const item6 = {
			firstElement: "Adult",
			elementConnection: " ",
			secondElement: "Egg",
			id: Math.random(),
		};
		const item7 = {
			firstElement: "Adult",
			elementConnection: " ",
			secondElement: "Adult",
			id: Math.random(),
		};
		diagramElements = [
			...diagramElements,
			item1,
			item6,
			item7,
			item4,
			item5,

		];

		updateDiagram();
		showNewDiagram();
	};

	async function showNewDiagram() {
		let promise = Promise.resolve(10);
		await promise;
		mermaidDiagram.update();
	}

	const clearAll = () => {
		modelStructure = "";
		speciesDescription = "";
		environmentDescription = "";
		chemicalDriversIncluded = false;
		otherDriversIncluded = false;
		dependenciesIncluded = false;
		stochasticIncluded = false;
		lifeHistoriesIncluded = false;
		transitionsIncluded = false;
		behaviorsIncluded = false;
		energeticsIncluded = false;
		abundancesIncluded = false;
		bioMassesIncluded = false;
		structuresIncluded = false;
		dynamicsIncluded = false;
		othersIncluded = false;
		chemicalDrivers = [];
		otherDrivers = [];
		densityDependencies = [];
		stochasticEffects = [];
		modelOutputs = [];
		diagramElements = [];
		lifeHistories = [];
		transitions = [];
		behaviors = [];
		energetics = [];
		abundances = [];
		bioMasses = [];
		structures = [];
		dynamics = [];
		others = [];
		updateDiagram();
		showNewDiagram();
	};
</script>

<!-- svelte-ignore non-top-level-reactive-declaration -->
<main>
	<h1>CONCEPTUAL MODEL DIAGRAM GENERATOR</h1>

	<div
		style="display:flex; height:100%; width:100%; border:solid 0px black; justify-content:center; align-items:center; "
	>
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label style="margin: 5px">What is the model structure?</label>
		<select style="margin: 10px" bind:value={modelStructure}>
			<option value="unstructured">unstructured</option>
			<option value="structured/matrix">structured/matrix</option>
			<option value="agent-based model">agent-based model</option>
		</select>

		<input
			style="width: 250px; margin: 10px; font-style: italic"
			type="text"
			placeholder="What is the species modeled?"
			bind:value={speciesDescription}
		/><br />
		<input
			style="width: 250px; margin: 10px"
			type="text"
			placeholder="What is the environment?"
			bind:value={environmentDescription}
		/><br />
	</div>

	<div
		style="display:flex; height:100%; width:100%; border:solid 0px black; justify-content:center; align-items:center; "
	>
		<button
			style="color:olivedrab; margin: 10px; border-color:lightgray"
			on:click={showExample1}><em>Boltonia decurrens</em></button
		>
		<button
			style="color:olivedrab; margin: 10px; border-color:lightgray"
			on:click={showExample2}>Delta Smelt</button
		>
		<button
			style="color:olivedrab; margin: 10px; border-color:lightgray"
			on:click={showExample3}>Fathead Minnow</button
		>
		<button
			style="color:darkred; margin: 10px; border-color:lightgray"
			on:click={clearAll}>Clear All</button
		>
	</div>

	<div id="parent" style="display:flex; height:100%; border:solid 1px black">
		<div id="left" class="top" style="width: 400px;">
			<!-- ENVIRONMENT -->

			<div
				style="background-color:darkgreen; color: white; width: 100%; height: 25px; padding-top: 5px"
			>
				Environment
			</div>

			<LeftPanelCategory
				heading="Chemical drivers"
				headingColor="#569e3a"
				categoryIncluded={chemicalDriversIncluded}
			>
				<AddItemForm
					questionChecked={chemicalDriversIncluded}
					questionText="Does the model include external drivers?"
					placeholderText="New external driver"
					on:addItem={addDriver}
					on:deleteItem={deleteDriver}
					on:handleClick={() =>
						(chemicalDriversIncluded = !chemicalDriversIncluded)}
					items={chemicalDrivers}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Other drivers"
				headingColor="#569e3a"
				categoryIncluded={otherDriversIncluded}
			>
				<AddItemForm
					questionChecked={otherDriversIncluded}
					questionText="Does the model include external drivers?"
					placeholderText="New external driver"
					on:addItem={addOtherDriver}
					on:deleteItem={deleteOtherDriver}
					on:handleClick={() =>
						(otherDriversIncluded = !otherDriversIncluded)}
					items={otherDrivers}
				/>
			</LeftPanelCategory>

			<!-- POPULATION -->

			<div
				style="background-color:#767b1a; color: white; width: 100%; height: 25px; padding-top: 5px"
			>
				Population
			</div>

			<LeftPanelCategory
				heading="Life history stages"
				headingColor="#c4b018"
				categoryIncluded={lifeHistoriesIncluded}
			>
				<AddItemForm
					questionChecked={lifeHistoriesIncluded}
					questionText="Does the model include any life history stages?"
					placeholderText="New life history stage"
					on:addItem={addLifeHistories}
					on:deleteItem={deleteLifeHistories}
					on:handleClick={() =>
						(lifeHistoriesIncluded = !lifeHistoriesIncluded)}
					items={lifeHistories}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Transitions"
				headingColor="#c4b018"
				categoryIncluded={transitionsIncluded}
			>
				<AddItemForm
					questionChecked={transitionsIncluded}
					questionText="Does the model include any transitions between stages?"
					placeholderText="New transition"
					on:addItem={addTransitions}
					on:deleteItem={deleteTransitions}
					on:handleClick={() =>
						(transitionsIncluded = !transitionsIncluded)}
					items={transitions}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Behaviors"
				headingColor="#c4b018"
				categoryIncluded={behaviorsIncluded}
			>
				<AddItemForm
					questionChecked={behaviorsIncluded}
					questionText="Does the model include any behavioral components?"
					placeholderText="New behavioral component"
					on:addItem={addBehaviors}
					on:deleteItem={deleteBehaviors}
					on:handleClick={() =>
						(behaviorsIncluded = !behaviorsIncluded)}
					items={behaviors}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Energetics"
				headingColor="#c4b018"
				categoryIncluded={energeticsIncluded}
			>
				<AddItemForm
					questionChecked={energeticsIncluded}
					questionText="Does the model include any energetics components?"
					placeholderText="New energetics component"
					on:addItem={addEnergetics}
					on:deleteItem={deleteEnergetics}
					on:handleClick={() =>
						(energeticsIncluded = !energeticsIncluded)}
					items={energetics}
				/>
			</LeftPanelCategory>

			<!-- OTHER FEATURES -->

			<div
				style="background-color:#7e3415; color: white; width: 100%; height: 25px; padding-top: 5px"
			>
				Other model features
			</div>

			<LeftPanelCategory
				heading="Density dependence"
				headingColor="#d99116"
				categoryIncluded={dependenciesIncluded}
			>
				<AddItemForm
					questionChecked={dependenciesIncluded}
					questionText="Does the model include density dependence?"
					placeholderText="New density dependency"
					on:addItem={addDependency}
					on:deleteItem={deleteDependency}
					on:handleClick={() =>
						(dependenciesIncluded = !dependenciesIncluded)}
					items={densityDependencies}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Stochasticity"
				headingColor="#d99116"
				categoryIncluded={stochasticIncluded}
			>
				<AddItemForm
					questionChecked={stochasticIncluded}
					questionText="Does the model include stochasticity?"
					placeholderText="New stochastic element"
					on:addItem={addStochasicity}
					on:deleteItem={deleteStochasicity}
					on:handleClick={() =>
						(stochasticIncluded = !stochasticIncluded)}
					items={stochasticEffects}
				/>
			</LeftPanelCategory>

			<!-- MODEL OUTPUTS -->

			<div
				style="background-color:#3a43a8; color: white; width: 100%; height: 25px; padding-top: 5px"
			>
				Model outputs
			</div>

			<LeftPanelCategory
				heading="Abundance"
				headingColor="#4180ad"
				categoryIncluded={abundancesIncluded}
			>
				<AddItemForm
					questionChecked={abundancesIncluded}
					questionText="Does the model include abundance outputs?"
					placeholderText="New abundance output"
					on:addItem={addAbundances}
					on:deleteItem={deleteAbundances}
					on:handleClick={() =>
						(abundancesIncluded = !abundancesIncluded)}
					items={abundances}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Biomass"
				headingColor="#4180ad"
				categoryIncluded={bioMassesIncluded}
			>
				<AddItemForm
					questionChecked={bioMassesIncluded}
					questionText="Does the model include biomass outputs?"
					placeholderText="New biomass output"
					on:addItem={addBiomasses}
					on:deleteItem={deleteBiomasses}
					on:handleClick={() =>
						(bioMassesIncluded = !bioMassesIncluded)}
					items={bioMasses}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Structure"
				headingColor="#4180ad"
				categoryIncluded={structuresIncluded}
			>
				<AddItemForm
					questionChecked={structuresIncluded}
					questionText="Does the model include structural outputs?"
					placeholderText="New structural output"
					on:addItem={addStructures}
					on:deleteItem={deleteStructures}
					on:handleClick={() =>
						(structuresIncluded = !structuresIncluded)}
					items={structures}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Dynamics"
				headingColor="#4180ad"
				categoryIncluded={dynamicsIncluded}
			>
				<AddItemForm
					questionChecked={dynamicsIncluded}
					questionText="Does the model include dynamic outputs?"
					placeholderText="New dynamic output"
					on:addItem={addDynamics}
					on:deleteItem={deleteDynamics}
					on:handleClick={() =>
						(dynamicsIncluded = !dynamicsIncluded)}
					items={dynamics}
				/>
			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Other outputs"
				headingColor="#4180ad"
				categoryIncluded={othersIncluded}
			>
				<AddItemForm
					questionChecked={othersIncluded}
					questionText="Does the model include other outputs?"
					placeholderText="New output"
					on:addItem={addOthers}
					on:deleteItem={deleteOthers}
					on:handleClick={() => (othersIncluded = !othersIncluded)}
					items={others}
				/>
			</LeftPanelCategory>
		</div>

		<!-- DIAGRAM BODY -->
		<div style="display:flex; flex-flow:column; width: 100%; ">
			<div
				style="display:flex; height: 200px; width: 100%; align-items:stretch; "
			>
				{#each chemicalDrivers as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: green; height: 140px; max-width: 120px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/down-arrow.svg"
							alt="Loading..."
							style="width: 40px; fill: grey"
						/>
					</div>
				{/each}

				{#each otherDrivers as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: green; height: 140px; max-width: 120px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/down-arrow.svg"
							alt="Loading..."
							style="width: 40px; fill: grey"
						/>
					</div>
				{/each}

				{#each densityDependencies as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: #d08008; height: 140px; max-width: 120px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/down-arrow.svg"
							alt="Loading..."
							style="width: 40px; fill: grey"
						/>
					</div>
				{/each}

				{#each stochasticEffects as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: #d08008; height: 140px; max-width: 120px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/down-arrow.svg"
							alt="Loading..."
							style="width: 40px; fill: grey"
						/>
					</div>
				{/each}
			</div>

			<div
				class="backdrop"
				style="background-color: #ead464; height: 100%; margin:10px; padding:10px"
			>
				<Tabs
					{tabItems}
					{currentItem}
					on:tabChange={tabChange}
					on:updateDisplay={() => mermaidDiagram.update()}
				/>

				{#if currentItem === "Show Diagram"}
					<Mermaid bind:this={mermaidDiagram} {mermaidMarkdown} />
				{:else if currentItem === "Edit Diagram"}
					<DiagramElementForm
						{diagramElements}
						{actionElements}
						lifehistoryElements={lifeHistories}
						on:addItem={addDiagramElement}
						on:deleteItem={deleteDiagramElement}
					/>
					<div />
					<p>Markdown Text:</p>
					<p>{mermaidMarkdown}</p>
				{/if}
			</div>
		</div>

		<!-- DIAGRAM OUTPUT -->
		<div style="width: 300px; margin: 0px ; ">
			<div style="height: 200px;" />
			{#each abundances as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; fill: grey"
					/>
					<div
						class="background"
						style="background-color: #3653be; width: 100%"
					>
						<p style="font-size: 8;">Abundance</p>
						<p style="align: center; color: white">
							{output.name}
						</p>
					</div>
				</div>
			{/each}
			{#each bioMasses as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; fill: grey"
					/>
					<div
						class="background"
						style="background-color: #3653be; width: 100%"
					>
						<p style="font-size: 8;">Biomass</p>
						<p style="align: center; color: white">
							{output.name}
						</p>
					</div>
				</div>
			{/each}
			{#each structures as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; fill: grey"
					/>
					<div
						class="background"
						style="background-color: #3653be; width: 100%"
					>
						<p style="font-size: 8;">Structure</p>
						<p style="align: center; color: white">
							{output.name}
						</p>
					</div>
				</div>
			{/each}
			{#each dynamics as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; fill: grey"
					/>
					<div
						class="background"
						style="background-color: #3653be; width: 100%"
					>
						<p style="font-size: 8;">Dynamics</p>
						<p style="align: center; color: white">
							{output.name}
						</p>
					</div>
				</div>
			{/each}
			{#each others as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; fill: grey"
					/>
					<div
						class="background"
						style="background-color: #3653be; width: 100%"
					>
						<p>Other outputs</p>
						<p style="align: center; color: white">
							{output.name}
						</p>
					</div>
				</div>
			{/each}
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
		color: black;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

	h5 {
		color: black;
		font-size: 24;
		font-weight: 14;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
