<script lang="ts">
</script>

<div>
  <h3>Differential Gene Expression Data</h3>
  <p>
    The original gene expression data for stages of arabidopsis can be found
    here: <a href="https://www.ebi.ac.uk/gxa/experiments/E-GEOD-94457/Results"
      >https://www.ebi.ac.uk/gxa/experiments/E-GEOD-94457/Results</a
    >
  </p>
  <h3>Network Data</h3>
  <p>
    The network used for this project, which originally plotted interactions
    between transcription factors and their target genes, can be found here:
    <a
      href="https://www.ndexbio.org/viewer/networks/5df21e52-97c8-11e5-b435-06603eb7f303"
      >https://www.ndexbio.org/viewer/networks/5df21e52-97c8-11e5-b435-06603eb7f303</a
    >
  </p>
  <h3>Selection of genes</h3>
  <p>
    Genes from the differential gene expression data were selected based on
    linear regression with adjusted p-values to account for the number of
    comparisons being made, as well as any genes with a fold increase of 500x or
    higher from the lowest recorded value to the highest recorded value.
  </p>
  <p>
    Not all of these significant genes were found in the network model,
    therefore the network does not show all significant genes from the original
    dataset.
  </p>
  <p>
    The network's genes were widened out to include the 1 nearest neighbour of
    each of the significant genes which could be found in the network.
  </p>
  <h3>Interpreting the network's colours</h3>
  <p>
    Genes which have no expression data, whether because they were not measured
    to have any at that timepoint in the original dataset, or because they were
    found by Nearest Neighbour from the network dataset, are shown in magenta.
  </p>
  <p>
    Genes are otherwise coloured by the fold increase/decrease which they
    experienced since the previous timepoint. Decreases are shown in blue, with
    larger decreases in darker blue. Increases are shown in orange and red, with
    larger increases shown in deeper red.
  </p>
  <h3>Modifying the network</h3>
  <p>
    Nodes from the network can be deleted by clicking on a node to select it and
    clicking 'Delete Selected Node'. This allows users to view the effect of
    network peturbations on the network structure.
  </p>
  <p>
    The network can be reset by clicking "Reset Graph", which will set the
    timepoint back to 0 hours and replace all nodes on the network.
  </p>
</div>
