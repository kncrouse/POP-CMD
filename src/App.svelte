<script>
	import Mermaid from "./Mermaid.svelte";
	import LeftPanelCategory from "./LeftPanelCategory.svelte";
	import AddItemForm from "./AddItemForm.svelte";
	import Tabs from "./Tabs.svelte";
	import DiagramElementForm from "./DiagramElementForm.svelte";
	import MultiSelect from 'svelte-multiselect'
	// import Tooltip from './Tooltip.svelte';
	// import { tooltip } from './tooltip';
	// import { tooltip as tooltipv1 } from './tooltip.v1';

	let spatiallyExplicit = false;
	let modelStructure;
	let speciesDescription;
	let environmentDescription;

	let selectedChemicalImpacts;
	let selectedDensityImpacts;
	let selectedStochasticImpacts;
	let selectedSpatialHeterogeneities;

	$: actionElements = transitions;
	$: lifeElements = behaviors.concat(lifeHistories, energetics);
	$: modelOutputs = abundances.concat(
		bioMasses,
		structures,
		others
	);

	// ENVIRONMENT

	let chemicalDrivers = [];
	let otherDrivers = [];

	// ORGANISM

	let lifeHistories = [];
	let transitions = [];
	let behaviors = [];
	let energetics = [];

	// OTHER MODEL FEATURES

	let densityDependencies = [];
	let stochasticEffects = [];
	let spatialHeterogeneities = [];

	// MODEL OUTPUTS

	let abundances = [];
	let bioMasses = [];
	let structures = [];
	let others = [];

	// TABS

	let tabItems = ["Show Diagram", "Edit Diagram"]; //"Markdown Text"
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

		if (currentItem != "Edit Diagram") {
			updateDiagram();
		}
	};

	function updateDiagram() {
		mermaidMarkdown = "flowchart TD \n ";
		let styleMarkdown = ""

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
			let arrow = "-.-"
			let connection = element.elementConnection;
			let leftLeftBracket = '(["'
			let rightLeftBracket = '"]) '
			let leftRightBracket = '(["'
			let rightRightBracket = '"]) '

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

			// UPDATE BOX STYLE
			for (let i = 0; i < lifeHistories.length; i++) {
				let lH = lifeHistories[i];
				if (lH.name == first) {
					leftLeftBracket = '["'
					rightLeftBracket = '"] '
				}
				if (lH.name == second) {
					leftRightBracket = '["'
					rightRightBracket = '"] '
				}
			}

			// UPDATE ARROW STTLE
			if (leftLeftBracket == '["' && leftRightBracket == '["' ) {
				arrow = '-->'
			}

			// UPDATE RED TEXT
			if ( selectedChemicalImpacts.includes(first) ) {
				styleMarkdown += 'style ' + firstLetter + ' color:red \n'
			}
			if ( selectedChemicalImpacts.includes(second) ) {
				styleMarkdown += 'style ' + secondLetter + ' color:red \n'
			}

			// UPDATE UNDERLINED TEXT
			if ( selectedDensityImpacts.includes(first) ) {
				styleMarkdown += 'classDef underlined text-decoration: underline; class ' + firstLetter + ' underlined; \n'
			}
			if ( selectedDensityImpacts.includes(second) ) {
				styleMarkdown += 'classDef underlined text-decoration: underline; class ' + secondLetter + ' underlined; \n'
			}

			// UPDATE STOCASTICITY ITALICS TEXT
			if ( selectedStochasticImpacts.includes(first) ) {
				styleMarkdown += 'classDef stochasticity font-style: italic; class ' + firstLetter + ' stochasticity; \n'
			}
			if ( selectedStochasticImpacts.includes(second) ) {
				styleMarkdown += 'classDef stochasticity font-style: italic; class ' + secondLetter + ' stochasticity; \n'
			}

			// UPDATE SPATIAL HETEROGENEITY TEXT
			if ( selectedSpatialHeterogeneities.includes(first) ) {
				styleMarkdown += 'classDef heterogeneity fill: #dbc410, stroke: #d99116, stroke-width: 3px ; class ' + firstLetter + ' heterogeneity; \n'
			} else {
				styleMarkdown += 'classDef qwert1 fill: #dbc410, stroke: #c4b018, stroke-width: 1px ; class ' + firstLetter + ' qwert1; \n'
			} 

			if ( selectedSpatialHeterogeneities.includes(second) ) {
				styleMarkdown += 'classDef heterogeneity fill: #dbc410, stroke: #d99116, stroke-width: 3px ; class ' + secondLetter + ' heterogeneity; \n'
			} else {
				styleMarkdown += 'classDef qwert fill: #dbc410, stroke: #c4b018, stroke-width: 1px ; class ' + secondLetter + ' qwert; \n'
			} 

			// STYLES FOR THE DIAGRAM BOXES
		
			
			let newLine =
				firstLetter +
				leftLeftBracket +
				first +
				rightLeftBracket + 
				arrow +
				'|"' +
				connection +
				'"| ' +
				secondLetter +
				leftRightBracket +
				second +
				rightRightBracket + 
				' \n';
			//console.log(newLine)

			mermaidMarkdown += newLine;
		}

		mermaidMarkdown += styleMarkdown;


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

	// ORGANISM

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
		// thisLH = lifeHistories.filter((item) => item.id == e.detail)[0];

		// diagramElements = diagramElements.filter((item) => item.firstElement != thisLH.name);
		// diagramElements = diagramElements.filter((item) => item.secondElement != thisLH.name);
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

	const addSpatialHeterogeneity = (e) => {
		const item = e.detail;
		spatialHeterogeneities = [...spatialHeterogeneities, item];
	};

	const deleteSpatialHeterogeneity = (e) => {
		spatialHeterogeneities = spatialHeterogeneities.filter(
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

		spatiallyExplicit = true;
		modelStructure = "agent-based model";
		speciesDescription = "Boltonia decurrens";
		environmentDescription = "Illinois River Floodplain";

		const driver1 = {
			name: "Atrazine exposure",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver2 = {
			name: "Seasonally variable flood occurrence and duration",
			id: Math.random(),
		};

		const driver3 = {
			name: "Shading from other vegetation",
			id: Math.random(),
		};

		otherDrivers = [...otherDrivers, driver2];

		const depend1 = {
			name: "Intra- and interspecific competition for light",
			id: Math.random(),
		};
		densityDependencies = [...densityDependencies, depend1];

		const stoch1 = {
			name: "Stochasticity in spatial plant establishment",
			id: Math.random(),
		};
		stochasticEffects = [...stochasticEffects, stoch1];

		const spatial1 = {
			name: "Atrazine exposure varies spatially",
			id: Math.random(),
		};
		spatialHeterogeneities = [...spatialHeterogeneities, spatial1];

		const output1 = {
			name: "Flowering plant density",
			id: Math.random(),
		};
		const output2 = {
			name: "Stage structure",
			id: Math.random(),
		};
		const output3 = {
			name: "Total plant density",
			id: Math.random(),
		};
		const output4 = {
			name: "Years to quasi-extinction",
			id: Math.random(),
		};

		abundances = [...abundances, output1, output3];
		structures = [...structures, output2];
		others = [...others, output4];

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
			behavior6,
			behavior2, 
			behavior4,
		];
		// behaviors = [...behaviors, ];
		// energetics = [...energetics, ];

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

		for (let i = 0; i < lifeHistories.length; i++) {
			let lH = lifeHistories[i].name;
			if (lH == 'Seed load') {
				selectedStochasticImpacts = [...selectedStochasticImpacts, lH]
			}
			if (lH == 'Seedling') {
				selectedChemicalImpacts = [...selectedChemicalImpacts, lH]
				selectedDensityImpacts = [...selectedDensityImpacts, lH]
				selectedStochasticImpacts = [...selectedStochasticImpacts, lH]
				selectedSpatialHeterogeneities = [...selectedSpatialHeterogeneities, lH]
			}
			if (lH == 'Rosette') {
				selectedChemicalImpacts = [...selectedChemicalImpacts, lH]
				selectedDensityImpacts = [...selectedDensityImpacts, lH]
				selectedStochasticImpacts = [...selectedStochasticImpacts, lH]
				selectedSpatialHeterogeneities = [...selectedSpatialHeterogeneities, lH]
			}

		}

		updateDiagram();
		showNewDiagram();
	};

	const showExample2 = () => {
		clearAll();

		spatiallyExplicit = true;
		modelStructure = "conceptual only";
		speciesDescription = "Hypomesus transpacificus";
		environmentDescription = "Sacramento–San Joaquin Estuary";

		const driver1 = {
			name: "Chlorpyrifos exposure",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver2 = {
			name: "Habitat management",
			id: Math.random(),
		};
		const driver5 = {
			name: "Variation in habitat suitability",
			id: Math.random(),
		};
		const driver3 = {
			name: "Abundance of prey affected by chlorpyrifos",
			id: Math.random(),
		};
		const driver4 = {
			name: "Seasonal variability",
			id: Math.random(),
		};
		otherDrivers = [...otherDrivers, driver2, driver5, driver3, driver4];

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

		const spatial1 = {
			name: "Habitat suitability varies spatially",
			id: Math.random(),
		};

		const spatial2 = {
			name: "Migration varies spatially",
			id: Math.random(),
		};
		spatialHeterogeneities = [...spatialHeterogeneities, spatial1, spatial2];

		const output1 = {
			name: "Population abundance",
			id: Math.random(),
		};
		const output2 = {
			name: "Life stage distribution",
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
		abundances = [...abundances, output1];
		structures = [...structures, output2];
/* 		dynamics = [...dynamics, output3, output4];
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
			elementConnection: "Survival",
			secondElement: "Adult",
			id: Math.random(),
		};
		const item8 = {
			firstElement: "Adult",
			elementConnection: " ",
			secondElement: "Migration/dispersal",
			id: Math.random(),
		};
		diagramElements = [
			...diagramElements,
			item1,
			item6,
			item7,
			item4,
			item5,
			item8,
		];

		for (let i = 0; i < lifeHistories.length; i++) {
			let lH = lifeHistories[i].name;
			selectedChemicalImpacts = [...selectedChemicalImpacts, lH]
			if (lH == 'Adult') {
				selectedDensityImpacts = [...selectedDensityImpacts, lH]
			}
		}

		updateDiagram();
		showNewDiagram();
	};

	const showExample3 = () => {
		clearAll();

		modelStructure = "conceptual only";
		speciesDescription = "Pimephales promelas";
		environmentDescription = "North America";

		const driver1 = {
			name: "Chlorpyrifos exposure",
			id: Math.random(),
		};
		chemicalDrivers = [...chemicalDrivers, driver1];

		const driver4 = {
			name: "Variability in juvenile overwinter survival",
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
			name: "Population abundance",
			id: Math.random(),
		};
		const output2 = {
			name: "Life stage distribution",
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
		abundances = [...abundances, output1];
		structures = [...structures, output2];
/* 		dynamics = [...dynamics, output3, output4];
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
			elementConnection: "Survival",
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

		for (let i = 0; i < lifeHistories.length; i++) {
			let lH = lifeHistories[i].name;
			selectedChemicalImpacts = [...selectedChemicalImpacts, lH]
		}
		
		updateDiagram();
		showNewDiagram();
		
	};

	const triggerDiagram = () => {
		updateDiagram();
		showNewDiagram();
	}

	async function showNewDiagram() {
		let promise = Promise.resolve(10);
		await promise;
		mermaidDiagram.update();
	}

	const clearAll = () => {
		spatiallyExplicit = false;
		modelStructure = "";
		speciesDescription = "";
		environmentDescription = "";
		chemicalDrivers = [];
		otherDrivers = [];
		densityDependencies = [];
		stochasticEffects = [];
		spatialHeterogeneities = [];
		modelOutputs = [];
		diagramElements = [];
		lifeHistories = [];
		transitions = [];
		behaviors = [];
		energetics = [];
		abundances = [];
		bioMasses = [];
		structures = [];
		others = [];
		selectedChemicalImpacts = [];
		selectedDensityImpacts = [];
		selectedStochasticImpacts = [];
		selectedSpatialHeterogeneities = [];
		updateDiagram();
		showNewDiagram();
	};

	function generatePDF() {

        // Choose the element id which you want to export.
        var element = document.getElementById('parent');
        element.style.width = '1200px';
        element.style.height = '800px';
        var opt = {
            margin:       0.25,
            filename:     'myCMD.pdf',
            image:        { type: 'pdf', quality: 10 },
            html2canvas:  { scale: 1 },
            jsPDF:        { unit: 'in', format: [13.5,9], orientation: 'landscape', precision: '12' }
          };
        
        // choose the element and pass it to html2pdf() function and call the save() on it to save as pdf.
        html2pdf().set(opt).from(element).save();

	}

</script>




<!-- svelte-ignore non-top-level-reactive-declaration -->
<main style="margin-right: 20px" >
	<div style="padding: 10px; margin-right: 10px; background-color: #e3e3e3; height:100%; width:100%; border:solid 0px black; justify-content:center; align-items:center; ">
		<h1>POP-CMD</h1>
		<h5>a CONCEPTUAL MODEL DIAGRAM generator</h5>

		<div style="display:flex; height:100%; width:100%; justify-content:center; align-items:center; ">
			<button style="color:teal; margin: 10px; border-color:lightgray">User Manual
			</button>
			<button
				style="color:olivedrab; margin: 10px; border-color:lightgray"
				on:click={showExample1}>Decurrent False Aster
			</button>
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
			<button
				style="color:grey; margin: 10px; border-color:lightgray"
				on:click={generatePDF}>Generate PDF</button
			>
		</div>
	</div>

	<div style="font-size:14px; margin: 10px; display:flex; height:100%; width:100%; justify-content:center; align-items:center; ">
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label style="margin: 5px">Species modeled:</label>
		<input
			style="width: 250px; margin: 10px; font-style: italic"
			type="text"
			placeholder="Species"
			bind:value={speciesDescription}
		/><br />
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label style="margin: 5px">Modeled environment:</label>
		<input
			style="width: 250px; margin: 10px"
			type="text"
			placeholder="Environment"
			bind:value={environmentDescription}
		/><br />
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label style="margin: 5px">Model structure:</label>
		<select style="margin: 10px" bind:value={modelStructure} placeholder="Structure">
			<option value="conceptual only">conceptual only</option>
			<option value="unstructured">unstructured</option>
			<option value="structured/matrix">structured/matrix</option>
			<option value="agent-based model">agent-based model</option>
		</select>
		<!-- svelte-ignore a11y-label-has-associated-control -->
		<label style="margin: 5px">
			Spatially explicit:
		</label>
		<input style="margin: 10px" type="checkbox" bind:checked={spatiallyExplicit}/>
	</div>

	<div id="parent" style="display:flex; height:100%; border:solid 0px black">
		<div id="left" class="top" style="width: 400px;">
			<!-- ENVIRONMENT -->

			<div style="background-color:darkgreen; color: white; width: 100%; height: 25px; padding-top: 5px">
				Environment
			</div>

			<LeftPanelCategory
				heading="Chemical drivers"
				headingColor="#569e3a"
				categoryIncluded={chemicalDrivers.length > 0}>

				<MultiSelect --sms-font-size="10px"
					options={lifeElements.map((x) => x.name)} 
					bind:selected={selectedChemicalImpacts} 
					on:change={triggerDiagram}/>

				<AddItemForm
					placeholderText="New chemical driver"
					on:addItem={addDriver}
					on:deleteItem={deleteDriver}
					items={chemicalDrivers}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Other drivers"
				headingColor="#569e3a"
				categoryIncluded={otherDrivers.length > 0}>

				<AddItemForm
					placeholderText="New external driver"
					on:addItem={addOtherDriver}
					on:deleteItem={deleteOtherDriver}
					items={otherDrivers}/>

			</LeftPanelCategory>

			<!-- ORGANISM -->

			<div
				style="background-color:#767b1a; color: white; width: 100%; height: 25px; padding-top: 5px"
			>
				Organism
			</div>

			<LeftPanelCategory
				heading="Life history stages"
				headingColor="#c4b018"
				categoryIncluded={lifeHistories.length > 0}>

				<AddItemForm
					placeholderText="New life history stage"
					on:addItem={addLifeHistories}
					on:deleteItem={deleteLifeHistories}
					items={lifeHistories}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Transitions"
				headingColor="#c4b018"
				categoryIncluded={transitions.length > 0}>

				<AddItemForm
					placeholderText="New transition component"
					on:addItem={addTransitions}
					on:deleteItem={deleteTransitions}
					items={transitions}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Behaviors"
				headingColor="#c4b018"
				categoryIncluded={behaviors.length > 0}>

				<AddItemForm
					placeholderText="New behavioral component"
					on:addItem={addBehaviors}
					on:deleteItem={deleteBehaviors}
					items={behaviors}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Energetics"
				headingColor="#c4b018"
				categoryIncluded={energetics.length > 0}>

				<AddItemForm
					placeholderText="New energetics component"
					on:addItem={addEnergetics}
					on:deleteItem={deleteEnergetics}
					items={energetics}/>

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
				categoryIncluded={densityDependencies.length > 0}>

				<MultiSelect  --sms-font-size="10px"
					options={lifeElements.map((x) => x.name)} 
					bind:selected={selectedDensityImpacts} 
					on:change={triggerDiagram}/>

				<AddItemForm
					placeholderText="New density dependence component"
					on:addItem={addDependency}
					on:deleteItem={deleteDependency}
					items={densityDependencies}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Stochasticity"
				headingColor="#d99116"
				categoryIncluded={stochasticEffects.length > 0}>

				<MultiSelect  --sms-font-size="10px"
					options={lifeElements.map((x) => x.name)} 
					bind:selected={selectedStochasticImpacts} 
					on:change={triggerDiagram}/>

				<AddItemForm
					placeholderText="New stochasticity component"
					on:addItem={addStochasicity}
					on:deleteItem={deleteStochasicity}
					items={stochasticEffects}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Spatial heterogeneity"
				headingColor="#d99116"
				categoryIncluded={spatialHeterogeneities.length > 0}>

				<MultiSelect  --sms-font-size="10px"
					options={lifeElements.map((x) => x.name)} 
					bind:selected={selectedSpatialHeterogeneities} 
					on:change={triggerDiagram}/>

				<AddItemForm
					placeholderText="New spatially heterogeneous component"
					on:addItem={addSpatialHeterogeneity}
					on:deleteItem={deleteSpatialHeterogeneity}
					items={spatialHeterogeneities}/>

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
				categoryIncluded={abundances.length > 0}>

				<AddItemForm
					placeholderText="New abundance output"
					on:addItem={addAbundances}
					on:deleteItem={deleteAbundances}
					items={abundances}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Biomass"
				headingColor="#4180ad"
				categoryIncluded={bioMasses.length > 0}>

				<AddItemForm
					placeholderText="New biomass output"
					on:addItem={addBiomasses}
					on:deleteItem={deleteBiomasses}
					items={bioMasses}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Structure"
				headingColor="#4180ad"
				categoryIncluded={structures.length > 0}>

				<AddItemForm
					placeholderText="New structural output"
					on:addItem={addStructures}
					on:deleteItem={deleteStructures}
					items={structures}/>

			</LeftPanelCategory>

			<LeftPanelCategory
				heading="Other outputs"
				headingColor="#4180ad"
				categoryIncluded={others.length > 0}>

				<AddItemForm
					placeholderText="New output"
					on:addItem={addOthers}
					on:deleteItem={deleteOthers}
					items={others}/>
					
			</LeftPanelCategory>
		</div>

		<!-- DIAGRAM BODY -->
		<div style="display:flex; flex-flow:column; width: 100%; font-size: 14px;">
			<div
				style="display:flex; height: 200px; width: 100%; align-items:stretch; margin-bottom: 10px; "
			>
				{#each chemicalDrivers as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: green; height: 140px; max-width: 120px; margin-bottom: 5px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/right-arrow.png"
							alt="Loading..."
							style="height: 40px; transform: rotate(90deg);"
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
							style="background-color: green; height: 140px; max-width: 120px; margin-bottom: 5px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/right-arrow.png"
							alt="Loading..."
							style="height: 40px; transform: rotate(90deg);"
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
							style="background-color: #d08008; height: 140px; max-width: 120px; margin-bottom: 5px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/right-arrow.png"
							alt="Loading..."
							style="height: 40px; transform: rotate(90deg);"
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
							style="background-color: #d08008; height: 140px; max-width: 120px; margin-bottom: 5px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/right-arrow.png"
							alt="Loading..."
							style="height: 40px; transform: rotate(90deg);"
						/>
					</div>
				{/each}

				{#each spatialHeterogeneities as factor (factor.id)}
					<div
						class="background"
						style="height: 100%; max-width: 120px; margin: 10px"
					>
						<div
							class="background"
							style="background-color: #d08008; height: 140px; max-width: 120px; margin-bottom: 5px"
						>
							<p
								style="align: center; padding: 10px; color: white"
							>
								{factor.name}
							</p>
						</div>
						<img
							src="images/right-arrow.png"
							alt="Loading..."
							style="height: 40px; transform: rotate(90deg);"
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
						lifehistoryElements={lifeElements}
						on:addItem={addDiagramElement}
						on:deleteItem={deleteDiagramElement}
					/>
					<div />
				{:else if currentItem === "Markdown Text"}
					<div style="display:flex; align-items:center; padding: 10px;  width:90%; height: 70%; background-color: white; margin: 20px; font-size: 10px">
						<p>{mermaidMarkdown}</p>
					</div>
				{/if}
			</div>
		</div>

		<!-- DIAGRAM OUTPUT -->
		<div style="width: 300px; margin: 0px ; font-size: 14px">
			<div style="height: 200px;" />
			{#each abundances as output (output.id)}
				<div
					style="display:flex; width: 280px; align-items:center; margin: 10px"
				>
					<img
						src="images/right-arrow.png"
						alt="Loading..."
						style="height: 40px; "
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
						<p style="align: center; color: white;">
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

	<h6>© 2023 Kristin Crouse </h6>

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: rgb(91, 91, 91);
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 2;
		padding: 0px;
		margin: 0px;
		margin-top:10px;
	}

	h5 {
		color:grey;
		font-size: 1em;
		font-weight: 14;
		margin: 0px;	
		margin-bottom: 30px;
	}

	h6 {
		color:grey;
		font-size: 24;
		font-weight: 14;
		margin-top: 30px;	
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
