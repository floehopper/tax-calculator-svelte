<script>
  export let employeeContributions;
  export let employerContributions;

  const width = 200;
  const height = 200;

  $: barWidth = width / 2;
  $: totalContributions = parseFloat(employeeContributions) + parseFloat(employerContributions);
  $: scaleFactor = height / totalContributions;
  $: employeeHeight = parseFloat(employeeContributions) * scaleFactor;
  $: employerHeight = parseFloat(employerContributions) * scaleFactor;
  $: employeeY = height - employeeHeight;
  $: employerY = height - employerHeight;
</script>

<style>
  #employee {
    fill: rgb(255, 99, 132);
  }

  #employer {
    fill: rgb(54, 162, 235);
  }
</style>

<div class="chart">
  <svg {width} {height}>
    <g class='bars'>
      <rect id="employer" x="0" y="{employerY}" height="{employerHeight}" width="{barWidth}"/>
      <rect id="employee" x="{barWidth}" y="{employeeY}" height="{employeeHeight}" width="{barWidth}"/>
    </g>
  </svg>
</div>
