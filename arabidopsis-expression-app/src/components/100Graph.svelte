<script lang="ts">
  import cytoscape from "cytoscape";
  import { onMount, onDestroy } from "svelte"; // import data0 from "../data/100_fold/0.json";
  import data100 from "../data/100_fold/100_fold_increase_final.json";

  let cyInstance: any = [];
  let chartCanvas: HTMLElement;
  let nodePositions = {};
  let selectedNode: any = null;
  let originalData: any;

  // const dataFiles = {
  //   file0: data0,
  // };

  const namesToNiceNamesMap = {
    "0": "Freshly harvested (0 hours)",
    "0_to_336": "Dry seed stage (0-336 hours)",
    "336_to_337": "Stratification, 1 hour (336-337 hours)",
    "337_to_348": "Stratification, 12 hours (337-348 hours)",
    "348_to_384": "Stratification, 48 hours (348-384 hours)",
    "384_to_385": "Continuous light, 1 hour (384-385 hours)",
    "385_to_390": "Continuous light, 6 hours (385-390 hours)",
    "390_to_396": "Continuous light, 12 hours (390-396 hours)",
    "396_to_408": "Continuous light, 24 hours (396-408 hours)",
    "408_to_432": "Continuous light, 48 hours (seedling) (408-432 hours)",
  };

  let selectedFileIndex = 0;
  let fileOptions = [
    "0",
    "0_to_336",
    "336_to_337",
    "337_to_348",
    "348_to_384",
    "384_to_385",
    "385_to_390",
    "390_to_396",
    "396_to_408",
    "408_to_432",
  ];
  let selectedFileName = fileOptions[selectedFileIndex];

  // function updateGraph(data) {
  //   data.elements.nodes.forEach((nodeData) => {
  //     const node = cyInstance.getElementById(nodeData.data.id);
  //     if (node.length > 0) {
  //       node.data(nodeData.data);
  //       node.data("degree", node.degree()); // Update degree
  //       node.style(
  //         "background-color",
  //         typeof nodeData.data.colour === "object"
  //           ? `rgba(${nodeData.data.colour[0] * 255},${nodeData.data.colour[1] * 255},${nodeData.data.colour[2] * 255},1)`
  //           : nodeData.data.colour
  //       );
  //     }
  //   });

  // Update edge colors if needed
  //   data.elements.edges.forEach((edgeData) => {
  //     const edge = cyInstance.getElementById(edgeData.data.id);
  //     if (edge.length > 0) {
  //       // Assuming you have a color property for edges, adjust as needed
  //       edge.style("line-color", edgeData.data.colour);
  //     }
  //   });
  //   cyInstance.remove(cyInstance.elements());

  //   // Add new elements
  //   cyInstance.add(data.elements); // Update node colors
  //   createInitialGraph(data);
  //   // Optionally, update any other style properties that might change
  // }

  onMount(() => {
    // const initialData = dataFiles.file0;
    if (cyInstance && typeof cyInstance.destroy === "function") {
      cyInstance.destroy();
      cyInstance = null;
    }
    if (chartCanvas) {
      chartCanvas.innerHTML = "";
    }
    createInitialGraph(data100);
  });

  // if (cyInstance) {
  //   cyInstance.destroy();
  // }

  onDestroy(() => {
    if (cyInstance) {
      cyInstance.destroy();
      cyInstance = null;
    }
  });
  function createInitialGraph(data: {
    format_version?: string;
    generated_by?: string;
    target_cytoscapejs_version?: string;
    data?: {
      shared_name: string;
      name: string;
      SUID: number;
      selected: boolean;
    };
    elements: any;
  }) {
    
    originalData = JSON.parse(JSON.stringify(data));

    const minSize = 0.5; // Minimum node size
    const maxSize = 40; // Maximum node size

    cyInstance = cytoscape({
      container: chartCanvas,
      elements: data.elements,
      // boxSelectionEnabled: false,
      // autounselectify: true,
      layout: {
        name: "preset", // Use preset layout to maintain positions
        // animate: false,
        // nodeOverlap: 20,
        // componentSpacing: 100,
        // edgeElasticity: 100,
        // nestingFactor: 5,
        // gravity: 80,
        // numIter: 1000,
        // initialTemp: 200,
        // coolingFactor: 0.95,
        // minTemp: 1.0,
      },
      style: [
        {
          selector: "node",
          style: {
            width: "mapData(degree, 0, 10, " + minSize + ", " + maxSize + ")",
            height: "mapData(degree, 0, 10, " + minSize + ", " + maxSize + ")",
            "background-color": function (ele) {
              return getColorForValue(ele.data(selectedFileName));
            },
            label: function (ele: { data: (arg0: string) => string }) {
              return (
                ele.data("name") +
                "\nDegree: " +
                ele.data("degree") +
                "\nFold change: " +
                String(ele.data(selectedFileName))
              );
            },
            "border-color": "black",
            "border-style": "solid",
            "border-width": "1px",
            "text-opacity": 0, // This hides the label
            color: "black", // Text color
            "text-background-color": "white",
            "text-background-opacity": 1, // Fully opaque background
            "text-background-shape": "roundrectangle",
            "text-background-padding": "2px",
            "text-border-opacity": 1,
            "text-border-width": 1,
            "text-border-color": "black",
            "text-valign": "center",
            "text-halign": "center",
            "font-size": "18px",
            "text-wrap": "wrap",
            "text-max-width": "100px",
          },
        },
        {
          selector: 'edge[interaction="Activation"]',
          style: {
            "line-color": "green",
            "target-arrow-color": "green",
            "target-arrow-shape": "triangle",
          },
        },
        {
          selector: 'edge[interaction="Repression"]',
          style: {
            "line-color": "red",
            "target-arrow-color": "red",
            "target-arrow-shape": "tee",
          },
        },
        {
          selector: 'edge[interaction="Unknown"]',
          style: {
            "line-color": "gray",
            "target-arrow-color": "gray",
          },
        },
        {
          selector: "edge",
          style: {
            width: 1, // Set this to a lower value for thinner edges
            // "line-color": "#ccc",
            // "target-arrow-color": "#ccc",
            "curve-style": "bezier",
            // label: "data(Interaction)",
            "font-size": "8px",
          },
        },
      ],
    });
    cyInstance.nodes().positions(function (node: { position: () => any }) {
      return node.position(); // This will use the position from the JSON data
    });
    // Calculate and set the degree for each node
    cyInstance
      .nodes()
      .forEach(
        (node: {
          data: (arg0: string, arg1: any) => void;
          degree: () => any;
        }) => {
          node.data("degree", node.degree());
        }
      );
    cyInstance.on("tap", "node", function (evt: { target: any }) {
      var node = evt.target;
      selectedNode = node;
      cyInstance.batch(function () {
        // Reset all nodes
        cyInstance.nodes().style("text-opacity", 0);
        // Show label for the clicked node
        node.style("text-opacity", 1);
        node.style("z-index", 10);
      });
    });

    cyInstance.on("tap", function (evt: { target: any }) {
      if (evt.target === cyInstance) {
        cyInstance.nodes().style("text-opacity", 0);
        selectedNode = null;
      }
    });

    cyInstance.fit();
    generateColorKey();
  }

  function getColorForValue(value: number) {
    if (value < 1) {
      const intensity = Math.max(0, Math.min(255, Math.round(255 * value)));
      return `rgb(${intensity}, ${!isNaN(intensity) ? intensity : 50}, 255)`;
    } else if (value === 1) {
      return "rgb(255, 255, 255)"; // White
    } else {
      // White to Yellow to Orange to Red (1 to 3+)
      const redIntensity = Math.min(255, Math.round((255 * (value - 1)) / 2));
      const greenIntensity = Math.max(
        0,
        Math.min(255, Math.round((255 * (3 - value)) / 2))
      );
      return !isNaN(greenIntensity)
        ? `rgb(255, ${greenIntensity}, 0)`
        : "rgb(255, 0, 255)";
    }
  }

  function handleReset() {
    cyInstance.destroy();
    createInitialGraph(data100);
    recalculateLayout();
    selectedNode = null;
  }

  function handleDeleteNode() {
    if (selectedNode) {
      const nodeId = selectedNode.id();
      cyInstance.remove(selectedNode);
      delete nodePositions[nodeId as unknown as keyof object];
      recalculateLayout();
      selectedNode = null;
    }
  }

  function recalculateLayout() {
    cyInstance
      .layout({
        name: "cose",
        animate: false,
        nodeOverlap: 20,
        componentSpacing: 100,
        nodeRepulsion: 400000,
        edgeElasticity: 100,
        nestingFactor: 5,
        gravity: 80,
        numIter: 1000,
        initialTemp: 200,
        coolingFactor: 0.95,
        minTemp: 1.0,
      })
      .run();
    cyInstance
      .nodes()
      .forEach((node: { id: () => string | number; position: () => any }) => {
        nodePositions[node.id() as unknown as keyof Object] = node.position();
      });
  }

  function updateGraph() {
    cyInstance
      .nodes()
      .forEach(
        (node: {
          data: (arg0: string) => any;
          style: (arg0: string, arg1: string) => void;
        }) => {
          const newColor = getColorForValue(
            node.data((selectedFileName = fileOptions[selectedFileIndex]))
          );
          node.style("background-color", newColor);
        }
      );

    // Update node labels if needed
    cyInstance
      .nodes()
      .forEach(
        (node: {
          style: (arg0: string, arg1: (ele: any) => string) => void;
        }) => {
          node.style(
            "label",
            function (ele: { data: (arg0: string) => string }) {
              return (
                ele.data("name") +
                "\nDegree: " +
                ele.data("degree") +
                "\nFold change: " +
                (ele.data((selectedFileName = fileOptions[selectedFileIndex]))
                  ? String(
                      ele.data(
                        (selectedFileName = fileOptions[selectedFileIndex])
                      )
                    )
                  : "N/A")
              );
            }
          );
        }
      );

    // Trigger a redraw
    cyInstance.style().update();
  }

  function handleFileSelect() {
    selectedFileName = fileOptions[selectedFileIndex];
    updateGraph();
    selectedNode = null;
  }

  function generateColorKey() {
    const colorKeyContainer = document.getElementById("colorKey");
    if (!colorKeyContainer) return; // Exit if the container doesn't exist

    colorKeyContainer.innerHTML = ""; // Clear existing content

    const keyValues = [0, 0.5, 1, 1.5, 2, 2.5, 3];

    keyValues.forEach((value) => {
      const keyItem = document.createElement("div");
      keyItem.className = "color-key-item";
      keyItem.style.cssText = `    display: flex;
    flex-direction: row;
    align-items: center;
    margin-bottom: 5px`;
      const colorSwatch = document.createElement("div");
      colorSwatch.className = "color-key-swatch";
      const backgroundColor = getColorForValue(value);
      colorSwatch.style.cssText = `
      width: 20px;
      height: 20px;
      margin-right: 10px;
      border: 1px solid black;
      background-color: ${backgroundColor};
    `;

      const label = document.createElement("span");
      label.textContent =
        value.toFixed(1) === "3.0" ? "> 3.0" : value.toFixed(1);

      keyItem.appendChild(colorSwatch);
      keyItem.appendChild(label);
      colorKeyContainer.appendChild(keyItem);
    });
    const keyItem = document.createElement("div");
    keyItem.className = "color-key-item";
    keyItem.style.cssText = `    display: flex;
    flex-direction: row;
    align-items: center;
    margin-bottom: 5px`;
    const colorSwatch = document.createElement("div");
    colorSwatch.className = "color-key-swatch";
    colorSwatch.style.cssText = `
      width: 20px;
      height: 20px;
      margin-right: 10px;
      border: 1px solid black;
      background-color: rgb(255, 0, 255);
    `;

    const label = document.createElement("span");
    label.textContent = "No Data";

    keyItem.appendChild(colorSwatch);
    keyItem.appendChild(label);
    colorKeyContainer.appendChild(keyItem);
  }
