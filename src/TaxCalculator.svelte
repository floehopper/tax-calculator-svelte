<script>
  import NationalInsuranceBarGraph from './NationalInsuranceBarGraph.svelte';

  class TaxBand {
    constructor (minimum, maximum, rateInPercent) {
      this.minimum = minimum
      this.maximum = maximum
      this.rateInPercent = rateInPercent
    }

    taxFor (amount) {
      return this.amountWithinBand(amount) * this.rateInPercent / 100.0
    }

    amountWithinBand (amount) {
      return Math.max(Math.min(amount - this.minimum, this.maximum - this.minimum), 0)
    }
  }

  class TaxBandCollection {
    constructor (taxBands) {
      this.taxBands = taxBands
    }

    taxFor (amount) {
      return this.taxBands.reduce((total, taxBand) => total + taxBand.taxFor(amount), 0)
    }
  }

  class EmployeeNationalInsurance {
    static for2018to2019 () {
      return new EmployeeNationalInsurance({
        lowerEarningsLimit: 6032,
        primaryThreshold: 8424,
        upperEarningsLimit: 46350,
        rates: {
          belowLowerEarningsLimit: 0.0,
          lowerEarningsLimitToPrimaryThreshold: 0.0,
          primaryThresholdToUpperEarningsLimit: 12.0,
          aboveUpperEarningsLimit: 2.0
        }
      })
    }

    constructor (options) {
      this.collection = new TaxBandCollection([
        new TaxBand(0, options.lowerEarningsLimit, options.rates.belowLowerEarningsLimit),
        new TaxBand(options.lowerEarningsLimit, options.primaryThreshold, options.rates.lowerEarningsLimitToPrimaryThreshold),
        new TaxBand(options.primaryThreshold, options.upperEarningsLimit, options.rates.primaryThresholdToUpperEarningsLimit),
        new TaxBand(options.upperEarningsLimit, Number.POSITIVE_INFINITY, options.rates.aboveUpperEarningsLimit)
      ])
    }

    taxFor (amount) {
      return this.collection.taxFor(amount)
    }
  }

  class EmployerNationalInsurance {
    static for2018to2019 () {
      return new EmployerNationalInsurance({
        lowerEarningsLimit: 6032,
        secondaryThreshold: 8424,
        upperEarningsLimit: 46350,
        rates: {
          belowLowerEarningsLimit: 0.0,
          lowerEarningsLimitToSecondaryThreshold: 0.0,
          secondaryThresholdToUpperEarningsLimit: 13.8,
          aboveUpperEarningsLimit: 13.8
        }
      })
    }

    constructor (options) {
      this.collection = new TaxBandCollection([
        new TaxBand(0, options.lowerEarningsLimit, options.rates.belowLowerEarningsLimit),
        new TaxBand(options.lowerEarningsLimit, options.secondaryThreshold, options.rates.lowerEarningsLimitToSecondaryThreshold),
        new TaxBand(options.secondaryThreshold, options.upperEarningsLimit, options.rates.secondaryThresholdToUpperEarningsLimit),
        new TaxBand(options.upperEarningsLimit, Number.POSITIVE_INFINITY, options.rates.aboveUpperEarningsLimit)
      ])
    }

    taxFor (amount) {
      return this.collection.taxFor(amount)
    }
  }

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
