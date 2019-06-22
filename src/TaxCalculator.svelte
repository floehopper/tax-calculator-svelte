<script>
  import TaxBand from './TaxBand.js';
  import TaxBandCollection from './TaxBandCollection.js';
  import EmployeeNationalInsurance from './EmployeeNationalInsurance.js';
  import EmployerNationalInsurance from './EmployerNationalInsurance.js';
  import NationalInsuranceBarGraph from './NationalInsuranceBarGraph.svelte';

  function roundToNearestPenny(amount) {
    return Number.parseFloat(amount).toFixed(2);
  }

  const employeeCalculator = EmployeeNationalInsurance.for2018to2019();
  const employerCalculator = EmployerNationalInsurance.for2018to2019();

  let salary = 20000;
  $: employeeContributions = roundToNearestPenny(employeeCalculator.taxFor(salary));
  $: employerContributions = roundToNearestPenny(employerCalculator.taxFor(salary));
</script>

<style>
</style>

<div>
  <label>
    Salary: £<input type="number" bind:value={salary}/>
  </label>
  <p>Employee contributions: £{ employeeContributions }</p>
  <p>Employer contributions: £{ employerContributions }</p>

  <NationalInsuranceBarGraph {employeeContributions} {employerContributions} />
</div>