</script>

<h3>
  Genes showing significance through linear regression or a 100-fold increase
  over the course of germination, and their nearest neighbours (n=1)
</h3>
<div class="side-by-side">
  <div bind:this={chartCanvas} class="cytoscape-container"></div>
  <div class="color-key-container">
    <h3>Fold Increase Since Previous Timepoint</h3>
    <div id="colorKey"></div>
  </div>
</div>
<div class="slider-container">
  <h4>Select timepoint</h4>

  <input
    class="slider-input"
    type="range"
    min="0"
    max={fileOptions.length - 1}
    step="1"
    bind:value={selectedFileIndex}
    on:input={handleFileSelect}
    list="tickmarks"
  />
  <datalist id="tickmarks">
    {#each fileOptions as option, i}
      <option value={i}>{option}</option>
    {/each}
  </datalist>
  <div class="slider-label">
    {namesToNiceNamesMap[fileOptions[selectedFileIndex]]}
  </div>

  <button disabled={!selectedNode} on:click={handleDeleteNode}
    >Delete Selected Node</button
  >
  <button on:click={handleReset}>Reset Graph</button>
</div>

<style>
  .cytoscape-container {
    width: calc(80vw - 250px);
    min-width: calc(80vw - 250px);
    height: calc(80vh - 150px);
    /* min-height: calc(80vh - 60px); */
    margin-bottom: 20px;
    max-height: calc(80vh - 150px);
  }
  .slider-container {
    display: flex;
    align-items: center;
    gap: 10px;
    height: 150px;
  }
  button {
    padding: 5px 10px;
    background-color: #ff6473;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 200px;
    height: 50px;
  }

  button:hover {
    background-color: #ff6473;
  }
  button:last-child {
    background-color: #40a798; /* Green color for the reset button */
  }

  button:last-child:hover {
    background-color: #46b8a7;
  }

  button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
  .slider-input {
    width: 50%;
  }

  .slider-label {
    width: 10%;
  }
  .cytoscape-container {
    width: 80%;
    height: calc(80vh - 60px);
  }
  .color-key-container {
    width: 20%;
    padding: 10px;
    background-color: rgba(0, 0, 0, 0);
  }
  .side-by-side {
    display: flex;
    flex-direction: row;
    max-height: calc(100vh - 300px);
  }
</style>
